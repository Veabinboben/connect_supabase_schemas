```sql
create or replace function public.recommend_paginated_users(p_limit INT, p_timestamp timestamptz default CURRENT_TIMESTAMP)
returns table(avatar varchar, username varchar, telegram varchar, id uuid,common_count int,is_followed boolean, register_date timestamptz)
language plpgsql
SECURITY DEFINER
as $$
declare
 target_user uuid;
begin
    target_user := auth.uid();
    RETURN QUERY
    with target_follows as (
     select followee_id
     from follows
     where follower_id = target_user
   ),
   common_counts as (
    select
      f.follower_id as other_user,
      count(*)::int as common_count
    from follows f
    join target_follows tf on f.followee_id = tf.followee_id
    where f.follower_id <> target_user
    group by f.follower_id
  )
  select
    ud.avatar,
    ud.name,
    ud.telegram,
    u.id,
    coalesce(cc.common_count, 0) as common_count,
    CASE
        WHEN EXISTS (SELECT 1 FROM follows WHERE follower_id = auth.uid() and followee_id = u.id) THEN true
        ELSE false
    END AS is_followed,
    ud.register_date
  from auth.users u
  left join public.user_data ud on ud.id = u.id
  left join common_counts cc on cc.other_user = u.id
  where u.id <> target_user
  and u.id not in (select followee_id from target_follows)
  and ud.register_date < p_timestamp
  order by common_count desc, u.id, ud.register_date DESC
  LIMIT p_limit;
end;
$$
```
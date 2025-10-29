```sql
create or replace function public.get_user(p_user_id uuid default null)
returns table(user_id uuid, name varchar,  phone varchar,  
email varchar, birthday varchar,  gender varchar, telegram varchar,
bio varchar, avatar varchar, banner varchar, followers bigint, following bigint, is_me boolean, is_followed boolean)
language plpgsql
SECURITY DEFINER
as $$
begin
    RETURN QUERY
    SELECT u.id, u.name, u.phone , u.email, u.birthday, u.gender, u.telegram, u.bio, u.avatar, u.banner,
    (
         SELECT COUNT(DISTINCT f.follower_id)
         FROM follows f
         WHERE f.followee_id = u.id
       ) AS follower_count,
    (
         SELECT COUNT(DISTINCT f.followee_id)
         FROM follows f
         WHERE f.follower_id = u.id
       ) AS following_count,
    CASE
        WHEN EXISTS (SELECT 1 FROM likes WHERE u.id = auth.uid()) THEN true
        ELSE false
    END AS is_me,
    CASE
        WHEN EXISTS (SELECT 1 FROM follows WHERE follower_id = auth.uid() and followee_id = u.id) THEN true
        ELSE false
    END AS is_followed
    FROM user_data u
    WHERE u.id = COALESCE(p_user_id,auth.uid())
    GROUP BY u.id
    LIMIT 1;
end;
$$
```
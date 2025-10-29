```sql
create or replace function public.find_paginated_users(search_querry varchar, p_limit INT, p_timestamp timestamptz default CURRENT_TIMESTAMP)
returns table(avatar varchar, username varchar, telegram varchar, id uuid,is_followed boolean, register_date timestamptz)
language plpgsql
SECURITY DEFINER
as $$
declare
 _count int;
begin
    -- RAISE LOG 'RPC function called with search_querry: %', search_querry;
    RETURN QUERY EXECUTE
    'SELECT u.avatar, u.name,u.telegram , u.id,
    CASE
        WHEN EXISTS (SELECT 1 FROM follows WHERE follower_id = auth.uid() and followee_id = u.id) THEN true
        ELSE false
    END AS is_followed,
    u.register_date
     FROM user_data u
     WHERE (u.name ILIKE $1 or u.telegram ILIKE $1) and  u.register_date < $2
      ORDER BY u.register_date DESC
     LIMIT $3'
  USING ('%' || replace(replace(search_querry, '\', '\\'), '%', '\%') || '%')::text, p_timestamp,p_limit;
   --GET DIAGNOSTICS _count = ROW_COUNT;
  --RAISE LOG 'find_paginated_users returned % rows with % ', _count,search_querry ;
end;
$$
```
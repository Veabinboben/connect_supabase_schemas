```sql
create or replace function public.find_paginated_posts(search_querry varchar, p_limit INT,
  p_timestamp timestamptz default CURRENT_TIMESTAMP )
returns table(text varchar, avatar varchar, username varchar, id bigint, posted_at timestamptz)
language plpgsql
SECURITY DEFINER
as $$
begin
    RETURN QUERY EXECUTE
    'SELECT p.text, u.avatar, u.name, p.id, p.posted_at
     FROM posts p
     JOIN user_data u ON u.id = p.user_id
     WHERE p.posted_at < $1
     AND p.text ILIKE $2
     ORDER BY p.posted_at DESC
     LIMIT $3'
  USING p_timestamp, ('%' || replace(replace(search_querry, '\', '\\'), '%', '\%') || '%')::text, p_limit;
end;
$$
```
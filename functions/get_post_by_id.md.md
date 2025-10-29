```sql
create or replace function public.get_post_by_id(p_post_id int)
returns table(user_id uuid, text varchar, posted_at timestamptz, avatar varchar,
username varchar, likes int, has_attachments boolean, id bigint, has_my_like boolean, is_my_post boolean)
language plpgsql
SECURITY DEFINER
as $$
begin
    RETURN QUERY
    SELECT posts.user_id, posts.text, posts.posted_at , user_data.avatar,  user_data.name, posts.likes, posts.has_attachments, posts.id,
    CASE
        WHEN EXISTS (SELECT 1 FROM likes WHERE likes.post_id = posts.id and likes.user_id = auth.uid()) THEN true
        ELSE false
    END AS has_my_like,
    CASE
        WHEN EXISTS (SELECT 1 FROM likes WHERE posts.user_id = auth.uid()) THEN true
        ELSE false
    END AS is_my_post
    FROM posts JOIN user_data on user_data.id = posts.user_id
    WHERE posts.id = p_post_id
    LIMIT 1;
end;
$$
```
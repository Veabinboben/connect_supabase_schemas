```sql
create or replace function public.add_or_remove_like(p_post_id int)
returns void
language plpgsql
SECURITY DEFINER
as $$
begin
     IF EXISTS (SELECT 1 FROM likes WHERE likes.post_id = p_post_id and likes.user_id = auth.uid()) THEN
        DELETE FROM likes WHERE likes.post_id = p_post_id and likes.user_id = auth.uid();
        Update posts set likes = likes - 1 where posts.id = p_post_id;
    ELSE
        INSERT INTO likes (post_id, user_id) VALUES (p_post_id, auth.uid());
        Update posts set likes = likes + 1 where posts.id = p_post_id;
    END IF;
end;
$$
```
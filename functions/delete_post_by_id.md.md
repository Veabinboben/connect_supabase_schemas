
```sql
create or replace function public.delete_post_by_id(p_post_id int)
returns boolean
language plpgsql
SECURITY DEFINER
as $$
begin
    IF EXISTS (SELECT 1 FROM posts WHERE posts.id = p_post_id and posts.user_id = auth.uid()) THEN
       delete
       FROM posts
       WHERE posts.id = p_post_id and posts.user_id = auth.uid();
       return TRUE;
    ELSE
       return FALSE;
    END IF;
end;
$$
```
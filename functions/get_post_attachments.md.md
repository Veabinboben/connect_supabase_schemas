```sql
create or replace function public.get_post_attachments(p_post_id int)
returns table(attachment_url varchar)
language plpgsql
SECURITY DEFINER
as $$
begin
    RETURN QUERY
    SELECT image_attachments.url
    FROM image_attachments
    WHERE image_attachments.post_id = p_post_id;
end;
$$
```
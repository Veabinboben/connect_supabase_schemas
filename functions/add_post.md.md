```sql
create or replace function public.add_post(content varchar(500))
returns int
language plpgsql
SECURITY DEFINER
as $$
declare
 new_id int;
begin
  insert into public.posts (user_id,text,has_attachments)
  values (
    auth.uid(),
    content,
    false
   )
   returning id into new_id;
   return new_id;
end;
$$
```

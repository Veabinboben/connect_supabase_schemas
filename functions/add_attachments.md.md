```sql
create or replace function public.add_attachment(links text[], post_id  int)
returns void
language plpgsql
SECURITY DEFINER
as $$
declare
 array_len int;
 val text;
begin
  array_len := array_length(links,1);
  if array_len < 6 and array_len > 0 then
   foreach val in array links loop
    insert into image_attachments (post_id, url) values (post_id,val);
   end loop;
  else
   RAISE EXCEPTION 'invalid ammount';
  end if;
end;
$$
```
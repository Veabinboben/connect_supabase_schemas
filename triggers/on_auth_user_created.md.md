```sql
create or replace function public.handle_new_user()
returns trigger
language plpgsql
security definer set search_path = ''
as $$
begin
  insert into public.user_data (id, name,email,phone,birthday,gender,telegram,register_date)
  values (new.id,
    new.raw_user_meta_data ->> 'name',
    new.raw_user_meta_data ->> 'email',
    new.raw_user_meta_data ->> 'phone',
    new.raw_user_meta_data ->> 'birthday',
    new.raw_user_meta_data ->> 'gender',
    new.raw_user_meta_data ->> 'telegram',
    now()
   );
  return new;
end;
$$;
create or replace trigger on_auth_user_created
  after insert on auth.users
  for each row execute procedure public.handle_new_user();
```
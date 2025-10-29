```sql
create or replace function public.update_user(
  p_name text DEFAULT NULL,
  p_banner text DEFAULT NULL,
  p_gender text DEFAULT NULL,
  p_telegram text DEFAULT NULL,
  p_avatar text DEFAULT NULL,
  p_bio text DEFAULT NULL
  )
returns void
language plpgsql
SECURITY DEFINER
as $$
begin
    update user_data
    set
      name = COALESCE(p_name, name),
      banner = COALESCE(p_banner, banner),
      gender = COALESCE(p_gender, gender),
      telegram = COALESCE(p_telegram, telegram),
      avatar = COALESCE(p_avatar, avatar),
      bio = COALESCE(p_bio, bio)
    where id = auth.uid();
end;
$$
```
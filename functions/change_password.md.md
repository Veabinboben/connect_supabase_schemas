```sql
create or replace function public.change_password(current_password varchar, new_password varchar)
returns void
language plpgsql
SECURITY DEFINER
as $$
declare
 _uid uuid;
 user_id uuid;
begin
  -- Check current password
  user_id := auth.uid();
  SELECT id INTO _uid
  FROM auth.users
  WHERE id = user_id
  AND encrypted_password =
  crypt(current_password::text, auth.users.encrypted_password);
  -- Throw if not found
  IF NOT FOUND THEN
    RAISE EXCEPTION 'incorrect password';
  END IF;
  UPDATE auth.users SET
  encrypted_password =
  crypt(new_password, gen_salt('bf'))
  WHERE id = user_id;
end;
$$
```
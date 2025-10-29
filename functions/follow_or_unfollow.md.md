```sql
create or replace function public.follow_or_unfollow(p_user_id uuid)
returns void
language plpgsql
SECURITY DEFINER
as $$
begin
     IF EXISTS (SELECT 1 FROM follows WHERE follower_id = auth.uid() and followee_id = p_user_id) THEN
        DELETE FROM follows where follower_id = auth.uid() and followee_id = p_user_id;
    ELSE
        INSERT INTO follows (follower_id, followee_id) VALUES (auth.uid(), p_user_id);
    END IF;
end;
$$
```
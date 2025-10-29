
```sql
CREATE OR REPLACE FUNCTION update_post_attachments()
RETURNS TRIGGER
language plpgsql
AS $$
BEGIN
    UPDATE posts
    SET has_attachments = TRUE
    WHERE id = NEW.post_id;
    RETURN NEW;  
END;
$$;
CREATE TRIGGER post_insert_trigger
AFTER INSERT ON image_attachments
FOR EACH ROW
EXECUTE FUNCTION update_post_attachments();
```
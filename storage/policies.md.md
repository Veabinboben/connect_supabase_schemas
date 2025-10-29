
Create public bucket **images** with allowed types `image/jpg, image/jpeg, image/png, image/webp`

Give all authenticated users access to **attachments**

```sql
((bucket_id = 'images'::text) AND ((storage.foldername(name))[1] = 'attachments'::text) AND (auth.role() = 'authenticated'::text))
```

Give all authenticated users access to **banners**

```sql
((bucket_id = 'images'::text) AND ((storage.foldername(name))[1] = 'banners'::text) AND (auth.role() = 'authenticated'::text))
```

Give all authenticated users access to **avatars**

```sql
((bucket_id = 'images'::text) AND ((storage.foldername(name))[1] = 'avatars'::text) AND (auth.role() = 'authenticated'::text))
```
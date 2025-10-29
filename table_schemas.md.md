-- WARNING: This schema is for context only and is not meant to be run.
-- Table order and constraints may not be valid for execution.
```sql
CREATE TABLE public.follows (
  id bigint GENERATED ALWAYS AS IDENTITY NOT NULL,
  follower_id uuid,
  followee_id uuid,
  CONSTRAINT follows_pkey PRIMARY KEY (id),
  CONSTRAINT follows_follower_id_fkey FOREIGN KEY (follower_id) REFERENCES auth.users(id),
  CONSTRAINT follows_followee_id_fkey FOREIGN KEY (followee_id) REFERENCES auth.users(id)
);

CREATE TABLE public.image_attachments (
  id bigint GENERATED ALWAYS AS IDENTITY NOT NULL,
  post_id bigint,
  url character varying,
  CONSTRAINT image_attachments_pkey PRIMARY KEY (id),
  CONSTRAINT image_attachments_post_id_fkey FOREIGN KEY (post_id) REFERENCES public.posts(id)
);

CREATE TABLE public.likes (
  id bigint GENERATED ALWAYS AS IDENTITY NOT NULL,
  user_id uuid,
  post_id bigint,
  CONSTRAINT likes_pkey PRIMARY KEY (id),
  CONSTRAINT likes_user_id_fkey FOREIGN KEY (user_id) REFERENCES auth.users(id),
  CONSTRAINT likes_post_id_fkey FOREIGN KEY (post_id) REFERENCES public.posts(id)
);

CREATE TABLE public.posts (
  id bigint GENERATED ALWAYS AS IDENTITY NOT NULL,
  user_id uuid,
  text character varying,
  posted_at timestamp with time zone DEFAULT now(),
  has_attachments boolean DEFAULT false,
  likes integer DEFAULT 0,
  CONSTRAINT posts_pkey PRIMARY KEY (id),
  CONSTRAINT posts_user_id_fkey FOREIGN KEY (user_id) REFERENCES auth.users(id)
);

CREATE TABLE public.user_data (
  id uuid NOT NULL,
  name character varying,
  phone character varying,
  email character varying CHECK (email::text ~ '^[\w\-\.]+@([\w\-]+\.)+[\w\-]+$'::text),
  birthday character varying,
  gender character varying CHECK (gender::text = ANY (ARRAY['male'::character varying, 'female'::character varying]::text[])),
  telegram character varying,
  avatar character varying,
  bio character varying,
  banner character varying,
  register_date timestamp with time zone DEFAULT now(),
  CONSTRAINT user_data_pkey PRIMARY KEY (id),
  CONSTRAINT user_data_id_fkey FOREIGN KEY (id) REFERENCES auth.users(id)
);
```
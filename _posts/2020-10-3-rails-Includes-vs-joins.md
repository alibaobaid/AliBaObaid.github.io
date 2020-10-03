---
layout: post
title: Rails Includes vs Joins
summary: ActiveRecord's joins, includes methods, it is so important to Know when and where to use each method
category: rails
author: Ali BaObaid
thumbnail: /assets/img/posts/includesvsjoins.jpg
keywords: rails,  Rails Includes vs Joins
---
When I am delaing with data within assosations in Rails, I always got confusion about using ActiveRecord's joins, includes methods, it is so important to Know when and where to use each method that what this article will explore

**includes** 

If we go through Rails documenting, it clearly says — ‘With includes, Active Record ensures that all of the specified associations are loaded using the minimum possible number of queries’. When we need data to be used from associated tables, includes must be used

**Can includes prevent N+1 queries?**

Yes. includes will load all records of the parent and all of the records referenced as arguments in the includes method.

Notice how using includes in the example below only triggers 1 additional query. Without includes, there would be an additional query to count the number of comments for every post

```ruby
Post.includes(:comments).map { |post| post.comments.size }
```
```sql
Post Load (1.2ms)  SELECT  "posts".* FROM "posts"
  Comment Load (2.0ms)  SELECT "comments".* FROM "comments" WHERE "comments"."post_id" IN (1, 3, 4, 5, 6)
=> [3,5,2,4,2,1]
```
Isn’t it fantastic? Yes, it is. It saves hell lot of time. And it proves to be very efficient, when application contains huge data.
So, does includes always preferred over joins?
Hopefully not, there are some cases when includes does not exceed joins in terms of efficiency.

**joins**

If you are just filtering results - not accessing records from a relationship - joins is your goto. The example below fetches all blog posts with a comment authored by Derek. I'm not accessing any of the associated comments, so joins is a great fit

```ruby
Post.joins(:comments).
  where(:comments => {author: 'Derek'}).
  map { |post| post.title}
```
```sql
Post Load (1.2ms)  SELECT  "posts".* FROM "posts" INNER JOIN "comments" ON "comments"."post_id" = "posts"."id" WHERE "comments"."author" = $1
```
I hope that you get the difference between includes and joins and understand when to use each method 

references:
- [Derek Haynes](https://scoutapm.com/blog/activerecord-includes-vs-joins-vs-preload-vs-eager_load-when-and-where)
- [Swapnil Gourshete](https://medium.com/@swapnilggourshete/rails-includes-vs-joins-9bf3a8ada00)

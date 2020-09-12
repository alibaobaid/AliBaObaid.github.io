---
layout: post
title: misuse ActiveRecord scopes on associations
summary: Don’t call scopes on associations when you’re rendering collections
category: rails
author: Ali BaObaid
thumbnail: /assets/img/posts/scopes.jpg
keywords: rails perofmance, where rails methods, rails, activerecord scopes, rails associations
---

ActiveRecord is great. Really, it is. But it’s an abstraction, intended to insulate you from the actual SQL queries being run on your database. And, if you don’t understand how ActiveRecord works, you may be causing SQL queries to run that you didn’t intend to, at this series posts I will clarify the misuse of ActiveRecord  methods that may slow down your app

**Scopes On Associations** 

Calling scopes on associations means we cannot preload the result. In the example below, we can preload the comments on a post, but we can’t preload the active comments on a post, so we have to go back to the database and execute new queries for every element in the collection

```
class Comment < ActiveRecord::Base
  belongs_to :post

  scope :active, -> { where(soft_deleted: false) }
end

```
```
<% @posts.each do |post| %>
  <%= post.content %>
  <%= render partial: :comment, collection: post.active %>
<% end %>

```
This isn’t a problem when you only do it once, and not on every element of a collection (like every post, as above). Feel free to use scopes galore in those situations - for example, if this was a PostsController#show action that only displayed one post and its associated comments. But in collections, scopes on associations cause N+1s, every time.

The best way I’ve found to fix this particular problem is to create a new association.
The idea is that you create a new association, which you can preload

```
class Post
  has_many :comments
  has_many :active_comments, -> { active }, class_name: "Comment"
end

class Comment
  belongs_to :post
  scope :active, -> { where(soft_deleted: false) }
end

class PostsController
  def index
    @posts = Post.includes(:active_comments)
  end
end

```
now executes just 2 SQL queries, one on the Posts table and one on the Comments table. Nice!

references:
- [Nate Berkopec](https://www.speedshop.co/2019/01/10/three-activerecord-mistakes.html)
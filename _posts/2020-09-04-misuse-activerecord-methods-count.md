---
layout: post
title: misuse ActiveRecord methods (count)
summary: if you don’t understand how ActiveRecord works, you may be causing SQL queries to run that you didn’t intend to
category: rails
author: Ali BaObaid
thumbnail: /assets/img/posts/code.jpg
keywords: rails perofmance, ruby methods count, count vs size in rails, rails, activerecord methods
---

ActiveRecord is great. Really, it is. But it’s an abstraction, intended to insulate you from the actual SQL queries being run on your database. And, if you don’t understand how ActiveRecord works, you may be causing SQL queries to run that you didn’t intend to, at this series posts I will clarify the misuse of ActiveRecord  methods that may slow down your app

**COUNT** 

count on an ActiveRecord relation will always try to execute a SQL query, every time. This is inappropriate in most scenarios, but, in general, only use count if you want to always execute a SQL COUNT right now, the most common cause of unnecessary count queries is when you count an association you will use later in the same block or have already used

```
comment_count = post.comments.count

comment_details = post.comments.each{|comment| ... }

```
This will executes 2 queries, a COUNT and a SELECT. The COUNT is executed by `post.comments.count`, and `post.comments.each` executes a SELECT to load all the comments. changing the order of the code in the partial and replace count by size eliminates the COUNT query completely and keeps the SELECT

```
comment_details = post.comments.each{|comment| ... }

comment_count = post.comments.size

```
let look inside the actual method definition of size on ActiveRecord::Relation

```
def size
  loaded? ? @records.length : count(:all)
end

```
If the relation is loaded (that is, the query that the relation describes has been executed and we have stored the result), we call length on the already loaded record array. That’s just a simple Ruby method on Array. If the ActiveRecord::Relation isn’t loaded, we trigger a COUNT query

On the other hand, here’s how count is implemented (in ActiveRecord::Calculations):

```
def count(column_name = nil)
  if block_given?
    # ...
    return super()
  end

  calculate(:count, column_name)
end

```
And, of course, the implementation of calculate doesn’t memoize or cache anything, and executes a SQL calculation every time it is called

I hope that you got the difference between the two methods so that won't cause misuse of count method.
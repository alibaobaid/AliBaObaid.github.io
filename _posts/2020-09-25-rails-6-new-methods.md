---
layout: post
title: Helpful methods in rails 6
summary: New methods introduced in Rails 6
category: rails
author: Ali BaObaid
thumbnail: /assets/img/posts/rails-6.jpg
keywords: Rails 6, rails, Helpful mehtods
---
Rails 6 introduced new helpful methods in this article I will clarify that methods with a brief explanation

**create_or_find_by** 

```create_or_find_by``` Method added to Rails 6. it rely on unique constraints on the underlying table. It is used to create a new record in the database with given data that has one or more unique constraints of its columns. If a record already exists with unique constraints, it raises an exception which is the same as raised in insertion and finds the existing record with given attributes using find_by

```ruby
class CreatePosts < ActiveRecord::Migration[6.0]
  def change
    create_table :posts do |t|
      t.string :title, index: { unique: true }

      t.timestamps
    end
  end
end
```
```ruby
class Post < ApplicationRecord
  validates :title, presence: true
end
```
![](/assets/img/posts/code_1.jpg)

![](/assets/img/posts/code_2.jpg)


**upsert_all**

```upsert_all``` Updates or inserts (upserts) multiple records into the database in a single SQL INSERT statement. It does not instantiate any models nor does it trigger Active Record callbacks or validations. Though passed values go through Active Record’s type casting and serialization.

The attributes parameter is an Array of Hashes. Every Hash determines the attributes for a single row and must have the same keys, the example below clarify the use of ```upsert_all```

```ruby
Book.upsert_all([
  { title: "Rework", author: "David", isbn: "1" },
  { title: "Eloquent Ruby", author: "Russ", isbn: "1" }
], unique_by: :isbn)
```


**insert_all**

```insert_all``` inserts multiple records into the database in a single SQL INSERT statement. It does not instantiate any models nor does it trigger Active Record callbacks or validations. Though passed values go through Active Record’s type casting and serialization.

The attributes parameter is an Array of Hashes. Every Hash determines the attributes for a single row and must have the same keys. the example below clarify the use of ```insert_all```

```ruby
Book.insert_all([
  { id: 1, title: "Rework", author: "David" },
  { id: 1, title: "Eloquent Ruby", author: "Russ" }
])
```
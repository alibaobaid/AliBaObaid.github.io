---
layout: post
title: hash helpfull methods
summary: Ruby comes with wonderful methods that made dealing with hashes easier 
category: ruby
author: Ali BaObaid
thumbnail: /assets/img/posts/ruby_hash.jpg
keywords: rails,  Rails Includes vs Joins
---
Most developers this a days are dealing with Hashes (json data) in their daily work for many reasons,
and happily ruby comes with wonderful methods that made dealing with hashes easier
so at this article I will clarify the most helpful methods from ruby hash methods

**merge**
Returns the new Hash object formed by merging the hash into self and each new-key entry is added at the end each duplicate-key entry’s value overwrites the previous value.

```ruby
h = {foo: 0, bar: 1, baz: 2}
h1 = {bat: 3, bar: 4}
h.merge(h1) # => {:foo=>0, :bar=>4, :baz=>2, :bat=>3}
```

**compact** 
Returns a copy of the hash with all nil-valued entries removed

```ruby
h = {foo: 0, bar: nil, baz: 2, bat: nil}
h1 = h.compact
h1 # => {:foo=>0, :baz=>2}
```
**fetch**
Returns a value from hash for the given key. If the key can't be found, and there are no other arguments, it raises an IndexError exception; if default is given, it is returned; if the optional block is specified, its result is returned.

```ruby
h = {bar: 1, baz: 2}
h.fetch(:bar) # => 1
h.fetch(:foo) # => KeyError (key not found: :foo)
h.fetch(:foo, 0) # => 0
```

**fetch_values**
Returns a new Array containing the values associated with the given keys

```ruby
h = {foo: 0, bar: 1, baz: 2}
h.fetch_values(:baz, :foo) # => [2, 0]
```
Returns a new empty Array if no arguments given and raises an exception if the key can't be found.

**dig**
Ruby’s dig methods are useful for accessing nested data.

```ruby
h = {foo: {bar: {baz: 2}}}
h.dig(:foo) # => {:bar=>{:baz=>2}}
h.dig(:foo, :bar) # => {:bar=>{:baz=>2}}
h.dig(:foo, :bar, :baz) # => 2
h.dig(:foo, :bar, :BAZ) # => nil
```
it can be use same as fetch in one level hash and if the key not found it won't raises an exception

**select**
Returns a new Hash object whose entries are those for which the block returns a truthy value

```ruby
h = {foo: 0, bar: 1, baz: 2}
h.select {|key, value| value < 2 } # => {:foo=>0, :bar=>1}
```

references:
- [ruby-doc](https://ruby-doc.org/core-2.7.2/Hash.html)

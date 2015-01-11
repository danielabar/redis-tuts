Redis Essentials
==========

> Learning [Redis](http://redis.io/) with Tuts Plus [course](https://code.tutsplus.com/courses/redis-essential).

## Installation

  ```bash
  brew install redis
  ```

Start server

  ```bash
  redis-server /usr/local/etc/redis.conf
  ```

Start CLI

  ```bash
  redis-cli
  ```

By default, you're in data structure "0". Switch/create a new data structure "1"

  ```
  select 1
  ```

Set a key "name" with string value "joe", then retrieve it

  ```
  set name "joe"
  get name      // "joe"
  ```

Switch back to data structure "0", "name" key is not here

  ```
  select 0
  get name    // (nil)
  ```

## Data Structures Overview

Does not have tables or documents. Everything is just keys and values.

Redis supports 5 different data structures, that can be set as values for keys.

* String (or scalar)
* List
* Hash
* Set
* Sorted Set

Example, set the value "redis essentials" in a key named "name"

  ```
  set name "redis essentials"
  ```

### Keys

Keys are always strings. Length of key affects memory usage and lookup time.

Keys can be namespced, for example

  ```
  set user:1 "joe smith"
  set user:2 "jane doe"
  ```

No matter what data structure is used, ALL data put in redis is treated as a byte array.
Each value must be <= 512MB

## String Commands

Set and get a new key

  ```
  set name "john"
  get name // john
  ```

Append to an existing key

  ```
  append name " smith"
  get name  // john smith
  ```

Get only a part of a string

  ```
  getrange name 0 3   // john
  ```

Set part of a string

  ```
  setrange name 5 johnson
  getname   // john johnson
  ```

Get and set a value in one command. This will return whatever is currently in name, and then set the new value

  ```
  getset name "joe smith"   // john johnson (that's what was there before)
  get name                  // joe smith
  ```

ALL Redis commands are atomic. When multiple redis clients are using a single Redis server,
any given client command will be executed entirely before any other client can operate on that same data.


<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](http://doctoc.herokuapp.com/)*

- [Redis Essentials](#redis-essentials)
  - [Installation](#installation)
  - [Data Structures Overview](#data-structures-overview)
    - [Keys](#keys)
  - [String Commands](#string-commands)
  - [List Commands](#list-commands)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

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

Multiselect is used to set multiple key/value pairs in one command

  ```
  mset name "jane doe" age 25
  get name    // jane doe
  get age     // 25
  ```

Just like `getset`, `mset` is also atomic.


Multiget will fetch values for multiple keys in one command

  ```
  mget name age
  // 1) "jane doe"
  // 2) "25"
  ```
Note earlier age was set as number, but running `get` returns what looks like a quoted string.
However, some redis commands know to treat the value as a number. For example, increment (and decrement `decr`)

  ```
  incr age
  // (integer) 26
  get age
  // "26"
  decr age
  // (integer) 25
  get age
  // "25"
  ```

Can also increment/decrement by a particular value

  ```
  incrby age 10
  // (integer) 35
  get age
  // "35"
  decrby age 3
  // (integer) 32
  ```

Note increment/decrement operations work with integers. If try floating point, for example `incrby age 2.5`, will get an error.
To use floating point

  ```
  incrbyfloat age 2.5
  // "34.5"
  ```

For some reason there is no `decrbyfloat`.

If value is originally stored as floating point, then always have to use `decrby`, even for incrementing/decrementing by an integer amount.

To set a key that will expire after some time. For example to set color to blue and expire after 4 seconds

  ```
  setex color 4 blue
  ```

To set expiry in milliseconds instead of seconds

  ```
  psetex color 4321 blue
  ```

To set a key only if the key doesn't already exist, for example, if 'name' key already exists with value of 'jane doe'

  ```
  get name // "jane doe"
  setnx name "john smith"
  // (integer) 0 --> this means command didn't run
  get name // "jane doe" --> unchanged
  ```

To set multiple keys at once only if they don't already exist. For example, age already exists but favcolor does not

  ```
  get age         // "34.5"
  get favcolor    // (nil) --> doesn't exist
  msetnx age 40 favcolor purple
  // (integer 0) --> command didn't run
  get age       // "34.5" --> value unchanged as expected
  get favcolor  // (nil) --> not set even though could have, due to usage of msetnx
  ```

Note with `msetnx` either ALL keys are set or NONE are.

To get number of characters in a key value

  ```strlen name
  (integer) 8

## List Commands

Lists are stored as link lists and they are kept in a specific order.

To push an item into a list, for example to add the value "blue" to a list named "colors"

  ```
  lpush colors "blue"
  ```

Note 'colors' list did not have to exist previously, `lpush` will create it. To add more items

  ```
  lpush colors red orange
  ```

Note 'l' does not stand for list, it stands for 'left', as in `lpush` pushes onto the left side of the list.
To push to the right of the list

  ```
  rpush colors yellow green
  ```

To push a value into a list only if the list already exists (but doesn't care about whether the value exists in the listor not).
For example, the list 'colors' already exists and the value blue is already in it, this push will go through

  ```
  lpushx colors blue
  lpushx names jim  // doesn't work because there is no 'names' list
  ```

To get the number of items in a length

  ```
  llen colors  // (integer) 6
  ```

In this case, the 'l' does stand for list. Inconsistent!

To get all items from a list, specify range starting from 0 (beginning) to -1 (last)

  ```
  lrange colors 0 -1
  ```

To get just a sub range

  ```
  lrange colors 3 4
  ```

To retrieve an item AND remove it from list, for example, from left side (`rpop` for right side)

  ```
  lpop colors  // "blue"
  ```


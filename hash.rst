Hash
====

HDEL
----

.. function:: HDEL key field [field ...]

删除哈希表\ ``key``\ 中的一个或多个指定域。

不存在的域将被忽略。

时间复杂度:
    O(N)，\ ``N``\ 为要删除的域的数量。

返回值:
    | 若域存在且被成功删除，返回\ ``1``\ 。
    | 若\ ``key``\ 不存在或域不存在，返回\ ``0``\ 。

.. note:: 在Redis2.2及2.2以下的版本里，HDEL每次只能删除单个域，如果你需要在一个原子时间内删除多个域，请将命令包含在MULT/EXEC块内。

::

    redis> HSET hash_name jack "Jack Sparrow"
    (integer) 1

    redis> HGET hash_name jack
    "Jack Sparrow"

    redis> HDEL hash_name jack
    (integer) 1

    redis> HGET hash_name jack
    (nil)


HLEN
----

.. function:: HLEN key

返回哈希表\ ``key``\ 中域的数量。

复杂度：
    O(1)

返回值：
    | 哈希表中域的数量。
    | 当\ ``key``\ 不存在时，返回\ ``0``\ 。

::

    redis> HSET hash_name jack "Jack Sparrow"
    (integer) 1

    redis> HSET hash_name gump "Forrest Gump"
    (integer) 1

    redis> HLEN hash_name
    (integer) 2


HEXISTS
-------

.. function:: HEXISTS key field 

查看哈希表\ ``key``\ 中，给定域\ ``field``\ 是否存在。

复杂度：
    O(1)

返回值：
    | 如果哈希表含有给定域，返回\ ``1``\ 。
    | 如果哈希表不含有给定域，或\ ``key``\ 不存在，返回\ ``0``\ 。

::

    redis> HEXISTS phone myphone
    (integer) 0

    redis> HSET phone myphone nokia-1110
    (integer) 1

    redis> HEXISTS phone myphone
    (integer) 1


HMGET
-----

.. function:: HMGET key field [field ...] 

返回哈希表\ ``key``\ 中，一个或多个给定域的值。

如果给定的域不存在于哈希表，那么返回一个\ ``nil``\ 值。

因为不存在的\ ``key``\ 被当作一个空哈希表来处理，所以对一个不存在的\ ``key``\ 进行\ `HMGET`_\ 操作将返回一个只带有\ ``nil``\ 值的表。

复杂度：
    O(N)，\ ``N``\ 为给定域的数量。

返回值：
    一个包含多个给定域的关联值的表，表值的排列顺序和给定域参数的请求顺序一样。

::

    redis> HMSET pet dog "doudou" cat "nounou"  # 一次保存多个值
    OK

    redis> HMGET pet dog cat fake_pet  # 返回值的顺序和传入参数的顺序一样。
    1) "doudou"  
    2) "nounou"
    3) (nil)  # 不存在的域返回nil值


HGET
----

.. function:: HGET key field

返回哈希表\ ``key``\ 中给定域\ ``field``\ 的值。

复杂度：
    O(1)

返回值：
    | 给定域的值。
    | 当给定域不存在或是给定\ ``key``\ 不存在时，返回\ ``nil``\ 。

::

    redis> HSET huangz blog huangz.iteye.com
    (integer) 1

    redis> HGET huangz blog
    "huangz.iteye.com"


HMSET
-----

.. function:: HMSET key field value [field value ...] 

同时将多个\ ``field - value``\ (域-值)对设置到哈希表\ ``key``\ 中。

此命令会覆盖哈希表中已存在的域。

如果\ ``key``\ 不存在，一个空哈希表被创建并执行\ `HMSET`_\ 操作。

复杂度：
    O(N)，\ ``N``\ 为\ ``field - value``\ 对的数量。

返回值：
    | 如果命令执行成功，返回\ ``OK``\ 。
    | 当\ ``key``\ 不是哈希表(hash)类型时，返回一个错误。

::

    redis> HMSET website google www.google.com yahoo www.yahoo.com 
    OK

    redis> HGET website google
    "www.google.com"

    redis> HGET website yahoo
    "www.yahoo.com"

    redis> SET G 10  # 出错情况
    OK

    redis> HMSET G name huangz age 20
    (error) ERR Operation against a key holding the wrong kind of value


HGETALL
-------

.. function:: HGETALL key 

返回哈希表\ ``key``\ 中，所有的域和值。

在返回值里，每个域名(field name)之后是域的值(value)，所以返回值的长度是哈希表大小的两倍。

复杂度：
    O(N)，\ ``N``\ 为哈希表的大小。

返回值：
    以列表形式返回哈希表的域和域的值。
    若\ ``key``\ 不存在，返回空列表。

::

    redis> HSET hash_name jack "Jack Sparrow"
    (integer) 1

    redis> HSET hash_name gump "Forrest Gump"
    (integer) 1

    redis> HGETALL hash_name
    1) "jack"          # 域
    2) "Jack Sparrow"  # 值
    3) "gump"
    4) "Forrest Gump"


HSET
----

.. function:: HSET key field value 

将哈希表\ ``key``\ 中的域\ ``field``\ 的值设为\ ``value``\ 。

如果\ ``key``\ 不存在，一个新的哈希表被创建并进行\ `HSET`_\ 操作。

如果域\ ``field``\ 已经存在于哈希表中，旧值将被覆盖。
                
复杂度：
    O(1)

返回值：
    | 如果\ ``field``\ 是哈希表中的一个新建域，并且值设置成功，返回\ ``1``\ 。
    | 如果哈希表中域\ ``field``\ 已经存在且旧值已被新值覆盖，返回\ ``0``\ 。

::

    redis> HSET website google "www.g.cn"   # 一个新域
    (integer) 1

    redis> HSET website google "www.google.com" # 覆盖一个旧域
    (integer) 0


HINCRBY
-------

.. function:: HINCRBY key field increment

为哈希表\ ``key``\ 中的域\ ``field``\ 的值加上增量\ ``increment``\ 。

如果\ ``key``\ 不存在，一个新的哈希表被创建并执行\ `HINCRBY`_\ 命令。

如果域\ ``field``\ 不存在，或域已有的字符串值不能表示为数字，那么在执行命令前，域的值被设置为\ ``0``\ 。

本操作的值限制在64位(bit)有符号数字表示之内。
                    
复杂度：
    O(1)

返回值：
    哈希表执行\ `HINCRBY`_\ 命令之后，域\ ``field``\ 的值。

::

    redis> HEXISTS hash_count page_views
    (integer) 0

    redis> HINCRBY hash_count page_views 200    
    (integer) 200

    redis> HINCRBY hash_count page_views 10
    (integer) 210


HSETNX
------

.. function:: HSETNX key field value

将哈希表\ ``key``\ 中的域\ ``field``\ 的值设置为\ ``value``\ ，当且仅当域\ ``field``\ 不存在。

若域\ ``field``\ 已经存在，该操作无效。    

如果\ ``key``\ 不存在，一个新哈希表被创建并执行\ `HSETNX`_\ 命令。

复杂度：
    O(1)

返回值：
    设置成功，返回\ ``1``\ 。
    给定域已经存在且没有操作被执行，返回\ ``0``\ 。

::

    redis> HSETNX nosql key-value-store redis
    (integer) 1

    redis> HSETNX nosql key-value-store redis  # 操作无效，域key-value-store已存在
    (integer) 0


HKEYS
-----

.. function:: HKEYS key 

返回哈希表\ ``key``\ 中的所有域。

复杂度：
    O(N)，\ ``N``\ 为哈希表的大小。

返回值：
    | 一个包含哈希表中所有域的表。
    | 当\ ``key``\ 不存在时，返回一个空表。

::

    redis> HMSET website google www.google.com yahoo www.yahoo.com 
    OK

    redis> HKEYS website
    1) "google"
    2) "yahoo"

    redis> EXISTS fake_key
    (integer) 0

    redis> HKEYS fake_key  # 对不存在的key进行HKEYS操作，返回一个空表
    (empty list or set)


HVALS
-----

.. function:: HVALS key 

返回哈希表\ ``key``\ 中的所有值。

复杂度：
    O(N)，\ ``N``\ 为哈希表的大小。

返回值：
    | 一个包含哈希表中所有值的表。
    | 当\ ``key``\ 不存在时，返回一个空表。

::

    redis> HMSET website google www.google.com yahoo www.yahoo.com 
    OK

    redis> HVALS website
    1) "www.google.com"
    2) "www.yahoo.com"
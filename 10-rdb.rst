第 10 章：RDB 持久化
============================

Redis 服务器数据库示例。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        label = "\n图 10-1    数据库状态示例";

        subgraph cluster_server {

            label = "Redis 服务器";

            db0 [label = "数据库 0 | { k1 | v1} | { k2 | v2 } | { k3 | v3 }"];

            db1 [label = "数据库 1 | { k1 | v1} | { k2 | v2 } | { k3 | v3 }"];

            db2 [label = "数据库 2 | { k1 | v1} | { k2 | v2 } | { k3 | v3 }"];

            db0->db1 -> db2 [style = invis];

        }

    }

----

Redis 保存和载入 RDB 文件的流程。

.. graphviz::

    digraph {

        rankdir = LR;

        label = "\n图 10-2    将数据库状态保存为 RDB 文件";

        //

        state [label = "数据库状态", shape = circle];

        rdb [label = "RDB 文件", shape = note, height = 1.8, width = 1.4];

        //

        state -> rdb [label = "保存为", minlen = 2.5];

    }

.. graphviz::

    digraph {

        rankdir = LR;

        label = "\n图 10-3    用 RDB 文件来还原数据库状态";

        //

        state [label = "数据库状态", shape = circle];

        rdb [label = "RDB 文件", shape = note, height = 1.8, width = 1.4];

        //

        state -> rdb [dir = back, label = "还原", minlen = 2.5];

    }


RDB 文件的创建与载入
-------------------------

服务器判断是载入 RDB 文件还是载入 AOF 文件的流程。

.. graphviz::

    digraph {

        label = "\n图 10-4    服务器载入文件时的判断流程";

        node [shape = box];

        //

        server_star [label = "服务器启动", width = 3]

        start_load [label = "执行载入程序", width = 3];

        aof_or_not [label = "已开启 AOF 持久化功能？", shape = diamond];

        load_by_aof [label = "载入 AOF 文件"];

        load_by_rdb [label = "载入 RDB 文件"];

        //

        server_star -> start_load -> aof_or_not;

        aof_or_not -> load_by_aof [label = "是"];

        aof_or_not -> load_by_rdb [label = "否"];

    }

----

负责创建和载入 RDB 文件的两个函数之间的关系。

.. graphviz::

    digraph {

        label = "\n图 10-5    创建和载入 RDB 文件";

        rankdir = LR;

        splines = polyline

        //

        node [shape = circle, width = 1.3, height = 1.3];

        state [label = "数据库状态"];

        rdb [label = "RDB 文件"];

        //

        edge [minlen = 2.5];

        state -> rdb [label = "rdbSave"];

        rdb -> state [label = "\nrdbLoad"];

    }


自动间隔性保存
---------------------

记录了服务器触发自动 ``BGSAVE`` 条件的 ``saveparams`` 属性。

.. graphviz::

    digraph {

        label = "\n图 10-6    服务器状态中的保存条件";

        rankdir = LR;

        node [shape = record];

        //

        redisServer [label = " redisServer | ... | <saveparams> saveparams | ... "];

        saveparams [label = " { { saveparams[0] | seconds \n 900 | changes \n 1 } | { saveparams[1] | seconds \n 300 | changes \n 10 } | { saveparams[2] | seconds \n 60 | changes \n 10000 } } "];

        //

        redisServer:saveparams -> saveparams;

    }

----

记录服务器最后一次执行 ``SAVE`` 或者 ``BGSAVE`` 的时间，
以及自最后一次保存 RDB 文件以来，
服务器进行了多少次写入的 ``lastsave`` 属性和 ``dirty`` 属性。

.. graphviz::

    digraph {

        label = "\n图 10-7    服务器状态示例";

        rankdir = LR;

        node [shape = record];

        //

        redisServer [label = " redisServer | ... | dirty \n 123 | lastsave \n 1378270800 | ... "];

    }

----

用于记录和检查服务器是否需要自动执行 ``BGSAVE`` 的相关属性和数据结构的示例。

.. graphviz::

    digraph {

        label = "\n图 10-8    服务器状态";

        rankdir = LR;

        node [shape = record];

        //

        redisServer [label = " redisServer | ... | <saveparams> saveparams | ... | dirty \n 123 | lastsave \n 1378270800 | ... "];

        saveparams [label = " { { saveparams[0] | seconds \n 900 | changes \n 1 } | { saveparams[1] | seconds \n 300 | changes \n 10 } | { saveparams[2] | seconds \n 60 | changes \n 10000 } } "];

        //

        redisServer:saveparams -> saveparams;

    }


RDB 文件结构
---------------

RDB 文件的总体结构。

.. graphviz::

    digraph {

        label = "\n图 10-10    RDB 文件结构";

        node [shape = record];

        rdb [label = " REDIS | db_version | databases | EOF | check_sum "];

    }

包含数据库 ``0`` 和数据库 ``3`` 的非空 RDB 文件结构示例。

.. graphviz::

    digraph {

        label = "\n图 10-12    带有两个非空数据库的 RDB 文件示例";

        node [shape = record];

        rdb [label = " REDIS | db_version | database 0 | database 3 | EOF | check_sum "];

    }

----

RDB 文件中的数据库结构。

.. graphviz::

    digraph {

        label = "\n图 10-13    RDB 文件中的数据库结构";

        node [shape = record];

        database [label = " SELECTDB | db_number | key_value_pairs "];

    }

示例。

.. graphviz::

    digraph {

        label = "\n图 10-14    数据库结构示例";

        node [shape = record];

        value [label = " SELECTDB | 0 | key_value_pairs "];

    }

包含了数据库部分的 RDB 文件示例。

.. graphviz::

    digraph {

        label = "\n图 10-15    RDB 文件中的数据库结构示例";

        node [shape = record];

        v [label = " REDIS | db_version | SELECTDB | 0 | pairs | SELECTDB | 3 | pairs | EOF | check_sum"];

    }

----

不带过期时间的键值对结构。

.. graphviz::

    digraph {

        label = "\n图 10-16    不带过期时间的键值对";

        node [shape = record];

        kvp [label = " TYPE | key | value "];

    }

示例。

.. graphviz::

    digraph {

        label = "\n图 10-18    无过期时间的字符串键值对示例";

        node [shape = record];

        string [label = " REDIS_RDB_TYPE_STRING | key | value "];

    }


----

带有过期时间的键值对结构。

.. graphviz::

    digraph {

        label = "\n图 10-17    带有过期时间的键值对";

        node [shape = record];

        kvp [label = " EXPIRETIME_MS | ms | TYPE | key | value "];

    }

示例。

.. graphviz::

    digraph {
        
        label = "\n图 10-19    带有过期时间的集合键值对示例";

        node [shape = record];

        set [label = " EXPIRETIME_MS | 1388556000000 | REDIS_RDB_TYPE_SET | key | value "];

    }


----

``int`` 编码的字符串对象的结构。

.. graphviz::

    digraph {

        label = "\n图 10-20    INT 编码字符串对象的保存结构";

        node [shape = record];

        v [label = " ENCODING | integer "];

    }

示例。

.. graphviz::

    digraph {

        label = "\n图 10-21    用 8 位来保存整数的例子";

        node [shape = record];

        v [label = " REDIS_RDB_ENC_INT8 | 123 "];

    }

----

内容没有被压缩的字符串对象的结构。

.. graphviz::

    digraph {

        label = "\n图 10-22    无压缩字符串的保存结构";

        node [shape = record];

        value [ label = " len | string "];

    }

示例。

.. graphviz::

    digraph {

        label = "\n图 10-24    无压缩的字符串";

        node [shape = record];

        value [ label = " 5 | \"hello\" "];

    }

----

内容被压缩后的字符串对象的结构。

.. graphviz::

    digraph {

        label = "\n图 10-23    压缩后字符串的保存结构";

        node [shape = record];

        value [ label = " REDIS_RDB_ENC_LZF | compressed_len | origin_len | compressed_string "];

    }

示例，
其中 ``?`` 代表的是无法用字符串形式打印出来的字节。

.. graphviz::

    digraph {

        label = "\n图 10-25    压缩后的字符串";

        node [shape = record];

        value [label = " REDIS_RDB_ENC_LZF | 6 | 21 | \"?aa???\" "];

    }

----

列表对象的结构。

.. graphviz::

    digraph {

        label = "\n图 10-26    LINKEDLIST 编码列表对象的保存结构";

        node [shape = record];

        value [label = " list_length | item1 | item2 | ... | itemN "];

    }

示例。

.. graphviz::

    digraph {

        label = "\n图 10-27    保存 LINKEDLIST 编码列表的例子";

        node [shape = record];

        list [label = " 3 | 5 | \"hello\" | 5 | \"world\"  |  1 | \"!\"  "];

    }

----

集合对象的结构。

.. graphviz::

    digraph {

        label = "\n图 10-28    HT 编码集合对象的保存结构";

        node [shape = record];

        value [ label = " set_size | elem1 | elem2 | ... | elemN "];

    }

示例。

.. graphviz::

    digraph {

        label = "\n图 10-29    保存 HT 编码集合的例子";

        node [shape = record];

        set [label = " 4 | 5 | \"apple\" | 6 | \"banana\" | 3 | \"cat\" | 3 | \"dog\" "];

    }

----

哈希对象的结构。

.. graphviz::

    digraph {

        label = "\n图 10-30    HT 编码哈希表对象的保存结构";

        node [shape = record];

        hash [label = " hash_size | key_value_pair 1 | key_value_pair 2 | ... | key_value_pair N "];

    }

更详细的哈希对象结构。

.. graphviz::

    digraph {

        label = "\n图 10-32    更详细的 HT 编码哈希表对象的保存结构";

        node [shape = record];

        hash [label = " hash_size | key1 | value1 | key2 | value2 | ... | keyN | valueN "];
    }

示例。

.. graphviz::

    digraph {

        label = "\n图 10-33    保存 HT 编码哈希表的例子";

        node [shape = record];

        hash [label = " 2 | 1 | \"a\" | 5 | \"apple\" | 1 | \"b\" | 6 | \"banana\" "];
    }

----

有序集合对象结构。

.. graphviz::

    digraph {

        label = "\n图 10-34    SKIPLIST 编码有序集合对象的保存结构";

        node [shape = record];

        zset [label = " sorted_set_size | element1 | element2 | ... | elementN "];

    }

更详细的有序集合对象结构。

.. graphviz::

    digraph {

        label = "\n图 10-36    更详细的 SKIPLIST 编码有序集合对象的保存结构";

        node [shape = record];

        sorted_set [label = " sorted_set_size | member1 | score1 | member2 | score2 | ... | memberN | scoreN "];

    }

示例。

.. graphviz::

    digraph {

        label = "\n图 10-37    保存 SKIPLIST 编码有序集合的例子";

        node [shape = record];

        sorted_set [label = " 2 | 2 | \"pi\" | 4 | \"3.14\" | 1 | \"e\" | 3 | \"2.7\" "];

    }



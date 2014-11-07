第 4 章：字典
================

一个空的哈希表。

.. graphviz::

    digraph {

        label = "\n 图 4-1    一个空的哈希表";

        rankdir = LR;

        //

        node [shape = record];

        dictht [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 0"];

        table [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        //

        node [shape = plaintext, label = "NULL"];

        null0;
        null1;
        null2;
        null3;

        //

        dictht:table -> table:head;

        table:0 -> null0;
        table:1 -> null1;
        table:2 -> null2;
        table:3 -> null3;

    }

----

使用链地址法解决冲突的哈希表。

.. graphviz::

    digraph {

        label = "\n 图 4-2    连接在一起的键 k1 和键 k0";

        rankdir = LR;

        //

        node [shape = record];

        dictht [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 2"];

        table [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        dictEntry0 [label = " <head> dictEntry | { k0 | v0 }"];
        dictEntry1 [label = " <head> dictEntry | { k1 | v1 }"];

        //

        node [shape = plaintext, label = "NULL"];

        null0;
        null1;
        null2;
        null3;

        //

        dictht:table -> table:head;

        table:0 -> null0;
        table:1 -> null1;
        table:2 -> dictEntry1;
        dictEntry1 -> dictEntry0 -> null2 [label = "next"];
        table:3 -> null3;

    }

----

普通状态下（未在进行 rehash）的字典。

.. graphviz::

    digraph {

        label = "\n 图 4-3    普通状态下的字典";

        rankdir = LR;

        //

        node [shape = record];

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n -1 "];

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 2"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 0 | <sizemask> sizemask \n 0 | <used> used \n 0"];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];
        table1 [label = "NULL", shape = plaintext];

        dictEntry0 [label = " <head> dictEntry | { k0 | v0 }"];
        dictEntry1 [label = " <head> dictEntry | { k1 | v1 }"];

        //

        node [shape = plaintext, label = "NULL"];

        null0;
        null1;
        null2;
        null3;

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1;

        table0:0 -> null0;
        table0:1 -> dictEntry0:head -> null1;
        table0:2 -> null2;
        table0:3 -> dictEntry1:head -> null3;
    }

----

包含了键值对的字典。

.. graphviz::

    digraph {

        label = "\n 图 4-5    添加键值对 k0 和 v0 之后的字典";

        rankdir = LR;

        //

        node [shape = record];

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n -1 "];

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 1"];

        dictht1 [label = "...", shape = plaintext];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];
        //table1 [label = "NULL", shape = plaintext];

        dictEntry [label = " <head> dictEntry | { k0 | v0 } "];

        //

        node [shape = plaintext, label = "NULL"];

        null0;
        null1;
        null2;
        null3;

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        //dictht1:table -> table1;

        table0:0 -> dictEntry:head -> null0;
        table0:1 -> null1;
        table0:2 -> null2;
        table0:3 -> null3;

    }

----

字典的 rehash 过程。

.. graphviz::

    digraph {

        label = "\n 图 4-8    执行 rehash 之前的字典";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n -1 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 4"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 0 | <sizemask> sizemask \n 0 | <used> used \n 0"];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        table1 [label = "NULL", shape = plaintext];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        null0;
        null1;
        null2;
        null3;

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1;

        table0:0 -> kv2:head -> null0;
        table0:1 -> kv0:head -> null1;
        table0:2 -> kv3:head -> null2;
        table0:3 -> kv1:head -> null3;

    }

.. graphviz::

    digraph {

        label = "\n 图 4-9    为字典的 ht[1] 哈希表分配空间";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n -1 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 4"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 8 | <sizemask> sizemask \n 7 | <used> used \n 0"];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        table1 [label = " <head> dictEntry*[8] | <0> 0 | <1> 1 | <2> 2 | ... | <7> 7 "];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1:head;

        table0:0 -> kv2:head -> null0;
        table0:1 -> kv0:head -> null1;
        table0:2 -> kv3:head -> null2;
        table0:3 -> kv1:head -> null3;

        table1:0 -> null10;
        table1:1 -> null11;
        table1:2 -> null12;
        table1:7 -> null17;

    }

.. graphviz::

    digraph {

        label = "\n 图 4-10    ht[0] 的所有键值对都已经被迁移到 ht[1]";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n -1 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 0"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 8 | <sizemask> sizemask \n 7 | <used> used \n 4"];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        table1 [label = " <head> dictEntry*[8] | ... | <1> 1 | ... | <4> 4 | <5> 5 | ... | <7> 7 "];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1:head;

        table0:0 -> null0;
        table0:1 -> null1;
        table0:2 -> null2;
        table0:3 -> null3;

        table1:1 -> kv3:head -> null11;
        table1:4 -> kv2:head -> null14;
        table1:5 -> kv0:head -> null15;
        table1:7 -> kv1:head -> null17;

    }


.. graphviz::

    digraph {

        label = "\n 图 4-11    完成 rehash 之后的字典";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n -1 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 8 | <sizemask> sizemask \n 7 | <used> used \n 4"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 0 | <sizemask> sizemask \n 0 | <used> used \n 0"];

        table0 [label = " <head> dictEntry*[8] | ... | <1> 1 | ... | <4> 4 | <5> 5 | ... | <7> 7 "];

        table1 [label = "NULL", shape = plaintext];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1;

        table0:1 -> kv3:head -> null11;
        table0:4 -> kv2:head -> null14;
        table0:5 -> kv0:head -> null15;
        table0:7 -> kv1:head -> null17;

    }

----

字典的渐进式 rehash 过程。

.. graphviz::

    digraph {

        label = "\n 图 4-12    准备开始 rehash";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n -1 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 4"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 8 | <sizemask> sizemask \n 7 | <used> used \n 0"];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        table1 [label = " <head> dictEntry*[8] | <0> 0 | <1> 1 | <2> 2 | ... | <7> 7 "];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1:head;

        table0:0 -> kv2:head -> null0;
        table0:1 -> kv0:head -> null1;
        table0:2 -> kv3:head -> null2;
        table0:3 -> kv1:head -> null3;

        table1:0 -> null10;
        table1:1 -> null11;
        table1:2 -> null12;
        table1:7 -> null17;

    }

.. graphviz::

    digraph {

        label = "\n 图 4-13    rehash 索引 0 上的键值对";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n 0 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 3"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 8 | <sizemask> sizemask \n 7 | <used> used \n 1"];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        table1 [label = " <head> dictEntry*[8] | ... | <4> 4 | ... "];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1:head;

        table0:0 -> null0;
        table0:1 -> kv0:head -> null1;
        table0:2 -> kv3:head -> null2;
        table0:3 -> kv1:head -> null3;

        table1:4 -> kv2:head -> null14

    }

.. graphviz::

    digraph {

        label = "\n 图 4-14    rehash 索引 1 上的键值对";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n 1 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 2"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 8 | <sizemask> sizemask \n 7 | <used> used \n 2"];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        table1 [label = " <head> dictEntry*[8] | ... | <4> 4 | <5> 5 | ... "];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1:head;

        table0:0 -> null0;
        table0:1 -> null1;
        table0:2 -> kv3:head -> null2;
        table0:3 -> kv1:head -> null3;

        table1:4 -> kv2:head -> null14
        table1:5 -> kv0:head -> null15;

    }

.. graphviz::

    digraph {

        label = "\n 图 4-15    rehash 索引 2 上的键值对";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n 2 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 1"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 8 | <sizemask> sizemask \n 7 | <used> used \n 3"];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        table1 [label = " <head> dictEntry*[8] | ... | <1> 1 | ... | <4> 4 | <5> 5 | ... "];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1:head;

        table0:0 -> null0;
        table0:1 -> null1;
        table0:2 -> null2;
        table0:3 -> kv1:head -> null3;

        table1:1 -> kv3:head -> null11;
        table1:4 -> kv2:head -> null14
        table1:5 -> kv0:head -> null15;

    }

.. graphviz::

    digraph {

        label = "\n 图 4-16    rehash 索引 3 上的键值对";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n 3 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 4 | <sizemask> sizemask \n 3 | <used> used \n 0"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 8 | <sizemask> sizemask \n 7 | <used> used \n 4"];

        table0 [label = " <head> dictEntry*[4] | <0> 0 | <1> 1 | <2> 2 | <3> 3 "];

        table1 [label = " <head> dictEntry*[8] | ... | <1> 1 | ... | <4> 4 | <5> 5 | ... | <7> 7 "];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1:head;

        table0:0 -> null0;
        table0:1 -> null1;
        table0:2 -> null2;
        table0:3 -> null3;

        table1:1 -> kv3:head -> null11;
        table1:4 -> kv2:head -> null14
        table1:5 -> kv0:head -> null15;
        table1:7 -> kv1:head -> null17;

    }

.. graphviz::

    digraph {

        label = "\n 图 4-17    rehash 执行完毕";

        rankdir = LR;

        node [shape = record];

        // 字典

        dict [label = " <head> dict | type | privdata | <ht> ht | rehashidx \n -1 "];

        // 哈希表

        dictht0 [label = " <head> dictht | <table> table | <size> size \n 8 | <sizemask> sizemask \n 7 | <used> used \n 4"];

        dictht1 [label = " <head> dictht | <table> table | <size> size \n 0 | <sizemask> sizemask \n 0 | <used> used \n 0"];

        table0 [label = " <head> dictEntry*[8] | ... | <1> 1 | ... | <4> 4 | <5> 5 | ... | <7> 7 "];

        table1 [label = "NULL", shape = plaintext];

        // 哈希表节点

        kv0 [label = " <head> dictEntry | { k0 | v0 } "];
        kv1 [label = " <head> dictEntry | { k1 | v1 } "];
        kv2 [label = " <head> dictEntry | { k2 | v2 } "];
        kv3 [label = " <head> dictEntry | { k3 | v3 } "];

        //

        node [shape = plaintext, label = "NULL"];

        //

        dict:ht -> dictht0:head [label = "ht[0]"];
        dict:ht -> dictht1:head [label = "ht[1]"];

        dictht0:table -> table0:head;
        dictht1:table -> table1;

        table0:1 -> kv3:head -> null11;
        table0:4 -> kv2:head -> null14;
        table0:5 -> kv0:head -> null15;
        table0:7 -> kv1:head -> null17;

    }




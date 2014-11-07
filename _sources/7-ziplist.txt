第 7 章：压缩列表
======================

压缩列表的各个部分

.. graphviz::

    digraph {

        label = "\n 图 7-1    压缩列表的各个组成部分";

        node [shape = record];

        ziplist [label = " zlbytes | zltail | zllen | entry1 | entry2 | ... | entryN | zlend "];

    }

----

一个包含三个节点的压缩列表示例。

.. graphviz::

    digraph {

        rankdir = BT;

        label = "\n 图 7-2    包含三个节点的压缩列表";

        node [shape = record];

        ziplist [label = " <zlbytes> zlbytes \n 0x50 | zltail \n 0x3c | zllen \n 0x3 | entry1 | entry2 | <entry3> entry3 | zlend \n 0xFF "];

        node [shape = plaintext];

        p [label = "p"];

        p -> ziplist:zlbytes;

        tail [label = "p + 60"];

        tail -> ziplist:entry3;

    }

----

一个包含五个节点的压缩列表示例。

.. graphviz::

    digraph {

        label = "\n 图 7-3    包含五个节点的压缩列表";

        rankdir = BT;

        node [shape = record];

        ziplist [label = " <zlbytes> zlbytes \n 0xd2 | zltail \n 0xb3 | zllen \n 0x5 | entry1 | entry2 | entry3 | entry4 | <entry5> entry5 | zlend \n 0xFF "];

        node [shape = plaintext];

        p [label = "p"];

        p -> ziplist:zlbytes;

        tail [label = "p + 179"];

        tail -> ziplist:entry5;

    }

----

压缩列表节点的各个组成部分。

.. graphviz::

    digraph {

        label = "\n 图 7-4    压缩列表节点的各个组成部分";

        node [shape = record];

        n [label = " previous_entry_length | encoding | content "];

    }

----

压缩列表节点的 ``previous_entry_length`` 属性示例。

.. graphviz::

    digraph {

        label = "\n 图 7-5    当前节点的前一节点的长度为 5 字节";

        node [shape = record];

        n [label = " previous_entry_length \n 0x05 | encoding \n ... | content \n ... "];

    }

----

使用压缩列表节点的 ``previous_entry_length`` 属性遍历整个压缩列表的过程。

.. graphviz::

    digraph {

        rankdir = BT;

        node [shape = record];

        entry1 [label = " zlbytes | zltail | zllen | <e1> entry1 | <e2> entry2 | <e3> entry3 | <e4> entry4 | zlend "];

        node [shape = plaintext];

        p1 -> entry1:e4;

    }

.. graphviz::

    digraph {

        rankdir = BT;

        node [shape = record];

        entry2 [label = " zlbytes | zltail | zllen | <e1> entry1 | <e2> entry2 | <e3> entry3 | <e4> entry4 | zlend "];

        node [shape = plaintext];

        p2 [label = "p2 = p1 - entry4.previous_entry_length"];
        p2 -> entry2:e3;

    }

.. graphviz::

    digraph {

        rankdir = BT;

        node [shape = record];

        entry3 [label = " zlbytes | zltail | zllen | <e1> entry1 | <e2> entry2 | <e3> entry3 | <e4> entry4 | zlend "];

        node [shape = plaintext];

        p3 [label = "p3 = p2 - entry3.previous_entry_length"];
        p3 -> entry3:e2;

    }

.. graphviz::

    digraph {

        label = "\n 图 7-8    一个从表尾向表头遍历的例子";

        rankdir = BT;

        node [shape = record];

        entry4 [label = " zlbytes | zltail | zllen | <e1> entry1 | <e2> entry2 | <e3> entry3 | <e4> entry4 | zlend "];

        node [shape = plaintext];

        p4 [label = "p4 = p3 - entry2.previous_entry_length"];
        p4 -> entry4:e1;

    }

----

一个保存着 ``"hello world"`` 字符串的压缩列表节点示例。

.. graphviz::

    digraph {

        label = "\n 图 7-9    保存着字节数组 \"hello world\" 的节点";

        node [shape = record];

        entry [label = " previous_entry_length \n ... | encoding \n 00001011 | content \n \"hello world\" "];

    }

----

一个保存着整数 ``10086`` 的压缩列表节点示例。

.. graphviz::

    digraph {

        label = "\n 图 7-10    保存着整数值 10086 的节点";

        node [shape = record];

        entry [label = " previous_entry_length \n ... | encoding \n 11000000 | content \n 10086 "];

    }

----

向压缩列表添加新节点并引发连锁更新的过程。

.. graphviz::

    digraph {

        label = "\n 图 7-11    包含节点 e1 至 eN 的压缩列表";

        node [shape = record];

        ziplist [label = " zlbytes| zltail | zllen | e1 | e2 | e3 | ... | eN | zlend "];

    }

.. graphviz::

    digraph {

        label = "\n 图 7-12    添加新节点到压缩列表";

        rankdir = BT;

        node [shape = record];

        ziplist [label = " zlbytes | zltail | zllen | <new> new | e1 | e2 | e3 | ... | eN | zlend "];

        p [label = "添加新节点", shape = plaintext];

        p -> ziplist:new;

    }

.. graphviz::

    digraph {

        rankdir = BT;

        node [shape = record];

        ziplist [label = " zlbytes | zltail | zllen | <new> new | <e1> e1 | <e2> e2 | <e3> e3 | ... | <en> eN | zlend "];

        p [label = "扩展 e1 \n并引发对 e2 的扩展", shape = plaintext];

        p -> ziplist:e1;

    }

.. graphviz::

    digraph {

        rankdir = BT;

        node [shape = record];

        ziplist [label = " zlbytes | zltail | zllen | <new> new | <e1> e1 | <e2> e2 | <e3> e3 | ... | <en> eN | zlend "];

        p [label = "扩展 e2 \n并引发对 e3 的扩展", shape = plaintext];

        p -> ziplist:e2;

    }

.. graphviz::

    digraph {

        rankdir = BT;

        node [shape = record];

        ziplist [label = " zlbytes | zltail | zllen | <new> new | <e1> e1 | <e2> e2 | <e3> e3 | ... | <en> eN | zlend "];

        p [label = "扩展 e3 \n并引发对 e4 的扩展", shape = plaintext];

        p -> ziplist:e3;

    }

.. graphviz::

    digraph {

        rankdir = BT;

        node [shape = record];

        ziplist [label = " zlbytes | zltail | zllen | <new> new | <e1> e1 | <e2> e2 | <e3> e3 | <more> ... | <en> eN | zlend "];

        p [label = "一直扩展下去……", shape = plaintext];

        p -> ziplist:more;

    }

.. graphviz::

    digraph {

        label = "\n 图 7-13    连锁更新过程";

        rankdir = BT;

        node [shape = record];

        ziplist [label = " zlbytes | zltail | zllen | <new> new | <e1> e1 | <e2> e2 | <e3> e3 | <e4> e4 | ... | <eN> eN | zlend "];

        p [label = "为 eN-1 扩展 eN 的 previous_entry_length 属性 \n 连锁更新到此结束", shape = plaintext];

        p -> ziplist:eN;

    }



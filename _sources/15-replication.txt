第 15 章： 复制
===================

``SYNC`` 命令执行期间，
主从服务器的通信过程。

.. graphviz::

    digraph {

        label = "\n 图 15-2    主从服务器在执行 SYNC 命令期间的通信过程"

        rankdir = LR

        splines = polyline

        node [shape = box, height = 2]

        master [label = "主\n服\n务\n器"]

        slave [label = "从\n服\n务\n器"]

        master -> slave [dir = back,  label = "发送 SYNC 命令"]

        master -> slave [label = "\n 发送 RDB 文件"]

        master -> slave [label = "\n 发送缓冲区保存的所有写命令"]

    }

----

Redis 的命令传播过程示例。

.. graphviz::

    digraph {

        label = "\n 图 15-3    处于一致状态的主从服务器"

        rankdir = LR

        node [shape = record, width = 2]

        subgraph cluster_master {

            label = "主服务器"

            master_db [label = " <head> 数据库 | <k1> k1 | <k2> k2 | <k3> k3 | <k4> k4 | <k5> k5 "];

        }

        
        subgraph cluster_slave {

            label = "从服务器"

            slave_db [label = " <head> 数据库 | <k1> k1 | <k2> k2 | <k3> k3 | <k4> k4 | <k5> k5 "];

        }

        master_db -> slave_db [style = invis]
    }

如果这时，
客户端向主服务器发送命令 ``DEL k3`` ，
那么主服务器在执行完这个 :ref:`DEL` 命令之后，
主从服务器的数据库将出现不一致：
主服务器的数据库已经不再包含键 ``k3`` ，
但这个键却仍然包含在从服务器的数据库里面，
如图 15-4 所示。

.. graphviz::

    digraph {

        label = "\n 图 15-4    处于不一致状态的主从服务器"

        rankdir = LR

        node [shape = circle]

        client [label = "客户端"]

        node [shape = record, width = 2]

        subgraph cluster_master {

            label = "主服务器"

            master_db [label = " <head> 数据库 | <k1> k1 | <k2> k2 | <k4> k4 | <k5> k5 "];

        }

        
        subgraph cluster_slave {

            label = "从服务器"

            slave_db [label = " <head> 数据库 | <k1> k1 | <k2> k2 | <k3> k3 | <k4> k4 | <k5> k5 "];

        }

        master_db -> slave_db [style = invis]

        client -> master_db [label = "发送命令 \n DEL k3"]
    }

在上面的例子中，
主服务器因为执行了命令 ``DEL k3`` 而导致主从服务器不一致，
所以主服务器将向从服务器发送相同的命令 ``DEL k3`` ：
当从服务器执行完这个命令之后，
主从服务器将再次回到一致状态 —— 
现在主从服务器两者的数据库都不再包含键 ``k3`` 了，
如图 15-5 所示。

.. graphviz::

    digraph {

        label = "\n 图 15-5    主服务器向从服务器发送命令"

        rankdir = LR

        node [shape = record, width = 2]

        subgraph cluster_master {

            label = "主服务器"

            master_db [label = " <head> 数据库 | <k1> k1 | <k2> k2 | <k4> k4 | <k5> k5 "];

        }
        
        subgraph cluster_slave {

            label = "从服务器"

            slave_db [label = " <head> 数据库 | <k1> k1 | <k2> k2 | <k4> k4 | <k5> k5 "];

        }

        master_db -> slave_db [label = "发送命令 \n DEL k3"]
    }

----

``PSYNC`` 命令执行部分重同步时的过程。

.. graphviz::

    digraph {
   
        rankdir = LR;

        node [shape = record, height = 2];

        splines = polyline;

        master [label = "主\n服\n务\n器"];

        slave [label = "从\n服\n务\n器"];

        master -> slave [dir = back, label = "PSYNC"];

        master -> slave [label = "\n+CONTINUE"];

        master -> slave [label = "\n发送主从服务器断线期间\n主服务器执行的写命令"];

        label = "\n图 15-6    主从服务器执行部分重同步的过程";

    }

----

Redis 的复制积压缓冲区的结构图。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_master {

            label = "主服务器";

            propagater [label = "命令传播程序", height = 3.0];

            backlog [label = "复制积压缓冲区"];
            propagater -> backlog [label = "将写命令放入队列"];


        }

        node [height = 1.0];

        subgraph cluster_slaves {


            slave1 [label = "从服务器 A"];

            slave2 [label = "从服务器 B"];

            slave3 [label = "从服务器 C"];

            style = invis;

        }

        edge [label = "发送写命令"]

        backlog -> slave1 [style = invis];
        propagater -> slave1;
        propagater -> slave2;
        propagater -> slave3;

        label = "\n图 15-10    主服务器向复制积压缓冲区和所有从服务器传播写命令数据";
    }

----

Redis 使用复制积压缓冲区来为断线的从服务器发送缺失数据的过程。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = box, height = 1.0];

        //

        master [label = "主服务器\n\noffset = 10086"];

        slave1 [label = "从服务器 A\n\noffset = 10086"];

        slave2 [label = "从服务器 B\n\noffset = 10086"];

        slave3 [label = "从服务器 C\n\noffset = 10086"];

        //

        edge [style = invis];

        master -> slave1;
        master -> slave2;
        master -> slave3;

        label = "\n图 15-7    拥有相同偏移量的主服务器和它的三个从服务器";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = box, height = 1.0];

        //

        master [label = "主服务器\n\noffset = 10119"];

        slave1 [label = "从服务器 A\n\noffset = 10119"];

        slave2 [label = "从服务器 B\n\noffset = 10119"];

        slave3 [label = "从服务器 C\n\noffset = 10119"];

        //

        edge [label = "传播 33 字节数据"];

        master -> slave1;
        master -> slave2;
        master -> slave3;

        label = "\n图 15-8    更新偏移量之后的主从服务器";
    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = box, height = 1.0];

        //

        master [label = "主服务器\n\noffset = 10119"];

        slave1 [label = "从服务器 A\n\noffset = 10086"];

        slave2 [label = "从服务器 B\n\noffset = 10119"];

        slave3 [label = "从服务器 C\n\noffset = 10119"];

        //

        edge [label = "传播 33 字节数据"];

        master -> slave1 [style = dotted, dir = none, label = "（断线）"];
        master -> slave2;
        master -> slave3;

        label = "\n图 15-9    因为断线而处于不一致状态的从服务器 A";
    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = box, height = 1.0];

        //

        master [label = "主服务器\n\noffset = 10119"];

        slave1 [label = "从服务器 A\n\noffset = 10119"];

        slave2 [label = "从服务器 B\n\noffset = 10119"];

        slave3 [label = "从服务器 C\n\noffset = 10119"];

        //

        master -> slave1 [label = "发送断线时缺失的\n 33 字节数据"];
        master -> slave2 [style = invis, dir = none];
        master -> slave3 [style = invis, dir = none];

        label = "\n图 15-11    主服务器向从服务器发送缺失的数据";

    }

----

``PSYNC`` 命令判断是执行部分重同步还是完整重同步的流程。

.. graphviz::

    digraph {
        
        node [shape = box];

        data_sync [label = "从服务器接到客户端发来的 SLAVEOF 命令"];

        have_cache_or_not [label = "这是从服务器第一次执行复制？", shape = diamond];

        data_sync -> have_cache_or_not;

        force_full_resync [label = "向主服务器发送\nPSYNC ? -1"];

        try_partial_resync [label = "向主服务器发送\nPSYNC <runid> <offset>"];

        have_cache_or_not -> force_full_resync [label = "是"];

        have_cache_or_not -> try_partial_resync [label = "否"];

        full_resync [label = "主服务器返回\n+FULLRESYNC <runid> <offset>\n执行完整重同步"];

        force_full_resync -> full_resync;

        master_return_continue_or_not [label = "主服务器返回 +CONTINUE ？", shape = diamond];

        try_partial_resync -> master_return_continue_or_not;

        master_return_continue_or_not -> full_resync [label = "否"];

        partial_resync [label = "执行部分重同步"];

        master_return_continue_or_not -> partial_resync [label = "是"];

        label = "\n图 15-12    PSYNC 执行完整重同步和部分重同步时可能遇上的情况";

    }

----

Redis 服务器监测命令缺失，并补发缺失命令的过程。

.. graphviz::

    digraph {

        label = "\n 图 15-23    主从服务器处于一致状态"

        rankdir = LR;

        node [shape = box, height = 2]

        master [label = "主服务器\n复制偏移量为 200"]

        slave [label = "从服务器\n复制偏移量为 200"]

        master -> slave [style = invis]

    }

如果这时主服务器执行了命令 ``SET key value`` （协议格式的长度为 ``33`` 字节），
将自己的复制偏移量更新到了 ``233`` ，
并尝试向从服务器传播命令 ``SET key value`` ，
但这条命令却因为网络故障而在传播的途中丢失，
那么主从服务器之间的复制偏移量就会出现不一致：
主服务器的复制偏移量会被更新为 ``233`` ，
而从服务器的复制偏移量仍然为 ``200`` ，
如图 15-24 所示。

.. graphviz::

    digraph {

        label = "\n 图 15-24    主从服务器处于不一致状态"

        rankdir = LR;

        node [shape = box]

        master [label = "主服务器\n复制偏移量为 233", height = 2]

        stop [shape = point]

        slave [label = "从服务器\n复制偏移量为 200", height = 2]

        master -> stop [label = "SET key value", style = dashed]

        stop -> slave [style = invis]

    }

在这之后，
当从服务器向主服务器发送 :ref:`REPLCONF ACK <ping>` 命令的时候，
主服务器会察觉从服务器的复制偏移量依然为 ``200`` ，
而自己的复制偏移量为 ``233`` ，
这说明复制积压缓冲区里面复制偏移量为 ``201`` 至 ``233`` 的数据（也即是命令 ``SET key value`` ）在传播过程中丢失了，
于是主服务器会再次向从服务器传播命令 ``SET key value`` ，
从服务器通过接收并执行这个命令可以将自己更新至主服务器当前所处的状态，
如图 15-25 所示。

.. graphviz::

    digraph {

        label = "\n 图 15-25    主服务器向从服务器补发缺失的数据"

        rankdir = LR

        splines = polyline

        node [shape = box, height = 2]

        master [label = "主服务器\n复制偏移量为 233"]

        slave [label = "从服务器\n复制偏移量为 233"]

        master -> slave [dir = back, label = "REPLCONF ACK 200"]

        master -> slave [label = "\nSET key value"]

    }

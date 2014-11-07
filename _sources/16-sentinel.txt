第 16 章： Sentinel
=========================

Sentinel 状态以及已连接的两个 Sentinel 实例结构。

.. graphviz::

    digraph {

        label = "\n 图 16-5    master1 的实例结构";

        rankdir = LR;

        node [shape = record];

        //

        master1 [label = " <head> sentinelRedisInstance | flags \n SRI_MASTER | name \n \"master1\" | runid \n \"ee07959afc9d061233191c0f5bfe29580dfad0f4\" | config_epoch \n 0 | <addr> addr | down_after_period \n 30000 | quorum \n 2 | parallel_syncs \n 1 | failover_timeout \n 900000 | ... "];

        addr [label = " <head> sentinelAddr | ip \n \"127.0.0.1\" | port \n 6379 "];

        //

        master1:addr -> addr:head;

    }

.. graphviz::

    digraph {

        label = "\n 图 16-6    master2 的实例结构";

        rankdir = LR;

        node [shape = record];

        //
       
        master2 [label = " <head> sentinelRedisInstance | flags \n SRI_MASTER | name \n \"master2\" | runid \n \"a68408b775438a5dee54a638b3a6f3461920158a\" | config_epoch \n 0 | <addr> addr | down_after_period \n 50000 | quorum \n 5 | parallel_syncs \n 5 | failover_timeout \n 450000 | ... "];

        addr [label = " <head> sentinelAddr | ip \n \"127.0.0.1\" | port \n 12345 "];

        //

        master2:addr -> addr:head;

    }

.. graphviz::

    digraph {

        label = "\n 图 16-7    Sentinel 状态以及 masters 字典";

        rankdir = LR;

        node [shape = record];

        //

        sentinelState [label = " sentinelState | <masters> masters | ... "];

        masters [label = " <head> dict | <master1> \"master1\" | <master2> \"master2\" "];

        master1 [label = " <head> sentinelRedisInstance | ... | name \n \"master1\" | ... "];
        
        master2 [label = " <head> sentinelRedisInstance | ... | name \n \"master2\" | ... "];

        //

        sentinelState:masters -> masters:head;

        masters:master1 -> master1:head;
        masters:master2 -> master2:head;

    }

----

Sentinel 通过向主服务器发送 ``INFO`` 命令来获得从服务器的信息。

.. graphviz::

    digraph {

        label = "\n 图 16-9    Sentinel 向带有三个从服务器的主服务器发送 INFO 命令";

        sentinel [label = "Sentinel", shape = box, width = 4.5, height = 1.0];

        master [shape = doublecircle];

        node [shape = circle];
        edge [dir = none];

        master -> slave0;
        master -> slave1;
        master -> slave2;

        edge [dir = forward];

        sentinel -> master [label = "INFO"];

    }

以及 Sentinel 为这个主服务器以及三个从服务器创建的信息结构。

.. graphviz::

    digraph {

        label = "\n 图 16-10    主服务器和它的三个从服务器";

        rankdir = LR;

        node [shape = record];

        master [label = " sentinelRedisInstance | flags \n SRI_MASTER | run_id \n \"7611c59dc3a29aa6fa0609f841bb6a1019008a9c\" | name \n \"master\" | ... | <slaves> slaves | ... "];

        slaves [label = " <head> dict | <slave0> \"127.0.0.1:11111\" | <slave1> \"127.0.0.1:22222\" | <slave2> \"127.0.0.1:33333\" "];

        slave0 [label = " <head> sentinelRedisInstance | flags \n SRI_SLAVE | name \n \"127.0.0.1:11111\" | ... "];
        slave1 [label = " <head> sentinelRedisInstance | flags \n SRI_SLAVE | name \n \"127.0.0.1:22222\" | ... "];
        slave2 [label = " <head> sentinelRedisInstance | flags \n SRI_SLAVE | name \n \"127.0.0.1:33333\" | ... "];

        master:slaves -> slaves:head;
        slaves:slave0 -> slave0:head;
        slaves:slave1 -> slave1:head;
        slaves:slave2 -> slave2:head;

    }

----

Sentinel 通过命令连接向频道发送信息，
并通过订阅连接从频道中获取信息。

.. graphviz::

    digraph {

        label = "\n 图 16-13    Sentinel 同时向服务器发送和接收信息";

        rankdir = LR;

        sentinel [label = "Sentinel", shape = box, height = 1.8];

        master [label = "服务器", shape = circle];

        sentinel -> master [label = "通过命令连接\n发送信息到频道"];

        sentinel -> master [dir = back, label = "通过订阅连接\n从频道中接收信息"];

    }

----

示例：一个 Sentinel 向包括它自己在内的三个 Sentinel 发送信息。

.. graphviz::

    digraph {

        label = "\n 图 16-14    向服务器发送信息";

        node [shape = box];

        sentinel1 [label = "sentinel1", width = 2.0];

        sentinel2 [label = "sentinel2", width = 2.0];

        sentinel3 [label = "sentinel3", width = 2.0];

        server [label = "服务器", shape = circle];


        edge [label = "发送信息\n（命令连接）"];
        sentinel1 -> server;

        edge [style = dashed];

        edge [dir = back, label = "接收信息\n（订阅连接）"];
        sentinel1 -> server;
        sentinel2 -> server;
        sentinel3 -> server;
    }

----

Redis Sentinel 执行故障转移的整个过程。

.. graphviz::

    digraph {

        label = "\n 图 16-22    将 server2 升级为主服务器";

        subgraph cluster_servers {

            style = invis;

            node [shape = circle, width = 1.2];
            edge [dir = none, style = dashed];

            server1 [label = "server1", shape = doublecircle, style = dashed];

            server2 [label = "server2"];
            server3 [label = "server3"];
            server4 [label = "server4"];

            server1 -> server2;
            server1 -> server3;
            server1 -> server4;

        }

        sentinel_system [label = "领头 Sentinel", shape = box, width = 5.0, height = 1.0];

        edge [label = "监视"];

        sentinel_system -> server1 [style = dashed, label = "已下线", dir = none];
        sentinel_system -> server2 [label = "发送命令\nSLAVEOF no one"];
        sentinel_system -> server3 [dir = none];
        sentinel_system -> server4 [dir = none];

    }

.. graphviz::

    digraph {

        label = "\n 图 16-23    server2 成功升级为主服务器";

        subgraph cluster_servers {

            style = invis;

            node [shape = circle, width = 1.2];
            edge [dir = none, style = dashed];

            server1 [label = "server1", shape = doublecircle, style = dashed];

            server2 [label = "server2", shape = doublecircle];
            server3 [label = "server3"];
            server4 [label = "server4"];

            server1 -> server3;
            server1 -> server4;

        }

        sentinel_system [label = "领头 Sentinel", shape = box, width = 5.0, height = 1.0];

        edge [label = "监视", dir = none];

        sentinel_system -> server1 [style = dashed, label = "已下线"];
        sentinel_system -> server2;
        sentinel_system -> server3;
        sentinel_system -> server4;

    }

.. graphviz::

    digraph {

        label = "\n 图 16-24    让从服务器复制新的主服务器";

        subgraph cluster_servers {

            style = invis;

            node [shape = circle, width = 1.2];
            edge [dir = none, style = dashed];

            server1 [label = "server1", shape = doublecircle, style = dashed];

            server2 [label = "server2", shape = doublecircle];
            server3 [label = "server3"];
            server4 [label = "server4"];

            server1 -> server3;
            server1 -> server4;

        }

        sentinel_system [label = "领头 Sentinel", shape = box, width = 5.0, height = 1.0];

        sentinel_system -> server1 [style = dashed, label = "已下线", dir = none];
        sentinel_system -> server2 [label = "监视", dir = none];

        edge  [label = "发送命令\nSLAVEOF\n<server2_ip>\n<server2_port>"];
        sentinel_system -> server3;
        sentinel_system -> server4;

    }

.. graphviz::

    digraph {

        label = "\n 图 16-25    server3 和 server4 成为 server2 的从服务器";

        subgraph cluster_servers {

            style = invis;

            node [shape = circle, width = 1.2];
            edge [dir = none];

            server1 [label = "server1", shape = doublecircle, style = dashed];

            server2 [label = "server2", shape = doublecircle];
            server3 [label = "server3"];
            server4 [label = "server4"];

            server2 -> server3;
            server2 -> server4;

        }

        sentinel_system [label = "领头 Sentinel", shape = box, width = 5.0, height = 1.0];

        sentinel_system -> server1 [style = dashed, label = "已下线", dir = none];

        edge [label = "监视", dir = none];

        sentinel_system -> server2;
        sentinel_system -> server3;
        sentinel_system -> server4;

    }

.. graphviz::

    digraph {

        label = "\n 图 16-26    server1 被设置为新主服务器的从服务器";

        subgraph cluster_servers {

            style = invis;

            node [shape = circle, width = 1.2];
            edge [dir = none];

            server1 [label = "server1", shape = circle, style = dashed];

            server2 [label = "server2", shape = doublecircle];
            server3 [label = "server3"];
            server4 [label = "server4"];

            server2 -> server1 [style = dashed];
            server2 -> server3;
            server2 -> server4;

        }

        sentinel_system [label = "Sentinel 系统", shape = box, width = 5.0, height = 1.0];

        edge [dir = none];

        sentinel_system -> server1 [style = dashed, label = "已下线"];

        edge [label = "监视"];

        sentinel_system -> server2;
        sentinel_system -> server3;
        sentinel_system -> server4;

    }

.. graphviz::

    digraph {

        label = "\n 图 16-27    server1 重新上线并成为 server2 的从服务器";

        subgraph cluster_servers {

            style = invis;

            node [shape = circle, width = 1.2];
            edge [dir = none];

            server1 [label = "server1"];

            server2 [label = "server2", shape = doublecircle];
            server3 [label = "server3"];
            server4 [label = "server4"];

            server2 -> server1;
            server2 -> server3;
            server2 -> server4;

        }

        sentinel_system [label = "Sentinel 系统", shape = box, width = 5.0, height = 1.0];


        edge [label = "监视", dir = none];

        sentinel_system -> server1;
        sentinel_system -> server2;
        sentinel_system -> server3;
        sentinel_system -> server4;

    }

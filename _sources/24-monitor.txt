第 24 章： 监视器
===================

.. graphviz::

    digraph {

        label = "\n 图 24-1    命令的接收和信息的发送";

        rankdir = LR;

        server [label = "服\n务\n器", shape = box, height = 3.0, width = 1.0];

        node [shape = circle, width = 1.3];

        client [label = "客户端"];

        m1 [label = "监视器 1"];
        m2 [label = "监视器 2"];
        m3 [label = "监视器 3"];

        client -> server [label = "发送命令请求"];

        edge [label = "发送命令信息"];

        server -> m1;
        server -> m2;
        server -> m3;

    }

----

.. graphviz::

    digraph {

        label = "\n 图 24-3    客户端 c10086 执行 MONITOR 命令之后的 monitors 链表";

        rankdir = LR;

        node [shape = record];

        server [label = " redisServer | ... | <monitors> monitors | ... "];

        c128 [label = "c128"];

        c256 [label = "c256"];

        c512 [label = "c512"];

        c10086 [label = "c10086"];

        server:monitors -> c128 -> c256 -> c512 -> c10086;

    }

----

.. graphviz::

    digraph {

        label = "\n 图 24-4    服务器将信息发送给各个监视器";

        rankdir = LR;

        server [label = "服\n务\n器", shape = box, height = 4.0, width = 1.0];

        node [shape = circle, width = 1.3];

        c128;
        c256;
        c512;
        c10086;

        edge [label = "1378822257.329412\n[0 127.0.0.1:56604]\n\"KEYS\" \"*\""];

        server -> c128;
        server -> c256;
        server -> c512;
        server -> c10086;

    }

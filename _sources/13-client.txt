第 13 章： 客户端
======================

三个连接到服务器的客户端。

.. graphviz::

    digraph {

        label = "\n 13-1    客户端与服务器";

        rankdir = LR;

        node [shape = circle, width = 1.2];

        c1 [label = "客户端 1"];
        c2 [label = "客户端 2"];
        c3 [label = "客户端 3"];

        node [shape = box, height = 4.0, width = 1.5];

        server [label = "服\n务\n器"];

        //

        edge [dir = none, minlen = 2];

        c1 -> server;
        c2 -> server;
        c3 -> server;
    }

这三个客户端在服务器状态 ``clients`` 属性中的样子。

.. graphviz::

    digraph {

        label = "\n 13-2    clients 链表";
        
        rankdir = LR;

        //

        node [shape = record];

        redisServer [label = " redisServer | ... | <clients> clients | ... ", width = 2];

        c1 [label = " redisClient\n（客户端 1） "];
        c2 [label = " redisClient\n（客户端 2） "];
        c3 [label = " redisClient\n（客户端 3） "];

        //
        
        redisServer:clients -> c1;
        c1 -> c2;
        c2 -> c3;
    
    }

----

Redis 客户端的输入缓冲区示例。

.. graphviz::

    digraph {

        label = "\n 图 13-4    querybuf 属性示例";

        rankdir = LR;

        //

        node [shape = record];

        client [label = " redisClient | ... | <querybuf> querybuf | ... ", width = 2];

        sdshdr [label = " <head> sdshdr | free \n 0 | len \n 33 | <buf> buf "];

        buf [label = " { '*' | '3' | '\\r' | '\\n' | ... | 'v' | 'a' | 'l' | 'u' | 'e' | '\\r' | '\\n' | '\\0' } "];

        //

        client:querybuf -> sdshdr:head;

        sdshdr:buf -> buf;
    
    }

----

Redis 客户端的 ``argv`` 属性和 ``argc`` 属性示例。

.. graphviz::

    digraph {

        label = "\n 图 13-5    argv 属性和 argc 属性示例";

        rankdir = LR;

        node [shape = record];

        redisClient [label = " redisClient | ... | <argv> argv | argc \n 3 | ... ", width = 2];

        argv [label = " { { <head> argv[0] | StringObject \n \"SET\" } | { argv[1] | StringObject \n \"key\" } | { argv[1] | StringObject \n \"value\" } } "];

        redisClient:argv -> argv:head;

    }

----

Redis 客户端的固定大小输出缓冲区示例。

.. graphviz::

    digraph {

        label = "\n 图 13-8    固定大小缓冲区示例";

        rankdir = LR;

        node [shape = record];

        redisClient [label = " redisClient | ... | <buf> buf | bufpos \n 5 | ... "];

        buf [label = " { '+' | 'O' | 'K' | '\\r' | '\\n' | '\\0' | ... } "];

        redisClient:buf -> buf;

    }

Redis 客户端的可变大小输出缓冲区示例。

.. graphviz::

    digraph {

        label = "\n 图 13-9    可变大小缓冲区示例";

        rankdir = LR;

        node [shape = record];

        redisClient [label = " redisClient | ... | <reply> reply | ... ", width = 2];

        node [label = " <head> StringObject \n ... "];

        redisClient:reply -> s1:head -> s2:head -> s3:head;

    }




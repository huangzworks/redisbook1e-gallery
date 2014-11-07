第 9 章：数据库
===================

数据库示例。

.. graphviz::

    digraph {

        label = "\n 图 9-1    服务器数据库示例";

        rankdir = LR;

        node [shape = record];

        //

        redisServer [label = "redisServer | ... | <db> db | ... | dbnum \n 16 | ..."];

        db [label = "{ <0> db[0] | <1> db[1] | <2> db[2] | ... | <15> db[15] }"];

        //

        redisServer:db -> db;

    }

----

数据库键空间示例。

.. graphviz::

    digraph {

        label = "\n图 9-4    数据库键空间例子";

        rankdir = LR;

        node [shape = record];

        //

        redisDb [label = "redisDb | ... | <dict> dict | ..."];

        dict [label = "<dict> dict | <alphabet> StringObject \n \"alphabet\" | <book> StringObject \n \"book\" | <message> StringObject \n \"message\""];

        subgraph cluster_alphabet {

            a [label = " StringObject \n \"a\" "];
            b [label = " StringObject \n \"b\" "];
            c [label = " StringObject \n \"c\" "];

            a -> b -> c;

            label = "ListObject";

        }

        //alphabet [label = "<head> ListObject | { StringObject \n \"a\" | \"b\" | \"c\" }"];

        book [label = "<head> HashObject | <name> StringObject \n \"name\" | <author> StringObject \n \"author\" | <publisher> StringObject \n \"publisher\""];

        //name [label = " StringObject \n \"Redis in Action\""];
        name [label = " StringObject \n \"Redis in Action\""];

        author [label = " StringObject \n \"Josiah L. Carlson\""];

        publisher [label = " StringObject \n \"Manning\""];

        message [label = " StringObject \n \"hello world\""];

        //

        redisDb:dict -> dict:dict;

        dict:alphabet -> a;
        dict:book -> book:head;
        dict:message -> message;

        book:name -> name;
        book:publisher -> publisher;
        book:author -> author;

    }

----

带有过期时间的字典示例。

.. graphviz::

    digraph {

        label = "\n图 9-12    带有过期字典的数据库例子";

        rankdir = LR;

        node [shape = record];

        //

        redisDb [label = "redisDb | ... | <dict> dict | <expires> expires | ..."];

        dict [label = "<dict> dict | <alphabet> StringObject \n \"alphabet\" | <book> StringObject \n \"book\" | <message> StringObject \n \"message\""];

        subgraph cluster_alphabet {

            a [label = " StringObject \n \"a\" "];
            b [label = " StringObject \n \"b\" "];
            c [label = " StringObject \n \"c\" "];

            a -> b -> c;

            label = "ListObject";

        }

        book [label = "<head> HashObject | <name> StringObject \n \"name\" | <author> StringObject \n \"author\" | <publisher> StringObject \n \"publisher\""];

        name [label = "<head> StringObject \n \"Redis in Action\""];

        author [label = "<head> StringObject \n \"Josiah L. Carlson\""];

        publisher [label = "<head> StringObject \n \"Manning\""];

        message [label = "<head> StringObject \n \"hello world\""];

        //

        expires [label = "<head> dict | <alphabet> StringObject \n \"alphabet\" | <book> StringObject \n \"book\""];

        redisDb:expires -> expires:head;

        alphabet_expire [label = "<head> long long | 1385877600000"];
        book_expire [label = "<head> long long | 1388556000000"];

        expires:alphabet -> alphabet_expire:head;
        expires:book -> book_expire:head;

        //

        redisDb:dict -> dict:dict;

        dict:alphabet -> a;
        dict:book -> book:head;
        dict:message -> message:head;

        book:name -> name:head;
        book:publisher -> publisher:head;
        book:author -> author:head;

    }

----

``GET`` 命令判断是否需要删除过期键的过程。

.. graphviz::

    digraph {

        label = "\n图 9-16    GET 命令的执行过程";

        node [shape = box];

        //

        get [label = "GET <key>"];

        key_exists_or_not [label = "键不存在？", shape = diamond];

        key_expired_or_not [label = "键已过期？", shape = diamond];

        return_nil [label = "返回空回复"];

        return_value [label = "返回键 key 的值"];

        //

        get -> key_exists_or_not;

        key_exists_or_not -> return_nil [label = "是"];

        key_exists_or_not -> key_expired_or_not [label = "否"];

        key_expired_or_not -> return_nil [label = "是\n删除过期键"];

        key_expired_or_not -> return_value [label = "否"];

    }

----

当 Redis 运行在复制模式时，
主从服务器处理过期键的流程。

.. graphviz::

    digraph {

        label = "\n图 9-17    主从服务器删除过期键 （1）";

        rankdir = LR;

        //

        node [shape = record];

        subgraph cluster_master {

            label = "主服务器";

            master_db [label = " 数据库 | message \n （已过期） | xxx | yyy "];

        }

        subgraph cluster_slave {

            label = "从服务器";

            slave_db [label = " 数据库 | message \n （已过期） | xxx | yyy "];

        }

        //

        master_db -> slave_db [style = invis, minlen = 2];

    }

.. graphviz::

    digraph {

        label = "\n图 9-18    主从服务器删除过期键 （2）";

        rankdir = LR;

        //

        node [shape = record];

        subgraph cluster_master {

            label = "主服务器";

            master_db [label = " 数据库 | message \n （已过期） | xxx | yyy "];

        }

        subgraph cluster_slave {

            label = "从服务器";

            slave_db [label = " 数据库 | <message> message \n （已过期） | xxx | yyy "];

        }

        client [label = "客户端", shape = circle];

        //
        splines = polyline;

        master_db -> slave_db [style = invis, minlen = 2];

        slave_db -> client [dir = back, label = "GET message"];

        slave_db -> client [label = "(nil)"];

    }

.. graphviz::

    digraph {

        label = "\n图 9-19    主从服务器删除过期键 （3）";

        rankdir = LR;

        //

        node [shape = record];

        subgraph cluster_master {

            label = "主服务器";

            master_db [label = " 数据库 | xxx | yyy ", width = 1.25, height = 1.75];

        }

        subgraph cluster_slave {

            label = "从服务器";

            slave_db [label = " 数据库 | <message> message \n （已过期） | xxx | yyy "];

        }

        client [label = "客户端", shape = circle];

        //

        splines = polyline;

        master_db -> slave_db [label = "DEL message"];

        client -> master_db [label = "GET message"];
        client -> master_db [dir = back, label = "(nil)"];

    }

.. graphviz::

    digraph {

        label = "\n图 9-20    主从服务器删除过期键 （4）";

        rankdir = LR;

        //

        node [shape = record, width = 1.25, height = 1.75];

        subgraph cluster_master {

            label = "主服务器";

            master_db [label = " 数据库 | xxx | yyy "];

        }

        subgraph cluster_slave {

            label = "从服务器";

            slave_db [label = " 数据库 | xxx | yyy "];

        }

        //

        master_db -> slave_db [style = invis, minlen = 2];

    }

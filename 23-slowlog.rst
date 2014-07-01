第 23 章： 慢查询日志
===========================

``slowlogEntry`` 结构。

.. graphviz::

    digraph {

        label = "\n 图 23-1    slowlogEntry 结构示例";

        rankdir = LR;

        node [shape = record];

        slowlogEntry [label = " slowlogEntry | id \n 3 | time \n 1378781439 | duration \n 10 | <argv> argv | argc \n 3 "];

        argv [label = " { { argv[0] | StringObject \n \"SET\" } | { argv[1] | StringObject \n \"number\" } | { argv[2] | StringObject \n \"10086\" } } "];

        slowlogEntry:argv -> argv;

   }

服务器状态的 ``slowlog`` 属性。

.. graphviz::

    digraph {

        label = "\n 图 23-2    redisServer 结构示例";

        rankdir = LR;

        node [shape = record];

        redisServer [label = " redisServer | ... | slowlog_entry_id \n 6 | <slowlog> slowlog | slowlog_log_slower_than \n 0 | slowlog_max_len \n 5 | ... "];

        slowlogEntry_5 [label = " slowlogEntry | id \n 5 | time \n 1378781521 | duration \n 61 | <argv> argv | argc \n 2 "];

        slowlogEntry_1 [label = " slowlogEntry | id \n 1 | time \n 1378781425 | duration \n 11 | <argv> argv | argc \n 4 "];

        more [label = "...", shape = plaintext]

        redisServer:slowlog -> slowlogEntry_5 -> more -> slowlogEntry_1;

    }

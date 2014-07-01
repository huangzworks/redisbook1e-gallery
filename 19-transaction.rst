第 19 章： 事务
=================

在一个事务开始之后，
服务器判断命令是该入队还是该执行的过程。

.. graphviz:: 

    digraph enque_or_execute {

        label = "\n图 19-1    服务器判断命令是该入队还是该执行的过程";

        node [shape = box];

        //

        command_in [label = "服务器接到来自客户端的命令"];

        in_transaction_or_not [label = "这个客户端正处于事务状态？", shape = diamond];

        not_exec_and_discard [label = "这个命令是否\nEXEC 、 DISCARD 、\nWATCH 或 MULTI ？", shape = diamond];

        enqueu_command [label = "将命令放入事务队列"];

        return_enqueued [label = "向客户端返回 QUEUED"];

        exec_command [label = "执行这个命令"];

        return_command_result [label = "向客户端返回命令的执行结果"];

        // 

        command_in -> in_transaction_or_not;

        in_transaction_or_not -> not_exec_and_discard [label = "是"];

        not_exec_and_discard -> enqueu_command [label = "否"];

        not_exec_and_discard -> exec_command [label = "是"];

        in_transaction_or_not -> exec_command [label = "否"];

        exec_command -> return_command_result;

        enqueu_command -> return_enqueued;
    }

----

事务状态。

.. graphviz::

    digraph {

        label = "\n 图 19-2    事务状态";

        rankdir = LR;

        node [shape = record];

        //redisClient [label = " <head> redisClient | ... | <mstate> mstate | ... "];

        multiState [label = " <head> multiState | <commands> commands | count \n 4 "];

        commands [label = " <head> multiCmd[4] | <0> [0] | <1> [1] | <2> [2] | <3> [3] "];

        multiCmd0 [label = " <head> multiCmd | <argv> argv | argc \n 3 | <cmd> cmd "];

        multiCmd1 [label = " <head> multiCmd | <argv> argv | argc \n 2 | <cmd> cmd "];

        multiCmd2 [label = " <head> multiCmd | <argv> argv | argc \n 3 | <cmd> cmd "];

        multiCmd3 [label = " <head> multiCmd | <argv> argv | argc \n 2 | <cmd> cmd "];

        //redisClient:mstate -> multiState:head;

        multiState:commands -> commands:head;

        commands:0 -> multiCmd0:head;
        commands:1 -> multiCmd1:head;
        commands:2 -> multiCmd2:head;
        commands:3 -> multiCmd3:head;

        argv0 [label = " robj*[3] | { StringObject \n \"SET\" | StringObject \n \"name\" | StringObject \n \"Practical Common Lisp\" } "];
        cmd0 [label = " setCommand ", shape = plaintext];

        multiCmd0:argv -> argv0;
        multiCmd0:cmd -> cmd0;

        argv1 [label = " robj*[2] | { StringObject \n \"GET\" | StringObject \n \"name\" } "];
        cmd1 [label = " getCommand ", shape = plaintext];

        multiCmd1:argv -> argv1;
        multiCmd1:cmd -> cmd1;

        argv2 [label = " robj*[3] | { StringObject \n \"SET\" | StringObject \n \"author\" | StringObject \n \"Peter Seibel\" } "];
        cmd2 [label = " setCommand ", shape = plaintext];

        multiCmd2:argv -> argv2;
        multiCmd2:cmd -> cmd2;

        argv3 [label = " robj*[2] | { StringObject \n \"GET\" | StringObject \n \"author\" } "];
        cmd3 [label = " getCommand ", shape = plaintext];

        multiCmd3:argv -> argv3;
        multiCmd3:cmd -> cmd3;

    }

----

``watched_keys`` 字典示例。

.. graphviz::

    digraph {

        label = "\n 图 19-5    watched_keys 字典";

        rankdir = LR;

        node [shape = record];

        //

        watched_keys [label = "watched_keys | <name> \"name\" | <age> \"age\" | <address> \"address\" ", width = 2.0, height = 3.0];

        //

        name_c2 [label = "c2"];
        address_c2 [label = "c2"];
        name_c10086 [label = "c10086"];
        age_c10086 [label = "c10086"];

        //

        watched_keys:name -> c1 -> name_c2 -> name_c10086;
        watched_keys:age -> c3 -> age_c10086;
        watched_keys:address -> address_c2 -> c4;

    }

----

服务器判断是否执行事务的过程。

.. graphviz::

    digraph {

        label = "\n 图 19-6    服务器判断是否执行事务的过程";

        node [shape = box];

        exec [label = "客户端向服务器发送 EXEC 命令"];

        dirty_cas_or_not [label = "客户端的 \n REDIS_DIRTY_CAS \n 标识是否已经打开？", shape = diamond];

        reject [label = "拒绝执行客户端提交的事务"];

        ok [label = "执行客户端提交的事务"];

        exec -> dirty_cas_or_not;

        dirty_cas_or_not -> reject [label = "是"];

        dirty_cas_or_not -> ok [label = "否"];

    }

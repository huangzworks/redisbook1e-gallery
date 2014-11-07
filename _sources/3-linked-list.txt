第 3 章：链表
=================

由多个链表节点 ``listNode`` 组成的链表。

.. graphviz::

    digraph {

        label = "\n 图 3-1    由多个 listNode 组成的双端链表"

        rankdir = LR;

        node [shape = record];

        //

        more_prev [label = "...", shape = plaintext];
        x [label = "<head> listNode | value \n ..."];
        y [label = "<head> listNode | value \n ..."];
        z [label = "<head> listNode | value \n ..."];
        more_next [label = "...", shape = plaintext];

        //

        more_prev -> x [label = "next"];
        x -> more_prev [label = "prev"];
       

        x -> y [label = "next"];
        y -> x [label = "prev"];

        y -> z [label = "next"];
        z -> y [label = "prev"];

        z -> more_next [label = "next"];
        more_next -> z [label = "prev"];
    }

----

由 ``list`` 结构和 ``listNode`` 节点组成的链表。

.. graphviz::

    digraph {

        label = "\n 图 3-2    由 list 结构和 listNode 结构组成的链表"

        rankdir = LR;

        node [shape = record];

        //

        list [label = "list | <head> head | <tail> tail | <len> len \n 3 | <dup> dup | <free> free | <match> match ", width = 2.0];

        more_prev [label = "NULL", shape = plaintext];
        x [label = "<head> listNode | value \n ..."];
        y [label = "<head> listNode | value \n ..."];
        z [label = "<head> listNode | value \n ..."];
        more_next [label = "NULL", shape = plaintext];

        dup [label = "...", shape = plaintext];
        free [label = "...", shape = plaintext];
        match [label = "...", shape = plaintext];

        //

        list:head -> x;
        list:tail -> z;

        list:dup -> dup;
        list:free -> free;
        list:match -> match;

        x -> y;
        y -> x;

        y -> z;
        z -> y;

        //

        more_prev -> x [dir = back];
        z -> more_next;

    }



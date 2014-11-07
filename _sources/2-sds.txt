第 2 章： 简单动态字符串
=============================

没有任何未使用空间的 SDS 示例。

.. graphviz::

    digraph {

        label = "\n 图 2-1    SDS 示例";

        rankdir = LR;

        node [shape = record];

        //

        sdshdr [label = "sdshdr | free \n 0 | len \n 5 | <buf> buf"];

        buf [label = "{ 'R' | 'e' | 'd' | 'i' | 's' | '\\0' }"];

        //

        sdshdr:buf -> buf;

    }

----

带有五字节使用空间的 SDS 示例。

.. graphviz::

    digraph {

        label = "\n 图 2-2    带有未使用空间的 SDS 示例";

        rankdir = LR;

        node [shape = record];

        //

        sdshdr [label = "sdshdr | free \n 5 | len \n 5 | <buf> buf"];

        buf [label = "{ 'R' | 'e' | 'd' | 'i' | 's' | '\\0' | | | | | }"];

        //

        sdshdr:buf -> buf;

    }

----

遍历 C 字符串并获取其长度的过程。

.. graphviz::

    digraph {

        rankdir = TB;

        node [shape = record];

        str [label = " <1> 'R' | <2> 'e' | <3> 'd' | <4> 'i' | <5> 's' | <6> '\\0' "];

        node [shape = plaintext];

        p1 [label = "len = 1"];

        p1 -> str:1;

    }

.. graphviz::

    digraph {

        rankdir = TB;

        node [shape = record];

        str [label = " <1> 'R' | <2> 'e' | <3> 'd' | <4> 'i' | <5> 's' | <6> '\\0' "];

        node [shape = plaintext];

        p2 [label = "len = 2"];

        p2 -> str:2;

    }

.. graphviz::

    digraph {

        rankdir = TB;

        node [shape = record];

        str [label = " <1> 'R' | <2> 'e' | <3> 'd' | <4> 'i' | <5> 's' | <6> '\\0' "];

        node [shape = plaintext];

        p3 [label = "len = 3"];

        p3 -> str:3;

    }

.. graphviz::

    digraph {

        rankdir = TB;

        node [shape = record];

        str [label = " <1> 'R' | <2> 'e' | <3> 'd' | <4> 'i' | <5> 's' | <6> '\\0' "];

        node [shape = plaintext];

        p4 [label = "len = 4"];

        p4 -> str:4;

    }

.. graphviz::

    digraph {

        rankdir = TB;

        node [shape = record];

        str [label = " <1> 'R' | <2> 'e' | <3> 'd' | <4> 'i' | <5> 's' | <6> '\\0' "];

        node [shape = plaintext];

        p5 [label = "len = 5"];

        p5 -> str:5;

    }

.. graphviz::

    digraph {

        label = "\n 图 2-4    计算 C 字符串长度的过程";

        rankdir = TB;

        node [shape = record];

        str [label = " <1> 'R' | <2> 'e' | <3> 'd' | <4> 'i' | <5> 's' | <6> '\\0' "];

        node [shape = plaintext];

        p6 [label = "发现空字符 \n 停止计数 \n 字符串的长度为 5 字节"];

        p6 -> str:6;

    }



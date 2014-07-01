第 5 章：跳跃表
======================

一个跳跃表示例。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record, width = "0.5"];

        //

        l [label = " <header> header | <tail> tail | level \n 5 | length \n 3 "];

        subgraph cluster_nodes {

            style = invisible;

            header [label = " <l32> L32 | ... | <l5> L5 | <l4> L4 | <l3> L3 | <l2> L2 | <l1> L1 "];

            bw_null [label = "NULL", shape = plaintext];

            level_null [label = "NULL", shape = plaintext];

            A [label = " <l4> L4 | <l3> L3 | <l2> L2 | <l1> L1 | <backward> BW | 1.0 | o1 "];

            B [label = " <l2> L2 | <l1> L1 | <backward> BW | 2.0 | o2 "];

            C [label = " <l5> L5 | <l4> L4 | <l3> L3 | <l2> L2 | <l1> L1 | <backward> BW | 3.0 | o3 "];

        }

        subgraph cluster_nulls {

            style = invisible;

            n1 [label = "NULL", shape = plaintext];
            n2 [label = "NULL", shape = plaintext];
            n3 [label = "NULL", shape = plaintext];
            n4 [label = "NULL", shape = plaintext];
            n5 [label = "NULL", shape = plaintext];

        }

        //

        l:header -> header;
        l:tail -> C;

        header:l32 -> level_null [label = "0"];
        header:l5 -> C:l5 [label = "3"];
        header:l4 -> A:l4 [label = "1"];
        header:l3 -> A:l3 [label = "1"];
        header:l2 -> A:l2 [label = "1"];
        header:l1 -> A:l1 [label = "1"];

        A:l4 -> C:l4 [label = "2"];
        A:l3 -> C:l3 [label = "2"];
        A:l2 -> B:l2 [label = "1"];
        A:l1 -> B:l1 [label = "1"];

        B:l2 -> C:l2 [label = "1"];
        B:l1 -> C:l1 [label = "1"];

        C:l5 -> n5 [label = "0"];
        C:l4 -> n4 [label = "0"];
        C:l3 -> n3 [label = "0"];
        C:l2 -> n2 [label = "0"];
        C:l1 -> n1 [label = "0"];

        bw_null -> A:backward -> B:backward -> C:backward [dir = back];

        label = "\n 图 5-1    一个跳跃表";
    }

----

从跳跃表的表头向表尾进行遍历。

.. graphviz::

    digraph {


        rankdir = LR;

        node [shape = record, width = "0.5"];

        //

        l [label = " <header> header | <tail> tail | level \n 5 | length \n 3 "];

        subgraph cluster_nodes {

            style = invisible;

            header [label = " <l32> L32 | ... | <l5> L5 | <l4> L4 | <l3> L3 | <l2> L2 | <l1> L1 "];

            bw_null [label = "NULL", shape = plaintext];

            level_null [label = "NULL", shape = plaintext];

            A [label = " <l4> L4 | <l3> L3 | <l2> L2 | <l1> L1 | <backward> BW | 1.0 | o1 "];

            B [label = " <l2> L2 | <l1> L1 | <backward> BW | 2.0 | o2 "];

            C [label = " <l5> L5 | <l4> L4 | <l3> L3 | <l2> L2 | <l1> L1 | <backward> BW | 3.0 | o3 "];

        }

        subgraph cluster_nulls {

            style = invisible;

            n1 [label = "NULL", shape = plaintext];
            n2 [label = "NULL", shape = plaintext];
            n3 [label = "NULL", shape = plaintext];
            n4 [label = "NULL", shape = plaintext];
            n5 [label = "NULL", shape = plaintext];

        }

        //

        l:header -> header [style = dashed];
        l:tail -> C;

        header:l32 -> level_null [label = "0"];
        header:l5 -> C:l5 [label = "3"];
        header:l4 -> A:l4 [label = "1", style = dashed];
        header:l3 -> A:l3 [label = "1"];
        header:l2 -> A:l2 [label = "1"];
        header:l1 -> A:l1 [label = "1"];

        A:l4 -> C:l4 [label = "2"];
        A:l3 -> C:l3 [label = "2"];
        A:l2 -> B:l2 [label = "1", style = dashed];
        A:l1 -> B:l1 [label = "1"];

        B:l2 -> C:l2 [label = "1", style = dashed];
        B:l1 -> C:l1 [label = "1"];

        C:l5 -> n5 [label = "0"];
        C:l4 -> n4 [label = "0"];
        C:l3 -> n3 [label = "0"];
        C:l2 -> n2 [label = "0", style = dashed];
        C:l1 -> n1 [label = "0"];

        bw_null -> A:backward -> B:backward -> C:backward [dir = back];


        label = "\n 图 5-3    遍历整个跳跃表";
    }

----

从跳跃表的表尾向表头进行遍历。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record, width = "0.5"];

        //

        l [label = " <header> header | <tail> tail | level \n 5 | length \n 3 "];

        subgraph cluster_nodes {

            style = invisible;

            header [label = " <l32> L32 | ... | <l5> L5 | <l4> L4 | <l3> L3 | <l2> L2 | <l1> L1 "];

            bw_null [label = "NULL", shape = plaintext];

            level_null [label = "NULL", shape = plaintext];

            A [label = " <l4> L4 | <l3> L3 | <l2> L2 | <l1> L1 | <backward> BW | 1.0 | o1 "];

            B [label = " <l2> L2 | <l1> L1 | <backward> BW | 2.0 | o2 "];

            C [label = " <l5> L5 | <l4> L4 | <l3> L3 | <l2> L2 | <l1> L1 | <backward> BW | 3.0 | o3 "];

        }

        subgraph cluster_nulls {

            style = invisible;

            n1 [label = "NULL", shape = plaintext];
            n2 [label = "NULL", shape = plaintext];
            n3 [label = "NULL", shape = plaintext];
            n4 [label = "NULL", shape = plaintext];
            n5 [label = "NULL", shape = plaintext];

        }

        //

        l:header -> header;
        l:tail -> C [style = dashed];

        header:l32 -> level_null [label = "0"];
        header:l5 -> C:l5 [label = "3"];
        header:l4 -> A:l4 [label = "1"];
        header:l3 -> A:l3 [label = "1"];
        header:l2 -> A:l2 [label = "1"];
        header:l1 -> A:l1 [label = "1"];

        A:l4 -> C:l4 [label = "2"];
        A:l3 -> C:l3 [label = "2"];
        A:l2 -> B:l2 [label = "1"];
        A:l1 -> B:l1 [label = "1"];

        B:l2 -> C:l2 [label = "1"];
        B:l1 -> C:l1 [label = "1"];

        C:l5 -> n5 [label = "0"];
        C:l4 -> n4 [label = "0"];
        C:l3 -> n3 [label = "0"];
        C:l2 -> n2 [label = "0"];
        C:l1 -> n1 [label = "0"];

        bw_null -> A:backward -> B:backward -> C:backward [dir = back, style = dashed];

        label = "\n 图 5-6    从表尾向表头方向遍历跳跃表";
    }

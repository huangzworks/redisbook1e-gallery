第 21 章： 排序
=====================


SORT <key> 命令的实现
---------------------------

Redis 服务器在执行 ``SORT number`` 时创建的数据结构。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u } } "];
        }

       label = "\n 图 21-1    命令为排序 numbers 列表而创建的数组";

    }


.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_numbers {

            label = "numbers 链表"

            style = dashed;

            one [label = "StringObject \n \"1\""];
            two [label = "StringObject \n \"2\""];
            three [label = "StringObject \n \"3\""];

            three -> one -> two;

        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u } } "];
        }
       array:obj0 -> three;
       array:obj1 -> one;
       array:obj2 -> two;

       label = "\n 图 21-2    将 obj 指针指向列表的各个项";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_numbers {

            label = "numbers 链表"

            style = dashed;

            one [label = "StringObject \n \"1\""];
            two [label = "StringObject \n \"2\""];
            three [label = "StringObject \n \"3\""];

            three -> one -> two;

        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u.score \n 3.0 } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u.score \n 1.0 } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u.score \n 2.0 } } "];
        }
       array:obj0 -> three;
       array:obj1 -> one;
       array:obj2 -> two;

       label = "\n 图 21-3    设置数组项的 u.score 属性";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_numbers {

            label = "numbers 链表"

            style = dashed;

            one [label = "StringObject \n \"1\""];
            two [label = "StringObject \n \"2\""];
            three [label = "StringObject \n \"3\""];

            three -> one -> two;

        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u.score \n 1.0 } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u.score \n 2.0 } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u.score \n 3.0 } } "];
        }
       array:obj0 -> one;
       array:obj1 -> two;
       array:obj2 -> three;

       label = "\n 图 21-4    排序后的数组";

    }


ALPHA 选项的实现
-------------------

服务器在执行 ``SORT fruits ALPHA`` 时创建的数据结构。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_fruits {

            label = "fruits 集合";

            style = dashed;

            apple [label = "StringObject \n \"apple\""];
            banana [label = "StringObject \n \"banana\""];
            cherry [label = "StringObject \n \"cherry\""];

            apple -> cherry -> banana [style = invis];
        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u } } "];
        }
       array:obj0 -> apple;
       array:obj1 -> cherry;
       array:obj2 -> banana;

       label = "\n 图 21-5    将 obj 指针指向集合的各个元素";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_fruits {

            label = "fruits 集合";

            style = dashed;

            apple [label = "StringObject \n \"apple\""];
            banana [label = "StringObject \n \"banana\""];
            cherry [label = "StringObject \n \"cherry\""];

            apple -> cherry -> banana [style = invis];
        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u } } "];
        }
       array:obj0 -> apple;
       array:obj1 -> banana;
       array:obj2 -> cherry;

       label = "\n 图 21-6    按集合元素进行排序后的数组";

    }


ASC 选项和 DESC 选项的实现
-------------------------------

图 21-7 展示了 :ref:`SORT` 命令在对 ``numbers`` 列表执行升序排序时所创建的数组。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_numbers {

            label = "numbers 链表"

            style = dashed

            one [label = "StringObject \n \"1\""];
            two [label = "StringObject \n \"2\""];
            three [label = "StringObject \n \"3\""];

            three -> one -> two;

        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u.score \n 1.0 } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u.score \n 2.0 } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u.score \n 3.0 } } "];
        }
       array:obj0 -> one;
       array:obj1 -> two;
       array:obj2 -> three;

       label = "\n 图 21-7    执行升序排序的数组";

    }

图 21-8 展示了 :ref:`SORT` 命令在对 ``numbers`` 列表执行降序排序时所创建的数组。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_numbers {

            label = "numbers 链表"

            style = dashed;

            one [label = "StringObject \n \"1\""];
            two [label = "StringObject \n \"2\""];
            three [label = "StringObject \n \"3\""];

            three -> one -> two;

        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u.score \n 3.0 } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u.score \n 2.0 } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u.score \n 1.0 } } "];
        }
       array:obj0 -> three;
       array:obj1 -> two;
       array:obj2 -> one;

       label = "\n 图 21-8    执行降序排序的数组";

    }


BY 选项的实现
-------------------

服务器在执行 ``SORT fruits BY *-price`` 时创建的数据结构。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_fruits {

            label = "fruits 集合";

            style = dashed;

            apple [label = "StringObject \n \"apple\""];
            banana [label = "StringObject \n \"banana\""];
            cherry [label = "StringObject \n \"cherry\""];

            apple -> cherry -> banana [style = invis];
        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u } } "];
        }
       array:obj0 -> apple;
       array:obj1 -> cherry;
       array:obj2 -> banana;

       label = "\n 图 21-9    将 obj 指针指向集合的各个元素";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_fruits {

            label = "fruits 集合";

            style = dashed;

            apple [label = "StringObject \n \"apple\""];
            banana [label = "StringObject \n \"banana\""];
            cherry [label = "StringObject \n \"cherry\""];

            apple -> cherry -> banana [style = invis];
        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u.score \n 8.0 } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u.score \n 7.0 } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u.score \n 5.5 } } "];
        }
       array:obj0 -> apple;
       array:obj1 -> cherry;
       array:obj2 -> banana;

       label = "\n 图 21-10    根据权重键的值设置数组项的 u.score 属性";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_fruits {

            label = "fruits 集合";

            style = dashed;

            apple [label = "StringObject \n \"apple\""];
            banana [label = "StringObject \n \"banana\""];
            cherry [label = "StringObject \n \"cherry\""];

            apple -> cherry -> banana [style = invis];
        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u.score \n 5.5 } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u.score \n 7.0 } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u.score \n 8.0 } } "];
        }
       array:obj0 -> banana;
       array:obj1 -> cherry;
       array:obj2 -> apple;

       label = "\n 图 21-11    根据 u.score 属性进行排序之后的数组";

    }


带有 ALPHA 选项的 BY 选项的实现
------------------------------------

服务器执行 ``SORT fruits BY *-id ALPHA`` 时创建的数据结构。

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_fruits {

            label = "fruits 集合";

            style = dashed;

            apple [label = "StringObject \n \"apple\""];
            banana [label = "StringObject \n \"banana\""];
            cherry [label = "StringObject \n \"cherry\""];

            apple -> cherry -> banana [style = invis];
        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | u } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | u } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | u } } "];
        }
       array:obj0 -> apple;
       array:obj1 -> cherry;
       array:obj2 -> banana;

       label = "\n 图 21-12    将 obj 指针指向集合的各个元素";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_fruits {

            label = "fruits 集合";

            style = dashed;

            apple [label = "StringObject \n \"apple\""];
            banana [label = "StringObject \n \"banana\""];
            cherry [label = "StringObject \n \"cherry\""];

            apple -> cherry -> banana [style = invis];
        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | <cmpobj0> u.cmpobj } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | <cmpobj1> u.cmpobj } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | <cmpobj2> u.cmpobj } } "];
        }
       array:obj0 -> apple;
       array:obj1 -> cherry;
       array:obj2 -> banana;

       apple_id [label = "StringObject \n \"FRUIT-25\""];
       banana_id [label = "StringObject \n \"FRUIT-79\""];
       cherry_id [label = "StringObject \n \"FRUIT-13\""];

       array:cmpobj0 -> apple_id;
       array:cmpobj1 -> cherry_id;
       array:cmpobj2 -> banana_id;


       label = "\n 图 21-13    将 u.cmpobj 指针指向权重键";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        node [shape = record];

        subgraph cluster_fruits {

            label = "fruits 集合";

            style = dashed;

            apple [label = "StringObject \n \"apple\""];
            banana [label = "StringObject \n \"banana\""];
            cherry [label = "StringObject \n \"cherry\""];

            apple -> cherry -> banana [style = invis];
        }

        subgraph cluster_array {

            style = invis;

            array [label = " array | { <array0> array[0] \n redisSortObject | { <obj0> obj | <cmpobj0> u.cmpobj } } | { <array1> array[1] \n redisSortObject | { <obj1> obj | <cmpobj1> u.cmpobj } } | { <array2> array[2] \n redisSortObject | { <obj2> obj | <cmpobj2> u.cmpobj } } "];
        }
       array:obj0 -> cherry;
       array:obj1 -> apple;
       array:obj2 -> banana;

       apple_id [label = "StringObject \n \"FRUIT-25\""];
       banana_id [label = "StringObject \n \"FRUIT-79\""];
       cherry_id [label = "StringObject \n \"FRUIT-13\""];

       array:cmpobj0 -> cherry_id;
       array:cmpobj1 -> apple_id;
       array:cmpobj2 -> banana_id;


       label = "\n 图 21-14    按 u.cmpobj 所指向的字符串对象进行排序之后的数组";

    }


LIMIT 选项的实现
--------------------

服务器在执行 ``SORT alphabet ALPHA LIMIT 0 4`` 时创建的数据结构。

.. graphviz::

    digraph {

        rankdir = LR;

        subgraph cluster_alphabet {

            label = "alphabet 集合\n";

            style = dashed;

            node [shape = box];

            a [label = "StringObject \n \"a\""];
            b [label = "StringObject \n \"b\""];
            c [label = "StringObject \n \"c\""];
            d [label = "StringObject \n \"d\""];
            e [label = "StringObject \n \"e\""];
            f [label = "StringObject \n \"f\""];

            edge [style = invis];

            d -> c -> a;
            b -> f -> e;
        }

        array [label = " array | { array[0] \n redisSortObject | { <obj0> obj | u } } | { array[1] \n redisSortObject | { <obj1> obj | u } } | { array[2] \n redisSortObject | { <obj2> obj | u } } | { array[3] \n redisSortObject | { <obj3> obj | u } } | { array[4] \n redisSortObject | { <obj4> obj | u } } | { array[5] \n redisSortObject | { <obj5> obj | u } } ", shape = record];

        edge [minlen = 2.0];

        array:obj0 -> d;
        array:obj1 -> c;
        array:obj2 -> a;
        array:obj3 -> b;
        array:obj4 -> f;
        array:obj5 -> e;

        label = "\n 图 21-15    将 obj 指针指向集合的各个元素";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        subgraph cluster_alphabet {

            label = "alphabet 集合\n\n";

            style = dashed;

            node [shape = box];

            a [label = "StringObject \n \"a\""];
            b [label = "StringObject \n \"b\""];
            c [label = "StringObject \n \"c\""];
            d [label = "StringObject \n \"d\""];
            e [label = "StringObject \n \"e\""];
            f [label = "StringObject \n \"f\""];

            edge [style = invis];

            d -> c -> a;
            b -> f -> e;
        }

        array [label = " array | { array[0] \n redisSortObject | { <obj0> obj | u } } | { array[1] \n redisSortObject | { <obj1> obj | u } } | { array[2] \n redisSortObject | { <obj2> obj | u } } | { array[3] \n redisSortObject | { <obj3> obj | u } } | { array[4] \n redisSortObject | { <obj4> obj | u } } | { array[5] \n redisSortObject | { <obj5> obj | u } } ", shape = record];

        edge [minlen = 2.0];

        array:obj0 -> a;
        array:obj1 -> b;
        array:obj2 -> c;
        array:obj3 -> d;
        array:obj4 -> e;
        array:obj5 -> f;

        label = "\n 图 21-16    排序后的数组";

    }


GET 选项的实现
-------------------

服务器在执行 ``SORT students ALPHA GET *-name`` 时创建的数据结构。

.. graphviz::

    digraph {

        rankdir = LR;

        subgraph cluster_students {

            label = "students 集合";

            style = dashed;

            node [shape = box];

            peter [label = "StringObject \n \"peter\""];

            jack [label = "StringObject \n \"jack\""];

            tom [label = "StringObject \n \"tom\""];

            peter -> jack -> tom [style = invis];

        }

        node [shape = record];

        array [label = " array | { array[0] \n redisSortObject | { <obj0> obj | u } } | { array[1] \n redisSortObject | { <obj1> obj | u } } | { array[2] \n redisSortObject | { <obj2> obj | u } } "];

        array:obj0 -> peter;
        array:obj1 -> jack;
        array:obj2 -> tom;

        label = "\n 图 21-17    排序之前的数组";

    }

.. graphviz::

    digraph {

        rankdir = LR;

        subgraph cluster_students {

            label = "students 集合";

            style = dashed;

            node [shape = box];

            peter [label = "StringObject \n \"peter\""];

            jack [label = "StringObject \n \"jack\""];

            tom [label = "StringObject \n \"tom\""];

            peter -> jack -> tom [style = invis];

        }

        node [shape = record];

        array [label = " array | { array[0] \n redisSortObject | { <obj0> obj | u } } | { array[1] \n redisSortObject | { <obj1> obj | u } } | { array[2] \n redisSortObject | { <obj2> obj | u } } "];

        array:obj0 -> jack;
        array:obj1 -> peter;
        array:obj2 -> tom;

        label = "\n 图 21-18    排序之后的数组";

    }



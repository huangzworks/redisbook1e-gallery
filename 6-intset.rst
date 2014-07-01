第 6 章：整数集合
====================

一个整数集合示例，包含五个 ``int16_t`` 类型的整数值。

.. graphviz::

    digraph {

        label = "\n 图 6-1    一个包含五个 int16_t 类型整数值的整数集合";

        rankdir = LR;

        node [shape = record];

        intset [label = " intset | encoding \n INTSET_ENC_INT16 | length \n 5 | <contents> contents "];

        //contents [label = " { { 0 位至 15 位 | <arrow> -6370 } | { 16 位至 31 位 | -5 } | { 32 位至 47 位 | 18 } | { 48 至 63 位 | 233 } | { 64 位至 79 位 | 14632 } } "];

        //intset:contents -> contents:arrow;

        contents [label = " { -6370 | -5 | 18 | 233 | 14632 } "];
        intset:contents -> contents;
    }

----

另一个整数集合示例，包含四个 ``int64_t`` 类型的整数值。

.. graphviz::

    digraph {

        label = "\n 图 6-2    一个包含四个 int64_t 类型整数值的整数集合";

        rankdir = LR;

        node [shape = record];

        intset [label = " intset | encoding \n INTSET_ENC_INT64 | length \n 4 | <contents> contents "];

        contents [label = " { -2675256175807981027 | 1 | 3 | 5 } "];

        intset:contents -> contents:arrow;

    }

----

当向一个包含三个 ``int16_t`` 类型的值的整数集合添加一个 ``int32_t`` 类型的值时，
整数集合的扩展和转换过程。

.. graphviz::
    
    digraph {

        label = "\n 图 6-4    contents 数组的各个元素，以及它们所在的位";

        node [shape = record];

        contents [label = " { 位 | 元素 } | { 0 至 15 位 | 1 } | { 16 至 31 位 | 2 } | { 32 至 47 位 | 3 } "];

    }

.. graphviz::
    
    digraph {

        label = "\n 图 6-5    进行空间重分配之后的数组";

        node [shape = record];

        contents [label = " { 位 | 元素 } | { 0 至 15 位 | 1 } | { 16 至 31 位 | 2 } | { 32 至 47 位 | 3 } | { 48 至 127 位 | （新分配空间）} "];

    }

.. graphviz::
    
    digraph {

        label = "\n 图 6-6    对元素 3 进行类型转换，并保存在适当的位上";

        node [shape = record];

        contents [label = " { 位 | 元素 } | { 0 至 15 位 | 1 } | { 16 至 31 位 | 2 } | { 32 至 47 位 | <old> 3 } | { 48 至 63 位 | （新分配空间） } | { 64 位至 95 位 | <new> 3 } | { 96 位至 127 位 | （新分配空间）} "];

        contents:old -> contents:new [label = "从 int16_t 类型转换为 int32_t 类型"];

    }


.. graphviz::
    
    digraph {

        label = "\n 图 6-7    对元素 2 进行类型转换，并保存在适当的位上";

        node [shape = record];

        contents [label = " { 位 | 元素 } | { 0 至 15 位 | 1 } | { 16 至 31 位 | <old> 2 } | { 32 至 63 位 | <new> 2 } | { 64 位至 95 位 | 3 } | { 96 位至 127 位 | （新分配空间）} "];

        contents:old -> contents:new [label = "从 int16_t 类型转换为 int32_t 类型"];

    }

.. graphviz::
    
    digraph {

        label = "\n 图 6-8    对元素 1 进行类型转换，并保存在适当的位上";

        node [shape = record];

        contents [label = " { 位 | 元素 } | { 0 至 31 位 | <old> 1 } | { 32 至 63 位 | 2 } | { 64 位至 95 位 | 3 } | { 96 位至 127 位 | （新分配空间）} "];

        //contents:old -> contents:new [label = "从 int16_t 类型转换为 int32_t 类型"];
        contents:old -> contents:old [label = "从 int16_t 类型转换为 int32_t 类型"];

    }

.. graphviz::
    
    digraph {

        label = "\n 图 6-9    添加 65535 到数组";

        rankdir = BT;

        node [shape = record];

        set [label = "添加新元素", shape = plaintext];

        contents [label = " { 位 | 元素 } | { 0 至 31 位 | 1 } | { 32 至 63 位 | 2 } | { 64 位至 95 位 | 3 } | { 96 位至 127 位 | <new> 65535 } "];


        set -> contents:new;

    }

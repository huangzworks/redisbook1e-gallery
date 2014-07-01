第 22 章： 二进制位数组
=============================


位数组的表示
-----------------

.. graphviz::

    digraph {

        label = "\n 图 22-1    SDS 表示的位数组";

        rankdir = LR;

        //

        node [shape = record];

        redisObject [label = " redisObject | ... | type \n REDIS_STRING | ... | <ptr> ptr "];

        sds [label = " <head> sdshdr | free \n 0 | len \n 1 | <buf> buf "];

        buf [label = " { buf[0] | buf[1] （空字符） } "];

        //

        redisObject:ptr -> sds:head;

        sds:buf -> buf;

    }

.. graphviz::

    digraph {

        label = "\n 图 22-2    一字节长的位数组的 SDS 表示";

        rankdir = LR;

        //

        node [shape = record];

        redisObject [label = " redisObject | ... | type \n REDIS_STRING | ... | <ptr> ptr "];

        sds [label = " <head> sdshdr | free \n 0 | len \n 1 | <buf> buf "];

        buf [label = " { buf[0] | 1 | 0 | 1 | 1 | 0 | 0 | 1 | 0 } | { buf[1] （空字符） } "];

        //

        redisObject:ptr -> sds:head;

        sds:buf -> buf;

    }

.. graphviz::

    digraph {

        label = "\n 图 22-3    三字节长的位数组的 SDS 表示";

        rankdir = LR;

        //

        node [shape = record];

        redisObject [label = " redisObject | ... | type \n REDIS_STRING | ... | <ptr> ptr "];

        sds [label = " <head> sdshdr | free \n 0 | len \n 3 | <buf> buf "];

        buf [label = " { buf[0] | 1 | 0 | 1 | 0 | 0 | 1 | 0 | 1 } | { buf[1] | 1 | 1 | 0 | 0 | 0 | 0 | 1 | 1 } | { buf[2] | 0 | 0 | 0 | 0 | 1 | 1 | 1 | 1 } | { buf[3] （空字符） } "];

        //

        redisObject:ptr -> sds:head;

        sds:buf -> buf;

    }


GETBIT 命令的实现
---------------------

.. graphviz::

    digraph {

        label = "\n 图 22-4    查找并返回 offset 为 3 的二进制位的过程";

        //

        rankdir = LR;

        point_to_buf0 [label = "1) 定位到 buf[0] 字节", shape = plaintext];

        point_to_idx3 [label = "2) 返回第 4 个二进制位的值", shape = plaintext];

        buf [label = " { <buf0> buf[0] | 1 | 0 | 1 | <idx3> 1 | 0 | 0 | 1 | 0 } | { buf[1] （空字符） } ", shape = record];

        //

        edge [style = dashed];
        
        point_to_buf0 -> buf:buf0;
        point_to_idx3 -> buf:idx3;

    }


.. graphviz::

    digraph {

        label = "\n 图 22-5    查找并返回 offset 为 10 的二进制位的过程";

        rankdir = LR;

        //

        node [shape = record];

        buf [label = " { buf[0] | 1 | 0 | 1 | 0 | 0 | 1 | 0 | 1 } | { <buf1> buf[1] | 1 | 1 | <bit> 0 | 0 | 0 | 0 | 1 | 1 } | { buf[2] | 0 | 0 | 0 | 0 | 1 | 1 | 1 | 1 } | { buf[3] | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 } "];

        node [shape = plaintext];

        point_to_buf [label = "1） 定位到 buf[1] 字节"];
        point_to_bit [label = "2） 返回第 3 个二进制位的值"];

        //

        edge [style = dashed];
        point_to_buf -> buf:buf1;
        point_to_bit -> buf:bit;

    }


SETBIT 命令的实现
----------------------

无须扩展的 ``SETBIT`` 命令。

.. graphviz::

    digraph {

        label = "\n 图 22-6    SETBIT 命令的执行过程";

        //

        node [shape = plaintext];

        point_to_byte [label = "1） 定位到 buf[0] 字节"];

        point_to_bit [label = "2）定位到 buf[0] 字节的第 2 个二进制位 \n 将位现在的值 0 保存到 oldvalue 变量 \n 然后将位的值设置为 1 "];

        buf [label = " { { <byte> buf[0] | 1 | <bit> 0 | 1 | 1 | 0 | 0 | 1 | 0 } | { buf[1] （空字符） } } ", shape = record];

        //

        edge [style = dashed];

        point_to_byte -> buf:byte;
        point_to_bit -> buf:bit;
    }

需要进行扩展的 ``SETBIT`` 命令。

.. graphviz::

    digraph {

        label = "\n 图 22-8    扩展空间之后的位数组";

        rankdir = LR;

        //

        node [shape = record];

        redisObject [label = " redisObject | ... | type \n REDIS_STRING | ... | <ptr> ptr "];

        sds [label = " <head> sdshdr | free \n 2 | len \n 2 | <buf> buf "];

        buf [label = " { buf[0] | 1 | 0 | 1 | 1 | 0 | 0 | 1 | 0 } | { buf[1] | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 } | { buf[2] （空字符） } | { buf[3] （未使用） } | { buf[4] （未使用） } "];

        //

        redisObject:ptr -> sds:head;

        sds:buf -> buf;

    }

.. graphviz::

    digraph {

        label = "\n 图 22-9    SETBIT 命令的执行过程";

        //

        node [shape = plaintext];

        point_to_byte [label = "1）定位到 buf[1] 字节"];
        point_yo_bit [label = "2）定位到 buf[1] 字节的第 5 个二进制位 \n 首先将位现在的值 0 保存到 oldvalue 变量 \n 然后将位的值设置为 1 "];

        node [shape = record];

        buf [label = " { { buf[0] | 1 | 0 | 1 | 1 | 0 | 0 | 1 | 0 } | { <byte> buf[1] | 0 | 0 | 0 | 0 | <bit> 0 | 0 | 0 | 0 } | { buf[2] （空字符） } | { buf[3] （未使用） } | { buf[4] （未使用） } } "];

        //

        edge [style = dashed];
        
        point_to_byte -> buf:byte;
        point_yo_bit -> buf:bit;
    }

.. graphviz::

    digraph {

        label = "\n 图 22-10    执行 SETBIT 命令之后的位数组";

        //

        node [shape = record];

        buf [label = " { { buf[0] | 1 | 0 | 1 | 1 | 0 | 0 | 1 | 0 } | { <byte> buf[1] | 0 | 0 | 0 | 0 | <bit> 1 | 0 | 0 | 0 } | { buf[2] （空字符） } | { buf[3] （未使用） } | { buf[4] （未使用） } } "];

    }



---
title: hamming 码校验 汉明距离
layout: post
category: cryptology
author: 夏泽民
---
<!-- more -->
 海明码（Hamming Code）是一个可以有多个校验位，具有检测并纠正一位错误代码的纠错码，所以它也仅用于信道特性比较好的环境中，如以太局域网中，因为如果信道特性不好的情况下，出现的错误通常不是一位。
    海明码的检错、纠错基本思想是将有效信息按某种规律分成若干组，每组安排一个校验位进行奇偶性测试，然后产生多位检测信息，并从中得出具体的出错位置，最后通过对错误位取反（也是原来是1就变成0，原来是0就变成1）来将其纠正。
    要采用海明码纠错，需要按以下步骤来进行：计算校验位数→确定校验码位置→确定校验码→实现校验和纠错。下面来具体介绍这几个步骤。本文先介绍除最后一个步骤的其它几个步骤。
 1.    计算校验位数
     要使用海明码纠错，首先就要确定发送的数据所需要要的校验码（也就是“海明码”）位数（也称“校验码长度”）。它是这样的规定的：假设用N表示添加了校验码位后整个信息的二进制位数，用K代表其中有效信息位数，r表示添加的校验码位，它们之间的关系应满足：N=K＋r≤2r－1。
      如K=5，则要求2r-r≥5+1=6，根据计算可以得知r的最小值为4，也就是要校验5位信息码，则要插入4位校验码。如果信息码是8位，则要求2r-r≥8+1=9，根据计算可以得知r的最小值也为4。根据经验总结，得出信息码和校验码位数之间的关系如表5-1所示。
表5-1   信息码位数与校验码位数之间的关系
信息码位数  校验码位数

1           2
2~4         3
5~11        4
12~26       5
27~57		  6
58~120		  7
121~247     8
2．确定校验码位置
    上一步我们确定了对应信息中要插入的校验码位数，但这还不够，因为这些校验码不是直接附加在信息码的前面、后面或中间的，而是分开插入到不同的位置。但不用担心，校验码的位置很容易确定的，那就是校验码必须是在2n次方位置，如第1、2、4、8、16、32，……位（对应20、21、22、23、24、25，……，是从最左边的位数起的），这样一来就知道了信息码的分布位置，也就是非2n次方位置，如第3、5、6、7、9、10、11、12、13，……位（是从最左边的位数起的）。

    举一个例子，假设现有一个8位信息码，即b1、b2、b3、b4、b5、b6、b7、b8，由表5-1得知，它需要插入4位校验码，即p1、p2、p3、p4，也就是整个经过编码后的数据码（称之为“码字”）共有12位。根据以上介绍的校验码位置分布规则可以得出，这12位编码后的数据就是p1、p2、b1、p3、b2、b3、b4、p4、b5、b6、b7、b8。

    现假设原来的8位信息码为10011101，因现在还没有求出各位校验码值，现在这些校验码位都用“？”表示，最终的码字为：？？1？001？1101。

3.    确定校验码
    经过前面的两步，我们已经确定了所需的校验码位数和这些校验码的插入位置，但这还不够，还得确定各个校验码值。这些校验码的值不是随意的，每个校验位的值代表了代码字中部分数据位的奇偶性（最终要根据是采用奇校验，还是偶校验来确定），其所在位置决定了要校验的比特位序列。总的原则是：第i位校验码从当前位开始，每次连续校验i（这里是数值i，不是第i位，下同）位后再跳过i位，然后再连续校验i位，再跳过i位，以此类推。最后根据所采用的是奇校验，还是偶校验即可得出第i位校验码的值。
    1）计算方法    校验码的具体计算方法如下
    p1（第1个校验位，也是整个码字的第1位）的校验规则是：从当前位数起，校验1位，然后跳过1位，再校验1位，再跳过1位，……。这样就可得出p1校验码位可以校验的码字位包括：第1位（也就是p1本身）、第3位、第5位、第7位、第9位、第11位、第13位、第15位，……。然后根据所采用的是奇校验，还是偶校验，最终可以确定该校验位的值。

    p2（第2个校验位，也是整个码字的第2位）的校验规则是：从当前位数起，连续校验2位，然后跳过2位，再连续校验2位，再跳过2位，……。这样就可得出p2校验码位可以校验的码字位包括：第2位（也就是p2本身）、第3位，第6位、第7位，第10位、第11位，第14位、第15位，……。同样根据所采用的是奇校验，还是偶校验，最终可以确定该校验位的值。

    p3（第3个校验位，也是整个码字的第4位）的校验规则是：从当前位数起，连续校验4位，然后跳过4位，再连续校验4位，再跳过4位，……。这样就可得出p4校验码位可以校验的码字位包括：第4位（也就是p4本身）、第5位、第6位、第7位，第12位、第13位、第14位、第15位，第20位、第21位、第22位、第23位，……。同样根据所采用的是奇校验，还是偶校验，最终可以确定该校验位的值。

    p4（第4个校验位，也是整个码字的第8位）的校验规则是：从当前位数起，连续校验8位，然后跳过8位，再连续校验8位，再跳过8位，……。这样就可得出p4校验码位可以校验的码字位包括：第8位（也就是p4本身）、第9位、第10位、第11位、第12位、第13位、第14位、第15位，第24位、第25位、第26位、第27位、第28位、第29位、第30位、第31位，……。同样根据所采用的是奇校验，还是偶校验，最终可以确定该校验位的值。
……
   我们把以上这些校验码所校验的位分成对应的组，它们在接收端的校验结果（通过对各校验位进行逻辑“异或运算”得出）对应表示为G1、G2、G3、G4，……，正常情况下均为0。
    2）校验码计算示例
    同样举上面的例子来说明，码字为？？1？001？1101。
    先求第1个“？”（也就是p1，第1位）的值，因为整个码字长度为12（包括信息码长和校验码长），所以可以得出本示例中p1校验码校验的位数是1、3、5、7、9、11共6位。这6位中除了第1位（也就是p1位）不能确定外，其余5位的值都是已知的，分别为：1、0、1、1、0。现假设采用的是偶校验（也就是要求整个被校验的位中的“1”的个数为偶数），从已知的5位码值可知，已有3个“1”，所以此时p1位校验码的值必须为“1”，得出p1=1。
    再求第2个“？”（也就是p2，第2位）的值，根据以上规则可以很快得出本示例中p2校验码校验的位数是2、3、6、7、10、11，也是一共6位。这6位中除了第2位（也就是p2位）不能确定外，其余5位的值都是已知的，分别为：1、0、1、1、0。现假设采用的是偶校验，从已知的5位码值可知，也已有3个“1”，所以此时p2位校验码的值必须为“1”，得出p2=1。
   再求第3个“？”（也就是p3，第4位）的值，根据以上规则可以很快得出本示例中p3校验码校验的位数是4、5、6、7、12，一共5位。这5位中除了第4位（也就是p3位）不能确定外，其余4位的值都是已知的，分别为：0、0、1、1。现假设采用的是偶校验，从已知的4位码值可知，也已有2个“1”，所以此时p2位校验码的值必须为“0”，得出p3=0。

   最后求第4个“？”（也就是p4，第8位）的值，根据以上规则可以很快得出本示例中p4校验码校验的位数是8、9、10、11、12（本来是可以连续校验8位的，但本示例的码字后面的长度没有这么多位，所以只校验到第12位止），也是一共5位。这5位中除了第8位（也就是p4位）不能确定外，其余4位的值都是已知的，分别为：1、1、0、1。现假设采用的是偶校验，从已知的4位码值可知，已有3个“1”，所以此时p2位校验码的值必须为“1”，得出p4=1。
   最后就可以得出整个码字的各个二进制值码字为：111000111101（带阴影的4位就是校验码）。
 

汉明距离是使用在数据传输差错控制编码里面的，汉明距离是一个概念，它表示两个（相同长度）字对应位不同的数量，我们以d（x,y）表示两个字x,y之间的汉明距离。对两个字符串进行异或运算，并统计结果为1的个数，那么这个数就是汉明距离。

在一个码组集合中，任意两个编码之间汉明距离的最小值称为这个码组的最小汉明距离。最小汉明距离越大，码组越具有抗干扰能力。
用d表示码组的最小汉明距离。
1． 当码组用于检测错误时，设可检测e个位的错误，则
d>=e+1
　　设有两个距离为d的码字A和B，如果A出现了e个错误，则A变成了以A为圆心，e位半径的球体表面的码字。为了能够准确地分辨出这些码字既不是A也不是B，那么A误码后变成的球面上的点与B至少应该有一位距离（如果B在球面上或在球面内部则无法分辨出到底B是不是A的错误码），即A与B之间的最小距离d>=e+1。
2． 若码组用于纠错，设可纠错t个位的错误，则
d>=2t+1
设有码字A和B，如果A出现了t个错误，B也出现了t各错误，则A码变成以A为圆心，t为半径的球面上的码字；B码变成以B为圆心，t为半径的球面上的码字。为了在出现t个错之后仍能分辨一个码字到底是属于A的错码还是属于B的错码，A,B为球心的两个球面应该不相交，即球心A，B之间距离应该大于2t，所以d>=2t+1。
3.如果码组用于纠正t个错，检测e个错，则
d>=e+t+1
这里e>t，这种检错纠错方式结合的情况同上述两个情况类似。当码字出现t个或者小于t个错时，系统按照纠错方式工作。当码字出现超过t个错而小于等于e个错时，系统按照检错方式工作；当A出现e个错，B出现t个错时，既要纠正B的错，又要发现A的错，则以A为球心，e为半径的球和以B为球心，t为半径的球应该不相交，所以A,B之间的距离应该大于等于e+t+1，即d>=e+t+1。
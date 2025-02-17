---
title: 为什么计算机采用补码而不是原码或反码？
tag:
- computer_compositon
- complement
category:
- computer_compositon
date: 2022-04-21 18:42:45
---
简单的回答，采用[补码](https://www.zhihu.com/search?q=%E8%A1%A5%E7%A0%81&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1878359705%7D)的根本原因在于简化电路设计的复杂度。
<!--more-->
接下来我会从**根源上**详细讲解下[原码](https://www.zhihu.com/search?q=%E5%8E%9F%E7%A0%81&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1878359705%7D)、反码以及补码产生的来龙去脉。
前方高能，坐稳了！
先来看一个简单的问题：小孩子都知道数数：1,2,3,4,5,6,7,8,9,10，可为什么要这样数呢？为什么不是1,2,3,4,5,6,10呢？
一种比较流行的解释是因为人类有10个手指：1

{%asset_img Pasted_image_20220421162841.png 200 150 %}
所以人类的数字系统就是10进制的，如果这个解释成立，那么[变色龙](https://www.zhihu.com/search?q=%E5%8F%98%E8%89%B2%E9%BE%99&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1878359705%7D)的数字系统应该是4进制
{%asset_img Pasted_image_20220421162856.png 200 150%}
而计算机的手是单指，所以是2进制：
{%asset_img Pasted_image_20220421162944.png 200 150 %}
哈哈，开个玩笑，其实计算机在最最最最底层就是一个个开关，因此计算机系统是2进制
## **数字 0 与正整数**
0这个数字其实有非常重要的意义，可能大家都没想过这个问题，没关系，我们来看两个不同的数字系统：阿拉伯数字和罗马数字：
{%asset_img Pasted_image_20220421163007.png 600 60 %}
注意，罗马数字中没有 “0” 这个概念，你可能会想，这有什么大不了的吗？让我们来看一个例子，数字205，分别在两种系统的表示。
**罗马**：CCV
**阿拉伯**：205
0的出现可以让阿拉伯数字系统中205这样写：
{%asset_img Pasted_image_20220421163019.png 600 60 %}
可以看到，在阿拉伯系统中**数值和数字所在的位置有直接关系，这就是[进位制](https://www.zhihu.com/search?q=%E8%BF%9B%E4%BD%8D%E5%88%B6&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1878359705%7D)**，而在罗马数字系统则没有进位制，这使得罗马数字在表示大数值时非常困难。
计算机系统中的二进制同样是进位制，数字5用二进制表示就是101：
{%asset_img Pasted_image_20220421163041.png 600 80 %}
使用 k 个bit，那么可以表示 2^k 个整数，范围从 0 到 2^k - 1，假设 k 有 8 位，那么表示范围就是 0 到 255，当然这里说的是无符号正整数。
现在我们可以表示正整数了，但真正有用的计算不可避免会涉及到**负数**，也就是**带符号整数**，而这也是真正有趣的地方。
## **有符号整数**
正整数的表示非常简单，给定 k 个bit，那么我们可以表示 2^k 个数，假设k为4，那么我们可以表示16个数字。
如果要考虑有符号整数呢？
你可能会想这还不简单，一半一半嘛！其中一半用来表示正数，另一半用来表示负数！
假设有4个比特位，如果用来表示无符号正数，就是0 ~ 15，而如果要表示有符号整数，那么其中一半给到+1 ~ +7，另一半给到-1 ~ -7。
一切看最左边的 bit 是 0，如果最左边的 bit 位是 0 则表示正数，否则表示负数。
{%asset_img Pasted_image_20220421163054.png 600 60 %}
现在我们已经知道正负数是通过什么来决定的了，接下来的问题就是负数该怎么表示呢？也就是说对于比如-2，现在我们只知道其最左边的 bit 位是1，那么右边的这些 bit 到底该是多少呢？
关于这一问题就三种设计方法。
## **方法1：正数加上负号即对应负数**
第一种设计方法很简单，既然**0**010表示+2，那么如果最左边的bit位替换成1就表示对应的负数，即**1**010表示-2，这种设计方法简单直接，这是**最符合人类的思维的设计(不一定最好)**。
如果这样设计的话，4个 bit 位能表示的所有数字就是：
{%asset_img Pasted_image_20220421163104.png 200 300 %}
你给这种非常符合人类思维的数字表示方法起了个名字，**原码**。
这种设计下会有一个奇怪的表示：0000表示0这没什么问题，1000会表示 -0，这会导致出现-0这样的数字，其实 0 和 -0 不应该有什么区别。
身为设计天才的你显然不满足于此，是不是还有其它设计方法？
## **方法2：翻转**
你觉得第一种表示方法也就是原码太原始，可以说基本上没什么设计，你突发奇想，既然**0010**表示+2，那么将其全部翻转，即**1101**来表示-2好了，即：
{%asset_img Pasted_image_20220421163125.png 200 300 %}
你给这种表示方法也起了名字，**反码**。
在反码表示法下，也存在-0，0000表示0，全部翻转也就是1111来表示-0，可以看到这和原码表示方法差别没那么大。
## **设计计算机很容易吗？**
到这里有的同学可能会想，计算机设计者其实怎么来表示有符号数其实都是可以的，原码可以，反码也可以，都能表示出来，设计计算机太容易了吧！
你猜的没错，就是这么容易！**如果你是计算机的创造者，怎么设计都可以**！最初的计算机设计者真的可以有很多表示数字的方法，**采用反码表示数字的计算机系统在历史上真的出现过！！！**但这些表示方法不约而同都有一个问题，那就是**两数相加**。
## **不简单的两数相加**
我们以2 + ( -2 )为例。在原码表示法下，2为0010，-2为1010，那么计算机该怎么做2 + ( -2 )加法呢？
{%asset_img Pasted_image_20220421163135.png 150 150 %}
可是1100在原码表示法下是 -4，这与原码表示法本身是矛盾的。
再来看看反码，2为0010，-2为1101，两数相加：
{%asset_img Pasted_image_20220421163144.png 150 150 %}
1111在反码表示下为-0，**虽然-0不够优雅**，但好歹和反码表示法本身没有矛盾，这大概就是为什么早期会有计算机采用反码表示数字的原因。
计算机加法是通过加法器组合电路实现的，而这里的不管是原码还是反码，要想计算加法都不可避免的要在前面提到的加法器之上**额外添加组合电路来确保有符号数相加的正确性**，这无疑会增加电路设计的复杂度。
顺便说一句，提到**复杂度**这个词博主脑海里第一个跳出来的就是算法，哈哈，想到了面试时被手写算法支配的恐惧，正好在这里也顺便给大家推荐一份个人感觉非常不错的**学习算法刷题绝佳资料吧，除了计算机底层技术，算法不可忽视，如果想进BAT、TMD、快手这样的一线大厂，认认真真过上一遍，这些大厂算法面试一关大部分题目都不在话下**：
[Github疯传！阿里P8大佬写的Leetcode刷题笔记，秒杀80%的算法题！mp.weixin.qq.com/s/A-HPH3Tkl8KOvZgdRArvIg](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/A-HPH3Tkl8KOvZgdRArvIg)
{%asset_img Pasted_image_20220421163206.png 600 300 %}
让我们继续CPU这一话题。
人是懒惰的也是聪明的，我们就没有一种2+-2就是0(0000)的数字表达方法吗？
## **新的表示方法**
现在，对于最左边的bit位来说，0表示正数，1表示负数是没有什么异议的，这里的关键在于我们需要一种表示方法，可以让A+(-A) = 0，而且这里0的二进制也是0，如果是4个bit为的话就应该是0000。
假设A=2，那么我们重点研究下2+-2 = 0(0000)的表示方法。
对于2来说，很简单就是0010，对于-2来说，现在我们只能确定最左边的bit位是1，也就是说 ：
{%asset_img Pasted_image_20220421163214.png 150 150 %}
**显然 -2 应该用1110来表示，这样2+-2就真的是0了**，由此推断：
{%asset_img Pasted_image_20220421163222.png 150 250 %}
从图中可以看出这种表示方法下就没有-0了。
注意看-1和0，分别是1111和0000，当我们让-1(1111)加上1(0001)时，我们确实得到了0000，不过还有一个进位，实际上我们得到的是10000，但**我们可以放心的忽略掉该进位**。
**这种表示方法最美妙的地方在于加法器不用修改就可以直接计算有符号数字的加法**。
你可以自己试验几个数字相加就能发下这种表示方法的奇妙之处，你给这种数字表达方法起了名字，**补码，**这现代计算机系统所采用的数字表示方法。
采用补码，如果是4个bit位，那么我们可以表示的范围是-8 ~ 7。再来仔细看一下反码和补码：
{%asset_img Pasted_image_20220421163231.png 300 250 %}
因为补码不需要表示 -0 这个奇葩，你会发现一个很有意思的规律，那就是**负数的反码加上1就是对应的补码**，这是计算机教科书在讲解补码时很重要的一个知识点，现在你应该知道这个知识点是怎么来的了吧！
## **CPU真的识数吗？**
就像前面提到的，现代计算机采用补码的根本原因在于**这种表示方法可以简化电路设计**，尽管补码对人类来说不够直观。到这里我们可以看到，在计算机科学中，**最符合人类思维的设计并不一定对计算机最优**，这也是最迷人的地方，人和机器毕竟不同嘛！让我们再来看下采用补码时2+-2 的计算过程：
{%asset_img Pasted_image_20220421163239.png 300 250 %}
和十进制加法一样，从右到左，如果产生进位，那么进位就要参与左边一列的计算。
注意，在这个过程中加法器关心这个数字是正数还是负数了吗？答案是没有，**加法器或者更具体是CPU中的ALU根本就不关心是正数还是负数，它只知道我要进行加法计算**，除此之外不 CARE 任何其它信息，至于数字该采用反码还是补码这些是人类需要理解的，确切来说是编译器需要来理解的，程序员都无需关心，但**程序员需要知道数据类型的表示范围**。
现在你能明白补码以及 CPU 是如何识数的了吧。
在本文我们详细讲解了CPU是如何识数这一主题，正如本文所言，**CPU其实本质的上是不识数的，也不需要识数**。
转自[为什么计算机采用补码而不是原码或反码？](https://www.zhihu.com/question/352057791/answer/1878359705)
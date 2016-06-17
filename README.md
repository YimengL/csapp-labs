# CS:APP labs
[http://csapp.cs.cmu.edu/2e/home.html](http://csapp.cs.cmu.edu/2e/home.html)  
my solution of labs in CS:APP
最近在学习CS:APP，补一下CS的基础，方便以后学习OS(学习过，但是不够深入)，Database System，Distributed System，Computer Network等相对高阶的知识  
所有lab都能在书的网站下载，很方便：[http://csapp.cs.cmu.edu/2e/labs.html](http://csapp.cs.cmu.edu/2e/labs.html)。  
操作系统及gcc版本：  
```
$: cat /proc/version
Linux version 3.9.10-100.fc17.x86_64 (mockbuild@bkernel01.phx2.fedoraproject.org) (gcc version 4.7.2 20120921 (Red Hat 4.7.2-2) (GCC) ) #1 SMP Sun Jul 14 01:31:27 UTC 2013

```
*****
### Lab 1: Data Lab

> 本lab主要是考察位运算，难度不低，据估计做了15小时左右，其中```float_i2f```, ```ilog2```, ```bitCount```参考了答案才解决。。想想CMU的童鞋大二就这么吊，简直没法比。。。。

下面是题目的讲解：  

* bitAnd(x, y)

```C++
/*
 * bitAnd - x&y using only ~ and |
 *   Example: bitAnd(6, 5) = 4
 *   Legal ops: ~ |
 *   Max ops: 8
 *   Rating: 1
 */
int bitAnd(int x, int y) {
	return ~((~x) | (~y));
}
```
很简单，中学的逻辑题的水平。。直接看代码。  

*****

* getByte(x, n)

```C++
/*
 * getByte - Extract byte n from word x
 *   Bytes numbered from 0 (LSB) to 3 (MSB)
 *   Examples: getByte(0x12345678,1) = 0x56
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 6
 *   Rating: 2
 */
int getByte(int x, int n) {
	/* find 8 * n first, then left shift the x */
	return ((x >> (n << 3)) & 0xFF);
}
```
把目标的**byte**右移到最低位，然后用一个二进制值为11111111的**mask**遮掩住高位即可。

*****

* logicalShift(x, n)

```C++
/*
 * logicalShift - shift x to the right by n, using a logical shift
 *   Can assume that 0 <= n <= 31
 *   Examples: logicalShift(0x87654321,4) = 0x08765432
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 20
 *   Rating: 3
 */
int logicalShift(int x, int n) {
	int tmp = (((0x01 << 31) >> n) << 1);
	return ((~tmp) & (x >> n));
}
```
所谓逻辑左移就是再左移的时候最高位补充0，而不是像**算数左移**一样高位补充**原先最高位的数字**，```tmp```得到的是一个111...0000，其中从最高位开始，一共有n个1，之后都是0。此后将```tmp```**按位取反**，再和给定的```x```**按位与**可得到答案。

*****

* bitCount(int x)

```C++
/*
 * bitCount - returns count of number of 1's in word
 *   Examples: bitCount(5) = 2, bitCount(7) = 3
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 40
 *   Rating: 4
 */
int bitCount(int x) {
	int cnt = x;
	int tmp1 = 0x55 | (0x55 << 8);
	int mask1 = tmp1 | (tmp1 << 16);
	int tmp2 = 0x33 | (0x33 << 8);
	int mask2 = tmp2 | (tmp2 << 16);
	int tmp3 = 0x0f | (0x0f << 8);
	int mask3 = tmp3 | (tmp3 << 16);
	int mask4 = 0xff | (0xff << 16);
	int mask5 = 0xff | (0xff << 8);

	cnt = (cnt & mask1) + ((cnt >> 1) & mask1);
	cnt = (cnt & mask2) + ((cnt >> 2) & mask2);
	cnt = (cnt & mask3) + ((cnt >> 4) & mask3);
	cnt = (cnt & mask4) + ((cnt >> 8) & mask4);
	cnt = (cnt & mask5) + ((cnt >> 16) & mask5);
	return cnt;
}
```
这是一道超级大难题。。。个人认为是最难的一道，虽然有2道题我也没解出来，但是，那两道属于给我重组时间我能解决，这道则不行。。。
本题的思路是**divide & conquer**:
* 第一个```cnt```是把所有```mod(2)== 1;mod(2)==0```都放在```mod(2)==0```，**但是有可能会进位**，举个栗子：如果第0位是1，第1位是1，相加后得2，则第0位是0，第1位是1.
* 第二个```cnt```是把所有的```mod(4) == 0 or 1 or 2 or 3```都放在```mod(4)==0```，**同样会进位**，这种形式可以将上步的结果合并，比如上次结果0，1位的和是2即10，假设2，3位的和是1即01，这步就能将两步结果合并，2 + 1 = 3，即前四位是0011
* 如果看懂了前两步，后面的都是同理可得
*****

* bang(x)

```C++
/* 
 * bang - Compute !x without using !
 *   Examples: bang(3) = 0, bang(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int bang(int x) {
	return ((x | ((~x) + 1)) >> 31) + 1;
}
```
这道题的意思就是如果```x```是0，则返回1，如果是别的数，则通通返回0。。本题的关键是只有```x = 0```的时候```x```和```(~x) + 1```的最高位都是0，别的数的话，总有1个是1，用这个最高位就可以构建出0xffffffff或者0x00000000，此题即可解出。。虽然同样是4分题，但是比上一道简单太多了。。

*****

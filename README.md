# CSAPP-datalab1
### 1. 根据题目，可知，是要求利用～和&做出一个异或门

![image](https://user-images.githubusercontent.com/76896357/113309013-509fc900-9339-11eb-8477-ae65a454a4ec.png)

__这道题，最初一看没有啥思路，但是看的时间长发现能把它所有的情况列举出来，再进行组合：__

比如：

0     1     0     1

1     0     0     1

* 两组数据上下对应就这四种情况，运算符只有两个，数据之间只能进行&，因此，先看看这种情况下对应的异或值是多少：

求异或：

 0     1     0     1

^1     0     0     1

————————————————————————

 1     1     0     0
 
 * 这种情况下，可以尝试对每一组数据都取反(因为直接求&后发现与结果不对，即使单个求反也不行)，之后在求&：
 
 之后发现结果是：
 
 0      0     1     0
 
 * 这时(我本来是比较郁闷的)我刚好在草稿纸上瞟见了之前算的原来数据求的&：
 
 0      0     0     1
 
 对比一下，最后两个字符刚好相反，嘶···，先求了下&，发现是：
 
 0      0     0     0
 
 * 这时候也该意识到，这两者(未取反的& 和 求过反的&)前边的两个数据一样，后边的两个数据不一样，又看了看本来的异或结果。。。。
 就决定给他两都**取反再&**刚好得到:
 
 1      1     0     0
 
 所以，最终结果是：
 ```
 （～((~x)&(~y)))&(~(x&y))
 ```
 ### 2.返回二进制的的最小值：
  这个我觉得直接看课，理解二进制就可以做出来;
  
  ![image](https://user-images.githubusercontent.com/76896357/113317633-2272b700-9342-11eb-9d2c-24867083ad7c.png)

  
 * Tmin：符号位为1，其他位为0
 
 结果是：
 
 ```
 1<<31
 ```

### 3. 如果x是二进制最大值返回1，否则返回零:

![image](https://user-images.githubusercontent.com/76896357/113325806-7930be80-934b-11eb-9687-1b9704f13a8d.png)

* 先看看最大值是多少吧；
* tmax=0111 1111 1111 1111 1111 1111 1111 1111

* 首先只有这一种情况返回1，其他情况返回0


* 最大值的一个特点：

    加1后变成了最小的负数(1后全0)
    
    取反后为最小的二进制
    
    取非后为0
    
推测：重在考察最大值的性质：

  tmax+1经常被用来异或
  
  此时，尝试将x(假设为tmax)+1与x异或，发现得到：
  
  1111 1111 1111 1111 1111 1111 1111 1111
  
  思路还是不清晰，再将x+1与～x异或，此时刚好得到：
  
  0000 0000 0000 0000 0000 0000 0000 0000
  
  这时候就突然想到给这个结果取反，刚好返回1；
  
  试过之后不行
  
  原因是当x=-1时，即x的二进制位全部为1，异或结果也是0。。。。
  
  这时要排出-1的影响，而又不改变原来的值
  
  * 因此，想到了｜，利用｜当x=-1时，让其结果与1进行或运算，结果为1，取非后为0；
  * 综合上述情况，只能在!(~X^(X+1)|)的｜后面加上!(~x)这句话，在x全部为1时，返回1.在其他任何情况均返回0；

因此，
  ```
  !(~x^(x+1)|!(~x))
  ```
  
### 4.如果x的所有奇数位全为1，返回1：
![image](https://user-images.githubusercontent.com/76896357/113376787-1a982e80-93a5-11eb-873f-19c1271429be.png)

根据提示：可以想到0xAAAAAAAA为奇数位全为1的情况；

则符合条件的x与0xAAAAAAAA异或后所有位全部为0，取非后为1

试了一下不行

这是因为没仔细看体的原因：奇数位全部为1，就要返回1，不用关注偶数位；

* 所以，吸取教训，既然x满足奇数位全部为1，如果偶数位全部为0那么与0xAAAAAAAA异或后，值为0，所以想办法将x的偶数位化为0；

#### 即：
```
!((x&0xAAAAAAAA)^0xAAAAAAAA)
```

### 5.求-x
![image](https://user-images.githubusercontent.com/76896357/113378120-ca22d000-93a8-11eb-94fe-5b18abd495b2.png)

* 直接取反加一

```
~x+1
```

### 6.如果x在0x30和0x39之间返回1，否则返回0；

取反加一为减
* (x~0x30+1)为正(0x39~x+1)为正

* 整数都有一个特征：左移31位后全部为0；

* 因此，构建(x~0x30+1)>>31&(0x39~x+1)>>31,因为同时满足所以用&

* 最后，因为返回1，则需要取非(!((x~0x30+1)>>31))&(!((0x39~x+1)>>31))

##### 结果：
```
(!((x~0x30+1)>>31))&(!((0x39~x+1)>>31))
```

#### 7.如果x为0，返回z，x为1，返回y
![image](https://user-images.githubusercontent.com/76896357/113380949-394ff280-93b0-11eb-8eb3-94fc38d13c6d.png)

  x原本为二进制，现在要转换为0或1： !!x  两个‘!'防止改变x的布尔值；
  
  * return !!x&z|!!x&y

利用这个模版来控制返回z还是y

* 因为x布尔值为0是返回z，说明此时‘｜’左边的x的表达式为-1，右边x的表达式为0
* 同时x布尔值为1时返回y，说明此时‘｜’左边的x的表达式为0，右边x的表达式为-1

(!!x)为0或1，则取反后为-1或-2，那么-2加1仍为-1；

所以构造结果：
```
((~(～(!!x)+1))&z)|((~(!!x)+1)&y)
```
#### 8.如果x<=y，返回1否则返回0；
![image](https://user-images.githubusercontent.com/76896357/113384069-d6faf000-93b7-11eb-95a8-888a5119b5f7.png)


初步感觉：x-y<=0返回1即可其他情况返回0；

用diff表示差值
```
diff = x+~y+1
```
判断diff的正负：
```
diffsign=diff>>31&1
```
diffsign可能为0或1

* diffsign为0时有歧义，相等的时候也为0
因为负-正为负，正-正为负或为正，

分情况：
* 先求出x,y的符号位
```
xsign=x>>31&1
ysign=y>>31&1
```
* 第一种情况：sign为0，ysign为1，xsign为1，ysign为0
```
mask=xsign&!ysign
```
只能算出两种情况

* 第二种情况xsign与ysign的值相同
1. 做差，求差的符号位()：
```
diff = x+~y+1;
diffsign = diff>>31&1;
```

2. 要根据diff得出返回值：
	2.1. 同理，将diff布尔化(!!diff)

	2,2. 在！！diff为0的时候有:
		
		diff>0或diff==0

3. 此时xsign和ysign相同，两者异或后为0，取非后为1
```
samesign = !(ysign^xsign)
```

4. 返回值
```
flag = sameSignFlag & (~(!!diff) | diffSign);//为排除两者不相等的情况
```
5. 结果
```
int isLessOrEqual(int x, int y) {
    int xSign = (x >> 31) & 1;
    int ySign = (y >> 31) & 1;
    int mask = xSign & !ySign;
    int diff = x + ~y + 1;
    int diffSign = (diff >> 31) & 1;
    int sameSign = !(xSign ^ ySign);
    int Flag = sameSign & (~(!!diff) | diffSign);
    return (mask|Flag);
}
```
#### 9.用其他符号表示!,已知x求！x；
![image](https://user-images.githubusercontent.com/76896357/113397854-4977ca00-93d0-11eb-9db4-8b9c02b25e73.png)

	只要x不为0那么!x=1

对于一个二进制，要向不经过取非而得到它的非值；

只能先取反，找一下思路：
```
~x&x
```
* 第一步就想到这么多，对于任意的数，上面的结果均为0；

* 第二步，我们需要把0隔离出来，因为他取非是1

* 这时候想到0的性质0的相反数还是0，其他数的相反数肯定不是0:
```
~x&(~x+1)
```
* 这时只有0的结果是0，要想变为1；就要出现1&1

～x为全1

* 那么可以将&右边的式子取反,同时向右移动31

```
(~x&~(~x+1))>>31
```

* 此时0的返回值是1，但是为了返回0和1我们需要把结果前边的值过滤掉；

因此

```
(~x&~(~x+1))>>31&1
```

### 10.表示一个十进制最少要多少位：

	这个想了挺长时间，最后还是向知乎求救

![image](https://user-images.githubusercontent.com/76896357/113398238-d4f15b00-93d0-11eb-80b8-51d49c10fb31.png)

* 由此可知32位的val前边全为0，直到出现第一个1以及后面的所有位数就是x的位数

* 接下来不断缩小范围来确定1的位置

```
int b16,b8,b4,b2,b1,b0;
  int sign=x>>31;
  x = (sign&~x)|(~sign&x);//如果x为正则不变，否则按位取反（这样好找最高位为1的，原来是最高位为0的，这样也将符号位去掉了）


// 不断缩小范围
  b16 = !!(x>>16)<<4;//高十六位是否有1
  x = x>>b16;//如果有（至少需要16位），则将原数右移16位
  b8 = !!(x>>8)<<3;//剩余位高8位是否有1
  x = x>>b8;//如果有（至少需要16+8=24位），则右移8位
  b4 = !!(x>>4)<<2;//同理
  x = x>>b4;
  b2 = !!(x>>2)<<1;
  x = x>>b2;
  b1 = !!(x>>1);
  x = x>>b1;
  b0 = x;
  return b16+b8+b4+b2+b1+b0+1;//+1表示加上符号位
```
### 11.计算2*f：
![image](https://user-images.githubusercontent.com/76896357/113403019-919aea80-93d8-11eb-93b2-516a4b467dcc.png)

	首先排除无穷小，0，无穷大，NAN非数字情况

1. 计算exp
```
int exp = (uf&0x7f800000)>>23;
```
2.计算符号位：

```
int sign = uf&(1<<31);
```
3. exp==0时,无穷小返回乘2加上符号位

```
if(exp==0) return uf<<1|sign;
```
4. exp==255时无穷大返回本身
```
if(exp==255) return uf;
```
5. 之后剩下的情况，如果指数加1后为255返回符号位和无穷大,否则返回指数加1的原符号数
```
if(exp==255) return 0x7f800000|sign;
return (exp<<23)|(uf&0x807fffff);
```
### 12. 将浮点数转换为整数

	参考知乎https://zhuanlan.zhihu.com/p/59534845

先算出sign(s_),exp(exp_)和frac(frac_)

* 先看这个数是不是0，如果是0返回0

* 查看exp是不是大于31，因为溢出，返回最小的整数

* 如果exp<01右移x位,x>0，结果为0）则返回0。

* 首先把小数部分（23位）转化为整数（和23比较），然后判断是否溢出：如果和原符号相同则直接返回，否则如果结果为负（原来为正）则溢出返回越界指定值0x80000000u，否则原来为负，结果为正，则需要返回其补码（相反数）。

### 13. 求2的x次方
![image](https://user-images.githubusercontent.com/76896357/113407483-f4dc4b00-93df-11eb-86e5-73f72e487994.png)

	既然是x次方，那么exp=x+127
	
如果exp大于255说明溢出，越界
```
int m = 0xff<<23;
  int exp = x + 127;
  if(exp <= 0) return 0;
  if(exp >= 255) return m;
```
	
最后结果
![image](https://user-images.githubusercontent.com/76896357/113407543-14737380-93e0-11eb-97ca-428f033c309a.png)



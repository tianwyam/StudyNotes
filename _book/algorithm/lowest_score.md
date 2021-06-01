

## 最低分数线问题





*记录面试算法题，便于回顾*







### 问题描述



**题目：**

​	某比赛已经进入了淘汰赛阶段,已知共有n名选手参与了此阶段比赛，他们的得分分别是a_1,a_2….a_n,小美作为比赛的裁判希望设定一个分数线m，使得所有分数大于m的选手晋级，其他人淘汰。

​	但是为了保护粉丝脆弱的心脏，小美希望晋级和淘汰的人数均在[x,y]之间。

​	显然这个m有可能是不存在的，也有可能存在多个m，如果不存在，请你输出-1，如果存在多个，请你输出符合条件的最低的分数线。



**输入：**
	n x y 

​	n个选手的得分

**输出：**

​	m (最低分数)



**例如：**

输入： 

​	6 2 3

​	1 2 3 4 5 6

输出：

​	3





### 思路



<br>



**思路一：**

设 晋级的人数是 t， 则    t ∈ [x, y]

并且 淘汰人数 n-t，则  n-t ∈ [x, y] , 否则不存在，输出 -1

求 m的最小值，就相当于是 晋级的人 越多，淘汰的人越少，分数线就越低



<br>



**思路二：**

设 晋级的人数是 t， 则    t ∈ [x, y]

并且 淘汰人数 n-t，则  n-t ∈ [x, y] , 否则不存在，输出 -1

可以设定值 t 在 [x,y]之间依次循环，然后做判断，最后留下来的数组中最小数







### 解题



方式一：

~~~java

/**
 * @description
 *	求最小分数
 * @author TianwYam
 * @date 2021年5月25日下午7:39:59
 * @param n 总人数
 * @param x 最小范围值
 * @param y 最大范围值
 * @param a 各个人数的成绩
 * @return
 */
public static int lowestScore(int n, int x, int y, int[] a) {
	
	// 从低到高 排序 
	// 淘汰人最低分数就是淘汰人数对应的分数
	Arrays.sort(a);
	
	// 思路一：
	// 设 晋级的人数是 t， 则    t ∈ [x, y]
	// 并且 淘汰人数 n-t，则  n-t ∈ [x, y] , 否则不存在，输出 -1
	// 求 m的最小值，就相当于是 晋级的人 更多，淘汰的人更少
	
	// 取极限值，这样淘汰的人就更少，m就是最低的
	// 淘汰的人
	int t = n - y ;
	
	// t ∈ [x, y]
	
	if (t > y) {
		
		// 不存在
		return -1 ;
	} else {
		
		if (t >= x) {
			
			// 需要淘汰的人的个数，最小就是对应的分数
			return a[t -1];
		}else {
			
			// 必须淘汰的人是 在 x, y 直接，所以取最小的
			return a[x - 1 ] ;
			
		}
		
	}
	
}

~~~







方式二：

~~~java


/**
 * @description
 *	求最小分数
 * @author TianwYam
 * @date 2021年5月25日下午7:39:59
 * @param n 总人数
 * @param x 最小范围值
 * @param y 最大范围值
 * @param a 各个人数的成绩
 * @return
 */
public static int lowestScore2(int n, int x, int y, int[] a) {
	
	// 从低到高 排序 
	// 淘汰人最低分数就是淘汰人数对应的分数
	Arrays.sort(a);
	
	// 思路二：
	// 设 晋级的人数是 t， 则    t ∈ [x, y]
	// 并且 淘汰人数 n-t，则  n-t ∈ [x, y] , 否则不存在，输出 -1
	// 可以设定值 t 在 [x,y]之间依次循环，然后做判断，最后留下了的数组中最小数
	
	List<Integer> existNum = new ArrayList<>();
	
	// t ∈ [x, y]
	for (int t = x; t <= y; t++) {
		
		//  n-t ∈ [x, y]
		if (x <= n-t  && n-t <= y) {
			existNum.add(t);
		}
	}

	if (existNum.size() == 0) {
		return -1 ;
	}
	
	
//		// 最小淘汰的人数
//		Integer min = Collections.min(existNum);
//		return a[min-1] ;
	
	// 最大晋级的人数
	Integer max = Collections.max(existNum);
	// 最小淘汰人
	return a[n-max-1] ;
	
}


~~~








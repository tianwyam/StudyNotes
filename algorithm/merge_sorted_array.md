## 算法题



**题目：**

​	给定两个有序整数数组 A 和 B，将 B 合并到 A 中，使得 A 成为一个有序数组。



**说明:** 

​	初始化 A 和 B 的元素数量分别为 m 和 n。 

​	你可以假设 A 有足够的空间（空间大小大于或等于 m + n）来保存 B 中的元素。 



**示例:** 

输入: 

​	A = [1,3,3,4,0,0,0,0],  m = 4 

​	B = [2,5,6], n = 3 

输出: [1,2,3,3,4,5,6]



**解题思路：**

方式一：先把数组B放到A后面，然后对A做排序

方式二：使用两个指针，分别从AB的尾向前比较移动







### 方式一



~~~java
	
/**
 * @Description: 
 * 方法一：先把数组B放到A后面，然后对A做排序
 * @author: TianwYam
 * @date 2021年5月23日 上午10:07:45
 * @param A 整型数组A
 * @param m 数组A的有效长度m(实际长度 >= m+n )
 * @param B 整型数组B
 * @param n 数组B的有效长度n
 */
public static void merge1(int[] A, int m, int[] B, int n) {
	
	// 1.把数组B放到A后面
	for (int i = m, j = 0;  i < (m + n); i++, j++) {
		A[i] = B[j] ;
	}
	
	printMsg("合并后的数组A：");
	printAry(A);
	
	// 2.对A进行排序
	Arrays.sort(A);
	
}
~~~





### 方式二



~~~java

/**
 * @Description: 
 * 方式二：使用两个指针，分别从AB的尾向前比较移动
 * @author: TianwYam
 * @date 2021年5月23日 上午10:36:43
 * @param A 整型数组A
 * @param m 数组A的有效长度m(实际长度 >= m+n )
 * @param B 整型数组B
 * @param n 数组B的有效长度n
 */
public static void merge2(int[] A, int m, int[] B, int n) {

	// 1.找到存放AB中最大数的位置
	// A数组存放A+B的最后一个位置
	int p = m + n -1 ;
	
	// A数组的最后一个数
	int a = m -1 ;
	// B数组的最后一个数
	int b = n -1 ;
	while (a >= 0 && b >= 0) {
		// 2.循环判断
		// 若是A最大的数大于B最大的数，则A最大数移动
		// 若是B最大的数大于A最大的数，则B最大数移动
		if (A[a] > B[b]) {
			A[p] = A[a];
			a-- ;
		} else {
			A[p] = B[b];
			b-- ;
		}
		
		p--;
	}

	// 异常边界判断，还存在B数组还没有循环到的
	while (b >= 0) {
		A[p] = B[b];
		p-- ;
		b-- ; 
	}
	
	
}

~~~



方式二更加速度更快






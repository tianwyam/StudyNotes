

## 求数组的子集合



<br/>



*记录面试算法题，便于回顾*



<br/>



**题目一：**

给出一个数组 list = [1,2,3,4,5,6]

求此数组的所有子集合

输出：[1]，[1,2]，[1,2,3]........



<br/>



**题目二：**

给出一个数组 list = [1,2,3,4,5,6]

求此数组的所有子集合中和为6的子集

输出：[6，[2,4]，[1,5]，[1,2,3]



<br/>





**解题思路：**

方式一：递归

方式二：全排列方式





<br/>





### 方式一：递归



<br/>



#### 算法



~~~java
package com.tianya.java.algorithm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

/**
 * @description
 *	求数组的子集合，子数组
 *	方式一：递归方式
 * @author TianwYam
 * @date 2021年6月1日下午2:37:21
 */
public class SubArray {
	
	public static void main(String[] args) {
		
		// 测试数组
		int[] list = {1,2,3,4,5,6} ;
		
		// 保存结果集
		List<List<Integer>> resultList = new ArrayList<>();
		List<Integer> subList = new ArrayList<>();
		
		// 递归调用
		subList(list, 0, resultList, subList);
		
		// 求数组的子集
		System.out.println("数组的所有子集合：");
		for (List<Integer> sub : resultList) {
			System.out.println(Arrays.toString(sub.toArray()));
		}
		
		// 求各个子集中 和 为6的集合
		System.out.println("输出和为：6");
		for (List<Integer> sub : resultList) {
			int sum = sub.stream().reduce(0, Integer::sum);
			if (sum == 6) {
				System.out.println(Arrays.toString(sub.toArray()));
			}
		}
	}
	
	
	/**
	 * @description
	 *	递归 
	 * @author TianwYam
	 * @date 2021年6月1日下午3:22:41
	 * @param list
	 * @param index
	 * @param resultList
	 * @param subList
	 */
	public static void subList(int[] list , int index, 
			List<List<Integer>> resultList, List<Integer> subList) {
		
		resultList.add(new ArrayList<>(subList));
		
		int size = list.length ;
		for (int i = index; i < size; ) {
			subList.add(list[i]);
			subList(list, ++i, resultList, subList);
			subList.remove(subList.size() - 1);
		}
		
	}
	

}
~~~



<br/>



#### 结果

~~~java
数组的所有子集合：
[]
[1]
[1, 2]
[1, 2, 3]
[1, 2, 3, 4]
[1, 2, 3, 4, 5]
[1, 2, 3, 4, 5, 6]
[1, 2, 3, 4, 6]
[1, 2, 3, 5]
[1, 2, 3, 5, 6]
[1, 2, 3, 6]
[1, 2, 4]
[1, 2, 4, 5]
[1, 2, 4, 5, 6]
[1, 2, 4, 6]
[1, 2, 5]
[1, 2, 5, 6]
[1, 2, 6]
[1, 3]
[1, 3, 4]
[1, 3, 4, 5]
[1, 3, 4, 5, 6]
[1, 3, 4, 6]
[1, 3, 5]
[1, 3, 5, 6]
[1, 3, 6]
[1, 4]
[1, 4, 5]
[1, 4, 5, 6]
[1, 4, 6]
[1, 5]
[1, 5, 6]
[1, 6]
[2]
[2, 3]
[2, 3, 4]
[2, 3, 4, 5]
[2, 3, 4, 5, 6]
[2, 3, 4, 6]
[2, 3, 5]
[2, 3, 5, 6]
[2, 3, 6]
[2, 4]
[2, 4, 5]
[2, 4, 5, 6]
[2, 4, 6]
[2, 5]
[2, 5, 6]
[2, 6]
[3]
[3, 4]
[3, 4, 5]
[3, 4, 5, 6]
[3, 4, 6]
[3, 5]
[3, 5, 6]
[3, 6]
[4]
[4, 5]
[4, 5, 6]
[4, 6]
[5]
[5, 6]
[6]
输出和为：6
[1, 2, 3]
[1, 5]
[2, 4]
[6]
~~~





<br/>





### 方式二：全排列组合



<br/>



#### 算法



~~~java
package com.tianya.java.algorithm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

/**
 * @description
 *	求数组的子集合，子数组
 *	方式二：全排列方式
 * @author TianwYam
 * @date 2021年6月1日下午2:37:21
 */
public class SubArray2 {
	
	public static void main(String[] args) {
		
		// 数组
		int[] list = {1,2,3,4,5,6} ;
		
		// 保存结果集 
		List<List<Integer>> resultList = new ArrayList<>();
		
		// 全排列 循环
		int size = list.length ;
		for (int i = 0; i < size; i++) {
			
			// 保存单个
			resultList.add(new ArrayList<>(Arrays.asList(list[i])));
			
			for (int j = i+1; j < size; j++) {
				
				List<Integer> subList = new ArrayList<>();
				subList.add(list[i]);
				
				for (int k = j; k < size; k++) {
					subList.add(list[k]);
					
					// 保存重新一个数组
					resultList.add(new ArrayList<>(subList));
				}
			}
			
		}
		
		
		
		System.out.println("数组的所有子集合：");
		for (List<Integer> sub : resultList) {
			System.out.println(Arrays.toString(sub.toArray()));
		}
		
		
		System.out.println("输出和为：6");
		for (List<Integer> sub : resultList) {
			int sum = sub.stream().reduce(0, Integer::sum);
			if (sum == 6) {
				System.out.println(Arrays.toString(sub.toArray()));
			}
		}
	}
	
	

}

~~~



<br/>



#### 结果

~~~java
数组的所有子集合：
[1]
[1, 2]
[1, 2, 3]
[1, 2, 3, 4]
[1, 2, 3, 4, 5]
[1, 2, 3, 4, 5, 6]
[1, 3]
[1, 3, 4]
[1, 3, 4, 5]
[1, 3, 4, 5, 6]
[1, 4]
[1, 4, 5]
[1, 4, 5, 6]
[1, 5]
[1, 5, 6]
[1, 6]
[2]
[2, 3]
[2, 3, 4]
[2, 3, 4, 5]
[2, 3, 4, 5, 6]
[2, 4]
[2, 4, 5]
[2, 4, 5, 6]
[2, 5]
[2, 5, 6]
[2, 6]
[3]
[3, 4]
[3, 4, 5]
[3, 4, 5, 6]
[3, 5]
[3, 5, 6]
[3, 6]
[4]
[4, 5]
[4, 5, 6]
[4, 6]
[5]
[5, 6]
[6]
输出和为：6
[1, 2, 3]
[1, 5]
[2, 4]
[6]
~~~



<br/>




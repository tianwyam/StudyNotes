# Redis中的队列list实现秒杀活动抢购





<br/>

[TOC]

<br/>

<br/>



redis中的数据结构list中 rpush  | lpop | lpush | rpop  实现队列的先进先出的特性



<br/>

lpush：左边入队列，存入秒杀活动的商品

rpop：右边出队列，获取抢到的商品



## 1. 引入redis客户端maven依赖



~~~xml

<!-- java redis 客户端 -->
<dependency>
	<groupId>redis.clients</groupId>
	<artifactId>jedis</artifactId>
    <version>3.1.0</version>
</dependency>
~~~





## 2. 定义抢购商品实体类



~~~java

/**
 * @description
 *	商品货物
 * @author TianwYam
 * @date 2021年10月24日下午6:33:17
 */
@Data
@Builder
public class Goods {
	
	// 商品编号
	private int goodsId ;
	
	// 商品名称
	private String goodsName ;
	
	// 价格
	private double price ;

}

~~~





## 3. 定义模拟用户抢购线程类



~~~java

/**
 * @description
 *	用户抢购线程
 * @author TianwYam
 * @date 2021年10月25日下午1:22:09
 */
public class PersonShoppingThread  implements Runnable{
	
	public static final String LIST_GOODS = "LIST_GOODS";
	
	// 顺序
	private int index ;
	
	// 用户姓名
	private String name ;
	
	private Jedis jedis ;
	
	public PersonShoppingThread(int index, Jedis jedis) {
		this.index = index ;
		this.jedis = jedis ;
		this.name = new Faker(Locale.SIMPLIFIED_CHINESE).name().fullName();
	}

	@Override
	public void run() {
		
		// 模拟 用户抢购 耗时
		try {
			Thread.sleep(500);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		
		// 获取 秒杀商品队列 数量
		Long len = jedis.llen(LIST_GOODS);
		if (len == null || len <= 0) {
			System.out.println(String.format(" %d : %s 商品已经卖完了，没有抢到了秒杀商品", index, name));
			return ;
		}
		
		// 队列中取出商品
		String goods = jedis.rpop(LIST_GOODS);
		if (StringUtils.isNotBlank(goods)) {
			System.out.println(String.format(" %d : %s 抢到了 秒杀商品：%s" , index, name, goods));
		} else {
			System.out.println(String.format(" %d : %s 没有抢到秒杀商品", index, name));
		}
		
		// 关闭回收连接
		jedis.close();
	}

}

~~~



## 4. 实现秒杀抢购活动主类



~~~java

/**
 * @description
 *	redis 中的 list 实现秒杀活动
 * @author TianwYam
 * @date 2021年10月25日下午1:35:30
 */
public class RedisListSecondKill {
	
	public static final String LIST_GOODS = "LIST_GOODS";
	
	// redis 客户端连接
//	private static final Jedis jedis = new Jedis("localhost", 6379);
	
	// redis 连接池
	private static final JedisPool JEDIS_POOL = getJedisPool();
	
	// 线程池
	private final static ExecutorService THREAD_POOL = Executors.newFixedThreadPool(20);
	
	
	/**
	 * @description
	 *	redis连接池配置 
	 * @author TianwYam
	 * @date 2021年10月26日下午8:18:08
	 * @return
	 */
	public static JedisPool getJedisPool() {
		JedisPoolConfig poolConfig = new JedisPoolConfig();
		poolConfig.setMaxIdle(40);
		poolConfig.setMinIdle(10);
		poolConfig.setMaxTotal(50);
		
		return new JedisPool(poolConfig);
	}
	
	/**
	 * @description
	 *	模拟生成秒杀商品数据
	 * @author TianwYam
	 * @date 2021年10月26日下午8:29:27
	 */
	public static void pushGoods() {
		
		Jedis jedis = JEDIS_POOL.getResource();
		
		// 模拟生成秒杀商品数据
		System.out.println("开始模拟生成秒杀商品数据......");
		Faker faker = new Faker(Locale.SIMPLIFIED_CHINESE);
		for (int i = 0; i <= 10; i++) {
			Goods goods = Goods.builder()
					.goodsId(i)
					.goodsName(faker.food().fruit())
					.price(faker.number().randomDouble(2, 1, 150))
					.build();
			
			// 往队列中添加 秒杀商品
			jedis.lpush(LIST_GOODS, JSON.toJSONString(goods));
		}
		
		System.out.println("完成模拟生成秒杀商品数据");
		
		jedis.close();
	}
	
	/**
	 * @description
	 *	获取所有的秒杀商品
	 * @author TianwYam
	 * @date 2021年10月26日下午8:28:55
	 * @return
	 */
	public static List<String> getGoodsList() {
		Jedis jedis = JEDIS_POOL.getResource();
		List<String> lrangeGoods = jedis.lrange(LIST_GOODS, 0, -1);
		jedis.close();
		return lrangeGoods;
	}
	
	
	/**
	 * @description
	 *	启动秒杀活动主类
	 * @author TianwYam
	 * @date 2021年10月26日下午8:29:42
	 */
	public static void main(String[] args) {
		
		// 往队列中添加 秒杀商品
		pushGoods();
		
		// 查询 队列内商品数量
		List<String> lrangeGoods = getGoodsList();
		if (lrangeGoods == null || lrangeGoods.size() == 0) {
			System.out.println("没有秒杀商品了 或者 秒杀活动已结束");
			return ;
		}
		
		System.out.println("\n所有的秒杀商品：");
		System.out.println(JSON.toJSONString(lrangeGoods, true));
		
		System.out.println("\n所有的用户开始进行抢购：");
		for (int i = 0; i < 30; i++) {
			THREAD_POOL.submit(new PersonShoppingThread(i, JEDIS_POOL.getResource()));
		}
		
		try {
			THREAD_POOL.awaitTermination(10, TimeUnit.SECONDS);
			THREAD_POOL.shutdown();
			JEDIS_POOL.close();
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		
		System.out.println("秒杀活动结束");
	}

}

~~~



## 5. 秒杀抢购活动结果



~~~verilog
开始模拟生成秒杀商品数据......
完成模拟生成秒杀商品数据

所有的秒杀商品：
[
	"{\"goodsId\":10,\"goodsName\":\"Prunes\",\"price\":88.97}",
	"{\"goodsId\":9,\"goodsName\":\"Mangosteens\",\"price\":105.82}",
	"{\"goodsId\":8,\"goodsName\":\"Cherries\",\"price\":76.04}",
	"{\"goodsId\":7,\"goodsName\":\"Sultanas\",\"price\":59.16}",
	"{\"goodsId\":6,\"goodsName\":\"Cavalo\",\"price\":20.93}",
	"{\"goodsId\":5,\"goodsName\":\"Corella Pear\",\"price\":12.8}",
	"{\"goodsId\":4,\"goodsName\":\"Kiwi Fruit\",\"price\":125.18}",
	"{\"goodsId\":3,\"goodsName\":\"Nectarines\",\"price\":19.5}",
	"{\"goodsId\":2,\"goodsName\":\"Mulberries\",\"price\":71.42}",
	"{\"goodsId\":1,\"goodsName\":\"Olives\",\"price\":122.51}",
	"{\"goodsId\":0,\"goodsName\":\"Honeydew melon\",\"price\":122.05}"
]

所有的用户开始进行抢购：
 0 : 叶泽洋 抢到了 秒杀商品：{"goodsId":0,"goodsName":"Honeydew melon","price":122.05}
 1 : 白明 抢到了 秒杀商品：{"goodsId":2,"goodsName":"Mulberries","price":71.42}
 2 : 洪志泽 抢到了 秒杀商品：{"goodsId":1,"goodsName":"Olives","price":122.51}
 3 : 洪锦程 抢到了 秒杀商品：{"goodsId":3,"goodsName":"Nectarines","price":19.5}
 4 : 黎乐驹 抢到了 秒杀商品：{"goodsId":5,"goodsName":"Corella Pear","price":12.8}
 5 : 莫靖琪 抢到了 秒杀商品：{"goodsId":4,"goodsName":"Kiwi Fruit","price":125.18}
 6 : 汪志泽 抢到了 秒杀商品：{"goodsId":6,"goodsName":"Cavalo","price":20.93}
 7 : 唐越彬 抢到了 秒杀商品：{"goodsId":7,"goodsName":"Sultanas","price":59.16}
 9 : 高鹏飞 抢到了 秒杀商品：{"goodsId":8,"goodsName":"Cherries","price":76.04}
 8 : 龚正豪 抢到了 秒杀商品：{"goodsId":9,"goodsName":"Mangosteens","price":105.82}
 11 : 阎晓啸 抢到了 秒杀商品：{"goodsId":10,"goodsName":"Prunes","price":88.97}
 10 : 张笑愚 没有抢到秒杀商品
 13 : 武明杰 商品已经卖完了，没有抢到了秒杀商品
 12 : 傅文 商品已经卖完了，没有抢到了秒杀商品
 14 : 袁锦程 商品已经卖完了，没有抢到了秒杀商品
 16 : 孙子默 商品已经卖完了，没有抢到了秒杀商品
 15 : 戴绍齐 商品已经卖完了，没有抢到了秒杀商品
 17 : 彭乐驹 商品已经卖完了，没有抢到了秒杀商品
 18 : 彭越彬 商品已经卖完了，没有抢到了秒杀商品
 19 : 邱伟宸 商品已经卖完了，没有抢到了秒杀商品
 20 : 洪俊驰 商品已经卖完了，没有抢到了秒杀商品
 21 : 吕志强 商品已经卖完了，没有抢到了秒杀商品
 22 : 魏伟宸 商品已经卖完了，没有抢到了秒杀商品
 23 : 蒋浩然 商品已经卖完了，没有抢到了秒杀商品
 24 : 莫修杰 商品已经卖完了，没有抢到了秒杀商品
 25 : 徐立果 商品已经卖完了，没有抢到了秒杀商品
 26 : 傅志泽 商品已经卖完了，没有抢到了秒杀商品
 27 : 段旭尧 商品已经卖完了，没有抢到了秒杀商品
 29 : 谢思源 商品已经卖完了，没有抢到了秒杀商品
 28 : 覃楷瑞 商品已经卖完了，没有抢到了秒杀商品
秒杀活动结束

~~~




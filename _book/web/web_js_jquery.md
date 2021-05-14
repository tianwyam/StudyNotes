# 1、a标签 





**1. 返回上一页并刷新：**

```html

<a href="javascript:" onclick="self.location=document.referrer;" ></a>

```

== 此处只是返回上一步操作并刷新



---


# 2、jquery积累

---

## 刷新当前页

js:
```javascript
window.location.reload();
```



---



## 显示/隐藏/移除


```javascript


$("#id").hide(); // 隐藏

$("#id").show(); // 显示


// 如果状态是显示，则隐藏；反之亦然
$("#id").toggle(); // 显示/隐藏



// 移除 并不是真正意义上的删除，只是相当于隐藏掉了
$("#id").remove(); //移除


```

---


## 复选框/全选-取消



```javascript
    
    
// 全选 按钮 响应事件
$("#checkboxChoose").on("click", function(){
	
	$("[name='checkbox']").prop("checked", true);  // 全选
		
});
	
	
	
// 取消 按钮 响应事件
$("#checkboxCancel").on("click", function(){
	
	$("[name='checkbox']").prop("checked", false); //取消全选 
		
});
		
```







---


## 获取复选框的值




```javascript


var studyPlanIDs = [] ; // 要删除的学习计划的id
		
$("[name='checkbox']:checked").each(function(){
	studyPlanIDs.push($(this).val()); // 存入数组中
});


```





---


## 获取父元素/查找




```javascript


//获取父元素
// 获取 当前元素 的 第一个 父元素 为 tr 的
var tr = $(this).parents("tr");  
    
    
//查找 并 赋值
body.find("#planId").val(id);



```



---


## 三目运算符（？：）


javascript中也具有三目运算符的用法，跟java中的基本一样


例如：

```javascript

    
startMonth = ((startMonth.length > 1) ? "" : "0") + startMonth ;


```





---


## 表单 异步提交


```javascript


$(function(){
	
	// 提交按钮响应事件 
	$("#SubmitBtnID").click(function(){
		
		// 表单 添加
		$("#FormID").submit(function(){
			
			$.ajax({
				type:"post",
				url:"addPlan",
				async: true,
				traditional: true,
				contentType: "application/json;charset=UTF-8",
				
				//此处也可以用$(this).serialize()
				data:$(this).serializeArray(),
				dataType: "json",
				success: function(data){
					
					alert(data);

					console.log("成功！");

				},
			});
			
			// serialize()方法 获取表单值域/并序列化了
			console.log($(this).serialize());
			
			
		});
	});
	
});


```



其中 方法：

**serializeArray()**
**serialize()**

 可以获取表单的值并序列化。

**并且这两个方法是jquery里面的，后台可以直接在request中根据在html中name/id取值，也可以直接用javabean 来接收。**




# 4、setTimeout

```javascript
setTimeout("document.getElementById('my').src='include/common.php'; ",3000);
```










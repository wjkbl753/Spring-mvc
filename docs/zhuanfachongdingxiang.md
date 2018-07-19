### 转发：reward:

之前我们直接return其实就是转发
那么我们如何传参?

#### 传参：

`原生api方式`
```java
@RequestMapping("/hello")
public String hello(String name, HttpServletRequest request){
		request.setAttribute("name","小明2");
		return "success";
}
```

`springmvc推荐方式`: 1.Map|Model|ModelMap
```java
@RequestMapping("/hello")
public String hello(String name, Map<String,Object> map){
		map.put("name","小红");
		return "success";
}
```

`springmvc推荐方式`: 1.ModelAndView (模型+视图)
```java
@RequestMapping("/hello")
public ModelAndView hello(ModelAndView modelAndView){
		modelAndView.addObject("name","haha");
		modelAndView.setViewName("success");
		return modelAndView;
}
```
或者：
```java
@RequestMapping("/hello")
public ModelAndView hello(){
		ModelAndView modelAndView = new ModelAndView("success");
		modelAndView.addObject("name","haha2");
		return modelAndView;
}
```
!> 推荐使用map 或者ModelAndView方式，而且以上方式属性都是放在了request域中

### 重定向： redirect:

```java
	@RequestMapping("/dispatcherTest")
	public String dispatcherTest() {
		return "redirect:/index.jsp";
//		return "forward:/index.jsp";
	}
//或者：	
	@RequestMapping("/dispatcherTest2")
	public ModelAndView dispatcherTest2(ModelAndView modelAndView) {
		modelAndView.setViewName("redirect:/index.jsp");
	  //modelAndView.setViewName("forward:/index.jsp");
		return modelAndView;
	}
```
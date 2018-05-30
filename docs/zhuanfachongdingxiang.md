转发：reward:

重定向： redirect:

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
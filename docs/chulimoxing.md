## ModelAndView

handler可以返回ModelAndView，可以既包含视图，也包含模型数据

```java
@RequestMapping("/modelAndViewTest")
public ModelAndView modelAndViewTest() {
    ModelAndView modelAndView = new ModelAndView("success");
    modelAndView.addObject("name", "xiaoming");
    return modelAndView;
}
//或者：
@RequestMapping("/modelAndViewTest")
public ModelAndView modelAndViewTest(ModelAndView modelAndView) {
    modelAndView.setViewName("success");
    modelAndView.addObject("name", "xiaoming");
    return modelAndView;
}
```

## Map Model  ModelMap

```java
@RequestMapping("/mapTest")
public String mapTest(Map<String,Object> map) {
    map.put("age", 20);
    return "success";
}
```
Model和ModelMap用法和Map基本一致，我们一般用熟悉的Map就好


!>上面的方式都是把数据放入了request中，那怎么放到session里？

## @SessionAttribute

@SessionAttribute只能放在类上

它有两个属性 value  type

@SessionAttributes(value= {"gender"},types= {String.class})
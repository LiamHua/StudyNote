# 一、注解

### 1. 接收前端数据

+ `@RestController:` 

  ​	这个注解是@Controller与@ResponseBody的组合，@Controller的本质就是@Component，配置该注解使该类能被Spring扫描到；@ResponseBody指示该类中方法的返回值被写入到Response 的 body 中

+ `@RequestMapping:`

  ​	负责将web请求映射到方法上，可以在类层和方法层使用，具体又细分为@GetMapping、@PostMapping、@PutMapping、@DeleteMapping、@PatchMapping

+ `@RequestBody:`

  ​	指示方法参数应该与web请求到body部分绑定，会调用HttpMessageConverter来转换body 中的数据；若参数是实体类，则属性名要一一对应，否则会读取不到；注意前端传递参数与方法参数类型一致，即json对象对应Java实体类，string 对应 String；若前端传递json对象，后端使用String来接收，则该对象会整体被转化为json字符串格式

+ `@RequestParam:`

  ​	指示方法参数应该与web请求的URL参数部分绑定，如以get方式提交的数据

+ `@RequestPart:`

  ​	该注解被用来处理 multipart/form-data 类型的请求；同样的，@RequestParam也可以处理该类型的请求，区别是@RequestParam常用来处理数据类型为string的简单请求，而@RequestPart用来处理数据类型为JSON、XML的复杂请求

+ `@RequestHeader:`

  ​	该注解被用来接收请求中header部分的参数
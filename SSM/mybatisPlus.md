

# yml配置

```yaml
spring:
  # 配置链接数据库
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:13306/MyBatis
    username: root
    password: origin
mybatis-plus:
  configuration:
    # 配置日志,即可查看具体执行的sql语句和执行结果信息
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      # 设置主键生成策略,auto表示自增长
      id-type: auto
```



# BaseMapper

BaseMapper是mybatisPlus中的一个接口,我们让自己的mapper接口继承该接口,即可使用该接口提供的一系列方法实现对单表的增删改查,无需我们手动写方法

```java
@Mapper
public interface EmpMapper extends BaseMapper<Employee> {
}
```



# BaseMapper提供的基础crud方法

## insert(T entity)

向表中插入一条数据



## deleteById(T entity)

根据id删除一条数据

注意:mybatisPlus中id即代表主键,该方法的sql默认是`DELETE FROM xxx WHERE id=?`,因此如果我们的主键名字不叫id就会报错,可以在创建domain对象时使用`@TableId`注解来标明主键字段

```java
@TableId
private Integer eid;
```

这样,该方法的sql就变成`DELETE FROM xxx WHERE eid=?`



## deleteByMap(Map<String, Object> map)

传入一个map,map中包含一系列条件

```java
HashMap<String, Object> map = new HashMap<>();
map.put("emp_name", "peter");
map.put("salary", 23456);
empMapper.deleteByMap(map);
```

sql: `DELETE FROM employee WHERE emp_name = ? AND salary = ?`



## deleteBatchIds(Collection<?> idList)

根据id批量删除,传入一个Collection集合

```java
List<Integer> idList = Arrays.asList(6, 8);
empMapper.deleteBatchIds(idList);
```

sql: `DELETE FROM employee WHERE eid IN ( ? , ? )`



## updateById(T entity)

修改一条数据

```java
Employee employee = new Employee(5, "peter", 12345, 2);
empMapper.updateById(employee);	
```

sql: `UPDATE employee SET emp_name=?, salary=?, did=? WHERE eid=?`

注意:如果传入的对象某个字段为null,表示不修改该字段



## selectById(T entity)

根据id查询

```java
Employee employee = empMapper.selectById(1);
```



## selectBatchIds(Collection<?> idList)

根据id批量查询



## selectByMap(Map<String, Object> map)

根据map中的条件查询



## selectList(null)

查询所有数据



# 通用service

mybatisPlus为我们提供了一套service层实现

让我们的service继承mybatisPlus提供的`IService`

```java
public interface EmpService extends IService<Employee> {
}
```

再让我们的ServiceImpl继承mybatisPlus提供的`ServiceImpl`并实现我们自己写的service接口

```java
public class EmpServiceImpl extends ServiceImpl<EmpMapper, Employee> implements EmpService {
}
```



# 实体类注解

## @TableName&@TableId

使用`@TableName`注解来标注实体类和所对应的表名,使用`@TableId`来标识实体类的某个字段为主键

```java
@Data
@AllArgsConstructor
@TableName("emp_name")
public class Employee {
    @TableId(value = "e_id", type = "idType.AUTO")//value用于标识该主键在表中的名字,type用于指定主键生成策略,默认是雪花算法,idType.AUTO表示自增长
    private Integer eid;
    private String empName;
    private Integer salary;
    private Integer did;
}
```



## @TableField

标注于实体类字段上,标识该类在表中的名称

```java
@TableField("emp_name")
private String name;
```



## @TableLogic

该注解标识于实体类的`isDeleted`字段上,意为逻辑删除

该字段的作用是:执行查询操作时,会将`isDeleted`的值作为查询条件`select xxx from xxx where xxx and is_deleted = 0`;当

执行删除操作时,并不会实际删除,而是执行`update xxx set is_deleted = 1 where xxx and is_deleted = 0`



# 条件构造器Wrapper

wrapper是一个接口,其有一系列实现类,用于封装条件传入baseMapper的方法中实现条件sql



## QueryWarpper

**查询**

```java
QueryWrapper<Employee> empQueryWrapper = new QueryWrapper<>();
empQueryWrapper.between("salary", 5000, 20000).like("emp_name", "e");//warpper支持链式编程
List<Employee> employeeList = empMapper.selectList(empQueryWrapper);
```

sql:

 `SELECT eid,emp_name AS name,salary,did FROM emp_name WHERE (salary BETWEEN ? AND ? AND emp_name LIKE ?)`

注意:queryWarpper拼接的条件之间默认以and连接,如果想用or,可以调用`or()`方法



**修改**

```java
QueryWrapper<Employee> empQueryWrapper = new QueryWrapper<>();
empQueryWrapper.like("emp_name", "o").and(i -> i.between("salary", 1000, 10000));//and表示加一个小括号,优先执行
Employee employee = new Employee(null, null, 6666, null);//要修改为的数据(为null表示不修改该字段)
empMapper.update(employee, empQueryWrapper);
```



## updateWrapper

updateWrapper的查询方法与queryWarpper相同

UpdateWrapper提供了set方法来修改字段

```java
UpdateWrapper<Employee> employeeUpdateWrapper = new UpdateWrapper<>();
employeeUpdateWrapper.like("emp_name", "gene");
employeeUpdateWrapper.set("salary", 40000);
empMapper.update(null, employeeUpdateWrapper);
```

sql: `UPDATE t_emp SET salary=? WHERE (emp_name LIKE ?)`



## condition

```java
like(age >= 10, "name", "a")//当第一个参数condition为true时才真正添加该like条件到sql中
```



# 分页插件

创建mp配置类

```java
@Configuration
public class MybatisPlusConfig {

    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mpInterceptor = new MybatisPlusInterceptor();
        mpInterceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return mpInterceptor;
    }
}
```



使用分页

```java
Page<Employee> page = new Page(1, 2);//传入当前页码和每页的展示条数
empMapper.selectPage(page, null);
```



如果想在我们自定义的mapper方法中使用分页功能,只要给该方法的形参设置一个page对象即可(要放在形参第一位),并且在sql中不用管分页,只要调用该方法的时候传入一个page对象即可实现分页功能



# 乐观锁

用`@Version`注解标识实体类的某个Integer字段用于标识该字段为版本号


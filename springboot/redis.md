Redis
=====

在Spring中，RedisConnection接口通过驱动Jedis连接Redis，进而操作Redis。

**序列化与反序列化**

> 如果想要通过Redis存储java对象，实体类就得实现序列化接口Serializable，才能将对象以二进制序列的形式存储到磁盘里面。

* **序列化**：对象转换成字节序列的过程。
* **反序列化**：字节序列转换成对象的过程。

实现Serializable接口，最好的方式是自己生成一个serialVersionUID，而不是等待它自动生成，因为一个确定的serialVersionUID才能更好的让对象进行反序列化。

在IDEA中，可以自动生成serialVersionUID。
![0001](../img/springboot/0001.png)
设置好之后对着想要序列化的类名`Alt + Enter`。
![0002](../img/springboot/0002.png)

```java
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;
import java.util.Date;

/**
 * 用户信息实体类
 * @author noTalent
 * @version 1.0
 * 2019.05.08
 */

@Data
@NoArgsConstructor
public class UserInfo implements Serializable {

    private static final long serialVersionUID = 3913083570663658513L;

    private Integer userInfoId;     // 用户信息id

    private String username;        // 用户名

    private String salt;            // 盐  值

    private String password;        // 密  码

}
```
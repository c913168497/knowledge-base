```
package org.my.springcloud.userapiserver.template2;

import javax.annotation.PostConstruct;
import java.util.*;
import java.util.function.BiConsumer;

public class LambdaTemplateDemo {

    /**
     * 基础填充方法 map
     */
    public static final Map<String, BiConsumer<String, String>> BASE_TEST_MAP = new HashMap<>();

    public static final List<String> FUN_LIST = Arrays.asList("demo1", "demo2", "demo3", "demo4");

    /**
     * 初始化 业务逻辑分派Map 其中value 存放的是 lambda表达式
     */
    @PostConstruct
    public void fillOrderExtendInfo() {
        BASE_TEST_MAP.put("demo1", (param1, param2) -> {
            System.out.println("demo1传入了两个参数：" + param1 + "--" + param2);
        });

        BASE_TEST_MAP.put("demo2", (param1, param2) -> {
            System.out.println("demo2传入了两个参数：" + param1 + "--" + param2);
        });

        BASE_TEST_MAP.put("demo3", (param1, param2) -> {
            System.out.println("demo3传入了两个参数：" + param1 + "--" + param2);
        });

        BASE_TEST_MAP.put("demo4", (param1, param2) -> {
            System.out.println("demo4传入了两个参数：" + param1 + "--" + param2);
        });
    }

    public static void main(String[] args) {
        Set<String> keys = BASE_TEST_MAP.keySet();
        for (String key : FUN_LIST) {
            if (!keys.contains(key)){
                continue;
            }

            BiConsumer<String, String> result = BASE_TEST_MAP.get(key);
            if (Objects.isNull(result)){
                throw new IllegalArgumentException("没有找到对应的填充方法，请检查！" + key);
            }

            result.accept("测试参数一", "测试参数2");
        }
    }

}
```
# Okhttp创建时设置一些参数

```java

import okhttp3.OkHttpClient;

import java.util.concurrent.TimeUnit;

public class OkDemo {
    public static void main(String[] args) {
        // 创建一个 okhttp 客户端，并设置一些参数
        OkHttpClient okHttpClient = new OkHttpClient()
                .newBuilder()
                .readTimeout(15, TimeUnit.SECONDS) // 读取超时为 15 秒
                .connectTimeout(12000, TimeUnit.MILLISECONDS) // 连接超时为 12 秒
                .writeTimeout(1, TimeUnit.MINUTES) // 写入超时为 1 分钟
                .build();
    }
}
```
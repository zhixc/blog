# Okhttp3 post请求不传递参数

下面是一个完整的案例代码：
```java

import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

public class OkDemo {
    public static void main(String[] args) throws Exception{
        String postURL = "https://www.baidu.com";
        String ua = "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:34.0) Gecko/20100101 Firefox/34.0";
        // 创建一个空的请求体对象
        RequestBody requestBody = RequestBody.create(null, "");
        // 创建一个请求对象
        Request request = new Request.Builder()
                .url(postURL)
                .post(requestBody)
                .addHeader("User-Agent", ua)
                .build();
        // 创建一个 okhttp 客户端
        OkHttpClient okHttpClient = new OkHttpClient();
        // 客户端执行请求（同步请求）
        Response response = okHttpClient.newCall(request).execute();
        ResponseBody body = response.body();
        // 注意 body.bytes() 或 body.string() 只能调用一次
//        byte[] bytes = body.bytes(); // 获取请求体内容二进制
        String str = body.string(); // 获取请求体内容字符串
        System.out.println(str);
        response.close();
    }
}
```
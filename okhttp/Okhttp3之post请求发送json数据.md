# Okhttp3之post请求发送json数据

```java

import okhttp3.*;

public class OkDemo {
    public static void main(String[] args) throws Exception{
        String postURL = "https://www.baidu.com";
        String ua = "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:34.0) Gecko/20100101 Firefox/34.0";

        // json数据
        String json =  "{\"id\":1,\"name\":\"李四\"}";
        // 创建一个 requestBody 对象
        RequestBody requestBody = RequestBody.create(
                MediaType.parse("application/json; charset=utf-8"), json);

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

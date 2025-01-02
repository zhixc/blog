# Java实现AES加密128位CBC模式工具类

> 作者: 王绍桦
> 
> 文章出处: [Java实现AES-128-CBC算法加解密
](https://blog.csdn.net/rexueqingchun/article/details/86606376)
>
> 感谢他编写和分享代码，我在他的基础上适配了较新版本的 jdk

### 代码

```java

//import android.util.Base64;

import javax.crypto.Cipher;
import javax.crypto.spec.IvParameterSpec;
import javax.crypto.spec.SecretKeySpec;
import java.util.Base64;

/**
 * AES加密128位CBC模式工具类
 *
 * @author 王绍桦
 * https://blog.csdn.net/rexueqingchun/article/details/86606376
 * 下面的两个类在新 jdk 里面没有了，得用 import java.util.Base64;
 * import sun.misc.BASE64Decoder;
 * import sun.misc.BASE64Encoder;
 */
public class AES {

    public static String encrypt(String content, String key, String iv) throws Exception {
        byte[] raw = key.getBytes("utf-8");
        SecretKeySpec skeySpec = new SecretKeySpec(raw, "AES");
        Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");//"算法/模式/补码方式"
        //使用CBC模式，需要一个向量iv，可增加加密算法的强度    
        IvParameterSpec ips = new IvParameterSpec(iv.getBytes());
        cipher.init(Cipher.ENCRYPT_MODE, skeySpec, ips);
        byte[] encrypted = cipher.doFinal(content.getBytes());

        /**
         * 推荐使用java.util.Base64类来进行Base64编码操作。
         * 以下是使用java.util.Base64类对字节数组进行编码
         */
        return Base64.getEncoder().encodeToString(encrypted);

        /**
         * 如果用的是 import android.util.Base64;
         * 那么则用 下面这种写法
         */
        //return Base64.encodeToString(encrypted, Base64.DEFAULT);
    }


    public static String decrypt(String content, String key, String iv) throws Exception {
        byte[] raw = key.getBytes("utf-8");
        SecretKeySpec skeySpec = new SecretKeySpec(raw, "AES");
        Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
        IvParameterSpec ips = new IvParameterSpec(iv.getBytes());
        cipher.init(Cipher.DECRYPT_MODE, skeySpec, ips);

        /**
         * 推荐使用java.util.Base64类来进行Base64解码操作。
         * 以下是使用java.util.Base64类对Base64编码的字符串进行解码
         */
        byte[] encrypted1 = Base64.getDecoder().decode(content);

        /**
         * 如果用的是 import android.util.Base64;
         * 那么则用 下面这种写法
         */
        //byte[] encrypted1 =  Base64.decode(content, Base64.DEFAULT);

        byte[] original = cipher.doFinal(encrypted1);
        return new String(original);
    }
}
```


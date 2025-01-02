# Okhttp3之SSL证书异常情况处理

证书异常情况，报下面这种异常：
```
Caused by: sun.security.validator.ValidatorException: PKIX path validation failed: java.security.cert.CertPathValidatorException: validity check failed

Caused by: java.security.cert.CertPathValidatorException: validity check failed

Caused by: java.security.cert.CertificateNotYetValidException
```
出现这种情况的话，可以考虑给okhttp创建的客户端取消SSL证书验证。

取消的步骤：

- 第1步
    - 创建一个 SSLSocketFactoryCompat 类 继承 SSLSocketFactory 类
- 第2步
    - okhttp创建的客户段传入 SSLSocketFactoryCompat 对象

具体代码如下

SSLSocketFactoryCompat 类
```java

import java.io.IOException;
import java.net.InetAddress;
import java.net.Socket;
import java.security.GeneralSecurityException;
import java.security.cert.X509Certificate;
import java.util.Arrays;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.List;

import javax.net.ssl.HostnameVerifier;
import javax.net.ssl.HttpsURLConnection;
import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLSocket;
import javax.net.ssl.SSLSocketFactory;
import javax.net.ssl.X509TrustManager;

/**
 * @author FongMi
 */
public class SSLSocketFactoryCompat extends SSLSocketFactory {

    public static final HostnameVerifier hostnameVerifier = (hostname, session) -> true;

    public static final X509TrustManager trustAllCert = new X509TrustManager() {

        @Override
        public void checkClientTrusted(X509Certificate[] chain, String authType) {
        }

        @Override
        public void checkServerTrusted(X509Certificate[] chain, String authType) {
        }

        @Override
        public X509Certificate[] getAcceptedIssuers() {
            return new X509Certificate[]{};
        }
    };

    static String[] protocols = null;
    static String[] cipherSuites = null;

    static {
        try {
            SSLSocket socket = (SSLSocket) SSLSocketFactory.getDefault().createSocket();
            if (socket != null) {
                List<String> protocols = new LinkedList<>();
                for (String protocol : socket.getSupportedProtocols()) if (!protocol.toUpperCase().contains("SSL")) protocols.add(protocol);
                SSLSocketFactoryCompat.protocols = protocols.toArray(new String[protocols.size()]);
                List<String> allowedCiphers = Arrays.asList("TLS_RSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256", "TLS_ECHDE_RSA_WITH_AES_128_GCM_SHA256", "TLS_RSA_WITH_3DES_EDE_CBC_SHA", "TLS_RSA_WITH_AES_128_CBC_SHA", "TLS_RSA_WITH_AES_256_CBC_SHA", "TLS_ECDHE_ECDSA_WITH_3DES_EDE_CBC_SHA", "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA", "TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA", "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA");
                List<String> availableCiphers = Arrays.asList(socket.getSupportedCipherSuites());
                HashSet<String> preferredCiphers = new HashSet<>(allowedCiphers);
                preferredCiphers.retainAll(availableCiphers);
                preferredCiphers.addAll(new HashSet<>(Arrays.asList(socket.getEnabledCipherSuites())));
                SSLSocketFactoryCompat.cipherSuites = preferredCiphers.toArray(new String[preferredCiphers.size()]);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private final SSLSocketFactory defaultFactory;

    public SSLSocketFactoryCompat() {
        try {
            SSLContext sslContext = SSLContext.getInstance("TLS");
            sslContext.init(null, new X509TrustManager[]{SSLSocketFactoryCompat.trustAllCert}, null);
            defaultFactory = sslContext.getSocketFactory();
            HttpsURLConnection.setDefaultSSLSocketFactory(defaultFactory);
        } catch (GeneralSecurityException e) {
            throw new AssertionError();
        }
    }

    private void upgradeTLS(SSLSocket ssl) {
        if (protocols != null) {
            ssl.setEnabledProtocols(protocols);
        }
        if (cipherSuites != null) {
            ssl.setEnabledCipherSuites(cipherSuites);
        }
    }

    @Override
    public String[] getDefaultCipherSuites() {
        return cipherSuites;
    }

    @Override
    public String[] getSupportedCipherSuites() {
        return cipherSuites;
    }

    @Override
    public Socket createSocket(Socket s, String host, int port, boolean autoClose) throws IOException {
        Socket ssl = defaultFactory.createSocket(s, host, port, autoClose);
        if (ssl instanceof SSLSocket) upgradeTLS((SSLSocket) ssl);
        return ssl;
    }

    @Override
    public Socket createSocket(String host, int port) throws IOException {
        Socket ssl = defaultFactory.createSocket(host, port);
        if (ssl instanceof SSLSocket) upgradeTLS((SSLSocket) ssl);
        return ssl;
    }

    @Override
    public Socket createSocket(String host, int port, InetAddress localHost, int localPort) throws IOException {
        Socket ssl = defaultFactory.createSocket(host, port, localHost, localPort);
        if (ssl instanceof SSLSocket) upgradeTLS((SSLSocket) ssl);
        return ssl;
    }

    @Override
    public Socket createSocket(InetAddress host, int port) throws IOException {
        Socket ssl = defaultFactory.createSocket(host, port);
        if (ssl instanceof SSLSocket) upgradeTLS((SSLSocket) ssl);
        return ssl;
    }

    @Override
    public Socket createSocket(InetAddress address, int port, InetAddress localAddress, int localPort) throws IOException {
        Socket ssl = defaultFactory.createSocket(address, port, localAddress, localPort);
        if (ssl instanceof SSLSocket) upgradeTLS((SSLSocket) ssl);
        return ssl;
    }
}
```

创建的 okhttp 客户端传入
```java

import okhttp3.OkHttpClient;

import javax.net.ssl.HostnameVerifier;
import javax.net.ssl.SSLSession;

public class OkDemo {
    public static void main(String[] args) {
        OkHttpClient okHttpClient = new OkHttpClient()
                .newBuilder()
                .sslSocketFactory(new SSLSocketFactoryCompat(), SSLSocketFactoryCompat.trustAllCert)
                .hostnameVerifier(new HostnameVerifier() {
                    @Override
                    public boolean verify(String hostname, SSLSession session) {
                        return true;
                    }
                })
                .build();
    }
}
```
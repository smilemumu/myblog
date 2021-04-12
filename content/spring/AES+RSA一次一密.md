#RSA+AES混合加解密流程(java实现)

![RSA+AES混合加解密流程(java实现)](img/RSA+AES.png)

##前言
- AES128
	- 密钥长度：16位
	- 算法/模式/补码方式:AES/ECB/PKCS5Padding
	- 生成字节码后用Base64转码成字符串
- RSA1024
	- 密钥格式：PKCS#8
	- A私钥：
	```
	MIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBANgzOg6jy2eiAFmw/E9zC2zRCpPWIvN7YKkjDFMt8g3besq0eau3d+7ktXAX4c3qXp7TFXdTQsjV2d354+T3/mTBkXLVHHCLaucgS9+cl6k7BJCcHE5jOEDx4jLqeOKF474xSIz7v0WhrlxFMl7XDXpDhka4clH1IdStHbvqjoODAgMBAAECgYEAwumMEd7BZnC6+CGDlu0VA8mCS73mTLSSdGnQlrz5WFzi2xTSDGmfipROrvwY6te87ltHIwjBUObbQDAlsSuTAAI2tnkFt57G7DXuYnYAufknT21z6CoLVJdiDt57kk+Num2wVQiSxWNVfDx+A/UUPQ7+/HfmHwC+QcEhDCfyB6ECQQD6CtI4nUIJOBPrnwhJZo+Yw54VOu1y5Z8+xxIkPqYfxtR9LE3v43SdVmC2bfEpHj3UwfiOpQI2BQ1JFigtOQu1AkEA3Vn6pfnmyioK3rxMhGqEyOoHUjI9QzhqVPN7T3r/2OXO6XYY2CSd2JHe89nLweY27LZ+oFYp87UTPCfTM80FVwJAJVzyltUg5gHEMEQ+V8GEmZ45hBCfJLkdN6NFmbbm2f67B81UBBGn/k7G+tuo2q0TLjSh8KbFks7kclRmQoOwtQJAezYaz0jLrry4UEOAVDT2tN+QM7DDaSd+CMt/WD6fK5zAEDQsoCPBuUt5T0NsqAH9kMYEtjtAHugsTM/eQHdfAQJAN7Sy0yXah/5RZ8oZe1bx8+XPt7R3ca6kRQtXP7NpEMI/lOuU8TAZRw7Ckj+C5lPZLu8WvEIPjtDQ4yTvvEJHiw==
	```

	- A公钥：
	```
	MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDYMzoOo8tnogBZsPxPcwts0QqT1iLze2CpIwxTLfIN23rKtHmrt3fu5LVwF+HN6l6e0xV3U0LI1dnd+ePk9/5kwZFy1Rxwi2rnIEvfnJepOwSQnBxOYzhA8eIy6njiheO+MUiM+79Foa5cRTJe1w16Q4ZGuHJR9SHUrR276o6DgwIDAQAB
	```

	- B私钥：
	```
	MIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBANCyrNMoq9QWMNim0l5XnCWfnpXHnO2AQJvt4ygugjXah8xTBdJ6N1N0rFzmw1CDN1qwJJ5X6DhJs1l3PUkzIp6/wJPl9ht55DJiqC40GvRA78gJJD4g/KQMS3t4F4xZhQpFsu9Tha8gHIACXoM05+A34qLzUvxBkFUdssjHFStPAgMBAAECgYAdMrJNRKZtoMEqvoQ2IMm+1cspJ2lELSpc5nZee8tJ344aPw9UeDbfXTZ0NPDkhccrz/L/mErr/Ruzi6QuZpIUJQTIbTlcDb1I0d/Hi7W/rkY+3Y/vBfYBPTdORhQ866LdjD84DVnP8wphcR8rnKL4H/8uYC12W+TI2nlRVIassQJBAO/0LYsSvGYNDLVMQZXjcIgPqSjheMRZHpRm+w0NdQvytJlqqAJOy+gIGeQu3fF4n/sgGdVpPcAdAGzDsCIjk4kCQQDep2vMnOvK2QCRHXSFDXSzUkhCUnIbXFHBBzh8KwluIexFblNOl/xLpHs9TM04+ei8ir2f6fPZ5chbcAl87xoXAkBrgwh066wmPWqYQNaSBhoBnKK6gmvN7fBZpdqOep0bAWAR7Uvh3NRX3mlbZ/RhoR5tMnDgwgq87UGnefaNFUuhAkBr86KJhz+vjvz+Xtiitf5x/x/3v/+BEoa03ypc0YT1986Vb0NG5Nu3VR1HgFg0Gr7jwyTjRswrRoSZCL4g69CrAkEA2e3Qxql488djYd+aH+BBuD1TBJtwMZflPf9vCr4hlMw9aYOR6/BtDGdT2XEi+uyQGPOXjbozf96+WDreyFkQhQ==
	```

	- B公钥：
	```
	MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDQsqzTKKvUFjDYptJeV5wln56Vx5ztgECb7eMoLoI12ofMUwXSejdTdKxc5sNQgzdasCSeV+g4SbNZdz1JMyKev8CT5fYbeeQyYqguNBr0QO/ICSQ+IPykDEt7eBeMWYUKRbLvU4WvIByAAl6DNOfgN+Ki81L8QZBVHbLIxxUrTwIDAQAB
	```

- 假设要加密的数据为：{"a":"1"}
## 加密签名

### 第一步：生成随机AES128密钥
```java
String aesRandomKey = EncryptUtil.getAesRandomKey();
//假设生成的密钥为：a7ab6ac42c20d747
```

### 第二步：将需要加密的数据进行AES128加密
```java
String data = "{\"a\":\"1\"}";
String data2Aes = EncryptUtil.encryptByAES(data2Aes,aesRandomKey);
//使用随机AES密钥a7ab6ac42c20d747加密data后得到数据：
//data2Aes=ag5nOGqwFa75tYBiwHB8hA==
```

### 第三步：将生成的随机AES128密钥用RSA方式加密
```java
String randomKey = EncryptUtil.encryptByRSA(aesRandomKey, publicKeyB);
//得到用RSA加密后的randomKey:
//w+glUqi5+RLkeWGNXP9SlVN7LmIa8Vi/EIKz/DUNHS6sfo6uDVssaCNDysOFpA0XhKRz28tIZ4p1eWmM3pOXDD9qXCi4PscRRlrPPA9njgigpvdm1sFLtEObr5CDBsKn/+XIsSZJ/8u9ZP33q6bw8yGRsfekD0FHSw4SsVEGs+Y=
```

### 第四步：将timesTamp、data2Aes、randomKey用A私钥签名
```
//例如：timesTamp=1609753530
//data2Aes=ag5nOGqwFa75tYBiwHB8hA==
//randomKey=w+glUqi5+RLkeWGNXP9SlVN7LmIa8Vi/EIKz/DUNHS6sfo6uDVssaCNDysOFpA0XhKRz28tIZ4p1eWmM3pOXDD9qXCi4PscRRlrPPA9njgigpvdm1sFLtEObr5CDBsKn/+XIsSZJ/8u9ZP33q6bw8yGRsfekD0FHSw4SsVEGs+Y=
//生成的签名串为（字典排序）：
//linkStr="data=ag5nOGqwFa75tYBiwHB8hA==&randomKey=w+glUqi5+RLkeWGNXP9SlVN7LmIa8Vi/EIKz/DUNHS6sfo6uDVssaCNDysOFpA0XhKRz28tIZ4p1eWmM3pOXDD9qXCi4PscRRlrPPA9njgigpvdm1sFLtEObr5CDBsKn/+XIsSZJ/8u9ZP33q6bw8yGRsfekD0FHSw4SsVEGs+Y=&timesTamp=1609753530"
//生成签名的方法：
String sign = EncryptUtil.signByPrivateKey(linkStr, privateKeyA);
//生成的签名：poxqmGGrnOtzzzy+HLmw6tAOHUWxi81b0JBJtmYk2ubFRGPRDiNSu3cRShRqOBEiRc0BukjvEZib4yyjNSk4w/4HFa1md9NmkY6B47ETnRvEShhjftoTPKBwwnQMrGRs6HYSPCbbFcG1QXqiQlIR7XvvI4shKNeKBljBA5Uk+08=
```
### 第五步：组装请求报文
```json
{"randomKey":"w+glUqi5+RLkeWGNXP9SlVN7LmIa8Vi/EIKz/DUNHS6sfo6uDVssaCNDysOFpA0XhKRz28tIZ4p1eWmM3pOXDD9qXCi4PscRRlrPPA9njgigpvdm1sFLtEObr5CDBsKn/+XIsSZJ/8u9ZP33q6bw8yGRsfekD0FHSw4SsVEGs+Y=","sign":"poxqmGGrnOtzzzy+HLmw6tAOHUWxi81b0JBJtmYk2ubFRGPRDiNSu3cRShRqOBEiRc0BukjvEZib4yyjNSk4w/4HFa1md9NmkY6B47ETnRvEShhjftoTPKBwwnQMrGRs6HYSPCbbFcG1QXqiQlIR7XvvI4shKNeKBljBA5Uk+08=","data":"ag5nOGqwFa75tYBiwHB8hA==","timesTamp":"1609753530"}
```

## 验签解密
```
接收到数据：
{"randomKey":"w+glUqi5+RLkeWGNXP9SlVN7LmIa8Vi/EIKz/DUNHS6sfo6uDVssaCNDysOFpA0XhKRz28tIZ4p1eWmM3pOXDD9qXCi4PscRRlrPPA9njgigpvdm1sFLtEObr5CDBsKn/+XIsSZJ/8u9ZP33q6bw8yGRsfekD0FHSw4SsVEGs+Y=","sign":"poxqmGGrnOtzzzy+HLmw6tAOHUWxi81b0JBJtmYk2ubFRGPRDiNSu3cRShRqOBEiRc0BukjvEZib4yyjNSk4w/4HFa1md9NmkY6B47ETnRvEShhjftoTPKBwwnQMrGRs6HYSPCbbFcG1QXqiQlIR7XvvI4shKNeKBljBA5Uk+08=","data":"ag5nOGqwFa75tYBiwHB8hA==","timesTamp":"1609753530"}
```

### 第一步：验证签名
```
//先生成签名串(签名串不包含sign)
//linkStr="data=ag5nOGqwFa75tYBiwHB8hA==&randomKey=w+glUqi5+RLkeWGNXP9SlVN7LmIa8Vi/EIKz/DUNHS6sfo6uDVssaCNDysOFpA0XhKRz28tIZ4p1eWmM3pOXDD9qXCi4PscRRlrPPA9njgigpvdm1sFLtEObr5CDBsKn/+XIsSZJ/8u9ZP33q6bw8yGRsfekD0FHSw4SsVEGs+Y=&timesTamp=1609753530"
//验证方法：用A的公钥验证签名
Boolean verifyResult = EncryptUtil.verifySignByPublicKey(linkStr, publicKeyA, sign);

```

### 第二步：如果验证通过，解密randomKey获取aesRandomKey
```
//根据B自己的私钥解密randomKey
randomKey=w+glUqi5+RLkeWGNXP9SlVN7LmIa8Vi/EIKz/DUNHS6sfo6uDVssaCNDysOFpA0XhKRz28tIZ4p1eWmM3pOXDD9qXCi4PscRRlrPPA9njgigpvdm1sFLtEObr5CDBsKn/+XIsSZJ/8u9ZP33q6bw8yGRsfekD0FHSw4SsVEGs+Y=
String aesKey = EncryptUtil.decryptByRSA(randomKey, privateKeyB);
//解密后获取aesKey=a7ab6ac42c20d747
```

### 第三步：通过AES解密data获取解密后数据
```
//待解密data=ag5nOGqwFa75tYBiwHB8hA==
String decryptBizData = EncryptUtil.decryptByAES(data, aesKey);
//解密后decryptBizData = "{\"a\":\"1\"}";
```

##代码：
### EncryptUtil.class
```
import org.apache.commons.codec.binary.Base64;
import org.bouncycastle.jce.provider.BouncyCastleProvider;

import javax.crypto.Cipher;
import javax.crypto.spec.SecretKeySpec;
import java.security.*;
import java.util.*;

public class RsaUtils
{
    private static final Random random = new Random();

    public static String getAESRandomKey()
    {
        long longValue = random.nextLong();
        return String.format("%016x", new Object[] { Long.valueOf(longValue) });
    }


    public static PublicKey getPublicKeyFromString(String base64String)
            throws NoSuchAlgorithmException, InvalidKeySpecException
    {
        byte[] bt = Base64.decodeBase64(base64String);
        X509EncodedKeySpec publicKeySpec = new X509EncodedKeySpec(bt);
        KeyFactory keyFactory = KeyFactory.getInstance("RSA");
        PublicKey publicKey = keyFactory.generatePublic(publicKeySpec);
        return publicKey;
    }

    public static PrivateKey getPrivateKeyFromString(String base64String)
            throws InvalidKeySpecException, NoSuchAlgorithmException
    {
        byte[] bt = Base64.decodeBase64(base64String);
        PKCS8EncodedKeySpec privateKeySpec = new PKCS8EncodedKeySpec(bt);
        KeyFactory keyFactory = KeyFactory.getInstance("RSA");
        PrivateKey privateKey = keyFactory.generatePrivate(privateKeySpec);
        return privateKey;
    }

    public static boolean verifySignByPublicKey(String inputStr, String publicKey, String base64SignStr)
            throws Exception
    {
        return verifyByPublicKey(inputStr.getBytes(), getPublicKeyFromString(publicKey), Base64.decodeBase64(base64SignStr));
    }

    public static boolean verifyByPublicKey(byte[] data, PublicKey publicKey, byte[] signature)
            throws Exception
    {
        Signature sig = Signature.getInstance("SHA256withRSA");
        sig.initVerify(publicKey);
        sig.update(data);
        boolean ret = sig.verify(signature);
        return ret;
    }

    public static String signByPrivateKey(String inputStr, String privateKey)
            throws Exception
    {
        return Base64.encodeBase64String(signByPrivateKey(inputStr.getBytes(), getPrivateKeyFromString(privateKey)));
    }

    public static byte[] signByPrivateKey(byte[] data, PrivateKey privateKey)
            throws Exception
    {
        Signature sig = Signature.getInstance("SHA256withRSA");
        sig.initSign(privateKey);
        sig.update(data);
        byte[] ret = sig.sign();
        return ret;
    }

    public static String encryptByRSA(String inputStr, String publicKey)
            throws Exception
    {
        PublicKey key = getPublicKeyFromString(publicKey);
        return Base64.encodeBase64String(encryptByRSA(inputStr.getBytes(), key));
    }

    public static byte[] encryptByRSA(byte[] input, Key key)
            throws Exception
    {
        Cipher cipher = Cipher.getInstance("RSA");
        cipher.init(1, key);
        byte[] output = cipher.doFinal(input);
        return output;
    }

    public static String decryptByRSA(String inputStr, String privateKey)
            throws Exception
    {
        PrivateKey key = getPrivateKeyFromString(privateKey);
        return new String(decryptByRSA(Base64.decodeBase64(inputStr), key));
    }

    public static byte[] decryptByRSA(byte[] input, Key key)
            throws Exception
    {
        Cipher cipher = Cipher.getInstance("RSA");
        cipher.init(2, key);
        byte[] output = cipher.doFinal(input);
        return output;
    }

    public static String encryptByAES(String inputStr, String password)
            throws Exception
    {
        byte[] byteData = inputStr.getBytes();
        byte[] bytePassword = password.getBytes();
        return Base64.encodeBase64String(encryptByAES(byteData, bytePassword));
    }

    public static byte[] encryptByAES(byte[] data, byte[] pwd)
            throws Exception
    {
        Cipher cipher = Cipher.getInstance("AES");
        SecretKeySpec keySpec = new SecretKeySpec(pwd, "AES");
        cipher.init(1, keySpec);
        byte[] ret = cipher.doFinal(data);
        return ret;
    }

    public static String decryptByAES(String inputStr, String password)
            throws Exception
    {
        byte[] byteData = Base64.decodeBase64(inputStr);
        byte[] bytePassword = password.getBytes();
        return new String(decryptByAES(byteData, bytePassword));
    }

    public static byte[] decryptByAES(byte[] data, byte[] pwd)
            throws Exception
    {
        Cipher cipher = Cipher.getInstance("AES");
        SecretKeySpec keySpec = new SecretKeySpec(pwd, "AES");
        cipher.init(2, keySpec);
        byte[] ret = cipher.doFinal(data);
        return ret;
    }



    /**
     * 将map对象转成需要签名的数据
     * @param params
     * @return key=value&key=value......（按字母顺序排序）
     */
    public static String createLinkString(Map<String, String> params)
    {
        List<String> keys = new ArrayList(params.keySet());
        Collections.sort(keys);
        StringBuffer linkStr = new StringBuffer();
        for (int i = 0; i < keys.size(); i++)
        {
            String key = String.valueOf(keys.get(i));
            String value = String.valueOf(params.get(key));
            if (value != null) {
                if (i == keys.size() - 1) {
                    linkStr.append(key).append("=").append(value);
                } else {
                    linkStr.append(key).append("=").append(value).append("&");
                }
            }
        }
        return linkStr.toString();
    }

    public static String MD5(String source)
            throws NoSuchAlgorithmException
    {
        StringBuffer sb = new StringBuffer();
        String part = null;
        MessageDigest md = MessageDigest.getInstance("MD5");
        byte[] bytes = source.getBytes();
        byte[] md5 = md.digest(bytes);
        for (int i = 0; i < md5.length; i++)
        {
            part = Integer.toHexString(md5[i] & 0xFF);
            if (part.length() == 1) {
                part = "0" + part;
            }
            sb.append(part);
        }
        return sb.toString();
    }


    private static byte[] decryptByRSA4MicroSoft(byte[] input, PrivateKey key)
            throws Exception
    {
        Security.addProvider(new BouncyCastleProvider());
        Cipher cipher = Cipher.getInstance("RSA/NONE/NoPadding");
        cipher.init(2, key);
        byte[] output = cipher.doFinal(input);
        return output;
    }
}
```

### RsaAesTest.class
```java
import com.alibaba.fastjson.JSON;
import com.google.common.collect.Maps;
import com.*.EncryptUtil;

import java.time.Instant;
import java.util.HashMap;
import java.util.Map;

/**
 * @Author 程序员小董
 * @Date 2020/12/31 16:40
 */
public class RsaAesTest {
    private static final String PUBLIC_KEY_A = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDYMzoOo8tnogBZsPxPcwts0QqT\n" +
            "1iLze2CpIwxTLfIN23rKtHmrt3fu5LVwF+HN6l6e0xV3U0LI1dnd+ePk9/5kwZFy\n" +
            "1Rxwi2rnIEvfnJepOwSQnBxOYzhA8eIy6njiheO+MUiM+79Foa5cRTJe1w16Q4ZG\n" +
            "uHJR9SHUrR276o6DgwIDAQAB";
    private static final String PRIVATE_KEY_A = "MIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBANgzOg6jy2eiAFmw\n" +
            "/E9zC2zRCpPWIvN7YKkjDFMt8g3besq0eau3d+7ktXAX4c3qXp7TFXdTQsjV2d35\n" +
            "4+T3/mTBkXLVHHCLaucgS9+cl6k7BJCcHE5jOEDx4jLqeOKF474xSIz7v0WhrlxF\n" +
            "Ml7XDXpDhka4clH1IdStHbvqjoODAgMBAAECgYEAwumMEd7BZnC6+CGDlu0VA8mC\n" +
            "S73mTLSSdGnQlrz5WFzi2xTSDGmfipROrvwY6te87ltHIwjBUObbQDAlsSuTAAI2\n" +
            "tnkFt57G7DXuYnYAufknT21z6CoLVJdiDt57kk+Num2wVQiSxWNVfDx+A/UUPQ7+\n" +
            "/HfmHwC+QcEhDCfyB6ECQQD6CtI4nUIJOBPrnwhJZo+Yw54VOu1y5Z8+xxIkPqYf\n" +
            "xtR9LE3v43SdVmC2bfEpHj3UwfiOpQI2BQ1JFigtOQu1AkEA3Vn6pfnmyioK3rxM\n" +
            "hGqEyOoHUjI9QzhqVPN7T3r/2OXO6XYY2CSd2JHe89nLweY27LZ+oFYp87UTPCfT\n" +
            "M80FVwJAJVzyltUg5gHEMEQ+V8GEmZ45hBCfJLkdN6NFmbbm2f67B81UBBGn/k7G\n" +
            "+tuo2q0TLjSh8KbFks7kclRmQoOwtQJAezYaz0jLrry4UEOAVDT2tN+QM7DDaSd+\n" +
            "CMt/WD6fK5zAEDQsoCPBuUt5T0NsqAH9kMYEtjtAHugsTM/eQHdfAQJAN7Sy0yXa\n" +
            "h/5RZ8oZe1bx8+XPt7R3ca6kRQtXP7NpEMI/lOuU8TAZRw7Ckj+C5lPZLu8WvEIP\n" +
            "jtDQ4yTvvEJHiw==";
    private static final String PUBLIC_KEY_B = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDQsqzTKKvUFjDYptJeV5wln56V\n" +
            "x5ztgECb7eMoLoI12ofMUwXSejdTdKxc5sNQgzdasCSeV+g4SbNZdz1JMyKev8CT\n" +
            "5fYbeeQyYqguNBr0QO/ICSQ+IPykDEt7eBeMWYUKRbLvU4WvIByAAl6DNOfgN+Ki\n" +
            "81L8QZBVHbLIxxUrTwIDAQAB";
    private static final String PRIVATE_KEY_B = "MIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBANCyrNMoq9QWMNim\n" +
            "0l5XnCWfnpXHnO2AQJvt4ygugjXah8xTBdJ6N1N0rFzmw1CDN1qwJJ5X6DhJs1l3\n" +
            "PUkzIp6/wJPl9ht55DJiqC40GvRA78gJJD4g/KQMS3t4F4xZhQpFsu9Tha8gHIAC\n" +
            "XoM05+A34qLzUvxBkFUdssjHFStPAgMBAAECgYAdMrJNRKZtoMEqvoQ2IMm+1csp\n" +
            "J2lELSpc5nZee8tJ344aPw9UeDbfXTZ0NPDkhccrz/L/mErr/Ruzi6QuZpIUJQTI\n" +
            "bTlcDb1I0d/Hi7W/rkY+3Y/vBfYBPTdORhQ866LdjD84DVnP8wphcR8rnKL4H/8u\n" +
            "YC12W+TI2nlRVIassQJBAO/0LYsSvGYNDLVMQZXjcIgPqSjheMRZHpRm+w0NdQvy\n" +
            "tJlqqAJOy+gIGeQu3fF4n/sgGdVpPcAdAGzDsCIjk4kCQQDep2vMnOvK2QCRHXSF\n" +
            "DXSzUkhCUnIbXFHBBzh8KwluIexFblNOl/xLpHs9TM04+ei8ir2f6fPZ5chbcAl8\n" +
            "7xoXAkBrgwh066wmPWqYQNaSBhoBnKK6gmvN7fBZpdqOep0bAWAR7Uvh3NRX3mlb\n" +
            "Z/RhoR5tMnDgwgq87UGnefaNFUuhAkBr86KJhz+vjvz+Xtiitf5x/x/3v/+BEoa0\n" +
            "3ypc0YT1986Vb0NG5Nu3VR1HgFg0Gr7jwyTjRswrRoSZCL4g69CrAkEA2e3Qxql4\n" +
            "88djYd+aH+BBuD1TBJtwMZflPf9vCr4hlMw9aYOR6/BtDGdT2XEi+uyQGPOXjboz\n" +
            "f96+WDreyFkQhQ==";


    public static void encryptDemo() throws Exception {
        //生成数据过程
        //需要加密的demo数据
        String json = "{\"a:\":\"1\"}";
        System.out.println("加密前数据:"+json);
        //生成的随机Aes密钥
        String aesKey = EncryptUtil.getAESRandomKey();
        System.out.println("随机生成的AES密钥："+aesKey);
        String randomKey = EncryptUtil.encryptByRSA(aesKey, PUBLIC_KEY_B);
        System.out.println("使用B公钥加密后的AES密钥【RSA加密后】："+randomKey);
        //随机Aes加密后的业务数据
        String data = EncryptUtil.encryptByAES(json, aesKey);
        //根据自己的RSA私钥生成的签名
        System.out.println("使用随机AES密钥"+aesKey+"加密后的数据："+data);
        //当前时间戳
        Long timesTamp = Instant.now().getEpochSecond();
        Map<String, String> result = Maps.newHashMapWithExpectedSize(4);
        result.put("data", data);
        result.put("randomKey", randomKey);
        result.put("timesTamp", timesTamp.toString());
        String linkStr = EncryptUtil.createLinkString(result);
        System.out.println("A生成待签名的串："+linkStr);
        String sign = EncryptUtil.signByPrivateKey(linkStr, PRIVATE_KEY_A);
        System.out.println("A生成的签名："+sign);
        result.put("sign", sign);
        System.out.println("A加密签名后的报文:" + JSON.toJSONString(result));

    }

    public static void decryptDemo() throws Exception {
        //解密数据过程
        //需要解密的demo数据
        String json = "{\"randomKey\":\"w+glUqi5+RLkeWGNXP9SlVN7LmIa8Vi/EIKz/DUNHS6sfo6uDVssaCNDysOFpA0XhKRz28tIZ4p1eWmM3pOXDD9qXCi4PscRRlrPPA9njgigpvdm1sFLtEObr5CDBsKn/+XIsSZJ/8u9ZP33q6bw8yGRsfekD0FHSw4SsVEGs+Y=\",\"sign\":\"poxqmGGrnOtzzzy+HLmw6tAOHUWxi81b0JBJtmYk2ubFRGPRDiNSu3cRShRqOBEiRc0BukjvEZib4yyjNSk4w/4HFa1md9NmkY6B47ETnRvEShhjftoTPKBwwnQMrGRs6HYSPCbbFcG1QXqiQlIR7XvvI4shKNeKBljBA5Uk+08=\",\"data\":\"ag5nOGqwFa75tYBiwHB8hA==\",\"timesTamp\":\"1609753530\"}";
        Map map = (Map) JSON.parse(json);
        String sign = (String) map.get("sign");
        String data = (String) map.get("data");
        String randomKey = (String) map.get("randomKey");
        String timesTamp = String.valueOf(map.get("timesTamp"));
        //验证签名
        Map<String, String> signMap = new HashMap<>();
        signMap.put("timesTamp", timesTamp);
        signMap.put("data", data);
        signMap.put("randomKey", randomKey);
        Boolean verifyResult = EncryptUtil.verifySignByPublicKey(EncryptUtil.createLinkString(signMap), PUBLIC_KEY_A, sign);
        if (verifyResult) {
            //解密加密的随机Aes密钥
            String aesKey = EncryptUtil.decryptByRSA(randomKey, PRIVATE_KEY_B);
            //用解密后的aes密钥解密数据
            String decryptBizData = EncryptUtil.decryptByAES(data, aesKey);
            System.out.println("解密报文:" + decryptBizData);
        }
    }

    public static void main(String[] args) throws Exception {
        System.out.println("A私钥："+PRIVATE_KEY_A.replaceAll("\\s", ""));
        System.out.println("A公钥："+PUBLIC_KEY_A.replaceAll("\\s", ""));
        System.out.println("B私钥："+PRIVATE_KEY_B.replaceAll("\\s", ""));
        System.out.println("B公钥："+PUBLIC_KEY_B.replaceAll("\\s", ""));
        encryptDemo();
        decryptDemo();
    }
```
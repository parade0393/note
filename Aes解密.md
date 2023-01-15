### 什么是AES

AES加密算法是密码学中的高级加密标准（Advanced Encryption Standard，AES），是一种区块加密标准。

### AES加密方式简介

1. AES使用128、192 和 256 位密钥，并且用 128 位（16字节）分组加密和解密数据

2. AES的加密方式会将明文拆分成不同的块进行加密，例如一个256 位的数据用128的密钥加密，则分成

| 明文1(128)位 | 明文2(128)位 |
| -----------: | :----------- |
| 加         |          密    |
| 密文1(128)位 | 密文2(128)位 |

3. 模式有CBC(有向量模式)和ECB(无向量模式)，向量模式可以简单理解为偏移量，使用CBC模式需要定义一个IvParameterSpec对象(偏移量)--**向量必须是一个与密钥长度相等的数据**
4. 填充模式

### java使用Cipher步骤

1. 新建Cipher对象时需要传入一个参数，参数格式`algorithm/mode/padding，其中algorithm为必输项`，algorithm有`algorithm，缺省的mode为ECB，缺省的padding为PKCS5Padding--使用CBC模式时必须传入padding

2. 使用之前还需要初始化，，共三个参数(“加密模式或者解密模式”,“密匙”,“向量”)

3. 调用数据转换：cipher.doFinal(content)，其中content是一个byte数组

4. 案例

   ```java
   //解密
   private static void decrypt() {
       String qwe = "4001234512par1q2";
       String asd = "6u7i8q1w2e3r4t5y";
       String str = "eryuiouopoiportertrytrytu";
       try {
           // 创建cipher实例 参数按"算法/模式/填充模式" "AES/ECB/PKCS5Padding"
           Cipher cipher = Cipher.getInstance("AES/CBC/NoPadding");
           SecretKeySpec keySpec = new SecretKeySpec(qwe.getBytes(),"AES");
           IvParameterSpec iv = new IvParameterSpec(asd.getBytes());
           //初始化cipher，需要三个参数
           //1)Cipher.ENCRYPT_MODE(加密模式)和 Cipher.DECRYPT_MODE(解密模式)
           //2)密钥：使用传入的盐构造一个密钥，可以使用SecretKeySpec、KeyGenerator和KeyPairGenerator创建密匙，其中secretKeySpec和KeyGenerator支持AES，DES，DESede三种加密算法创建密匙,KeyPairGenerator支持RSA加密算法创建密匙
           //3)使用CBC模式时必须传入该参数，该项目使用IvParameterSpec创建iv对象
           cipher.init(Cipher.DECRYPT_MODE,keySpec,iv);
           byte[] decode = Base64.getDecoder().decode(str);//先用Base64解密
           byte[] bytes = cipher.doFinal(decode);
           String s = new String(bytes, StandardCharsets.UTF_8);
           System.out.println(s);
       } catch (NoSuchAlgorithmException | NoSuchPaddingException | InvalidAlgorithmParameterException | InvalidKeyException | BadPaddingException | IllegalBlockSizeException e) {
           e.printStackTrace();
       }
   }
   ```

### Js中使用Crypto

1. 准备好密钥和偏移量

2. 知道模式模式和填充模式，**至关重要**，关系到能不能解密成功和解密出来的数据是否正确

3. 案例

   ```javascript
   <script>
       //首先加密肯定是要有密钥的，自然解密也需要密钥
       let qwe = "4001234512par1q2";
       let asd = "6u7i8q1w2e3r4t5y";
   
       let str = "lnUsqFPjscXlHxe2";
       //转成字节数组 十六位十六进制数作为密钥
       let key = CryptoJS.enc.Utf8.parse(qwe)
       //十六位十六进制数作为密钥偏移量
       let iv = CryptoJS.enc.Utf8.parse(asd)
       //我的项目中这里需要解密的字符串不需先Base64解码了，而java需要先解码，这里也摸索了好久
       let decrypt = CryptoJS.AES.decrypt(str,key,{
           iv:iv,
           mode:CryptoJS.mode.CBC,
           padding:CryptoJS.pad.ZeroPadding//后两个参数一定要正确
       });
       let decryptedStr = decrypt.toString(CryptoJS.enc.Utf8);
       console.log(JSON.parse(decryptedStr))
   </script>
   ```

### Postman中使用Crypto.js解密接口数据

Postman已经集成了Crypto，所以可以直接使用。再Tests里对响应写js脚本即可

![](C:\Users\flb\Desktop\image\QQ截图20220713235548.jpg)

##### 参考文章

1. [Java使用Cipher类实现加密的过程详解](https://blog.csdn.net/b1303110335/article/details/109717838)
2. [JAVA AES加密与解密](https://blog.csdn.net/u011781521/article/details/77932321)
3. [前端js使用crypto-js进行aes解密，解密内容为空](https://segmentfault.com/q/1010000039851492)
4. [postman脚本执行顺序](https://blog.csdn.net/qmhball/article/details/104425564)
5. [Postman JavaScript reference](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/)


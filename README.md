# AES-Encryption-Decryption

we can use it in Java to encrypt and decrypt a file. In Password based encryption (PBE), a password is chosen and it is used along with a generated salt (key) to encrypt. Then the same password is used along with the salt again to decrypt the file.

We have following 3 steps to achieve password encryption and decryption

* Generate Random Key
* Generate Encrypted Password from plain text password
* Retrieve plain text password from Encrypted password


## Key Generation
Generating random key which is being used to encrypt and decrypt password

```javascript
package com.narayanatutorial.password;

import java.security.NoSuchAlgorithmException;
import javax.crypto.KeyGenerator;
import javax.crypto.SecretKey;

public class GenerateKey {
    public static final String AES = "AES";
    private static String byteArrayToHexString(byte[] b) {
        StringBuffer sb = new StringBuffer(b.length * 2);
        for (int i = 0; i < b.length; i++) {
            int v = b[i] & 0xff;
            if (v < 16) {
                sb.append('0');
            }
            sb.append(Integer.toHexString(v));
        }
        return sb.toString().toUpperCase();
    }

    private static byte[] hexStringToByteArray(String s) {
        byte[] b = new byte[s.length() / 2];
        for (int i = 0; i < b.length; i++) {
            int index = i * 2;
            int v = Integer.parseInt(s.substring(index, index + 2), 16);
            b[i] = (byte) v;
        }
        return b;
    }

    public static void main(String args[]) throws NoSuchAlgorithmException {
        KeyGenerator keyGen = KeyGenerator.getInstance(GenerateKey.AES);
        keyGen.init(128);
        SecretKey sk = keyGen.generateKey();
        String key = byteArrayToHexString(sk.getEncoded());
        System.out.println("key:" + key);
    }
}
```

### Output
```javascript
key:F21E2A7FB6C68037FAEAA55222E320F7
```
## Generation Encrypted Password
Generate Encrypted Password from plain text password , we will generate encrypted password using above generated key.

```javascript
package com.narayanatutorial.password;

import java.io.IOException;
import java.io.StreamTokenizer;
import java.security.InvalidAlgorithmParameterException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.Scanner;
import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.spec.SecretKeySpec;

public class GenerateEncryptionPassword {

    public static final String AES = "AES";

    private static String byteArrayToHexString(byte[] b) {
        StringBuffer sb = new StringBuffer(b.length * 2);
        for (int i = 0; i < b.length; i++) {
            int v = b[i] & 0xff;
            if (v < 16) {
                sb.append('0');
            }
            sb.append(Integer.toHexString(v));
        }
        return sb.toString().toUpperCase();
    }

    private static byte[] hexStringToByteArray(String s) {
        byte[] b = new byte[s.length() / 2];
        for (int i = 0; i < b.length; i++) {
            int index = i * 2;
            int v = Integer.parseInt(s.substring(index, index + 2), 16);
            b[i] = (byte) v;
        }
        return b;
    }

    public static void main(String args[]) throws NoSuchAlgorithmException, NoSuchPaddingException, InvalidKeyException, InvalidAlgorithmParameterException, IllegalBlockSizeException, BadPaddingException, IOException {
        //String key = "DB99A2A8EB6904F492E9DF0595ED683C";
        //String password = "Admin";

        Scanner scanner = new Scanner(System.in);
        System.out.println("Please Enter Key:");
        String key = scanner.next();
        System.out.println("Please Enter Plain Text Password:");
        String password = scanner.next();

        byte[] bytekey = hexStringToByteArray(key);
        SecretKeySpec sks = new SecretKeySpec(bytekey, GenerateEncryptionPassword.AES);
        Cipher cipher = Cipher.getInstance(GenerateEncryptionPassword.AES);
        cipher.init(Cipher.ENCRYPT_MODE, sks, cipher.getParameters());
        byte[] encrypted = cipher.doFinal(password.getBytes());
        String encryptedpwd = byteArrayToHexString(encrypted);
        System.out.println("****************  Encrypted Password  ****************");
        System.out.println(encryptedpwd);
        System.out.println("****************  Encrypted Password  ****************");

    }
}
```
#### Output
```javascript
Please Enter Key:
F21E2A7FB6C68037FAEAA55222E320F7
Please Enter Plain Text Password:
Administrator
****************  Encrypted Password  ****************
3A602CF908B97B6C815A3DD7B5B537FD
****************  Encrypted Password  ****************
```


## Generate Decrypted password
Retrieve plain text password from Encrypted Password
We can retrieve the original password from the encrypted password and using key as follows

```javascript
package com.company;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.security.InvalidAlgorithmParameterException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.Properties;
import java.util.Scanner;
import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.spec.SecretKeySpec;

public class GenerateDecryptedPassword {
    public static final String AES = "AES";

    private static String byteArrayToHexString(byte[] b) {
        StringBuffer sb = new StringBuffer(b.length * 2);
        for (int i = 0; i < b.length; i++) {
            int v = b[i] & 0xff;
            if (v < 16) {
                sb.append('0');
            }
            sb.append(Integer.toHexString(v));
        }
        return sb.toString().toUpperCase();
    }

    private static byte[] hexStringToByteArray(String s) {
        byte[] b = new byte[s.length() / 2];
        for (int i = 0; i < b.length; i++) {
            int index = i * 2;
            int v = Integer.parseInt(s.substring(index, index + 2), 16);
            b[i] = (byte) v;
        }
        return b;
    }

    public static String generateDecryptedPassword(String password) throws NoSuchAlgorithmException, NoSuchPaddingException, InvalidKeyException, IllegalBlockSizeException, BadPaddingException, FileNotFoundException, IOException, InvalidAlgorithmParameterException {

        String key = "";

//        Properties prop = new Properties();
//        InputStream input = null;
//        input = new FileInputStream("c:/keypassword.properties");
//        // load a properties file
//        prop.load(input);
//        tempkey = prop.getProperty("Key");
//        password = prop.getProperty("Encrypted_Password");

        key = GenerateKey.generateKey();
        password = GenerateEncryptionPassword.generateEncryptedPassword(password,key);
        byte[] bytekey = hexStringToByteArray(key);
        SecretKeySpec sks = new SecretKeySpec(bytekey, GenerateDecryptedPassword.AES);
        Cipher cipher = Cipher.getInstance(GenerateDecryptedPassword.AES);
        cipher.init(Cipher.DECRYPT_MODE, sks);
        byte[] decrypted = cipher.doFinal(hexStringToByteArray(password));
        String OriginalPassword = new String(decrypted);
        System.out.println();
        System.out.println("****************  Original Password  ****************");
        System.out.println(OriginalPassword);
        System.out.println("****************  Original Password  ****************");
        return OriginalPassword;
    }
}

```

### Output
```javascript
****************  Original Password  ****************
Administrator
****************  Original Password  ****************
```

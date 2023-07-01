# LearnCAandEncrypt
Learn about CA and Encryption

###### NOTE: This tutorial is for demonstration purpose only. DO NOT USE THIS IN PRODCUTION.

#### Create your own CA(Certificate Authority) 
- Create the private key
    ```
    openssl genrsa -des3 -out CAPrivate.key 2048
    ```
- Create root CA certificate (and self-sign it)
    ```
    openssl req -x509 -new -nodes -key CAPrivate.key -sha256 -days 365 -out CAPrivate.pem
    ```
- Generate private key and CSR for Server
    ```
    openssl genrsa -out MyPrivate.key 2048
    openssl req -new -key MyPrivate.key -out MyRequest.csr
    ```
- Generate the Certificate using the CSR
    ```
    openssl x509 -req -in MyRequest.csr -CA CAPrivate.pem -CAkey CAPrivate.key -CAcreateserial -out X509Certificate.crt -days 365 -sha256
    ```
#### Testing the generated certificate or an example of verify a signing process
- First let’s sign some text file using the user’s private key
    ```
    echo "test" > sign.txt
    openssl dgst -sha256 -sign MyPrivate.key -out signature.txt sign.txt
    ```
- We can now verify this signature by using user’s certificate as follows
    ```
    openssl dgst -sha256 -verify <(openssl x509 -in X509Certificate.crt -pubkey -noout) -signature signature.txt sign.txt
    output> Verified OK
    ```
- Let’s change the content of the sign.txt file and try to verify this again.
    ```
    echo "testdata" > sign.txt
    openssl dgst -sha256 -verify <(openssl x509 -in X509Certificate.crt -pubkey -noout) -signature signature.txt sign.txt
    Verification Failure
    ```
#### Encryption and Decryption with public and private certificates
- Encryption
    ```
    echo -n 'string to encrypt' | openssl rsautl -encrypt -certin -inkey X509Certificate.crt -out enc.txt
    ```
- Decryption
    ```
    openssl rsautl -decrypt -inkey MyPrivate.key -in enc.txt
    output> string to encrypt
    ```
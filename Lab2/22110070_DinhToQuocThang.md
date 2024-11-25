# Lab #1,22110070, Dinh To Quoc Thang, INSE331280E_01FIE
# Task 1: Transfer files between computers  
**Question 1**: 
Conduct transfering a single plaintext file between 2 computers, 
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side. 

**Answer 1**:
## 1: Create 2 virtual machines Alice and Bob
![image](https://github.com/user-attachments/assets/15072eeb-94d0-4c3e-a65f-22e658fe7eca)

## 2: Create a data.txt file in the Alice virtual machine to transfer the content to Bob
-Below is the content of the data.txt file:
![image](https://github.com/user-attachments/assets/08879d6e-4560-4c17-a0df-d0fad33ca096)

## 3: Generate a secret key and HMAC that Alice and Bob both keep to authenticate and ensure the integrity of files sent and received.
-This command will generate a 32 byte (256 bit) secret key and save it to the secret.key file. You can change the key length if needed.
```sh
openssl rand -hex 32 > secret.key
```
-This is the key:
![image](https://github.com/user-attachments/assets/b6e00957-2ecd-4b9c-a1a9-ab4fe0e7c2f4)

-Generate HMAC using SHA256 hash algorithm with secret key and data.txt file
-This command will generate a 32 byte (256 bit) secret key and save it to the secret.key file. You can change the key length if needed.
```sh
openssl dgst -sha256 -hmac $(cat secret.key) -out data.hmac data.txt
```
-The generated HMAC code will be saved to the data.hmac file.
![image](https://github.com/user-attachments/assets/48f94b86-557d-41f0-8bfa-6b6659a021a9)

## 4: Bob receives the file data.txt and data.hmac from Alice and uses the SHA256 hash function to recalculate the HMAC to ensure the integrity and authenticity of the file.
-Bob receive file data.txt and data.hmac:
![image](https://github.com/user-attachments/assets/b9547f9f-3556-4d51-b89f-883cd393aefe)
![image](https://github.com/user-attachments/assets/aaa3d5c5-6e75-42cb-bc68-ed7dbf3d2adc)

-Bob count HMAC again with his secret key and data.txt:
```sh
openssl dgst -sha256 -hmac $(cat secret.key) -out data.hmac.verify data.txt
```
-The HMAC code after Bob calculates is stored in the file data.hmac.verify
![image](https://github.com/user-attachments/assets/c755a3d4-1f18-4595-8eb8-e253b67b3c9c)

Then Bob compares the HMAC code in the data.hmac.verify file that he calculated with the HMAC code in the data.hmac file that Alice sent.
![image](https://github.com/user-attachments/assets/a85b0e3b-e337-4bd0-8f1b-f536596b8240)

=>If there is no difference, it means the file has not been changed and you will see no result returned. Therefore the HMAC that Bob calculated is the same as the HMAC that Alice

## 5: Integrity check
-From Alice, generate a checksum for the file using the SHA256 hash function
```sh
sha256sum data.txt > checksum.txt
```
-This is the content of the checksum.txt file after using the SHA256 function to hash the data.txt file.
![image](https://github.com/user-attachments/assets/98e0ec28-62ac-41dd-8daa-97da430223fe)

-Bob receives the checksum.txt file from alice
```sh
docker cp alice-10.9.0.5:/home/lab/checksum.txt bob-10.9.0.6:/home/lab/
```
-After Bob receives the checksum.txt file from Alice, Bob uses SHA256 to recalculate the hash and see if it matches the checksum.txt.
![image](https://github.com/user-attachments/assets/296af0ef-bcf8-4a93-8e43-4b0fc5abac78)

-Displaying an "OK" message proves that the message has not been changed and ensures the integrity of the message.


Conclusion: Alice performs the process of generating the HMAC, sending the file and the HMAC, and then verifying the HMAC on Bob, two important aspects of security are demonstrated:

-Integrity through comparing the HMAC between Alice and Bob to ensure that the file contents have not been altered in transit

-Authentication through using Alice and Bob's shared secret key to generate and verify the HMAC

# Task 2: Transfering encrypted file and decrypt it with hybrid encryption. 
**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:
## 1: Generate RSA key pair on Alice
-This command is used to generate a 2048-bit RSA private key.
```sh
openssl genrsa -out sender_private.pem 2048
```
-This command uses the generated private key to export the public key and send it to Bob.
```sh
openssl rsa -in sender_private.pem -pubout -out sender_public.pem
```
## 2: Sign the data.txt file
-Sign the data.txt file and save it to the data.sig file to send to Bob:
```sh
openssl dgst -sha256 -sign sender_private.pem -out data.sig data.txt
```
-This is file data.sig:
![image](https://github.com/user-attachments/assets/7c227a68-d34d-4afa-976f-872f2e1f4dfb)

## 3:Bob receives the data.txt, data.sig files and the public key sender_public.pem to authenticate the information.

![image](https://github.com/user-attachments/assets/217ed7d6-0f6e-4f84-a9bd-07e69ec9fa54)

## 4: After receiving the file Bob verifies the signature:
-This is the command Bob uses to verify the signature by use data.txt, data.sig files and the public key sender_public.pem :
```sh
openssl dgst -sha256 -verify sender_public.pem -signature data.sig data.txt
```
-If the result is Verified OK, it means that the code that Bob calculated from the public key and the data.txt file using the SHA256 hash function matches the code in the received data.sig file, proving the authenticity of the received data.txt file. If there is an error, the file may have been modified or the signature is invalid:
![image](https://github.com/user-attachments/assets/bcf076c9-baa7-4844-b7b0-3d1d9eebae5a)

=>As we have seen the result is Verified OK so the file is authentic and integrity  that Alice send to Bob, Bob receive from Alice and the file hasn't been changed.




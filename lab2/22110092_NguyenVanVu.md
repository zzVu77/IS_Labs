# Task 1: Firewall configuration 
**Question 1**: 
Setup a set of vms/containers in a network configuration of 2 subnets (1,2) with a router forwarding traffic between them. Relevant services are also required:
- The router is initially can not route traffic between subnets
- PC0 on subnet 1 serves as a web server on subnet 1
- PC1,PC2 on subnet 2 acts as client workstations on subnet 2
  
**Answer 1**:

# Step 1: Prepare virtual environment
Command: ```docker pull ubuntu:latest  ``` 

Create container PC0: ```docker run -it --name PC0 --hostname PC0 --privileged ubuntu```

Create container PC1: ```docker run -it --name PC1 --hostname PC1 --privileged ubuntu```

Create container PC2: ```docker run -it --name PC2 --hostname PC2 --privileged ubuntu```

# Step 2: Tạo mạng cho subnet 1 và subnet 2

Tạo subnet 1:  docker network create --driver bridge --subnet=192.168.1.0/24 subnet1

Tạo subnet 2: docker network create --driver bridge --subnet=192.168.2.0/24 subnet2

# Step 3: Tạo container router và connect nó với subnet 1 và subnet 2:
Tạo container "router": docker run -it --name router --hostname router --privileged ubuntu
Connect với subnet 1: docker network connect subnet1 router --ip 192.168.1.1
Connect với subnet 2: docker network connect subnet2 router --ip 192.168.2.1
Gõ lệnh docker inspect router để kiểm tra : 
Khu vực khoanh đỏ là địa chỉ của router
![image](https://github.com/user-attachments/assets/ce882acd-8bf6-4bea-95c5-843f8e15e183)

Khu vực khoanh vàng là thông tin của 2 subnet 1 và 2 

![image](https://github.com/user-attachments/assets/2ad56302-9f98-4225-8eb8-6f10818e2bec)

=> Kết luận: Connect thành công

# Cài các tools cần thiết cho các container:
  ## PC0
  apt update
  apt install apache2 -y

**Question 2**:
- Enable packet forwarding on the router.
- Deface the webserver's home page with ssh connection on PC1
  
**Answer 2**:

**Question 3**:
  Config the router to block ssh to web server from PC1, leaving ssh/web access normally for all other hosts from subnet 1.   

**Answer 3**:

**Question 4**:
- PC1 now servers as a UDP server, make sure that it can reply UDP ping from other hosts on both subnets.
- Config personal firewall on PC1 to block UDP accesses from PC2 while leaving UDP access from the server intact.
**Answer 4**:


# Task 2: Encrypting large message 
Use PC0 and PC2 for this lab 
Create a text file at least 56 bytes on PC2 this file will be sent encrypted to PC0

**Question 1**:
Encrypt the file with aes-cipher in CTR and OFB modes. How do you evaluate both cipher in terms of error propagation and adjacent plaintext blocks are concerned. 

**Answer 1**:
- Demonstrate your ability to send file to PC0 to with message authentication measure.
- Verify the received file for each cipher modes

# Solution: 
 ## Step 1: Install needed packages for both container PC0 and PC2
```
apt update
apt install net-tools
apt install openssl
apt install netcat-traditional
```
apt install net-tools: In order to use ifconfig to check IP adress.

apt install openssl: Use openssl to achieve secure goal.

apt install netcat-traditional: Use netcat to send - receive files.

## Step 2: Create a text file at least 56 bytes on PC2
```echo "This is a test file for encryption. It contains more than 56 bytes." > plaintext.txt ```
![image](https://github.com/user-attachments/assets/b1566ff5-b4c0-4466-9ba0-23ecfb596c06)

Check the size of file

```
wc -c plaintext.txt
```

![image](https://github.com/user-attachments/assets/8c36619b-195e-473d-961a-361ad4f0f5b6)

The size of file is greater than 56 bytes

## Step 3: Mã hóa tệp bằng AES-256
Tạo khóa (256-bit = 32 bytes)
openssl rand -hex 32 > key.bin
Tạo một IV (Initialization Vector) 16 bytes:
openssl rand -hex 16 > iv.bin

Mã hóa tệp trong chế độ CTR

openssl enc -aes-256-ctr -in plaintext.txt -out encrypted_ctr.bin -K $(cat key.bin) -iv $(cat iv.bin)

Mã hóa tệp trong chế độ OFB

openssl enc -aes-256-ofb -in plaintext.txt -out encrypted_ofb.bin -K $(cat key.bin) -iv $(cat iv.bin)


## Step 4: Thêm Message Authentication Code (MAC)
 Tạo MAC cho file mã hóa bằng CTR
 
 openssl dgst -sha256 -mac HMAC -macopt key:$(cat key.bin) -out mac_ctr.txt encrypted_ctr.bin

 Tạo MAC cho file mã hóa bằng OFB
 
 openssl dgst -sha256 -mac HMAC -macopt key:$(cat key.bin) -out mac_ofb.txt encrypted_ofb.bin
 
## Step 5: Gửi file cho cho PC0
Xác định địa chỉ của PC0 và PC2
Ở container PC0, gõ ifconfig 

![image](https://github.com/user-attachments/assets/f90c44bd-4441-4182-b0f4-4ac6674f14c7)

Tương tự,  với PC2

![image](https://github.com/user-attachments/assets/2741dcc3-6124-4617-b81d-9d3d0dc3078a)

Sử dụng lênh này để nhận file từ PC2
nc -l -p 3333 | tar -xvf -

Bên container PC2 gửi files qua PC0, với địa chỉ là 172.17.0.2

tar -cvf - encrypted_ctr.bin mac_ctr.txt encrypted_ofb.bin mac_ofb.txt | nc 172.17.0.2 3333

Kết quả: PC0 đã nhận được những files cần thiết : 
 
![image](https://github.com/user-attachments/assets/df73742c-eb6f-4460-8b1f-a907534d58ad)



**Question 2**:
- Assume the 6th bit in the ciphered file is corrupted.
- Verify the received files for each cipher mode on PC0

**Answer 2**:

**Question 3**:
- Decrypt corrupted files on PC0.
- Comment on both ciphers in terms of error propagation and adjacent plaintext blocks criteria. 






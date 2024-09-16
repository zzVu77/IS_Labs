# Step 1: Set up
1. Build the image "img4lab" that comprises all required packages for labs
   **_docker build -t img4lab ._**
2. Create home folder
**_docker run -it --privileged -v $HOME/Seclabs:/home/seed/seclabs img4lab_**
# Step 2: Attack
1. Compile program with options to defeat stack protecting mechanism and code execution on stack <br>
   **gcc bof2.c -o bof2.out -fno-stack-protector -z execstack -mpreferred-stack-boundary=2**
2. Stack frame <br>
![image](https://github.com/user-attachments/assets/d23a3b8c-70bc-49dc-9592-b5457839efa5)
3. From the lines code in bof3.c we can see that
* Function pointersfunc are initialized to point to functions sup()
* The function fgets(buf, 133, stdin);allows the user to enter up to 132 characters , but the size of the array bufis only 128 bytes . This opens up the possibility of buffer overflow when the user enters too many characters.
* So we will overwrite the function pointer funcin the stack with the address of the function shell()to perform the attack.
4. Determin the address of shell function <br>
![image](https://github.com/user-attachments/assets/894bba0f-020e-4712-9cfe-2235e1233555) <br>
=> Address: **0x0804845b**
5. Perform attack
  ![image](https://github.com/user-attachments/assets/659c0155-52bf-4a3c-99b8-ecc038b70ccd)




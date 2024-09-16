# Step 1: Set up

1. Build the image "img4lab" that comprises all required packages for labs
   **_docker build -t img4lab ._**
2. Create home folder
   **_docker run -it --privileged -v $HOME/Seclabs:/home/seed/seclabs img4lab_**

---

# Step 2: Attack

1. Compile program with options to defeat stack protecting mechanism and code execution on stack:
   **gcc bof1.c -o bof1.out -fno-stack-protector -z execstack -mpreferred-stack-boundary=2**
2. We can see that in the function vuln they use "gets" function which is dangerous because it does not check the length of input. So we can attack here
3. Stack frame

  ![image-1](https://github.com/user-attachments/assets/4fc75067-78d2-4529-9f1d-e668b1cdab91)

5. From the stack frame, we can see that if we want to the program print " Congratulations ", we have to override the address of secretFunc function at return address of vuln function
   - Get the address of secretFunc function:     

    ![image-2](https://github.com/user-attachments/assets/31817dd5-bd65-43f8-b466-8820b33d64d8)


 
    ![image-3](https://github.com/user-attachments/assets/a8bbdb88-c2cd-4138-9e29-518f8e5cfd2d)

     So, the address of secretFunc is **0x0804846b**
   - _Estimate size nnn from top of stack to eip when compiling program_
     First, type **disas main** in gdb and set break point at **0x0808484d0**
     ![image-4](https://github.com/user-attachments/assets/fed3ef6a-5587-4db9-9421-8e88a06e21b3)

     Second, run the program and type 'a'\*204 + 'bbbb'
    ![image-5](https://github.com/user-attachments/assets/4271192a-0980-41ed-91ff-b6ffd54a6338)
![image-6](https://github.com/user-attachments/assets/fe84c9af-ec3a-4343-814f-dbbc3b7aed60)

     We can see that, the value of EIP register = 'bbbb', so we will alter 'bbbb' into the address of secretFunc     
![image-9](https://github.com/user-attachments/assets/d14c1566-b3f3-4d83-8ba1-3b947ab866ac)

# Step 1: Set up
1. Build the image "img4lab" that comprises all required packages for labs
   **_docker build -t img4lab ._**
2. Create home folder
**_docker run -it --privileged -v $HOME/Seclabs:/home/seed/seclabs img4lab_**
# Step 2: Attack
1. Compile program with options to defeat stack protecting mechanism and code execution on stack <br>
   **gcc bof2.c -o bof2.out -fno-stack-protector -z execstack -mpreferred-stack-boundary=2**
2. We can see that the max length of buf is 40 and fgets(buf,45,stdin) allow input 45 characters, so we will override the last 4 byte by the address 0xdeadbeef <br>
![image](https://github.com/user-attachments/assets/5e8cfb74-9a4f-4931-8413-e6173d24e298)

    

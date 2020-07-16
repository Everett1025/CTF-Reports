# pwnable.kr

## Toddlers Bottle (fd)

### ssh: 'fd@pwnable.kr -p 2222' (password: guest)

### Problem: Find a Flag with in the given directory

### Walkthrough:

1. First I ran ls just to view what we had inside our home directory

- I discovered three files "fd" "fd.c" and "flag".
- I attempted to just cat flag to see if a password would print, but I did not have permissions to do so
- I attempted to simply change my permissions using chmod, but that also did not work

2. Next I decided to view the source code using vim since I had the permissions to do so.

- The first thing I noticed is when executing the program we needed to provide at least one argument for the program not to crash
- Second thing I noticed is that the program using our first argument, converting it to an integer using atoi, subtracting the hex number 0x1234 from it, and storing into a variable called fd which is short for "file descriptor"

3. I then noticed the line "len = read(fd, buf, 32)" as well as "if(!strcmp("LETMEWIN\n"), buf)"

- If we successfully provided the right input into the buffer the linux system call system("/bin/cat flag") would execute
- Then the password for the current level would be printed

4. The file descriptor we needed is standard input wich is represented as the number 0.
5. We know from earlier that our argument that eventually becomes our fd variable is having the hex value 0x1234 subtracted from it.
6. Therefore we need to determine what the value of 0x1234 actually is. It is the decimal value 4660
7. Since we now the 4660 - 0x1234 = 0. We know we need to use that as our first argument so we can submit the user input "LETMEWIN\n"

### Final Solution

1. ./fd 4660
2. "LetMEWIN\n"

### Password: mommy! I think I know what a file descriptor is!!

### Takeaways:

- Learning what file discriptors are and how they work.

### Questions:

- Why does linux syscall within the c code "system("/bin/cat flag");" allow for the cat flag command to work, but when I attempted to use it in the terminal my permissions were denied?

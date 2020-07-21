# pwnable.kr

## Toddlers Bottle (collision)

### ssh: 'col@pwnable.kr -p 2222' (password: guest)

### Problem: Deliver input that allows the program to read the flag

### Code and my comments
``` c
#include <stdio.h>
#include <string.h>
unsigned long hashcode = 0x21DD09EC; // hash code in hex (568134124 in decimal)
unsigned long check_password(const char* p){
    
	int* ip = (int*)p; // create an integer pointer and convert the 20 char bytes to 5 ints
                       // AAAA + AAAA + AAAA + AAAA + AAAA = int1 + int2 + int 3 + int4 + int 5
	// Loop through the 4 integers and add them
    int i;
	int res=0;
	for(i=0; i<5; i++){
		res += ip[i];
	}
	return res;
}

int main(int argc, char* argv[]){
    // Provide the required arguments
	if(argc<2){
		printf("usage : %s [passcode]\n", argv[0]);
		return 0;
	}
    // Input must be 20 bytes in length
	if(strlen(argv[1]) != 20){
		printf("passcode length should be 20 bytes\n");
		return 0;
	}
    // If our input is correct cat the flag
	if(hashcode == check_password( argv[1] )){
		system("/bin/cat flag");
		return 0;
	}
	else
		printf("wrong passcode.\n");
	return 0;
}
```

### Walkthrough:
After reading through the code here is my walkthrough

1. We notice 20 bytes must be entered for the program to run properly
2. 20 bytes is equivalent to 20 ascii characters
3. We enter 20 characters and discover our 20 characters are being converted into C integers
4. An integer in C is equivalent to 4 bytes. One byte is equivalent to two hexadecimal numbers 0xff = 1 byte....0x2121 = 2 bytes etc
5. Therefore we discover we must enter 5 hex integers that add up to our desired hascode
6. The rest is done by simple math and conversions\
    568134124(decimal) =  0x21DD09EC\
    568134124(decimal) \\ 5 = 113626824 Remainder 4\
    113626824 = '0x6c5cec8' in hex\
    0x21DD09EC = '0x6c5cec8' + '0x6c5cec8' + '0x6c5cec8' + '0x6c5cec8' + '0x6c5cecc'
7. Final Solution = ./col `python -c 'print "\xc8\xce\xc5\x06" * 4 + "xcc\xce\xc5\x06"'` 
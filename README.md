# Linux-IPC-Message-Queues
Linux IPC-Message Queues

# AIM:
To write a C program that receives a message from message queue and display them

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux message queues API 

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## C program that receives a message from message queue and display them

## writer.c
```
#include <stdio.h> 
#include <string.h>
#include <sys/ipc.h> 
#include <sys/msg.h> 

// Structure for message queue 
struct mesg_buffer { 
    long mesg_type; 
    char mesg_text[100]; 
}; 

int main() 
{ 
    key_t key; 
    int msgid; 
    struct mesg_buffer message;
    
    // ftok to generate unique key using current directory 
    key = ftok(".", 'A'); 
    if (key == -1) {
        perror("ftok error");
        return 1;
    }
    
    // msgget creates a message queue and returns identifier 
    msgid = msgget(key, 0666 | IPC_CREAT); 
    if (msgid == -1) {
        perror("msgget error");
        return 1;
    }
    
    message.mesg_type = 1; 
    
    printf("Write Data : "); 
    // Replace unsafe gets() with fgets()
    if (fgets(message.mesg_text, sizeof(message.mesg_text), stdin) == NULL) {
        perror("fgets error");
        return 1;
    }
    
    // Remove trailing newline if present
    size_t len = strlen(message.mesg_text);
    if (len > 0 && message.mesg_text[len-1] == '\n') {
        message.mesg_text[len-1] = '\0';
    }
    
    // msgsnd to send message 
    if (msgsnd(msgid, &message, sizeof(message.mesg_text), 0) == -1) {
        perror("msgsnd error");
        return 1;
    }
    
    // display the message 
    printf("Data sent is : %s\n", message.mesg_text); 
    
    return 0; 
}
```
## reader.c
```
// C Program for Message Queue (Reader Process)
#include <stdio.h>
#include <sys/ipc.h>
#include <sys/msg.h>

// structure for message queue
struct mesg_buffer {
	long mesg_type;
	char mesg_text[100];
} message;
int main()
{
	key_t key;
	int msgid;
// ftok to generate unique key
	key = ftok("progfile", 65);
	// msgget creates a message queue
	// and returns identifier
	msgid = msgget(key, 0666 | IPC_CREAT);
	// msgrcv to receive message
	msgrcv(msgid, &message, sizeof(message), 1, 0);
	// display the message
	printf("Data Received is : %s \n",
			message.mesg_text);

	// to destroy the message queue
	msgctl(msgid, IPC_RMID, NULL);
	return 0;
}
```


## OUTPUT
![image](https://github.com/user-attachments/assets/35c83809-59a0-4626-ac5a-fc9b937ed452)



# RESULT:
The programs are executed successfully.

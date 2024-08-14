# 시스템 프로그래밍 실습 2 - 1 과제

### 이름 : 이준휘

### 학번 : 2018202046

### 교수 : 최상호 교수님

### 강의 시간 : 화

### 실습 분반 : 목 7 , 8


## 1. Introduction

```
해당 과제는 Proxy server에서 server와 client 간의 기본적인 연결을 구현하게 된다.
socket을 통해 다중의 client를 수용할 수 있는 서버를 구현한다. client 또한 socket을 열
어 연결을 시도한다. 만약 연결이 성공할 경우 server는 child process를 만들어 다음 작업
을 수행한다. client에서 전송한 URL을 받아 기존 1 - 3 까지 구현했던 작업인 Cache, log 생
성을 하고 client에게 HIT인지 MISS인지를 알린다. 해당 명령은 bye를 입력받을 때까지
반복한다.
```
## 2. Flow Chart

- server.c



- client.c


## 3. Pseudo Code

- server
static void handler(){
    pid_t pid;
    int status;
    Wait Any child with WNOHANG

}


Make_Cache_Dir_Log_File(char* cache_dir, char* log_file){

getHomeDirectory();

cache_dir = ~/cache;

log_file = ~/logfile;

set umask 000;

make cache and log directory;

log_file += /logfile.txt;

}

```
Check_Exist_File(char *path, char *file_name, int is_exist_file){
if(directory isn’t exist)
return 0;
```
```
DIR *dir = Open path directory
While(struct dirent *d = Read path directory){
If(d->name == file name)
Close directory and return 1;
}
Close directory and return 0;
```
}

```
Void Write_Log_File(File *log_file, char *input_url,
char *hashed_url_dir, char* hashed_url_file, int is_exist_file){
time_t now;
struct tm *ltp;
```

```
ltp = current local time;
if(miss state)
Write miss, input_url, local time in log_file;
Else
Write hit, hashed dir/file, local time, input_url in log_file;
}
```
void Sub_Process_Work(int client_fd, struct sock_addr, char *buf, char *char_dir, FILE
*log_file){

```
char[60] hashed_url;
char[4] first_dir;
char *dir_div;
char[100] temp_dir;
int is_exist_file, hit = 0, miss = 0;
```
```
FILE *temp_file;
pid_t current_pid = Current process ID;
time_t start_process_time, end_process_time;
Save start process time;
print Connect success with client address and port;
while(read data from client_fd to buf){
is_exist_file = 1;
hashed_url = hashed input using sha1;
first_dir = { hashed_url[0~3], ‘\0’ };
dir_div = hashed_url + 3 address
temp_dir = ~/cache/first_dir;
```

```
if make temp_dir directory(permission = drwxrwxrwx)
is_exist_file = 0;
is_exist_file = hit or miss state;
Write state, url, dir/file name, time in logfile.txt;
temp_dir = ~/cache/first_dir/dir_div;
temp_file = make temp_dir file and open file;
temp_file close;
write HIT or MISS state to Server_fd;
```
}

check end time;

print terminate state, running time, hit or miss state in logfile.txt

print Terminate connection with client address and port;

return;

}

main(void){

```
Make Cache and log directory and store path’s information;
if open socket is failed, print error and return;
update server’s address information;
if binding socket and server’s address data is failed, print error and return;
```
```
Waiting Connection and Collect SIGCHID signal using handler;
while true{
if connection didn’t occur, print error and return;
if make child process failed, close file descriptor and socket and continue;
```

```
if child process, Do Sub_Process_Work() and exit;
close client file descriptor;
}
```
close socket file descriptor;

}

- client
main(){
    if open socket is failed, return with error;

update server’s address information;

if connection failed, close socket and print error, return;

while(input data to buf){

if buf has bye command, break;

if write to server_fd successed{

if receive data from server_fd successed{

print receive data + \n;

}

}

}

```
close socket file descriptor;
return
}
```
## 4. 결과 화면


해당 함수는 signal함수에서 handling을 위해 만들어진 함수다. 해당 함수에서는
waitpid(WNOHANG)을 통해 child process의 종료를 확인시켜주는 역할을 수행한다.

해당 함수는 기존의 cache directory와 log directory를 생성하고 log_file과
cache_dir의 path를 저장하는 역할을 수행한다.


해당 함수는 기존의 Sub_Process_Work를 일부 변경하여 연결된 포트와 주소까지
출력할 수 있도록 작성되었다.

해당 그림은 server의 메인함수다. 해당 함수에서는 socket을 열고 binding을 통해
server의 정보를 묶는다. 그리고 listen을 통해 연결을 받는다. 연결을 accept할 경
우 child process를 생성하여 Sub_Process_Work 작업을 수행한다.


해당 그림은 client.c에서의 메인함수다. 해당 함수에서도 동일하게 socket을 열고
준비된 주소로 server address를 update한다. 그 후 connect 함수를 통해 server에
연결한다. 그리고 사용자의 입력이 있는 동안에는 다음 작업을 반복한다. 입력은
buffer에 저장되어 있고 bye 명령을 입력받은 경우 loop를 탈출한다. 만약 데이터
를 server로 보내고 답장을 받을 경우 받은 답장(HIT or MISS)를 출력한다.

해당 파일은 변경된 Makefile이다. server.c와 client.c에 대한 실행파일을 만드는 작
업을 수행한다.


해당 그림은 기존의 cache와 logfile이 없는 상태에서 시작한다. make 명령을 통해
실행파일이 정상 생성되는 것을 볼 수 있다.

server를 열고 2 개의 client의 접속이 성공하였다.


2 번의 입력을 받았을 때 MISS state를 정확히 출력하였다.

다른 client에서 2 번의 입력을 하였을 때 다른 client에서 중복된 입력이 HIT 상태
로 정상 출력되고 새로운 입력은 MISS 상태로 정상 출력된다. 그리고 해당 client
를 닫았을 때 server에 종료가 표시된다.


```
마지막으로 아직 열린 client에서 닫은 client에서 입력한 URL을 입력하였을 때 HIT
상태를 출력한다. 해당 client 또한 bye를 입력할 경우 정상적으로 종료되고 server
에서 disconnected된 것이 표시된다.
```
```
해당 결과를 살펴보면 로그에서 각 client의 입력들이 시간에 따라 저장되어있는
것을 볼 수 있다. 또한 해당 client 종료시 해당 시간과 정보 또한 정상 저장되었는
것을 볼 수 있다. 이를 통해 해당 과제를 성공적으로 수행한 것을 알 수 있다.
```
## 5. 고찰

```
해당 과제를 통해서 sever와 client 사이에서 어떻게 message를 주고받는지 알 수 있는
과제였다. socket을 열고 해당 socket을 통해 file descritor를 생성하여 소통하는 과정에서
```

```
쓰이는 함수들을 공부할 수 있는 과제였다. 또한 signal 함수를 통해 child process의
termination을 받을 수 있다는 사실을 알게되었다. 그리고 기존에 과제에서 시간과 관련
하여 실수한 부분을 발견하여 이를 고치면서 더욱 더 집중해서 검증을 수행해야겠다는
생각을 하였다.
```
## 6. Reference

### 강의 자료만을 참고



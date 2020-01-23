# tache 기법들 정리
## tcache dup
* 기존 malloc은 fastbin free시 top chunk를 검사 || tcache malloc은 보호가 하나도 없기에 바로 double free bug를 일으켜 동일한 주소를 계속해서 할당받을 수 있음
* 동일한 주소가 2번 할당하는 것이 가능
~~~c
  int *a=malloc(8);
  free(a);
  free(a);
  b=malloc(8);
  c=malloc(8);
~~~
## tcache poisoning
* tcache chunk의 next를 공격자가 원하는 주소로 바꿔 공격하는 기법(이때 주소는 stack 주소여야 함)
## tcache house of spirit
* tcache의 free는 invalid pointer, invalid size 검사만이 존재하기 때문에 next chunk size를 설정해줄 필요 없음
* fake chunk를 임의의 영역 내에 만든 후 data 영역 주소를 free시키면 그 주소 할당 가능

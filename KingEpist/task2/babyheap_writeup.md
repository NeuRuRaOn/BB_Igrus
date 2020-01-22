# babyheap
막혀서 _https://cr0wn.uk/2019/defcon-babyheap/_ 를 참고하였습니다.
## 몰랐던 개념
* tcache
  * tcache_chunk는 glibc version 2.29부터 지원
  * 같은 thread에 의해 allocated된 memory를 재사용함으로써 속도를 증가시킴
  * tcake_chunk는 prev_size와 inuse_bit를 셋팅하지 않으므로 size와 next만 존재


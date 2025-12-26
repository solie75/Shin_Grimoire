# Synchronouse vs Asynchronouse

### Synchronous

- request 와 result 가 동시에 일어나는 (혹은 순차적으로 연결된) 처리 방식
- 매커니즘 (Blocking)
	- A 함수가 B 함수를 호출하면서 제어권을 B 에게 넘겨준다.
	- B 가 작업을 끝내고 결과 값을 돌려줄 때까지 A 는 '아무것도 못하고 대기(block)' 한다.
	- B 가 끝나야 A 가 다시 제어권을 받아 다음 줄을 실행한다.

### Asynchronous

- request 와 result 가 동시에 일어나지 않는 처리 방식
- 매커니즘 (non-blocking)
	- A 함수가 B 함수에게 작업을 요청하고, 제어권을 바로 다시 돌려받는다. (B 의 작업 완료 여부는 신경쓰지 않는다.)
	- A 는 즉시 다음 작업을 수행한다.
	- B 는 작업이 끝나면 나중에 따로 알림(Callback/Event) 를 준다.
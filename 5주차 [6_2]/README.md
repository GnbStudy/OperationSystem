Interprocess Communication (IPC).pdf 로 공부할 것.<br>

목차<br>
1. 2주차목표
2. 학습내용
3. 학습 방법 안내

[ 2주차목표 ]<br>
Interprocess Communication (IPC)에 대해 이해하고, Sared memory, Message queues, Pipe, Mailbox에 대해 알아본다.<br>

[ 학습내용 preview ]<br>
 스터디원 중 한 명이 message queue에 대해 궁금해하여 이에 대한 내용으로 스터디를 준비하였다.<br>
 우선 message queue에 넘어가기에 앞서 Interprocess Communication (IPC)에 대해 알아야 했다. IPC란 완전히 독립된 두 프로세스가 있을 때 A가 B에게 B가 A에게 데이터를 전달하는 것을 말한다. 근데 여기서 단순히 보내고 받는 것만 생각하기 쉬운데, IPC는 메모리 공유 기법을 통해 send와 read가 이루어짐을 알아야 한다.<br>
 간단한 듯 보이지만 우리가 IPC를 어려워하는 이유는, kernel이 process 영역을 분리시켜서, 영역 외의 영역엔 접근이 불가능하기 때문이다. 단순하게 메모리 공유 방법을 생각해보면 process A의 data영역에 process B의 접근을 허용하는 영역을 만든 다음 그 영역에 A가 data를 보내면 B가 받아가서 쓰는 걸 생각할 수 있지만, OS에서 제한하고 있기 때문에 IPC가 어려운 것. 그래서 이에 대한 해결책도 OS가 제공하면 된다. 이러한 것을 IPC기법이라고 한다.<br>
 IPC 모델은 message passing과 shared memory 이 두 가지 방법이 있다. Message passing은 kernel이 memory protection을 위해 대리 전달해 주는 것을 말한다. 따라서 안전하고 동기화 문제가 없다. 하지만 성능이 떨어지는 단점을 가지고 있다. (message queue처럼 한 번 어딘가를 거쳤다가 전달해야 하는 오버헤드가 있기 때문.)<br>
Shared memory는 두 프로세스간 공유된 메모리를 생성한 후 이용하는 것을 말한다. 성능은 좋지만 동기화 문제가 발생한다. (동기화 문제란 프로세스 A가 쓴걸 프로세스 B가 읽는 상황인데 B가 조금 일찍 읽게 되는 상황이 발생한다면 A가 작성을 다 한걸 읽는 건지 작성 덜한걸 읽는 건지 알 수가 없음. 이런 경우 다 썼는지 안 썼는지 확인하는 것이 동기화.)<br>
 이 다음 message queue에 대해 보면, 웹에서 많은 데이터 처리로 인해 대기 요청이 쌓이게 되면 이러한 것들이 서버의 성능을 저하시키고 최악의 경우 서버를 다운 시킨다. 이러한 이유로 웹 서버를 구성할 때, 성능에 대한 부분도 고려를 해야 함.<br>
 이러한 문제점을 해결하기 위해 다양한 방법들이 있지만 좀 더 저렴하고 쉽게 구현할 수 있는 방법이 바로 메시지 처리 방식이다.<br>
기본적인 원리는 producer가 message를 queue에 넣어두면 consumer가 message를 가져가 처리하는 방식이다. (메시지를 queue 데이터 구조 형태로 관리하는 것.)<br>
 메시지 큐는 kernul에서 전역적으로 관리되며, 모든 프로세스에서 접근가능 하도록 되어있으므로, 하나의 메시지 큐 서버가 커널에 요청해서 메시지 큐를 작성하게 되면, 메시지 큐의 식별자를 아는 모든 프로세서는 동일한 메시지 큐에 접근함으로써 데이터를 공유할 수 있다.<br>
 message queue의 장점은 다른 IPC 공유방식에 비해서 사용방법이 매우 직관적이고 간단하다. 그리고 메시지의 type에 따라 여러 종류의 메시지를 효과적으로 다룰 수 있다.<br>
다음으로 pipe에 대해 다뤘다. 파이프는 그냥 우리가 생각하는 그 파이프와 비슷하게 작동한다고 생각하면 쉽다. 파이프의 특징은 파이프는 두 개의 프로세스를 연결하는데 파이프는 입구와 출구의 개념이 없는 단순한 통로이다. 방향성이 없기 때문에 자신이 쓴 메시지를 자신이 읽을 수도 있기에 두 개의 프로세스가 통신할 때는 읽기전용(read) 파이프와 쓰기전용(write) 파이프 두 개의 파이프를 쓰게 된다. 이때 읽기전용 파이프와 쓰기전용 파이프를 만드는 방법은 두 개의 파이프를 만든 후 한쪽 파이프를 일부러 닫아버리면 된다. 또한, 파이프의 중요한 특징이자 유의할 것은 파이프는 fork()함수에 의해 자식프로세스가 부모프로세스를 그대로 복사할 수 없다는 것이다.  파이프의 경우에는 복사되는 것이 아니라 자식 프로세스와 부모 프로세스가 그냥 같은 파이프를 가리키게 된다. 여기서 파이프는 여러 개의 프로세스가 데이터를 주고받기 위해 사용하는 임시공간이라고 했는데, 이때 이 임시 공간은 실제로 파일 시스템에 생성되는 임시 파일이다. (이 말이 와닿지 않아도, 파이프도 파일임을 유의할 것.) 내용에 대해 다 다룬 후, 예제를 보며 pipe를 완벽하게 이해하였다. <br>
마지막으로 mailbox에 대해 다루며 스터디를 마무리 하였다. 간단하게 원리를 설명하자면 앞에서 말했듯이 프로세스는 자신에게 할당된 메모리 공간 이외에는 접근할 수가 없다. 따라서 mail slot(우체통)을 이용하여 데이터를 송,수신 할 수 있다. 데이터를 수신하고자 하는 process A가 mail slot을 생성한다. 그리고 데이터를 송신하고자 하는 process B가 process A의 mail slot의 주소로 데이터를 송신한다. 그 후 process A가 자신의 mail slot을 통해 데이터를 얻게 된다.<br>


[ 학습자료파일 ]<br>
⊙ 위의 학습내용에 대한 학습자료는 Interprocess Communication (IPC).pdf 파일을 참고하면 된다.<br>
⊙ 위의 pdf 파일 안에 pipe 예제도 포함되어 있으니, 참고해서 해볼 것.<br>
⊙ 위의 pdf 파일을 만드는데 이용한 자료들의 출처도 남기겠다.<br>
참고 영상 : https://www.youtube.com/watch?v=biDbn7FBbLs<br>

https://www.youtube.com/watch?v=c_X1WqGOw_M&t=319s<br>

참고 사이트 : https://jhnyang.tistory.com/24<br>

https://heowc.tistory.com/35<br>

https://m.blog.naver.com/PostView.nhn?blogId=akj61300&logNo=80130589983&proxyReferer=https%3A%2F%2Fwww.google.com%2F<br>

https://www.joinc.co.kr/w/Site/system_programing/Book_LSP/ch08_IPC<br>

http://blog.naver.com/PostView.nhn?blogId=green187&logNo=110130416319<br>

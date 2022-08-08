---
layout:	single
title:	"Stack, Queue, Linked List"
date:	2019-07-24
last_modified_at: 2019-07-24
category: DataStructure
tags: [data,Algorithm]
excerpt: Stack, Quque, LinkedList
---

# 자료구조 정리 1편

## Stack

![](/assets/img/1*6MYEv9Rpy6xRYR0WNY5alA.png)

* LIFO(Last In First Out) 자료구조이다.
* 가장 최근에 일어난 일을 신경쓰고 빨리 처리하는 점에서 인간의 의식과도 닮아있다. 이때 처리되지 못하고 못한 오래된 ‘A’같은 일은 의식 바닥에 깔려있게 된다.
* 그 대신 ‘D’나 ‘C’같은 같은 최근 작업은 반응속도를 보장한다는 장점이 있다. 이런 자료구조를 다루기 위해서는 일을 넣는 push, 일을 빼는 pop 그리고 전체적으로 쌓여있는 양을 측정할 peek 같은 메서드가 필요하다.
* 미로문제를 풀때 가장 최근에 지나온길을 순서대로 기억하고 있는 점을 활용할 수 있다. 인간친화적 아니 물리친화적인 자료구조.

```js
class Stack {
  constructor() {
    this.stack = [];
  }
  push(item) {
    this.stack.push(item)
  }
  pop() {
    return this.stack.pop();
  }
  peek() {
    return this.stack.length + 1;
  }
}
var stack = new Stack();
stack.push(1);
stack.push(2);
stack.pop();  // 2
stack.peek(); // 2
```

---

## Queue

![](/assets/img/1*DokiKLEV62NEg_8QDSXXdg.png)

* FIFO (First In First Out)의 원칙으로 처리하는 자료구조이다. 은행의 대기열과 비슷하다. 대기열이 짧으면 바로 일을 처리하는 기적도 있을 수 있지만 대개는 줄을 서야한다.
* 그런 단점이 있지만 모든 작업이 공평하게 처리된다는 점에서 극단적인 대기시간을 가지는 문제는 없다는 장점을 가진다.
* 이런 Queue를 처리하기 위해선 가장 오래기다린 일, 즉 일들 중 가장 먼저 온 일을 처리하는 Dequeue. 새로 운 일을 대기열에 추가하는 enqueue. 대기열을 측정하는 Qlength가 필요하다.

```js
class Queue {
  constructor() {
    this.queue = [];
  }
  dequeue(item) {
    this.queue.push(item)
  }
  enqueue(item) {
    return this.queue.shift();
  }
  qLength(){
    return this.queue.length;
  }
}

var myQueue = new Queue();
myQueue.dequeue("A"); // A
myQueue.dequeue("B"); // A B
console.log(myQueue.enqueue());  // A;
console.log(myQueue.qLength());  // 1;
```

---

## Linked List

![](/assets/img/1*UatDF7EqPcMULUuriYwZlw.png)

* Linked List는 Array같은 단일구조체와 대비되는 자료구조이다.
* Data, Next Adress로 구성되고 next가 다음 데이터를 가르킨다. 각 정보가 분리되어 있지만 정보를 찾아낼 연락망은 다 갖추어진 구조이다.
* 배열이 순차적인 페이지번호가 쓰여진 질서정연한 인명기록부라면 Linked List는 단일인명정보다. 대신 일련된 페이지 번호대신 다음 인명정보가 어디있는지 기록되어있다.
* 새로운 정보를 기입할 일이 생기면 Array는 전체 페이지번호를 하나하나 변경해야하는 반면 Linked List는 종이 한 장에 있는 Next Adress를 새 Linked List로 고치는 정도로 변경이 끝난다. 그래서 출입력에는 강점을 가진다.
* 대신 탐색에는 시간이 오래걸린다. 원하는 사람을 찾기 위해서 처음부터 하나하나를 순서대로 다 들춰봐야하기 때문이다.

```js
 class LinkedList {
  data;
  nextNode;

  find ( num ){
    return dListAddressfinde
  }

  input (item, 넣고 싶은 인덱스){
    // NewList = new LinkedList
    // PrevList = find(넣고 싶은 인덱스)
    // NewList.nextnode = PrevList.nextnode
    // PrevList.nextnode = NewList
  }

  delete (item){
  }
}
```
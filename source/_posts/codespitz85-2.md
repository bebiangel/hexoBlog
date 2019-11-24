---
title: 코드스피츠85 2회
date: 2019-11-10 16:11:12
tags: [코드스피츠]
categories: [코드스피츠]
---
왜 프론트엔드 개발자를 무시할까?

- 잘 모르기 때문에..
- 프로그래밍 모델, 컴퓨터 내부, 컴퓨터 사이언스 내용에 대한 부족
- 바깥에 표현하는거에 관심
- 중급 이상이 넘어가면 얘기가 안통한다고 생각하게 됨

## Parallelism 병렬성

- 병렬성이 동시성을 이해하는 것보다 쉽다.
- Worker가 동시에 투입되고 일을 동시에 처리할 수 있다.
- 프론트엔드에서는 네트워크에 무언가 요청을 하면 네트워크 서버가 돌고 클라이언트에서 돌고있다.
- Task에 할당되는 worker가 별도로 존재해서 자기만의 속도와 스케줄로 일을 처리한다.
- 각각의 Task가 하나의 메모리를 동시에 같이 사용하면 문제가 생긴다.
- 하나의 Task가 메모리를 사용하고 있으면 나머지 Task를 Blocking을 하는 방법을 사용
- 자바스크립트는 병행성이 막혀있다(es2018 이전까지)
- `함수형은 프로그래밍`은 공유를 하지말고 특정 메모리만 쓰도록 한다
- `객체지향 프로그래밍`은 공유하는 메모리를 인정할 수 밖에 없다 → 객체 식별을 새로 만들어진 값으로 하는게 아니라 포인터 참조로 되기 때문에 각각의 Task들이 하나의 메모리를 참조 할 수 있다

## Concurrency 동시성

- 엄밀히 말하면 동시에 일어나지 않음
- 마치 동시에 일어나는 것처럼 보이는거임
- 메모리를 동시에 쓰는 경우가 없다
- 자바스크립트에서는 병행성이 없고 동시성만 있다
- 동시성과 병행성의 가장 큰 차이는 **Worker가 몇명인지**이다.

[](https://www.notion.so/5766d36067c94c919a5e7c302da5328e#0e478cba8ea4443681490664ec707bd7)

### 실제 세계의 Concurrency

- 한번에 하나만 하긴 하더라도 굉장히 복잡하게 많은 일을 한다

[](https://www.notion.so/5766d36067c94c919a5e7c302da5328e#372ab63062bf4026bcd575760ef3f6ff)

### 자바스크립트 Concurrency

**engine work** 

- 브라우저는 렌더링을 포함한 수많은 엔진들이 하는 일들을 한다. (통신 준비, favicon 요청, console 초기화, 메모리 initialize 등등) → 멀티스레드로 수행한다.
- 자바스크립트 코드가 영향을받는 메모리 블럭을 수행할 때는 하나의 스레드를 사용한다.
- 자바스크립트 메모리에 영향을 주지않는다면 여러개의 스레드를 사용한다
- queue에 걸리는게 없다면 engine work가 할일을 계속 수행한다
- queue에 실행할 일이 생기면 run JS 실행한다
- 실행이 끝나면 다시 engine work 수행한다.

**check queue**

- 자바스크립트 명령을 적재해 둔게 있는지 없는지 검사한다.
- callback queue를 바라본다.

**callback queue**

- 브라우저 로딩에 들어가는 스크립트 callback queue
- 이벤트 리스너 callback queue

**생산자 & 소비자 ⇒ 멀티 스레드 패턴**

- 소비자가 하나의 스레드 메모리에서 읽기만 한다.
- 소비자가 하나만 두는 것을 파이프 패턴이라 한다.
- network, timer, message, dom event는 멀티스레드로 돌리더라도 synchronize 문제가 생기지 않는다.

[](https://www.notion.so/5766d36067c94c919a5e7c302da5328e#7193c6371ea24a8da7a6b3b2e0eb8840)

- callback queue를 중심으로 동시성과 병행성이 나뉜다.
- 병행성에서는 생산만, 동시성에서는 소비만 하는 패턴

## setTimer
```
const Item = class{
    time;
    block;
    constructor(block, time) {
        this.block= block;
        this.time = time + performance.now();
}
const queue = new Set;

const f = time=> {
    queue.forEach(item=>{
        if(item.time > time) return;
        queue.delete(item);
        item.block();
    });
    requestAnimationFrame(f);
}
requestAnimationFrame(f);

const timeout = (block, time) => queue.add(new Item(block, time));
  ```  

- 함수는 어떻게 1초후에 일어나는걸까?
- 밀리세컨마다 1초지났는지 계속 확인하다가 1초지났을때 실행한다
- 푸쉬형을 선호한다 이벤트 기반의 프로그램을 짠다
- 원하는 사건에 내 코드가 호출되었으면 좋겠다
- 클릭할때 리스너가 뜨길 원한다(어떻게 동작하는지는 관여하고 싶ㅍ지 않다)
- 브라우저는 개발자를 바보로 만든다...ㅠㅠ
- 컴퓨터 사이언스로 보면 우리는 부탁만하는거다;
- `performance.now()` 브라우저가 시작되고 몇초가 지났는지(나노초 가능)

value 값

- 불변
- 메모리 자체가 아닌 그 자체로 판단

배열은 여러개일수도 있고 똑같은 값을 넣을수도있다

Set은 중복된 객체를 넣지 않기 위함

객체 지향에서 객체에대한 collection은 배열이 아니라 Set이다

## Non Blocking For

    const working = _ => {};
    for(let i=0; i< 100000; i++) working();
    
    const nbFor = (max, load, block) => {
    	let i = 0;
    	const f = time => {
    		let i = 0;
    		const f = time => {
    			let curr =load;
    			while(curr-- && i < max) {
    				block();
    				i++;
    			}
    			console.log(i);
    			if(i<max-1) requestAnimationFrame(i);
    		};
    		requestAnimationFrame(i);
    	}
    }
    
    nbFor(100, 10, working);
    -> 10씩 끊어서 분산해서 처리한다
    
    const nbFor = (max, load, block) => {
    	let i = 0;
    	const f = time => {
    		let i = 0;
    		const f = time => {
    			let curr =load;
    			while(curr-- && i < max) {
    				block();
    				i++;
    			}
    			console.log(i);
    			if(i<max-1) timeout(f, 0);
    		};
    		timeout(f, 0);
    	}
    }

## Generator

자바스크립트에서 인터페이스는 특정키나 값이 들어있는 형태를 지칭한다 ⇒ 오브젝트의 형태

루프의 주인공은 이터레이터다

이터레이터에게 넥스트 함수를 호출하면 오브젝트를 리턴한다

value, done이라는 키를 가진 오브젝트를 리턴

    const infinity = (function*() {
    	let i= 0;
    	while(true) yield i++;
    })();
    console.log(infinity.next());

generator를 호출하면 iterator 객체가 나온다

generator 자체로는 for..of를 사용 못함

infinity 만들자마 즉시 호출이므로 iterator 객체이다

function* 는 서스펜드 구간을 생성한다

동기명령은 멈출수 없지만 generator는 멈출수 있다

메모리 적재시에 명령마다 레코드라는것으로 감싸서  적재해두었고 하나를 실행할때마다 레코드를 풀어서 실행 → 서스펜드

yield 호출시에 서스펜드(멈춤)

resume(다시 시작)

명령어를 중간에 끊을수 있기 때문에 무한 크기의 배열을 만들수 있다

    const gene = function*(max, load, block) => {
    	let i = 0, curr = load;
    	while(i<max){
    		if(curr--){
    			block();
    			i++;
    		}else{
    			curr=load;
    			console.log(i);
    			yield;
    		}
    }
    
    const nbFor = (max, load, block)=>{
    	const iterator = gene(max, load, block);
    	const f=_=>iterator.next().done||timeout(f);
    	timeout(f,0);
    };

지역변수를 사용한다

제어구조를 외부에서 통제하도록 되어있다

## Promise
```
const gene2 = function*(max, load, block) => {
        let i = 0;
        while(i < max){
                yield new Promise(res => {
                    let curr = load;
                    while(curr-- && i<max){
                        block();
                        i++;
                    }
                console.log(i);
                    timeout(res, 0);
            });
    }
};
const nbFor = (max, load, block)=>{
    const iterator = gene(max, load, block);
    const next = ({value, done})=> done || value.then(v=>next(iiterator.next()));
    next(iterator.next());
};
```
callback 스타일은 제어권을 잃는다 

우리가 원하는 시간에 callback이 오면 좋겠다 

서버 응답은 우리가 원하는데로 오지 않는다

제어권을 통제하기 위해서는 프로미스 객체를 가지고 있다가 내가 원할때 then을 호출 →반제어권

promise와 callback의 차이 → 내가 원할때 then을 호출할 수 있다

promise.then으로 쓰면 callback과 똑같다

generator와 promise를 섞어서 사용하면 외부제어에 일부를 개입할 수 있는 제어 역전을 결정할 수 잇따
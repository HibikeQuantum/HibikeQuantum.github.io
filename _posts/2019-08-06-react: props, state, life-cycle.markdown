---
layout:	single
title:	"React — props, state, life-cycle"
date:	2019-08-06
toc: true
last_modified_at: 2019-08-06
category: react
tags: [javascript, language, Concept]
excerpt: 리액트를 배울때 가장 많이 언급되는 개념 그중 props, state, life-cycle을 정리하는글이다.
---

## 목표
리액트를 배울때 가장 많이 언급되는 개념 그중 props, state, life-cycle을 정리하는글이다.

리액트의 존재이유가 뭘까?
* 동적인 View 구현
* 그럼에도 코드를 줄인다.
이런 목표를 중심으로 라는 관점에서 위 세가지 키워드를 살펴 보고자 한다. 그전에 리액트의 가장 큰 단위인 컴포넌트를 짚고 넘어가자.

```js
import React from 'react';
import ReactDOM from 'react-dom';

class App extends React.Component {
  render() {
    return (
        <div>
          <h3> WELCOME KOREAN MART </h3>
        </div>
    )
  }
}

ReactDOM.render(<App/>, document.getElementById("root"));
```
{:.align-center}
리액트의 할아버지 코드다.
{: .captionC}

1. 먼저 import  
React 구조체 ‘react’ 와 리액트 라이브러리가 몰래 뒤에서 관리하는 가상의 DOM인 `‘react-dom’` 을 가지고 왔다.
2. class App 상속  
App 은 convention 하게 리액트의 최상위 컴포넌트에 할당하는 이름이다. 이 안에 컴포넌트를 계속 nested 하게 구현하여 우리가 원하는 복잡하지만 간단한 코드를 만들수 있게 된다. 컴포넌트는? 간단히 생각하면 사용자가 만드는 HTML 태그이자 리액트 프로젝트를 쌓아가는 블럭이다.
3. render에선 다음은 우리눈에 보이게될 View를 설명하는 곳이다.  따라서 이곳 밖에선 이 View를 결정할 외적인 넣을 수 있다는 말이다. render 와 return 사이에는 렌더전 로직을 작성할 수 있다. 직접적으로 DOM은 아니지만 라이프사이클을 통해 매번 호출되어 로직을 작성할 수 있다. return 안의 문법은 JSX를 따르게 되는데 바닐라 리액트로 처리할때보다 코드량이 줄어드는 장점이 있다. 대신 Bable이 컴파일 해주는 단계가 추가된다. 디버깅할때 살짝 까다로워질 수 있다.
4. 마지막으로 ReactDOM에 render 명령을 내려준 모습을 볼 수 있다. `<App>` 블럭만을 대상으로 했지만 실제코드에선 여러가지 블럭이 App에 딸려있으므로 프로젝트가 통째로 렌더링 되는 모습을 볼 수 있다.

### Props

Props는 컴포넌트를 사용할때 할당시켜주는 Data다. 코드내에서 수정하거나 임의로 추가하는것이 금지되어 있다. 부모 컴포넌트로 통신할 필요가 있다면 State를 위로 올려주는 방법을 사용한다.

```js
class App extends React.Component {
  render() {
    return (
        <div>
          <h3> WELCOME KOREAN MART </h3>
          <OrderList title={"<< inventory full >> "} list={['Kimchi', 'Bulgogi', 'Bibimbab']}/>
          <OrderList title={"<< not enough item >>"} list={['DON CASS', 'Asahi MACJU']}/>
        </div>
    )
  }
}

class OrderList extends React.Component {
  constructor(props) {
    super(props);
    this.state = {done: false}
  }

  render() {
    const mappingLists = this.props.list.map((item) =>
        <Item SECRETKEY={this.state.done} key={item + "key"} item={item} item2={this.props}/>
    );

    return (
        <div>
          <h3> {this.props.title} </h3>
          <ul> {mappingLists} </ul>
        </div>
    )
  }
}
```
{:.align-center}
구체적인 컴포넌트의 구조와 props의 운명
{: .captionC}

구체적인 컴포넌트의 구조와 props의 운명<App>에서 title과 list라는 이름의 props를 내려줬다. <OrderList>에선 그 이름을 잘 활용하고 있는 모습을 보여주고 있다. 아까전에 render와 return 사이에서 여러로직을 처리한다고 했는데 이 코드는 list를 map으로 돌려 중복되는 <Item>을 한번에 찍어내기위한 준비를 했다. mappingLists를 return 에서 {mappingLists}의 형태로 사용하고 있는 모습이다. 대신 Item에는 key값이 필요하다는 경고를 해준다. 그래서 key라는 props를 일부러 지정해주었다.

### State

State는 컴포넌트 단위로 가지는 자료창고다. 자료창고의 자료를 꺼내서 Props로 내려줘도 좋고 이후에 나올 라이프 사이클에서 만들 수 있는 로직을 state에 반영한다거나 여러가지 동작이 가능하다. 다만 this.state로 직접 접근하면 안되고 setstate 로직을 사용하여야 한다.

```js
class Item extends React.Component {
  constructor(props) {
    super(props);
    this.state = {done: false}
  }

  onListItemClick(e) {
    this.setState({done: !this.state.done})
    e.preventDefault();
  }

  render() {
    const style = {
      textDecoration: this.state.done ? 'line-through' : 'none'
    };
    return (
        <div>
          <li style={style} onClick={this.onListItemClick.bind(this)}> {this.props.item} </li>
        </div>
    )
  }
}
```

위 코드는 각 done 이라는 state의 키값이 클릭에 의해 토글되는 로직을 구현했다. 일반 자바스크립트라면 동작하지 않을 내용이지만 render는 라이프사이클 또는 setstate가 호출될때마다 동작된다. 매 렌더마다 새로운 스타일을 입은 <li>가 렌더되면서 삭선이 첨가되었다가 사라지는 모습을 볼 수 있다. 토글의 상태. 이것을 관리한다는 의미에서 Satatefull한 컴포넌트라 부른다. state를 선언한 컴포넌트는 각자 이런 관리가 필요하다. 일부러 부모컴포넌트만 state를 가지게하면 자료의 일관성을 유지할 수 있다.

### 라이프사이클

![](/img/1*3AdJb7oqj8UerlojaXv8kg.jpeg)
{:.align-center}
리액트 컴포넌트 구조가 어떻게 관리받는지를 정리한 표
{: .captionC}

녹색으로 표시된 지점이 우리가 별도의 로직을 넣을 수 있는 메서드를 구현하 수 있는 곳이다. 렌더 전후, 컴포넌트가 DOM에 등장한 시점이나 사라지기전 여러가지 로직을 집어넣기 쉽게 준비되어 있다. 재미있는 점이 있다면 state changed 뒤에도 업데이트를 하지 않는 케이스가 있다는 점이다. 리액트는 상태변화를 DOM에 반영할지 안할지 판단함으로서 바닐라 DOM보다 우월한 성능을 보장한다. DOM은 조금만 엘리먼츠를 바꿔도 전체를 바꿔야하는 약점을 가지고 있다.

```js
class Content extends React.Component {
  constructor(props) {
    super(props);
  }
  componentWillMount() {
    console.log('Component WILL MOUNT!')
  }
  componentDidMount() {
    console.log('Component DID MOUNT!')
  }
  componentWillReceiveProps(nextProps) {
    console.log('Component WILL RECIEVE PROPS!')
  }
  shouldComponentUpdate(nextProps, nextState) {
    return true;
  }
  componentWillUpdate(nextProps, nextState) {
    console.log('Component WILL UPDATE!');
  }
  componentDidUpdate(prevProps, prevState) {
    console.log('Component DID UPDATE!')
  }
  componentWillUnmount() {
    console.log('Component WILL UNMOUNT!')
  }
  render() {
    return (
        <div>
          <h1>{this.props.title}</h1>
        </div>
    );
  }
}
```

없는 라이프사이클은 구현해서 쓰면된다. IDE에 따라 자동으로 넣어주기도 한다.

  
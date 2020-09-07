# AboutReactJS
리액트 공부하기

# 출처 및 참고
이 문서는 [리액트 공식 문서](https://ko.reactjs.org/docs/hello-world.html)의 내용을 참고 및 정리하여 작성되었습니다.

# 정리한 내용

## Hello World

```
ReactDOM.render(
    <h1>Hello, world!</h1>,
    document.getElementById('root')
);
```

## JSX 

```
const element= <h1>Hello, world!</h1>;
```

JSX는 React 엘레먼트를 생성한다.

```
const element = <div>{name}</div>;
```

중괄호 안에는 유효한 모든 JS 표현식을 넣을 수 있다. (변수, 함수, 기타 문법 등등)

```
const element = (
    <h1 className="greeting">
        Hello, world!
    </h1>
);

const element = React.createElement(
    'h1',
    {className: 'greeting'},
    'Hello, world!'
);
```
JSX는 객체를 표현한다. 위의 코드는 동일한 결과를 지닌다. 

```
const element = {
    type : 'h1',
    props: {
        className: 'greeting'.
        children: 'Hello, world!'
    }
}
```
createElement는 위와 같은 객체를 생성하는 함수이다.

## 엘리먼트 렌더링

React 엘리먼트는 불변객체이다. 생성한 이후에는 해당 엘리먼트의 자식이나 속성을 변경할 수 없다.

<strong>UI를 업데이트하는 유일한 방법은 새로운 엘리먼트를 생성하고 이를  ReactDom.render()로 전달하는 것이다.</strong>

ReactDom은 해당 엘리먼트를 이전의 엘리먼트와 비교하고 DOM을 원하는 상태로 만드는데 필요한 경우에만 DOM을 업데이트한다.

## Components 와 Props

```
function Welcome(props){
    return <h1>Hello, {props.name}</h1>;
}

class Welcome extends React.Component {
    render(){
        return <h1>Hello, {this.props.name}</h1>;
    }
}
```

함수 컴포넌트, 클래스 컴포넌트이고 이 두 컴포넌트는 거의 동일하다. 클래스의 경우 몇가지 추가 기능이 있다.

React 엘리먼트는 사용자 정의 컴포넌트로도 나타낼 수 있다.

```
const element = <Welcome name="Sara" />
```

React가 사용자 정의 컴포넌트로 작성한 엘리먼트를 발견하면 JSX 어트리뷰트와 자식을 해당 컴포넌트에 단일 객체로 전달한다. 이 객체를 "props" 라고 한다.

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

위 코드를 실행하면 Welcome 컴포넌트에 props = { name : 'Sara' } 객체가 전달된다.

```
<h1> Hello, Sara </h1>
```

그 결과로 위 엘리먼트가 반환된다. ReactDom은 위 엘리먼트와 일치하도록 DOM을 효율적으로 업데이트한다.

리액트 공식 문서에서는 하나의 컴포넌트를 여러 개의 작은 컴포넌트로 나누어 관리하는 것을 권장한다.

```
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

위 Comment 컴포넌트는 Avatar, UserInfo 컴포넌트를 만들어 단순화 할 수 있다.

```
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.avatarUrl}
      alt={props.name}
    />
  );
}

function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}

function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

위 코드를 통해서 Comment 컴포넌트의 props 는 author 객체, text 문자열, data 날짜를 지니고 UserInfo 컴포넌트의 props는 (author) user, name 을 지니고, Avatar 컴포넌트의 props는 avatarUrl, name을 지닌다는 것을 알 수 있다.

<strong>모든 React 컴포넌트는 자신의 props를 다룰 때 반드시 순수 함수처럼 동작해야 한다.</strong>

이때 순수함수란 매개변수로 전달된 변수들을 바꾸지 않는 함수를 말한다.

## State and Lifecycle

```
function tick(){
    const element = {
        <div>
            <h1>Hello, World!</h1>
            <h2>It is {new Date().toLocaleTimeString()}.</h2>
        </div>
    };
    ReactDom.render(
        element,
        document.getElementById('root')
    );
}

setInterval(tick, 1000);
```

위 함수에서 Clock 컴포넌트를 만들어서 재사용하고 캡슐화해보자.

```
function Clock(props){
    return (
        <div>
            <h1>Hello, world!</h1>
            <h2>It is {props.data.toLocaleTimeString()}.</h2>
        </div>
    )
}

function tick(){
    ReactDom.render(
        <Clock date={new Date()} />,
        document.getElementById('root')
    );
}

setInterval(tick, 1000);
```

이를 다시 Clock 컴포넌트가 스스로 업데이트하도록 만들어보자. 이를 위해 Clock 컴포넌트에 state를 추가해야한다.

```
class Clock extends React.Component {
    render(){
        return (
            <div>
                <h1>Hello, world!</h1>
                <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
            </div>
        )
    }
}
```

Clock은 이제 함수가 아닌 클래스로 정의된다.

render 메서드는 업데이트가 발생할 때마다 호출되지만, 같은 DOM 노드로 Clock 컴포넌트를 렌더링하는 경우 단일 인스턴스만 사용된다. (재사용) 이것은 로컬 state와 생명주기 메서드와 같은 부가적인 기능을 사용할 수 있게 해준다.

```
class Clock extends React.Component {
    constructor(pros){
        super(props);
        this.state = {date : new Date()};
    }
    
    render(){
        return (
            <div>
                <h1>Hello, world!</h1>
                <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
            </div>
        )
    }
}

ReactDOM.render(
    <Clock />,
    document.getElementById('root')
);
```

생성자 함수 (constructor)를 만들어 props를 전달하고 state 변수를 새로 할당했다. 이제 Clock 컴포넌트에 별도로 data prop을 전달해 줄 필요가 없어졌다.

이제 Clock에 타이머를 설정해보자. 이를 위해 React에서 마운팅이라고 부르는 방법을 사용한다.

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

componentDidMount() 메서드는 컴포넌트 출력물이 DOM에 렌더링 된 후에 실행된다.

timerID 같이 데이터 흐름 안에 포함되지 않는 어떤 항목을 보관할 필요가 있다면 자유롭게 클래스에 수동으로 부가적인 필드를 추가해도 된다. 위 코드에선 타이머를 멈추기 위한 참조값으로 timerID 필드를 추가했다.

이때 timerID에 할당된 setInterval 함수는 1초마다 tick() 메서드를 호출한다.

tick() 메서드는 setState() 메서드를 호출하는데 <strong>React는 setState() 메서드가 호출될 때마다 state가 변경된 것을 인지하고 화면에 표시될 내용을 출력하기 위해 render() 메서드를 다시 호출한다.</strong> 이 때 render() 메서드 안의 this.state.data가 달라지고 렌더링 출력값은 업데이트된 시각을 포함한다. React는 이에 따라 DOM 업데이트한다.

Clock 컴포넌트가 DOM으로부터 한 번이라도 삭제된 적이 있다면 React는 타이머를 멈추기 위해 componentWillUnmount() 생명주기 메서드로 호출한다. 

state에 관해 꼭 알아야할 3가지가 있다.

1. 직접 수정하지 않기 
    ```
    this.state.comment = 'Hello'; //가 아니라
    this.setState({comment:'Hello'});
    ```
    this.state를 지정할 수 있는 유일한 공간은 constructor이다.

2. state 업데이트는 비동기적일 수도 있다.

    React는 성능을 위해 여러 setState() 호출을 단일 업데이트로 한꺼번에 처리할 수 있다.

    ```
    //wrong
    this.setState({
        counter : this.state.counter + this.props.increment
    });

    //correct
    this.setState((state,props)=> {
        counter : state.counter + props.increment
    });
    ```

    첫 번째 식은 this.props와 this.state가 비동기적으로 업데이트될 수 있기 때문에 다음 state를 계산할 때 해당 값에 의존해서는 안 된다.

    따라서 객체보다는 함수를 인자로 사용하는 setState를 사용해야 한다.

3. state 업데이트는 병합된다. (독립적으로 업데이트 할 수 있다)

    setState() 메서드를 호출할 때 React는 제공한 객체를 현재 state로 병합한다.

    예를 들어, state는 다양한 독립적인 변수를 포함할 수 있다.

    ```
    constructor(props) {
        super(props);
        this.state = {
        posts: [],
        comments: []
        };
    }
    ```

    별도의 setState() 호출로 이러한 변수를 독립적으로 업데이트할 수 있다.

    ```
    componentDidMount() {
        fetchPosts().then(response => {
        this.setState({
            posts: response.posts
        });
        });

        fetchComments().then(response => {
        this.setState({
            comments: response.comments
        });
        });
    }
    ```

    this.setState({comments})는 this.state.posts에 영향을 주진 않지만 this.state.comments는 완전히 대체된다.


컴포넌트는 자신의 state를 자식 컴포넌트에 props로 전달할 수 있다.

하지만 자식 컴포넌트는 자신이 받은 prop 객체가 어디에서 온 것인지 알지 못한다.

일반적으로 이를 하향식 또는 단방향식 데이터 흐름이라고 한다. 모든 state는 항상 특정한 컴포넌트가 소유하고 있으며 그 state로부터 파생된 UI 또는 데이터는 오직 자신의 아래에 있는 컴포넌트에만 영향을 미친다.

따라서 같은 종류의 컴포넌트가 여러개 생성되었다라도 모두 독립적이다.

## Event 처리하기

React에서 기본 동작을 방지하기 위해선 반드시 preventDefault를 명시적으로 호출해야 한다. 이때 기본 동작을 방지한다는 것은 해당 태그에 등록된 기본 이벤트를 실행하지 않으며 전파도 막는다는 것이다.

```
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 콜백에서 `this`가 작동하려면 아래와 같이 바인딩 해주어야 합니다.
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

위 코드에서 handleClick 메서드 내에서 this를 사용하기 위해 bind(this)를 해주었다.

만약 이게 불편하다면 퍼블릭 클래스 필드 문법을 사용해 콜백을 올바르게 바인딩할 수 있다.

```
class LoggingButton extends React.Component {
  // 이 문법은 `this`가 handleClick 내에서 바인딩되도록 합니다.
  // 주의: 이 문법은 *실험적인* 문법입니다.
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

## 조건부 렌더링

리액트는 if나 조건부 연산자와 같은 JavaScript 연산자를 지원한다.

```
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```

위와 같이 && 연산자로 더 짧은 구문을 사용할 수도 있다. 

## 리스트와 키

```
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

위 코드를 실행하면 리스트의 각 항목에 key를 넣어야 한다는 경고가 표시된다. key는 엘리먼트 리스트를 만들 때 포함해야 하는 특수한 문자열 어트리뷰트이다.

위 문제는 다음과 같이 해결할 수 있다.

```
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

왜 key 어트리뷰트가 필요한 걸까?

key는 React가 어떤 항목을 변경, 추가 또는 삭제할지 식별하는 것을 돕는다. key는 엘리먼트에 안정적인 고유성을 부여하기 위해 배열 내부의 엘리먼트에 지정해야 하는 것이다.

```
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);

const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);
```

데이터의 id 값을 사용하는 것이 일반적이지만 id 값이 없을 때는 index를 key로 사용하기도 한다. 하지만 권장하지는 않는다.

이는 항목의 순서가 바뀔 수 있는 경우 성능이 저하되거나 컴포넌트의 state와 관련된 문제가 발생할 수 있다. 

```
function ListItem(props) {
  // 맞습니다! 여기에는 key를 지정할 필요가 없습니다.
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 맞습니다! 배열 안에 key를 지정해야 합니다.
    <ListItem key={number.toString()} value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

충분히 헷갈릴 수 있는 상황의 코드이다. key가 필요한 엘리먼트를 잘 알아둬야 한다.

key는 전역에서 고유할 필요는 없다. 두 개의 다른 배열을 만들 때 동일한 key를 사용할 수 있다.

```
function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
];
ReactDOM.render(
  <Blog posts={posts} />,
  document.getElementById('root')
);
```

다음은 map() 함수를 사용해 중괄호 안에 모든 표현식을 포함 시키는 방식이다.

```
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```

어떤 방식이 더 가독성이 좋은가는 개발자 스스로가 판단해서 결정해야한다.
# React Lifecycle

컴포넌트가 실행되거나 업데이트되거나 제거될 때 특정 메서드를 호출한다.

![react-lifecycle](https://kyun2da.dev/static/69e54fe57da139eabae168b5e8304af4/01645/lifecycle.png)
출처 : [react-lifecycle](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

---

## Mounting
컴포넌트가 생성(instance) 되고 DOM에 적용될 때 메서드가 호출된다.
- `constructor`
- `getDerivedStateFromProps`
- `render`
- `componentDidMount`

### constructor
- mount과정에서 가장 먼저 호출 된다.
- `state`를 초기화 할 수 있다.
    - 해당 함수의 첫번째 라인은 반드시 `super(props)` 을 호출해야 한다. (미호출시 prop가 생성되지 않음)
```javascript
constructor(props) {
  super(props);
}
```

### ~~componentWillMount~~
- DOM 마운팅 이전에 한번 호출된다. (constructor / render 사이)
- render가 되기 직전에 호출되는 API 로, 브라우저가 아닌 환경에서도 호출하는 용도로 사용했으나 `componentDidMount`에서 처리할 수 있다.  

### render
- 랜더링을 한다.
- 필수 함수로 `components`에 메소드를 반드시 만들어야 한다.
- `state`나 `props`가 수정되면 render 메소드가 다시 호출된다.

### componentDidMount
- DOM 마운팅 이후 한번 호출 된다.
- DOM을 얻어올 수 있다.

```javascript 
componentDidMount() {
    console.log("componentDidMount");

    setInterval(() => {
        //console.log("setInterval");
        this.setState((state) => ({ ...state, age: state.age + 1 }));
    }, 1000);
}
```

---

## Updating
`props` 나 `state`가 변경된경우, 리렌더링 하는 경우 
- `componentWillReceiveProps`
- `shouldComponentUpdate`
- `componentWillUpdate`
- `componentDidUpdate`

### ~~componentWillReceiveProps~~
- 컴포넌트 생성후 첫 렌더링을 마친 후 호출 된다.
- `props`를 새로 지정했을 때 바로 호출 된다.
- `state` 변경에 반응하지 않는다.
- `props` 값에 따라 state를 변경해야 하면
    - `setState`를 이용해 `state` 변경한다.
    - 다음 이벤트로 각각 가는 것이 아니라 한번에 변경된다.
```javascript
componentWillReceiveProps(nextProps) {
    // props 변경을 감지하고 가장먼저 실행
    console.log("componentWillReceiveProps", nextProps);
}
```

### shouldComponentUpdate
- `props` 변경 || `state` 변경 
- 컴포넌트 업데이트 직전에 호출되는 메소드
- `props` 나 `state`가 변경 되었을 때 리렌더링 여부를 `return` 값으로 정한다.
```javascript
shouldComponentUpdate(nextProps, nextState) {
    // 변경된 다음 props와 state를 받아옴
    console.log("shouldComponentUpdate", nextProps, nextState);

    return true;
    // default = true
    // true면 render가 실행되고 false면 render가 실행이 안됨
}
```

### ~~componentWillUpdate~~
- 컴포넌트가 재 랜더링 되기 직전에 불린다.
- `setState` 같은 것을 쓰면 무한루프에 걸리므로 사용하면 안된다.

### componentDidUpdate
- 컴포넌트 업데이트 직후에 호출된다.
- 현재 props와 이전 props를 비교할 수 있다.
- render 과정에서 호출 되지 않는다.
- `shouldComponentUpdate`가 `false`를 리턴한 경우 호출되지 않는다.
```javascript
componentDidUpdate(prevProps, prevState) {
    console.log("componentDidUpdate", prevProps, prevState);
}
```

### componentWillUnmount
- 컴포넌트가 DOM에서 삭제된 후 실행된다.
```javascript
componentWillUnmount() {
    clearInterval(this.interval);
}
```
---

## Lifecycle 변경(V16.3 이후)
- componentWillMount, componentWillReceiveProps => `getDerivedStateFromProps` 
- componentWillUpdate => `getSnapshotBeforeUpdate`

### getDerivedStateFromProps
전
```javascript
componentWillReceiveProps(nextProps) {
  if (this.props.name !== nextProps.name) {
    this.setState({ name: nextProps.name });
  }
}
```

후
```javascript
static getDerivedStateFromProps(nextProps, prevState) {
  if (prevState.age !== nextProps.age) {
    return { age: nextProps.age };
  }

  return null;
  // state를 반환함
}
```
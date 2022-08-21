

### 创建新的react应用

```shell
#创建一个新的react应用my-app
npx create-react-app my-app --template typescript
```

### JSX

- JSX是什么

  ```jsx
  const element = <h1>Hello, world!</h1>;
  ```

  - JSX 是一个 JavaScript 语法扩展。它类似于模板语言，但它具有 JavaScript 的全部能力

  - `Babel`会把JSX编译为`React.createElement()`的一个函数调用，`React.createElement()`返回的对象被称为React元素

    ```jsx
    const element = (
      <h1 className="greeting">
        Hello, world!
      </h1>
    );
    
    // 转义后
    const element = React.createElement(
      'h1',
      {className: 'greeting'},
      'Hello, world!'
    );
    ```

    - `Babel`是一个Javascript的编译器。它是一个工具链，主要用于将ECMAScript 2015+版本的代码转换为向后兼容的JavaScript的语法，以便能够运行在当前和旧版本的浏览器或其他环境中。
    - React 通过读取这些对象（React元素），然后使用它们来构建 DOM 以及保持随时更新。

- 语法

  - 可以在大括号里放置任何有效的`javascript`表达式

    ```jsx
    const name = 'abc';
    const element = <h1>Hello, {name}</h1>;
    ```

  - 在属性中嵌入 JavaScript 表达式时，不要在大括号外面加上引号

    ```jsx
    const element = <div tabIndex = "0"></div>;  
    
    const index = 0;
    const element2 = <div tabIndex = {index}></div>; //index的值不要加引号
    ```

  - 变量的命名规则

    - 首字母小写，后面每个单词的第一个字母大写

  - 防止注入攻击

    ```jsx
    const title = response.potentiallyMaliciousInput;
    // 直接使用是安全的：
    const element = <h1>{title}</h1>;
    ```

    - React DOM 在渲染所有输入内容之前，默认会进行转义。
    - 所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 [XSS（cross-site-scripting, 跨站脚本）](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击。

### 元素的渲染

- React元素

  - React元素是构成React应用的最小组成

  - 元素描述了你想在画面上表示的内容

    ```jsx
    const element = <h1>Hello, world</h1>;
    ```

  - 与浏览器的 DOM 元素不同，React 元素是创建开销极小的普通对象。React DOM 会负责更新 DOM 来与 React 元素保持一致。

- 将一个React元素渲染为DOM

  - 在`html`文件中定义一个Root DOM节点，这个节点内的所有内容都将有React DOM进行管理

    ```html
    <div id="root"></div>
    ```

  - 将 React元素渲染到root DOM节点

    ```tsx
    const element = <h1>Hello, world</h1>;
    const root = ReactDOM.createRoot(
      document.getElementById('root')
    );
    root.render(element);
    ```

### 更新已经被渲染的元素

- React元素是不可变对象。一旦被渲染就无法改变它的子元素和属性

- React元素 = 某一个特定时刻的UI

- 所以更新的方式是创建一个新的元素，重新传入`root.render()`

  ```tsx
  function tick() {
    const element = (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {new Date().toLocaleTimeString()}.</h2>
      </div>
    );
    root.render(element);
  }
  
  // 每隔1秒重新渲染
  setInterval(tick, 1000);
  ```

- React只会对需要更新的部分进行更新

  - 尽管每一秒都会新建一个React元素，ReactDOM只会更新实际有改变的部分（时间）

### 组件（component）和props

- React 组件是可复用的小的代码片段，它们返回要在页面中渲染的 React 元素。(相当于一个自定义的tag)

- props是React组件的输入，它们是从父组件向下传递给子组件的数据。（相当于tag中的各种属性值）

- props是只读的。

- 组件的定义方法

  - 函数组件（function component）

    ```tsx
    function Welcome(props) {
      return <h1>Hello, {props.name}</h1>;
    }
    ```

  - class组件（class component）

    ```tsx
    class Welcome extends React.Component {
      render() {
        return <h1>Hello, {this.props.name}</h1>;
      }
    }
    ```

  - 组建的名字必须以大写字母开头（React 会将以小写字母开头的组件视为原生 DOM 标签。）

- 组件的渲染

  ```tsx
  function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
  }
  
  const element = <Welcome name="Sara" />;
  const root = ReactDOM.createRoot(document.getElementById('root'));
  root.render(element);
  ```

  1. 调用 `root.render()` 函数，并传入 `<Welcome name="Sara" />` 作为参数。
  2. React 调用 `Welcome` 组件，并将 `{name: 'Sara'}` 作为 props 传入。
  3. `Welcome` 组件将 `<h1>Hello, Sara</h1>` 元素作为返回值。
  4. React DOM 将 DOM 高效地更新为 `<h1>Hello, Sara</h1>`。

### state和生命周期

```tsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  root.render(element);
}

// 每隔1秒重新渲染
setInterval(tick, 1000);
```

- 上面这个Clock的问题点

  - 每次都要将整个root DOM刷新（尽管reactDOM会将前后的内容进行比较，只更新需要的部分，但比较也要花时间）
  - 每隔1秒刷新应该是组件自身提供的功能

- 改进

  - 首先将上面的function component转换成class component(这个时候画面是不会自动刷新的)

    ```tsx
    import React from "react";
    
    interface ClockProps {
        date: Date
    }
    
    export default class Clock extends React.Component<ClockProps> {
        render() {
            return (
              <div>
                <h1>Hello, world!</h1>
                <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
              </div>
            );
          }
    }
    ```

  - 我们想要更新`prpos.date`, 但是props是只读的，所以我们把想要更新的内容放到state中(这个时候画面还是不会自动刷新)

    ```tsx
    import React from "react";
    
    interface ClockProps {
        
    }
    
    interface ClockState {
        date: Date
    }
    
    export default class Clock extends React.Component<ClockProps, ClockState> {
        constructor({}: ClockProps){
            super({});
            this.state = {date: new Date()}
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
    ```

  - 当Clock元素被加载到画面上后添加`setInterval`

    ```tsx
    export default class Clock extends React.Component<ClockProps, ClockState> {
        constructor({}: ClockProps){
            super({});
            this.state = {date: new Date()}
        }
    
        componentDidMount() {
            setInterval(() => {
                this.setState({date: new Date()});
            }, 1000)
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
    ```

  - 当Clock元素从画面上移除时应该注销`setInterval`

    ```tsx
    export default class Clock extends React.Component<ClockProps, ClockState> {
        intervalId?: NodeJS.Timer;
    
        constructor({}: ClockProps){
            super({});
            this.state = {date: new Date()}
        }
    
        componentDidMount() {
            this.intervalId = setInterval(() => {
                this.setState({date: new Date()});
            }, 1000)
        }
    
        componentWillUnmount() {
            clearInterval(this.intervalId!);
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
    ```

- state

  - state是component的一个私有属性

  - 初始化state（在组件的构造方法中初始化）

    ```tsx
    constructor({}: ClockProps){
            super({});
            this.state = {date: new Date()}
        }
    ```

  - 更新state

    - state本身也是一个immutable的属性，所以应该通过`setState()`方法进行更新
    - 调用`setState()`方法是可以只更新一部分state属性值，没有被更新的属性会被合并过来

    ```tsx
    this.setState({date: new Date()});
    ```

    - 下面的做法是不会通知react重新渲染界面的

    ```tsx
    this.state.key = XXX;
    this.state = {xxx};
    ```

    

  - state发生更新，React就会更新画面（调用class component的`render()`方法）

  - state的更新是异步的

    - 也就是说，调用完`this.setState()`后，state可能并没有更新完

    - 所以我们不应该依赖于当前的state，props的值

      ```tsx
      // Wrong
      this.setState({
        counter: this.state.counter + this.props.increment,
      });
      ```

    - 要解决这个问题，可以让 `setState()` 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数

      ```tsx
      // Correct
      this.setState((state, props) => ({
        counter: state.counter + props.increment
      }));
      ```

    - 上面两种做法的区别
      - 如果一下子调用了`setState()`很多次，还没有被执行的task会被放到queue里
      - 第一种在task被加入到队列and实际的操作被执行之前就已经取得了state，和props的值，而这些值可能还没有被更新完成
      - 第二种在实际的函数被执行时才去取state，props的值，这个时候上一个task已经执行完成，因此可以保证取得的值已经被更新完成


### 事件处理

- React中绑定listener的方式

  ```tsx
  <button onClick={handelClick}> //传入一个函数作为事件处理函数
    Activate Lasers
  </button>
  ```

- `this`的问题

  - 通常做法是先定义一个函数，然后将函数传入

    ```tsx
    export default class Counter extends React.Component<CounterProps, CounterState> {
        constructor(props: CounterProps) {
            super(props);
            this.state = {num: props.increment};
        }
        render() {
            return (
                <div>
                    <span>{this.state.num}</span>
                    <button onClick={this.increment}>Increase</button>
                </div>
            );
        }
        increment() {
            this.setState((state, props) => ({num: state.num + props.increment}));
        }
    }
    ```

  - 上面的做法在点击按钮时控制台会报错

    ```javascript
    uncaught TypeError: Cannot read property 'setState' of undefined
    ```

    - 原因是在`increasement`函数中，this指的是`increasement`，而该函数中并不存在`this.setState`
    - 所以我们要把函数`increasement`的执行环境绑定到`Counter`类

  - 方法1：在`constructor`中进行绑定

    ```tsx
    constructor(props: CounterProps) {
            super(props);
            this.state = {num: props.increment};
            this.increment = this.increment.bind(this);
        }
    ```

  - 方法2：用lamda表达式来定义函数

    ```tsx
    increment = () => {
            this.setState((state, props) => ({num: state.num + props.increment}));
        }
    ```

  - 方法3：在创建React元素时绑定

    ```tsx
    render() {
            return (
                <div>
                    <span>{this.state.num}</span>
                    <button onClick={this.increment.bind(this)}>Increase</button>
                </div>
            );
        }
    ```

- 事件的传递

### list and key

- list

  - React可以通过在`{}`构建一个元素的集合，渲染这个集合中的多个组件

    ```tsx
    const numbers = [1, 2, 3, 4, 5];
    const listItems = numbers.map((number) =>
      <li>{number}</li>
    );
    
    <ul>{listItems}</ul>
    ```

- key

  - key 帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。

    ```tsx
    const numbers = [1, 2, 3, 4, 5];
    const listItems = numbers.map((number) =>
      <li key={number.toString()}>
        {number}
      </li>
    );
    ```

  - 一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。

  - 当元素没有确定 id 的时候，万不得已你可以使用元素索引 index 作为 key(但不推荐，因为元素的顺序可能会发生改变)

  - 下面这段代码，不设置key会发生什么

    ```typescript
    interface ListItemState {
        values: number[]
    }
    export default class ListItem extends React.Component<{}, ListItemState>{
        constructor(props: {}) {
            super(props);
            this.state = {values: [0, 1, 2]};
            this.handleClick = this.handleClick.bind(this);
        }
    
        handleClick(event: React.MouseEvent<HTMLButtonElement, MouseEvent>){
            this.setState(state => ({
                values: [state.values.length, ...state.values]
            }))
        }
    
        render() {
            const elements = this.state.values.map(v => <li key={v}><Counter increment={v} /></li>);
            return (
                <div>
                    <button onClick={this.handleClick}>Add</button>
                    <ul>
                        {elements}
                    </ul>
                </div>
            )
        }
    }
    ```

    - 第一次点击`Add`时结果是这样的

      ![image-20220505005052678](D:\git\knowledge\without-key.png)

    - 但我们想象的结果是这样的

      

    - ![image-20220505005435274](D:\git\knowledge\with-key.png)

    - 画面刚加载出来时，每个counter的state从上到下分别是

      - 0
      - 1
      - 2

    - 点击了`Add`后虽然我们想定是在最上面插入一个新的counter(3)，但是因为没有设置`key`属性，react按照行号，将新增的内容和原先第一行的内容进行比较，由于只改变了props，所以把原先第一行的state继承过来，表示的值为0

    - 而最后一行的内容，没有与之对应的更新前元素，所以生成了一个新的

    - 添加`key`属性后结果就正确了

      ```typescript
      const elements = this.state.values.map(v => <li key={v}><Counter increment={v} /></li>);
      ```

      

### 表单

- 受控组件

  - 值由react进行设置的组件叫做受控组件

  ``` typescript
  render() {
          return(
              <form>
                  <input type="text" value="1" name="num"></input>
                  <input type="submit" value="submit" />
              </form>
          )
      }
  ```

  - 上面这段代码，画面上加载出的入力框，因为已经指定`value=1`，所以用户不能进行任何操作

  - 解决方法

    - 让`<input>`根据state来设置

    - 当用户操作`<input>`使其发生改变时，更新state

      ```typescript
      interface NameFormState {
          value: number
      }
      export default class NameForm extends React.Component<{}, NameFormState> {
          constructor(props: {}){
              super(props);
              this.state = {value: 0}
              this.handleChange = this.handleChange.bind(this);
          }
      
          handleChange(event: React.ChangeEvent<HTMLInputElement>) {
              
              this.setState({value: parseInt(event.target.value)})
          }
      
          render() {
              return(
                  <form>
                      <input type="text" value={this.state.value} name="num" onChange={this.handleChange}></input>
                      <input type="submit" value="submit" />
                  </form>
              )
          }
      }
      ```

- 处理多个输入

  - 当需要处理多个 `input` 元素时，我们可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作。

    ```typescript
    interface NameFormState {
        isGoing?: boolean,
        numberOfGuests?: number
    }
    export default class NameForm extends React.Component<{}, NameFormState> {
        constructor(props: {}){
            super(props);
            this.state = {isGoing: true, numberOfGuests: 0}
            this.handleChange = this.handleChange.bind(this);
            this.handleSubmit = this.handleSubmit.bind(this);
        }
    
        handleChange(event: React.ChangeEvent<HTMLInputElement>) {
            const target = event.target;
            const value = target.type === 'checkbox' ? target.checked : target.value;
            const name = target.name;
        
            this.setState({
              [name]: value
            });
        }
    
        handleSubmit(event: React.FormEvent<HTMLFormElement>) {
            event.preventDefault();
            alert(this.state.isGoing + ":" +this.state.numberOfGuests);
        }
    
        render() {
            return(
                <form onSubmit={this.handleSubmit}>
                    <label>
                        参与:
                        <input
                            name="isGoing"
                            type="checkbox"
                            checked={this.state.isGoing}
                            onChange={this.handleChange} />
                        </label>
                        <br />
                        <label>
                        来宾人数:
                        <input
                            name="numberOfGuests"
                            type="number"
                            value={this.state.numberOfGuests}
                            onChange={this.handleChange} />
                        </label>
                    <input type="submit" value="submit" />
                </form>
            )
        }
    }
    ```

- 非受控组件

  - 数据不是由react来管理的组件叫做非受控组件

  - 那么想要拿到值只能从DOM去拿,我们需要建立一个和DOM节点的引用

    ```typescript
    export default class NameForm2 extends React.Component {
        input: React.RefObject<HTMLInputElement>;
    
        constructor(props: {}) {
            super(props);
            this.input = React.createRef();
            this.handleSubmit = this.handleSubmit.bind(this);
        }
        
        handleSubmit(event: React.FormEvent<HTMLFormElement>) {
            event.preventDefault();
            alert(this.input.current?.value);
        }
    
        render() {
            return(
                <form onSubmit={this.handleSubmit}>
                    <label>
                        Name:
                        <input type="text" ref={this.input} />
                    </label>
                    <input type="submit" value="submit"/>
                </form>
            )
        }
    }
    ```


### Hook

- 什么是Hook
  - Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数
  - Hook不能砸class component中使用

- State hook

  - 通过在函数组件里调用`useState()`来给组件添加一些内部 state。
  - `useState()`需要的参数
    - 初始的state值
  - `useState()`返回一个长度为2的数组
    - 第一个元素：当前state的值，或者计算初始state的函数
    - 第二个元素：更新state的函数

  ```typescript
  const [something, setSomething] = useState(0);
  ```

  - 调用更新函数时的方法和`this.setState()`相同
    - 可以传一个具体的值
    - 也可以传一个更新函数（参数为先前的state）

- Effect Hook

  - 给函数组件增加了操作副作用的能力。
  - 副作用：在 React 组件中执行数据获取、订阅或者手动修改DOM
  - 当你调用 `useEffect` 时，就是在告诉 React 在完成对 DOM 的更改后运行你的“副作用”函数。
  - 副作用函数还可以通过返回一个函数来指定如何“清除”副作用。
    - 原先我们需要分别在`componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount`种定义的副作用和清除负作用的代码
    - 而通常副作用和清除副作用我们把它们当做一对来考虑的话，这样的写法有些不自然
    - 而用`useEffect()`就能达成在同一处定义副作用和清除副作用

  - `useEffect()`的执行时机
    - 当画面被加载或更行后执行Effect
    - 画面更新前卸载Effect

  - `useEffect()`的参数
    - 第一个参数：Effect函数
    - 第二个函数（可选）：一个依赖列表，只有当列表中任意一个对象发生变化，Effect函数才会被执行

- Hook的一些使用规则

  - 只能在**函数最外层**调用 Hook。不要在循环、条件判断或者子函数中调用。
    - 因为我们需要保证在每一次渲染中Hook的调用顺序都是相同的
  - 只能在 **React 的函数组件**或者**自定义Hook**中调用 Hook。不要在其他 JavaScript 函数中调用。
  - Hook的函数名是以`use`开头的

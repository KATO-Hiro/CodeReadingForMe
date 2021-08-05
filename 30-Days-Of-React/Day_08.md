# 30 Days of React、Day 8: States

## States

### What is State?

+ Reactのオブジェクトで、データが変更されたときにコンポーネントを再描画するもの

### How to set a state

```js
// index.js
import React from 'react'
import ReactDOM from 'react-dom'
class App extends React.Component {
  // declaring state
  state = {
    count: 0,
  }

  // method which add one to the state
  addOne = () => {
    // this.setStateで状態を変更
    this.setState({ count: this.state.count + 1 })
  }

  // method which subtract one to the state
  minusOne = () => {
    this.setState({ count: this.state.count - 1 })
  }
  render() {
    // accessing the state value
    const count = this.state.count
    return (
      <div className='App'>
        <h1>{count} </h1>

        <div>
          <button className='btn btn-add' onClick={this.addOne}>
            +1
          </button>{' '}
          <button className='btn btn-minus' onClick={this.minusOne}>
            -1
          </button>
        </div>
      </div>
    )
  }
}
const rootElement = document.getElementById('root')
ReactDOM.render(<App />, rootElement)

```

+ Q: 関数コンポーネントのときは、Hooksを使えばよい?

## 疑問点

+ 。

+ 。

## 感想

+ 。

+ 。

## 抽象化

+ 。

+ 。

## 応用

+ 。

+ 。

## See

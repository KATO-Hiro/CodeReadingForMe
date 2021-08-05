# 30 Days of React、Day 10: React Project Folder Structure

## React Project Folder Structure and File Naming

+ フォルダ構造やファイル名に厳格な規則はない
+ プロジェクトや会社のガイドラインに合わせる

### File Naming

+ キャメルケースで、記述的な長い名称
  + HogeFugaFoo

### Folder

+ src/
  + assets: all images, icons and fonts
  + styles: all CSS style sheets
  + components: All components

+ 単一のファイルで記述していたコードを、コンポーネントに分割する

```js
// index.js
import React from 'react'
import ReactDOM from 'react-dom'
import { App } from './App' // { }で囲ってインポートしている。export defaultの場合は、不要

const rootElement = document.getElementById('root')
ReactDOM.render(<App />, rootElement)


// src/App.js
// Recommended for most of the cases
// 別のhigher order componentで使うことがあるかもしれないため
import React from 'react'

const App = () => <h1>Welcome to 30 Days Of React</h1>
export default App

```

### Components Folder

```terminal
src
  App.js
  index.js
  components
   -auth
    -Signup.js
    -Signin.js
    -Forgotpassword.js
    -Resetpassord.js
  header
   -Header.js
  footer
   -Footer.js
  assets
   -images
   -icnons
   - fonts
  styles
   -button.js
   -button.scss
  utils
    -random-id.js
    -display-time.js
    -generate-color.js
  shared
    -Button.js
    -InputField.js
    -TextAreaField.js
```

```js
// コンポーネントことにファイルを分ける
// CSSファイルはstylesへ
// src/components/header/Header.js
import React from 'react'

const Header = (props) => {
  return (
    <header>
      <div className='header-wrapper'>
        <h1>{welcome}</h1>
        <h2>{title}</h2>
        <h3>{subtitle}</h3>
        <p>
          {firstName} {lastName}
        </p>
        <small>{date}</small>
      </div>
    </header>
  )
}

export default Header

```

### Fragments

+ JSXで不要な親要素を避けるための方法

```js
import React from 'react'

// Recommended
const Skills = () => {
  return (
    // 最新の記法では、<>...</>で囲むだけで良い
    <>
      <li>HTML</li>
      <li>CSS</li>
      <li>JavaScript</li>
    </>
  )
}

const RequiredSkills = () => {
  return (
    <ul>
      <Skills />
    </ul>
  )
}


// クラスベース
// Componentをインポート
import React, { Component } from 'react'

// This is recommended
class App extends Component {
  render() {
    return <h1> 30 Days of React </h1>
  }
}
```

## 疑問点

+ higher order componentとは?

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

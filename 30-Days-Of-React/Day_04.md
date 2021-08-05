# 30 Days of React、Day 4: Components

## Components

+ 小規模で再利用可能なコード
+ React appは、Componentsの集合体
+ componentは、関数とクラスを使って作る

### Big picture of components

+ 同じようなパーツを使い回すときに効果を発揮する
+ 部分的にパーツを変えることもできる

### JavaScript function

+ 通常の関数と、アロー関数は厳密には同じではない
  + Q: どう違う?

```js
const getUserInfo = (firstName, lastName, country, title, skills) => {
  return `${firstName} ${lastName},  a ${title} developer based in ${country}. He knows ${skills.join(
    ' '
  )} `
}
// When we call this function we need parameters
const skills = ['HTML', 'CSS', 'JS', 'React']
console.log(
  getUserInfo('Asabeneh', 'Yetayeh', 'Finland', 'FullStack Developer', skills)
)
```

### JavaScript Class

```js
class Parent {
  constructor(firstName, lastName, country, title) {
    // we bind the params with this class object using this keyword
    this.firstName = firstName
    this.lastName = lastName
    this.country = country
    this.title = title
  }
  getPersonInfo() {
    return `${this.firstName} ${this.lastName},  a ${this.title} developer base in ${this.country} `
  }
  parentMethod() {
    // code goes here
  }
}

const p1 = new Parent('Asabeneh', 'Yetayeh', 'Finland', 'FullStack Developer')

class Child extends Parent {
  constructor(firstName, lastName, country, title, skills) {
    super(firstName, lastName, country, title)
    this.skills = skills
    // we bind the child params with the this keyword to this child object
  }
  getSkills() {
    let len = this.skills.length
    return len > 0 ? this.skills.join(' ') : 'No skills found'
  }
  childMethod() {
    // code goes here
  }
}

const skills = ['HTML', 'CSS', 'JS', 'React']

const child = new Child(
  'Asabeneh',
  'Yetayeh',
  'Finland',
  'FullStack Developer',
  skills
)
```

### Creating React Component

```js
// Functional Component
// React component syntax
// it can be arrow function, function declaration or function expression
const jsx = <tag> Content </tag>
const ComponentName = () => {
  return jsx
}


// JSX element, header
const header = (
  <header style={headerStyles}>
    <div className='header-wrapper'>
      <h1>Welcome to 30 Days Of React</h1>
      <h2>Getting Started React</h2>
      <h3>JavaScript Library</h3>
      <p>Asabeneh Yetayeh</p>
      <small>Oct 3, 2020</small>
    </div>
  </header>
)

// React Component
const Header = () => {
  return header
}

// or we can just return the JSX
// この書き方をよく見る気がする
// コンポーネント名は大文字から始まる
const Header = () => {
  return (
    <header style={headerStyles}>
      <div className='header-wrapper'>
        <h1>Welcome to 30 Days Of React</h1>
        <h2>Getting Started React</h2>
        <h3>JavaScript Library</h3>
        <p>Asabeneh Yetayeh</p>
        <small>Oct 3, 2020</small>
      </div>
    </header>
  )
}

// Even th above code can be written like this
// Explicitly returning the JSX
const Header = () => (
  <header style={headerStyles}>
    <div className='header-wrapper'>
      <h1>Welcome to 30 Days Of React</h1>
      <h2>Getting Started React</h2>
      <h3>JavaScript Library</h3>
      <p>Asabeneh Yetayeh</p>
      <small>Oct 3, 2020</small>
    </div>
  </header>
)


// Rendering components
// index.js
import React from 'react'
import ReactDOM from 'react-dom'
import asabenehImage from './images/asabeneh.jpg'

// Header Component
const Header = () => (
  <header>
    <div className='header-wrapper'>
      <h1>Welcome to 30 Days Of React</h1>
      <h2>Getting Started React</h2>
      <h3>JavaScript Library</h3>
      <p>Asabeneh Yetayeh</p>
      <small>Oct 3, 2020</small>
    </div>
  </header>
)

// User Card Component
const UserCard = () => (
  <div className='user-card'>
    <img src={asabenehImage} alt='asabeneh image' />
    <h2>Asabeneh Yetayeh</h2>
  </div>
)

// TechList Component
const TechList = () => {
  const techs = ['HTML', 'CSS', 'JavaScript']
  const techsFormatted = techs.map((tech) => <li key={tech}>{tech}</li>)
  return techsFormatted
}

// Main Component
const Main = () => (
  <main>
    <div className='main-wrapper'>
      <p>Prerequisite to get started react.js:</p>
      <ul>
        <TechList />
      </ul>
      <UserCard />
    </div>
  </main>
)

// Footer Component
const Footer = () => (
  <footer>
    <div className='footer-wrapper'>
      <p>Copyright 2020</p>
    </div>
  </footer>
)

// The App, or the parent or the container component
const App = () => (
  <div className='app'>
    // <ComponentName />の形式で記述
    // Q: 上から順番に処理されるという理解でいいのか?
    <Header />
    <Main />
    <Footer />
  </div>
)

const rootElement = document.getElementById('root')
// we render the App component using the ReactDOM package
ReactDOM.render(<App />, rootElement)


// Injecting data to JSX in React Component
// {}で変数を囲むことで、データを動的に変更できる
// To get the root element from the HTML document
const rootElement = document.querySelector('.root')
// JSX element, header
const welcome = 'Welcome to 30 Days Of React Challenge'
const title = 'Getting Started React'
const subtitle = 'JavaScript Library'
const author = {
  firstName: 'Asabeneh',
  lastName: 'Yetayeh',
}
const date = 'Oct 2, 2020'

// JSX element, header
const Header = () => (
  <header>
    <div className='header-wrapper'>
      <h1>{welcome}</h1>
      <h2>{title}</h2>
      <h3>{subtitle}</h3>
      <p>
        Instructor: {author.firstName} {author.lastName}
      </p>
      <small>Date: {date}</small>
    </div>
  </header>
)

const numOne = 3
const numTwo = 2

const result = (
  <p>
    {numOne} + {numTwo} = {numOne + numTwo}
  </p>
)

const yearBorn = 1820
const currentYear = 2020
const age = currentYear - yearBorn
const personAge = (
  <p>
    {' '}
    {author.firstName} {author.lastName} is {age} years old
  </p>
)

// User Card Component
const UserCard = () => (
  <div className='user-card'>
    <img src={asabenehImage} alt='asabeneh image' />
    <h2>
      {author.firstName} {author.lastName}
    </h2>
  </div>
)

// JSX element, main
const techs = ['HTML', 'CSS', 'JavaScript']
const techsFormatted = techs.map((tech) => <li key={tech}>{tech}</li>)

// JSX element, main
const Main = () => (
  <main>
    <div className='main-wrapper'>
      <div>
        <p>
          Prerequisite to get started{' '}
          <strong>
            <em>react.js</em>
          </strong>
          :
        </p>
        <ul>{techsFormatted}</ul>
        {result}
        {personAge}
      </div>
      <UserCard />
    </div>
  </main>
)

const copyRight = '2020'

// JSX element, footer
const Footer = () => (
  <footer>
    <div className='footer-wrapper'>
      <p>Copyright &copy;{copyRight}</p>
    </div>
  </footer>
)

// JSX element, app
const app = () => (
  <div className='app'>
    <Header />
    <Main />
    <Footer />
  </div>
)

// we render the App component using the ReactDOM package
ReactDOM.render(<App />, rootElement)


// Further on Functional components
// コンポーネントにスタイルを当てることもできる
const buttonStyles = {
  padding: '10px 20px',
  background: 'rgb(0, 255, 0',
  border: 'none',
  borderRadius: 5,
}
const Button = () => <button style={buttonStyles}> action </button>
```

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

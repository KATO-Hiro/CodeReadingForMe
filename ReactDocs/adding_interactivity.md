# Adding Interactivity

## 現状

+ 。

+ 。

## 目標

+ 。

+ 。

## 技術選定の理由

+ 。

+ 。

## Responding to events

+ ReactにJSXにイベントハンドラを追加できる
  + イベントハンドラとは、クリック、ホバー、フォーム入力へのフォーカスなどのユーザーインタラクションに反応してトリガーされる独自の関数
+ <button>のような組み込みコンポーネントは、onClickのような組み込みブラウザ・イベントのみをサポートします。しかし、独自のコンポーネントを作成し、そのイベントハンドラプロップに任意のアプリケーション固有の名前を付けることもできます。

```jsx
// 最上位のコンポーネントでonHogeを指定しているのはなぜ?
// このサンプルでは、Toolbarで定義しても動作するのでは?
export default function App() {
  return (
    <Toolbar
      onPlayMovie={() => alert('Playing!')}
      onUploadImage={() => alert('Uploading!')}
    />
  );
}

// onClickに自分で指定したイベントを流す
function Toolbar({ onPlayMovie, onUploadImage }) {
  return (
    <div>
      <Button onClick={onPlayMovie}>
        Play Movie
      </Button>
      <Button onClick={onUploadImage}>
        Upload Image
      </Button>
    </div>
  );
}

// buttonタグのwrapper
function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

### State: a component’s memory

+ useState Hookを使って、コンポーネントにステートを追加できる

```jsx
import { useState } from 'react';
import { sculptureList } from './data.js';

export default function Gallery() {
  // stateName, setStateName = useState(initialValue);
  const [index, setIndex] = useState(0);
  const [showMore, setShowMore] = useState(false);
  const hasNext = index < sculptureList.length - 1;

  // handleHogeActionという名称が慣例になっているっぽい
  function handleNextClick() {
    if (hasNext) {
      // setHogeで更新
      setIndex(index + 1);
    } else {
      setIndex(0);
    }
  }

  function handleMoreClick() {
    setShowMore(!showMore);
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleNextClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i>
        by {sculpture.artist}
      </h2>
      <h3>
        ({index + 1} of {sculptureList.length})
      </h3>
      <button onClick={handleMoreClick}>
        {showMore ? 'Hide' : 'Show'} details
      </button>
      {showMore && <p>{sculpture.description}</p>}
      <img
        src={sculpture.url}
        alt={sculpture.alt}
      />
    </>
  );
}
```

### Render and commit

+ レンダリングの仕組み
  1. Trigger
  2. Render
  3. Commit

### State as a snapshot

+ 通常のJavaScript変数とは異なり、Reactのステートはスナップショットのように振る舞う。これを設定しても、すでに持っているステート変数は変更されず、代わりに再レンダリングがトリガーされる。
  + メリット: 微妙なバグを回避するのに役立つ

### Queueing a series of state updates

+ ステートを設定すると新しい再レンダリングが要求されるが、すでに実行されているコードでは変更されない。

```jsx
import { useState } from 'react';

export default function Counter() {
  const [score, setScore] = useState(0);

  function increment() {
    // イベントハンドラで複数回指定しても、1ずつしか増えない
    // setScore(score + 1);
    setScore(s => s + 1);
  }

  return (
    <>
      <button onClick={() => increment()}>+1</button>
      <button onClick={() => {
        increment();
        increment();
        increment();
      }}>+3</button>
      <h1>Score: {score}</h1>
    </>
  )
}
```

### Updating objects in state

+ ステートには、オブジェクトを含め、あらゆる種類のJavaScriptの値を保持できる。しかし、Reactステートに保持するオブジェクトや配列を直接変更してはいけない。代わりに、オブジェクトや配列を更新したいときは、新しいものを作成し（または既存のもののコピーを作成し）、そのコピーを使用するようにステートを更新する必要がある。

+ 通常、...spread構文を使用して、変更したいオブジェクトや配列をコピーします。例えば、ネストしたオブジェクトを更新する場合は次のようになります：

```jsx
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  // 元々の変数の前に...を追加
  // 更新する部分のみを指定
  function handleNameChange(e) {
    setPerson({
      ...person,
      name: e.target.value
    });
  }

  function handleTitleChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        title: e.target.value
      }
    });
  }

  function handleCityChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        city: e.target.value
      }
    });
  }

  function handleImageChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        image: e.target.value
      }
    });
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          onChange={handleNameChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          onChange={handleTitleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
          onChange={handleCityChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
          onChange={handleImageChange}
        />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {' by '}
        {person.name}
        <br />
        (located in {person.artwork.city})
      </p>
      <img
        src={person.artwork.image}
        alt={person.artwork.title}
      />
    </>
  );
}
```

+ Immerのようなライブラリを使うと冗長な記述を減らせる

https://github.com/immerjs/use-immer

```jsx
import { useImmer } from 'use-immer';

export default function Form() {
  const [person, updatePerson] = useImmer({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e) {
    // 更新する部分のみ記述
    updatePerson(draft => {
      draft.name = e.target.value;
    });
  }

  function handleTitleChange(e) {
    updatePerson(draft => {
      draft.artwork.title = e.target.value;
    });
  }

  function handleCityChange(e) {
    updatePerson(draft => {
      draft.artwork.city = e.target.value;
    });
  }

  function handleImageChange(e) {
    updatePerson(draft => {
      draft.artwork.image = e.target.value;
    });
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          onChange={handleNameChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          onChange={handleTitleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
          onChange={handleCityChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
          onChange={handleImageChange}
        />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {' by '}
        {person.name}
        <br />
        (located in {person.artwork.city})
      </p>
      <img
        src={person.artwork.image}
        alt={person.artwork.title}
      />
    </>
  );
}
```

#### Updating arrays in state

+ 配列もまた、JavaScriptの変更可能なオブジェクトの一種であり、stateに保存して読み取り専用として扱うことができます。オブジェクトと同様に、stateに格納された配列を更新したい場合は、新しい配列を作成し（または既存の配列をコピーし）、新しい配列を使用するようにstateを設定する必要があります：
+ オブジェクトの場合と同様に、Immerなどを使うことでシンプルに記述できる

```jsx
import { useState } from 'react';

let nextId = 3;
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];

export default function BucketList() {
  const [list, setList] = useState(
    initialList
  );

  function handleToggle(artworkId, nextSeen) {
    setList(list.map(artwork => {
      if (artwork.id === artworkId) {
        return { ...artwork, seen: nextSeen };
      } else {
        return artwork;
      }
    }));
  }

  return (
    <>
      <h1>Art Bucket List</h1>
      <h2>My list of art to see:</h2>
      <ItemList
        artworks={list}
        onToggle={handleToggle} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map(artwork => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={e => {
                onToggle(
                  artwork.id,
                  e.target.checked
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  );
}
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

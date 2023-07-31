# Managing State

+ 中級者向け
+ アプリケーションが成長するにつれて、状態をどのように整理し、コンポーネント間でデータをどのように流すかについて、より意図的に行うことが役立ちます。ステートの冗長性や重複は、バグの一般的な原因です。この章では、状態をうまく構造化する方法、状態の更新ロジックを保守しやすく保つ方法、離れたコンポーネント間で状態を共有する方法について学びます。

## 現状

+ 。

+ 。

## 目標

+ 。

+ 。

## 技術選定の理由

+ 。

+ 。

## Reacting to input with state

+ コンポーネントのさまざまな視覚的な状態（「初期状態」、「タイピング状態」、「成功状態」）に対して表示したいUIを記述し、ユーザーの入力に応じて状態の変更をトリガーします。

```jsx
// クイズフォーム
// `status` stateで、submitボタンが有効・無効の状態を表現している
import { useState } from 'react';

export default function Form() {
  const [answer, setAnswer] = useState('');
  const [error, setError] = useState(null);
  const [status, setStatus] = useState('typing');

  if (status === 'success') {
    return <h1>That's right!</h1>
  }

  async function handleSubmit(e) {
    e.preventDefault();
    setStatus('submitting');
    try {
      await submitForm(answer);
      setStatus('success');
    } catch (err) {
      setStatus('typing');
      setError(err);
    }
  }

  function handleTextareaChange(e) {
    setAnswer(e.target.value);
  }

  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form onSubmit={handleSubmit}>
        <textarea
          value={answer}
          onChange={handleTextareaChange}
          disabled={status === 'submitting'}
        />
        <br />
        <button disabled={
          answer.length === 0 ||
          status === 'submitting'
        }>
          Submit
        </button>
        {error !== null &&
          <p className="Error">
            {error.message}
          </p>
        }
      </form>
    </>
  );
}

function submitForm(answer) {
  // Pretend it's hitting the network.
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let shouldError = answer.toLowerCase() !== 'lima'
      if (shouldError) {
        reject(new Error('Good guess but a wrong answer. Try again!'));
      } else {
        resolve();
      }
    }, 1500);
  });
}
```

## Choosing the state structure

+ ステートをうまく構造化することで、修正やデバッグが快適なコンポーネントが生まれます。最も重要な原則は、ステートには冗長な情報や重複した情報を含めるべきでない。

## Sharing state between components

+ 2つのコンポーネントの状態を常に一緒に変更したいことがある。そのためには、2つのコンポーネントからステートを削除し、最も近い共通の親コンポーネントに移動させ、propsを介して2つのコンポーネントに渡す。これは「ステートを持ち上げる」と呼ばれる。

## Preserving and resetting state

+ コンポーネントを再レンダリングするとき、Reactはツリーのどの部分を残し（更新し）、どの部分を破棄するか、ゼロから再作成するかを決定する必要がある。ほとんどの場合、Reactの自動動作は十分に機能する。デフォルトでは、Reactは、以前にレンダリングされたコンポーネントツリーと「一致する」ツリーの部分を保持します。
+ Reactでは、デフォルトの動作をオーバーライドして、<Chat key={fuga} />のように別のキーを渡すことで、コンポーネントの状態を強制的にリセットすることができます。

## Extracting state logic into a reducer

+ 多くの状態更新が多くのイベント・ハンドラにまたがっているコンポーネントは、圧倒されることがあります。このような場合、コンポーネントの外部にあるすべての状態更新ロジックを、"reducer "と呼ばれる単一の関数に集約することができます。ユーザーの "アクション "だけを指定するので、イベントハンドラは簡潔になります。ファイルの一番下にあるreducer関数は、各アクションに対してどのように状態を更新するかを指定します！

```jsx
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  // const [foo, dispatch] = useReducer(fooReducer関数、初期値)
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId
    });
  }

  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask
        onAddTask={handleAddTask}
      />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  { id: 0, text: 'Visit Kafka Museum', done: true },
  { id: 1, text: 'Watch a puppet show', done: false },
  { id: 2, text: 'Lennon Wall pic', done: false }
];
```

## Passing data deeply with context

+ 通常、親コンポーネントから子コンポーネントへは、propsを介して情報を渡します。
+ 課題: しかし、多くのコンポーネントにpropsを渡す必要がある場合や、多くのコンポーネントが同じ情報を必要とする場合には、propsを渡すことが不便になることがあります。
+ 解決方法: Contextは、親コンポーネントがその下にあるツリーのどのコンポーネントに対しても、それがどんなに深いコンポーネントであっても、明示的にpropsを通して情報を渡すことなく、何らかの情報を利用できるようにします。

## Scaling up with reducer and context

+ Reducersは、コンポーネントの状態更新ロジックを統合します。コンテキストは、他のコンポーネントに情報を渡すことができます。複雑な画面の状態を管理するために、リデューサーとコンテキストを一緒に組み合わせることができます。

+ このアプローチでは、複雑な状態を持つ親コンポーネントが、リデューサーでそれを管理します。ツリーの奥深くにある他のコンポーネントは、コンテキストを介してその状態を読み取ることができます。また、その状態を更新するアクションをディスパッチすることもできます。

## 疑問点

+ 。

+ 。

## 感想

+ 現時点では、トピックの見出しを流し読みしている程度。実際に必要になったタイミングで詳細な内容を読む。

+ 。

## 抽象化

+ 。

+ 。

## 応用

+ 。

+ 。

## See

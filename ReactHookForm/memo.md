# 「React Hook Form」

## 現状

+ ドキュメントを見ればフォームはなんとか作れそうな気がする。

+ 。

## 目標

+ 必要最小限の設定で実装したい。

+ 。

## 技術選定の理由

+ 。

+ 。

## インストール

```terminal
pnpm install react-hook-form
```

## Quick Start

```ts
// ライブラリをインポート
import { useForm, SubmitHandler } from "react-hook-form"


// 型を宣言
// type 名称
type Inputs = {
  // 属性: 型
  example: string
  exampleRequired: string
}


export default function App() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors },
  } = useForm<Inputs>()
  const onSubmit: SubmitHandler<Inputs> = (data) => console.log(data)


  console.log(watch("example")) // watch input value by passing the name of it


  return (
    // onSubmitでイベントを管理
    /* "handleSubmit" will validate your inputs before invoking "onSubmit" */
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* register your input into the hook by invoking the "register" function */}
      <input defaultValue="test" {...register("example")} />


      {/* include validation with required or other standard HTML validation rules */}
      <input {...register("exampleRequired", { required: true })} />
      // エラーメッセージを表示
      {/* errors will return when field validation fails  */}
      {errors.exampleRequired && <span>This field is required</span>}


      // 送信用のボタン
      <input type="submit" />
    </form>
  )
}
```

> https://react-hook-form.com/get-started#Quickstart

+ Q: ...registerは何をしている?
  + A: onChange, onBlur, name, refをそれぞれinputタグに代入
    + これによってタイプ量を減らす + (ルールを理解していれば)簡単に読める

```js
const { onChange, onBlur, name, ref } = register('firstName'); 
// include type check against field path with the name you have supplied.
        
<input 
  onChange={onChange} // assign onChange event 
  onBlur={onBlur} // assign onBlur event = 要素からカーソルが外れたとき
  name={name} // assign name prop
  ref={ref} // assign ref prop
/>
// same as above
<input {...register('firstName')} />
```

> https://react-hook-form.com/docs/useform/register

// フォームの情報を使ってAPIを叩けるらしい

## バリデーションライブラリ Zodの導入

### インストール方法

```terminal
pnpm add zod @hookform/resolvers
```

### 基本的な書き方

```ts
import { z } from "zod";
import { zodResolver } from "@hookform/resolvers/zod";

const schema = z.object({
  username: z.string(),
});

const {
  register,
  handleSubmit,
  formState: { errors },
} = useForm({
  resolver: zodResolver(schema),
});
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

https://reffect.co.jp/react/react-hook-form

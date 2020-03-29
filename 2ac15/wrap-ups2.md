지난 시간에는 배열에 값을 추가하고 보여주는 작업까지 했는데, 단순히 버튼을 만들기만 하고 onClick을 연결하면 유저가 '엔터를 눌렀을때'라는 이벤트를 따로 만들어줘야 해서 불편합니다. 따라서 연관된 input과 button을 form으로 묶어주고 form에 onSubmit이라는 이벤트를 달아봅시다.

```js
<form onSubmit={this.handleInsert}>
    <input
        name="username"
        value={username}
        onChange={this.handleChange}
    />
    <input
        name="password"
        value={password}
        onChange={this.handleChange}
    />
    <button type="submit">추가</button>
</form>
```




# Redux
JS로 만든 어플리케이션을 위한, 예측 가능한 상태의 저장소

눈에 보이지 않는 ‘복잡성’을 낮추어 코드가 어떤 결과를 가져올지 예측 가능하게 만들어준다.

**Single Source of Truth**

상태 → 객체

## Concept

1. 하나의 객체 안에 어플리케이션에 필요한 모든 데이터를 넣는다.

2. 외부로부터 철저히 차단시킨다. (직접 수정하거나 읽을 수 없다.)

    - getState를 사용해서 읽을 수 있다.
    - 예기치 않은 state의 변경을 사전에 차단하여 예측 가능하도록 한다.
    - state의 값이 바뀔때 마다 그 state를 사용하는 부품들에게 전달해준다.

   각각의 state 값을 생성할 때 철저하게 통제하고 데이터를 수정할 때 원본을 사용하지 않고 복제본 수정하기 때문에  각각의 상태 변화가 서로에게 영향을 주지 않는 독립적인 상태이기 때문에 Undo, Redo를 쉽게 할 수 있다.

   debuger에서 현재의 상태 뿐만 아니라 과거의 상태도 볼 수 있기 때문에 문제 해결에 도움을 준다.

   module reloading → hot module reloading을 사용하면 앱은 리로딩되지만 데이터는 남는다.


---

### State와 Render의 관계

<aside>
💡 Render - getState를 사용하여 store에서 **state 값을 참조하여 ui를 만든다.**
Subscribe - subscribe에 render 함수를 등록하면 store의 state 값이 바뀔때마다 render를 호출한다.

</aside>

---

### Action과 Reducer

<aside>
💡 Action - Dispatch에 전달되는 객체
Reducer - state를 입력값으로 받고 action을 참조하여 새로운 state를 반환하는 가공자
Dispatch - Subscribe에 등록된 render 함수를 모두 호출한다.

</aside>

---

## Redux가 좋은 가장 중요한 이유

### 1. 중앙 집중적인 데이터 스토어

같은 어플리케이션을 구현하는 방식을 비교해본다.

1. Redux 사용하지 않음

    - 부품이 2개라면 자신을 바꾸는 로직 + 다른 부품을 바꾸는 로직 = 각 2개씩 4개의 로직이 필요하다.
    - 3개라면? 9개, 4개라면? 16개, 100개라면 10000개... 기하급수적으로 늘어난다.
    - 각각의 부품이 강하게 연결되어있기 때문에 하나의 전체의 일부로 보는 것이 타당하다.
    - 하나의 부품에만 집중하지 못하고 다른 쪽으로 분산된다.

2. Redux 사용

    - 부품의 상태가 달라졌을 때 redux의 store에 데이터가 변경되었음을 알려주는 로직이 있다.
    - Store는 자신을 구독(subscribe)하고 있는 부품들에 상태의 변화를 전달한다.
    - 각각의 부품들은 상태가 바뀌면 어떻게 업데이트하는지 알고있기 때문에 각자 업데이트한다.
    - 각 부품의 로직 → 자기자신을 변화시키는 로직 + 상태 변화를 알려주는 로직 = 2개
    - 4 = 8, 5 = 10, 100 = 200

### 2. 시간여행

- Redux Dev Tool을 이용하면 store의 상태의 변화를 볼 수 있다.
- time traveling을 이용하면 현재 어플리케이션의 상태를 json파일로 저장하고 그 파일을 임포트하면 그 상태의 모습을 보여준다.

---

## 차이점

1. redux 사용하지 않음

![스크린샷 2022-01-24 11 37 14](https://user-images.githubusercontent.com/73153617/150920992-85dab767-c7ec-4626-9bba-2f872b7e79fc.png)


1. redux 사용함

![스크린샷 2022-01-24 14 03 46](https://user-images.githubusercontent.com/73153617/150921004-bc70848b-2273-4863-8bb2-15615740b73e.png)


<aside>
💡 1. store를 만든다.
2. reducer를 만들고 state 초기 값을 설정한다.
3. getState로 state 값을 받아온다.

</aside>

---

### Reducer

**이전의 state값과 action을 받아서 다음의 state값을 반환한다.**

```jsx
function reducer(state, action){
        console.log(state, action)
        if(state === undefined){
            // initializing
            return {color:'orange'}
        }
				if(action.type === 'CHANGE_COLOR'){
						state.color = 'red'
				}
        return state
  }
```

---

위 처럼 사용하면 이전의 state에 덮어씌우기 때문에 redux의 혜택을 누릴 수 없다.

<aside>
💡 state를 복제하여 복사본의 값을 바꿔주는 방법을 사용한다.
`Object.assign({}, prevState, state)`

</aside>

```jsx
function reducer(state, action){
        if(state === undefined){
            // initializing
            return {color:'orange'}
        }
        let newState
        if(action.type === 'CHANGE_COLOR'){
							// state 복제하고 새로운 state로 변경
            newState = Object.assign({}, state, {color:'red'})
        }
        return newState
  }
```

---

<aside>
💡 Reducer는 action의 값과 이전의 state 값을 이용하여 새로운 state를 반환한다.
반환된 값은 이전의 값을 복제한 값을 반환하여야 redux의 이점을 살릴 수 있다.

</aside>

---

### Subscribe, Dispatch

- **Dispatch → reducer를 호출하고 이전 state와 action 값을 전달한다.**
- **Subscribe → store의 변화를 감지하여 인자인 함수를 실행한다.**

```jsx
function red() {
        const state = store.getState()
        document.querySelector('#red').innerHTML = `
            <div class='container' id="component_red" style="background-color:${state.color}">
                <h1>red</h1>
                <input type="button" value="fire"
                    onClick="
                        store.dispatch({type: 'CHANGE_COLOR', color: 'red'})
                    "
                />
            </div>
	      `
}
store.subscribe(red)
```

---

### 결론

<aside>
💡 Redux를 사용하지 않았을 경우에는 각 컴포넌트가 커플링되어 있어 서로를 알고 있어야한다.
하나의 부품에 문제가 생겼을 경우 에러가 발생하고 새로운 컴포넌트가 생성되었을 경우 모두에게 알려주어야 제대로 작동한다.
하지만, redux를 사용하면 store라는 중개자를 통해 state를 중앙집중적으로 관리하여 각각의 컴포넌트들은 상태가 변경(action을 store에 dispatch)되면 어떻게 변화되어야 하는지에 대한 코드를 store에 구독(subscribe)해놓으면 store가 바뀔때마다 통보를 받아 자신을 변경시킨다.
그렇게 컴포넌트들이 서로 알 필요가 없이 자신에만 집중하도록 디커플링 된다.
</aside>

---

## Redux Dev Tool

## time traveling

<img width="1789" alt="스크린샷 2022-01-24 15 17 09" src="https://user-images.githubusercontent.com/73153617/150921302-1236625c-ec84-47ed-a56e-c6cd906a57ed.png">
<img width="1789" alt="스크린샷 2022-01-24 15 17 26" src="https://user-images.githubusercontent.com/73153617/150921324-72911d1f-fb2c-431f-9076-61a9b77bd525.png">


---

## CRUD App

<img width="25%" alt="스크린샷 2022-01-25 13 15 34" src="https://user-images.githubusercontent.com/73153617/150921395-4906e3b4-4c50-4a29-8edc-2cc8137032f7.png"><img width="25%" alt="스크린샷 2022-01-25 13 16 04" src="https://user-images.githubusercontent.com/73153617/150921397-03107bf1-114d-400d-9f6e-a69389206d2f.png"><img width="25%" alt="스크린샷 2022-01-25 13 16 11" src="https://user-images.githubusercontent.com/73153617/150921398-7dc6061c-6c28-4d75-bd86-5563c73e5ffe.png"><img width="25%" alt="스크린샷 2022-01-25 13 15 29" src="https://user-images.githubusercontent.com/73153617/150921401-fa7ea3f7-7552-4a63-b4da-818757ad40fb.png">

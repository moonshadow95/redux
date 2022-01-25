# Redux
JS로 만든 어플리케이션을 위한, 예측 가능한 상태의 저장소

눈에 보이지 않는 ‘복잡성’을 낮추어 코드가 어떤 결과를 가져올지 예측 가능하게 만들어준다.

**Single Source of Truth**

상태 → 객체

## Concept

1. 하나의 객체 안에 어플리케이션에 필요한 모든 데이터를 넣는다.

   ![스크린샷 2022-01-21 16.36.01.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b4b1548a-128c-40bf-9707-34ed9591e436/스크린샷_2022-01-21_16.36.01.png)

2. 외부로부터 철저히 차단시킨다. (직접 수정하거나 읽을 수 없다.)

   ![스크린샷 2022-01-21 16.48.10.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b8320866-0c56-4da8-8ece-0a61be5ed202/스크린샷_2022-01-21_16.48.10.png)

    - getState를 사용해서 읽을 수 있다.
    - 예기치 않은 state의 변경을 사전에 차단하여 예측 가능하도록 한다.
    - state의 값이 바뀔때 마다 그 state를 사용하는 부품들에게 전달해준다.

   각각의 state 값을 생성할 때 철저하게 통제하고 데이터를 수정할 때 원본을 사용하지 않고 복제본 수정하기 때문에  각각의 상태 변화가 서로에게 영향을 주지 않는 독립적인 상태이기 때문에 Undo, Redo를 쉽게 할 수 있다.

   debuger에서 현재의 상태 뿐만 아니라 과거의 상태도 볼 수 있기 때문에 문제 해결에 도움을 준다.

   module reloading → hot module reloading을 사용하면 앱은 리로딩되지만 데이터는 남는다.


---

### State와 Render의 관계

![스크린샷 2022-01-21 17.27.38.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/223bd8ec-742c-4aff-9b6d-9cd4ef66cecb/스크린샷_2022-01-21_17.27.38.png)

<aside>
💡 Render - getState를 사용하여 store에서 **state 값을 참조하여 ui를 만든다.**
Subscribe - subscribe에 render 함수를 등록하면 store의 state 값이 바뀔때마다 render를 호출한다.

</aside>

---

### Action과 Reducer

![스크린샷 2022-01-21 17.41.22.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/819220c4-3672-4e15-b242-285e9cb47a35/스크린샷_2022-01-21_17.41.22.png)

<aside>
💡 Action - Dispatch에 전달되는 객체
Reducer - state를 입력값으로 받고 action을 참조하여 새로운 state를 반환하는 가공자
Dispatch - Subscribe에 등록된 render 함수를 모두 호출한다.

</aside>

---

## Redux가 좋은 가장 중요한 이유

### 1. 중앙 집중적인 데이터 스토어

![스크린샷 2022-01-24 10.36.55.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f557f75d-2816-43bb-9863-c6880104c90b/스크린샷_2022-01-24_10.36.55.png)

위와 같은 어플리케이션을 구현하는 방식을 비교해본다.

1. Redux 사용하지 않음

   ![스크린샷 2022-01-24 10.39.38.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4bbe3ac6-5fe0-4de9-b5da-2fef68b6b601/스크린샷_2022-01-24_10.39.38.png)

    - 부품이 2개라면 자신을 바꾸는 로직 + 다른 부품을 바꾸는 로직 = 각 2개씩 4개의 로직이 필요하다.
    - 3개라면? 9개, 4개라면? 16개, 100개라면 10000개... 기하급수적으로 늘어난다.
    - 각각의 부품이 강하게 연결되어있기 때문에 하나의 전체의 일부로 보는 것이 타당하다.
    - 하나의 부품에만 집중하지 못하고 다른 쪽으로 분산된다.
2. Redux 사용

   ![스크린샷 2022-01-24 10.44.19.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6a545e0e-e2ce-401e-81f4-ddfe02960fdb/스크린샷_2022-01-24_10.44.19.png)

    - 부품의 상태가 달라졌을 때 redux의 store에 데이터가 변경되었음을 알려주는 로직이 있다.
    - Store는 자신을 구독(subscribe)하고 있는 부품들에 상태의 변화를 전달한다.
    - 각각의 부품들은 상태가 바뀌면 어떻게 업데이트하는지 알고있기 때문에 각자 업데이트한다.
    - 각 부품의 로직 → 자기자신을 변화시키는 로직 + 상태 변화를 알려주는 로직 = 2개
    - 4 = 8, 5 = 10, 100 = 200

### 2. 시간여행

![스크린샷 2022-01-24 10.51.30.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3440b8dd-94b9-4e92-b55d-aa85ab4f85ad/스크린샷_2022-01-24_10.51.30.png)

- Redux Dev Tool을 이용하면 store의 상태의 변화를 볼 수 있다.
- time traveling을 이용하면 현재 어플리케이션의 상태를 json파일로 저장하고 그 파일을 임포트하면 그 상태의 모습을 보여준다.

---

## 차이점 보기

1. redux 사용하지 않음

![스크린샷 2022-01-24 11.37.14.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f3bcb47e-02d2-444b-8148-bc302590aa46/스크린샷_2022-01-24_11.37.14.png)

1. redux 사용함

   ![스크린샷 2022-01-24 14.03.46.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a2d2e480-9e4f-4b4a-9b4b-62c16d4c0fa5/스크린샷_2022-01-24_14.03.46.png)


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

![스크린샷 2022-01-24 15.17.09.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ed5d6d3b-580c-431d-86b8-b2528a9f5232/스크린샷_2022-01-24_15.17.09.png)

![스크린샷 2022-01-24 15.17.26.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/43d292ef-df21-4381-a1f8-fb4faafc9fb1/스크린샷_2022-01-24_15.17.26.png)

---

## CRUD App

![스크린샷 2022-01-25 13.15.29.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5552777c-db3d-4774-a337-b7b8dcfc5acc/스크린샷_2022-01-25_13.15.29.png)

![스크린샷 2022-01-25 13.15.34.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/df8cdc72-116a-4a3a-8ef7-b2bc40122dee/스크린샷_2022-01-25_13.15.34.png)

![스크린샷 2022-01-25 13.16.04.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/adc0eca6-71b8-4f61-8f93-b4a2abcb13ce/스크린샷_2022-01-25_13.16.04.png)

![스크린샷 2022-01-25 13.16.11.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8626c9ad-dd02-4ad3-9813-17bd2d4624ca/스크린샷_2022-01-25_13.16.11.png)
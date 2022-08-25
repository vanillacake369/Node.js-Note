# Foreign Key / Source Key / Target Key

Created: 2022년 8월 19일 오후 4:34
Last Edited Time: 2022년 8월 23일 오후 3:23
State: Done
Tag: db, node.js, sequelize

# Foreign Key / Source Key / Target Key

---

1:1, 1:N, M:N 관계는 이해하고 있을 것이다. ~~모르면 찾아봐라;;~~

공식문서를 찾아보면 source model과 target model에 대해 찾아볼 수 있다. 아래와 같이 적혀있다.

```jsx
A.hasOne(B, { /* options */ });
A.belongsTo(B, { /* options */ });
A.hasMany(B, { /* options */ });
A.belongsToMany(B, { through: 'C', /* options */ });
```

<aside>
📄 The order in which the association is defined is relevant. In other words, the order matters, for the four cases. In all examples above, `A` is called the **source** model and `B` is called the **target** model. This terminology is important.
연관관계가 정의된 순서는 매우 중요하다. 네 가지 경우 모두 정의 순서에 따라 의미가 상의한데, 위 예제에서는 A를 소스모델, B를 타겟모델이라고 말한다. 이 정의는 매우 중요하다.

</aside>

<aside>
📄 The `A.hasOne(B)` association means that a One-To-One relationship exists between `A` and `B`, with the foreign key being defined in the target model (`B`).

The `A.belongsTo(B)` association means that a One-To-One relationship exists between `A` and `B`, with the foreign key being defined in the source model (`A`).

The `A.hasMany(B)` association means that a One-To-Many relationship exists between `A` and `B`, with the foreign key being defined in the target model (`B`).

These three calls will cause Sequelize to automatically add foreign keys to the appropriate models (unless they are already present).

A가 B 하나를 가지는 `hasOne()`연관관계에서는 B라는 타겟모델의 외래키로 1:1 관계를 정의한다.

A가 B에 속하는 `belongsTo()`연관관계에서는 A라는 소스모델의 외래키로 1:1 관계를 정의한다.

A가 여러 개의 B를 가지는 `hasMany()`연관관계에서는 B라는 타겟모델의 외래키로 1:N관계를 정의한다.

**위 세 가지 호출은 시퀄라이즈로 하여금 적합한 모델과의 외래키를 자동으로 생성하게 한다.**(적합한 모델이 존재한다면 말이다)

</aside>

요약을 하자면 이거다.

- **주인이 곧 source model 이며 주인이 여러 모델을 가지면 자식 모델들을 target model이라고 부른다.**

> A                —- (has) —>            B1
”source”                                       “target”
> 
> 
>       |—————— (has) —>             B2
>                                                     “target”
> 
>       |—————— (has) —>             B3
>                                                     “target”
> 
- **시퀄라이즈로 하여금 적합한 모델과의 외래키를 자동으로 생성하게 한다**

# MariaDB errno: 150 "Foreign key constraint is incorrectly formed”

---

MariaDB에 외래키를 적용하다가 errno: 150 "Foreign key constraint is incorrectly formed" 이러한 오류가 날 경우가 있습니다.

[Foreign%20Key%20Source%20Key%20Target%20Key%20c6644c417285420cbdcf6fe00d7cddd2/%EC%95%8C%20%EC%88%98%20%EC%97%86%EB%8A%94%20%ED%8C%8C%EC%9D%BC](Foreign%20Key%20Source%20Key%20Target%20Key%20c6644c417285420cbdcf6fe00d7cddd2/%EC%95%8C%20%EC%88%98%20%EC%97%86%EB%8A%94%20%ED%8C%8C%EC%9D%BC)

오류가 나는 이유는 외래키와 메인키 컬럼의 설정이 제대로 일치하지 않아서 나는 경우입니다.

이때 확인해야 할 순서입니다.

> 1) 순서
> 

1. 데이터에 타입이 같은지?

메인키가 INT면 외래키도 INT여야 합니다.

2. 메인키와 외래키 모두 NOT NULL 체크가 되어 있는지?

3. 참조하는 키(Reference Key)가 메인키 혹은 유니크 키로 되어 있는지?

여기까지하고 안되는 경우엔 문법이 틀리거나 올바르지 않은 테이블의 컬럼을 바라보는 경우가 많습니다!

> 2) 결과
> 

LoadType을 정의한 LoadType이라는 테이블과

LoadType을 월별로 기록한 Month이라는 테이블을 생성해서

Month의 TYPE_CODE가 LoadType의 TYPE_CODE를 바라보게 설정했습니다.

# Reference

---

1. [https://narup.tistory.com/93](https://narup.tistory.com/93)
2. [https://sequelize.org/docs/v6/core-concepts/assocs/](https://sequelize.org/docs/v6/core-concepts/assocs/)
3. [https://jsdysw.tistory.com/275](https://jsdysw.tistory.com/275)
4. [https://sequelize.org/docs/v6/core-concepts/validations-and-constraints/](https://sequelize.org/docs/v6/core-concepts/validations-and-constraints/)
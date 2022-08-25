# Sequelize 사용하여 ORM 구현하기

Created: 2022년 8월 9일 오전 3:16
Last Edited Time: 2022년 8월 24일 오후 10:09
State: Done
Tag: node.js, sequelize

# Sequelize란?

---

DB 작업을 쉽게 할 수 있도록 도와주는 ORM 라이브러리.

**즉, ORM은 자바스크립트 객체와 관계형 데이터베이스를 서로 연결해주는 도구입니다.**

# 설치

---

- sequelize
- sequelize-cli
- mysql2

```jsx
npm install sequelize sequelize
```

```jsx
sequelize init
```

# config.json

---

설치 이후 config.json을 통해 mysql에 대한 설정을 지정해주면 된다. 아래는 config.json 파일이다.

```jsx
{
  "development": {
    "username": "root", //DB와 연결할 유저 "이름"
    "password": null, //DB와 연결할 유저 "비밀번호"
    "database": "Ryan", //사용할 Database 이름
    "host": "127.0.0.1", //DB 서버 호스트
    "dialect": "mysql" //DB 타입 설정 (mysql이 아니면 다른 DB 설정)
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}
```

# index.js

---

생성한 모델들을 데이터베이스 고도화 하는 작업 파일로서, 데이터베이스 모델을 추가하거나 정의한 연관관계를 지정 시 사용한다. 다음 순서대로 동작한다.

1. //config/config.js 파일에 있는 정보를 가져와 sequelize 객체를 생성한다.
2. 작성한 Table파일을 찾아온다. // fs모듈을 통해 읽은 뒤, forEach문을 통해 db 객체에 삽입
3. db에 모델 연관관계 정의
4. 

```jsx
'use strict';

const fs = require('fs');
const path = require('path');
const Sequelize = require('sequelize');
const basename = path.basename(__filename);
const env = process.env.NODE_ENV || 'development';
const config = require(__dirname + '/../../../config/config.json')[env];
const db = {};

let sequelize;

//config/config.js 파일에 있는 정보를 가져와 sequelize 객체를 생성한다.
if (config.use_env_variable) {
  sequelize = new Sequelize(process.env[config.use_env_variable], config);
} else {
  sequelize = new Sequelize(config.database, config.username, config.password, config);
}

// 우리가 작성한 Table파일을 찾아온다.
fs
  .readdirSync(__dirname)
  .filter(file => {
    return (file.indexOf('.') !== 0) && (file !== basename) && (file.slice(-3) === '.js');
  })
  .forEach(file => {
    const model = require(path.join(__dirname, file))(sequelize, Sequelize.DataTypes);
    db[model.name] = model;
  });

//DB에 모델이름을 연결한다.
Object.keys(db).forEach(modelName => {

  if (db[modelName].associate) {
    db[modelName].associate(db);
  }
});

db.sequelize = sequelize;
db.Sequelize = Sequelize;

module.exports = db;
```

1. //config/config.js 파일에 있는 정보를 가져와 sequelize 객체를 생성한다.

```jsx
//config/config.js 파일에 있는 정보를 가져와 sequelize 객체를 생성한다.
if (config.use_env_variable) {
  sequelize = new Sequelize(process.env[config.use_env_variable], config);
} else {
  sequelize = new Sequelize(config.database, config.username, config.password, config);
}
```

# Reference

---

1. [https://baeharam.netlify.app/posts/Node.js/Node.js-Sequelize-다루기](https://baeharam.netlify.app/posts/Node.js/Node.js-Sequelize-%EB%8B%A4%EB%A3%A8%EA%B8%B0)
2. [https://lgphone.tistory.com/86](https://lgphone.tistory.com/86)
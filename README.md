# clean-code-javascript

## Содержание

1. [Введение](#введение)
2. [Переменные](#переменные)
3. [Функции](#функции)
4. [Objects and Data Structures](#objects-and-data-structures)
5. [Classes](#classes)
6. [SOLID](#solid)
7. [Testing](#testing)
8. [Concurrency](#concurrency)
9. [Error Handling](#error-handling)
10. [Formatting](#formatting)
11. [Comments](#comments)
12. [Translation](#translation)

## Введение

![Юмористическое изображение оценки качества программного обеспечения как счет того, сколько ругательств приходится кричать при чтении кода](https://www.osnews.com/images/comics/wtfm.jpg)


Принципы разработки программного обеспечения, из книги Роберта Мартина [_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), адаптированные для JavaScript. Это не руководство по стилю. Это руководство по созданию [читабельного, переиспользуемого и рефакторизированного](https://github.com/ryanmcdermott/3rs-of-software-architecture) программного обеспечения на JavaScript.




Не каждый изложенный здесь принцип должен строго соблюдаться, и тем более нет нужды насаждать их повсеместно. Это руководящие принципы и не более того, но они систематизированы и проверены многолетним коллективным опытом авторов _Clean Code_.




Специальности по разработке программного обеспечения чуть более 50 лет, и мы все еще многому учимся. Когда архитектура программного обеспечения так же стара, как сама архитектура, возможно, у нас будут более жесткие правила. На данный момент пусть эти рекомендации служат пробным камнем, с помощью которого можно оценить качество кода JavaScript, который вы и ваша команда создаете.





Еще одна вещь: знание этого материала не сделают вас сразу лучшим разработчиком программного обеспечения, и работа с ними в течение многих лет не означает, что вы не допустите ошибок. Каждый фрагмент кода начинается как первый набросок, как будто влажная глина превращается в окончательную форму. Наконец, мы отсеиваем недостатки, когда рассматриваем это с нашими коллегами. Не мучайте себя за первые черновики, которые нуждаются в улучшении. Потрясите код вместо этого!






## **Переменные**

### Используйте значимые и произносимые имена переменных

**Плохо:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Хорошо:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ к содержанию](#содержание)**

### Используйте один и тот же словарь для одного и того же типа переменной

**Плохо:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Хорошо:**

```javascript
getUser();
```

**[⬆ к содержанию](#содержание)**

### Используйте подходящие для поиска имена

В будущем мы будем читать еще больше кода, чем когда-либо. Важно, чтобы код, который мы пишем, был читабелен и доступен для поиска. _Не_ называя переменные (избегая переменных), которые оказываются значимыми для понимания нашей программы, мы наносим вред нашим читателям. Сделайте ваши имена доступными для поиска. Инструменты как
[buddy.js](https://github.com/danielstjules/buddy.js) and
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
могут помочь определить безымянные константы.




**Плохо:**

```javascript
// Какого черта, что за 86400000?
setTimeout(blastOff, 86400000);
```

**Хорошо:**

```javascript
// Объявляем их как прописные именованные константы.
const MILLISECONDS_IN_A_DAY = 86_400_000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```

**[⬆ к содержанию](#содержание)**

### Используйте пояснительные переменные

**Плохо:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**Хорошо:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

**[⬆ к содержанию](#содержание)**

### Избегайте ментального мапинга

Явное лучше, чем неявное.

**Плохо:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Подожди, что за `l`? Опять?
  dispatch(l);
});
```

**Хорошо:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ к содержанию](#содержание)**

### Не добавляйте ненужный контекст

Если ваше имя класса/объекта говорит вам что-то, не повторяйте это в имени вашей переменной или свойства.


**Плохо:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car) {
  car.carColor = "Red";
}
```

**Хорошо:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car) {
  car.color = "Red";
}
```

**[⬆ к содержанию](#содержание)**

### Используйте аргументы по умолчанию вместо неявной проверки или условий

Аргументы по умолчанию часто чище, чем неявная проверка. Имейте в виду, что если вы используете их, ваша функция будет предоставлять значения по умолчанию только для аргументов `undefined`. Другие "ложные" значения, такие как `''`, `" "`, `false`, `null`, `0` и `NaN`, не будут заменены значением по умолчанию.




**Плохо:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**Хорошо:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ к содержанию](#содержание)**

## **Функции**

### Аргументы функции (в идеале 2 или меньше)

Ограничение количества параметров функции невероятно важно, потому что это облегчает тестирование вашей функции. Наличие более трех аргументов приводит к комбинаторному взрыву, когда вам приходится тестировать множество различных случаев с каждым отдельным аргументом.




Один или два аргумента - идеальный случай, и, если возможно, следует избегать трех. Что-то большее, чем это, должно быть объединено Обычно, если у вас более двух аргументов, ваша функция пытается сделать слишком много. В тех случаях, когда это не так, в большинстве случаев общего объекта, покрывающего все параметры, достаточно в качестве аргумента.





Поскольку JavaScript позволяет создавать объекты на лету, без большого количества обвязки классов, вы можете использовать объект, если вам нужно много аргументов.



Чтобы было очевидно, какие свойства ожидает функция, вы можете использовать синтаксис деструктуризации ES2015/ES6. Это имеет несколько преимуществ:


1. Когда кто-то смотрит на сигнатуру функции, сразу становится ясно, какие свойства используются.

2. Может использоваться для имитации именованных параметров.
3. Деструктурирование также клонирует указанные примитивные значения объекта аргумента, переданного в функцию. Это может помочь предотвратить побочные эффекты. Примечание: объекты и массивы, которые деструктурированы из объекта аргумента, НЕ клонируются.



4. Линтеры могут предупредить вас о неиспользованных свойствах, которые были бы невозможны без деструктуризации.


**Плохо:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);

```

**Хорошо:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ к содержанию](#содержание)**

### Функции должны делать что-то одно

Это, безусловно, самое важное правило в разработке программного обеспечения. Когда функции выполняют больше чем одну цель, их сложнее составлять, тестировать и внедрять. Когда вы можете изолировать функцию только одним действием, ее можно легко реорганизовать, и ваш код будет читаться намного чище. Если вы не унесете ничего из этого руководства, кроме этого, вы будете впереди многих разработчиков.





**Плохо:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Хорошо:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ к содержанию](#содержание)**

### Имена функций должны говорить, что они делают

**Плохо:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// Трудно сказать по имени функции, что добавляется
addToDate(date, 1);
```

**Хорошо:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ к содержанию](#содержание)**

### Функции должны быть только одного уровня абстракции

Когда у вас есть более одного уровня абстракции, ваша функция обычно выполняет слишком много. Разделение функций приводит к повторному использованию и упрощению тестирования.



**Плохо:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // лексемы...
  });

  ast.forEach(node => {
    // парсинг...
  });
}
```

**Хорошо:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // парсинг...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ к содержанию](#содержание)**

### Удаляйте повторяющийся код

Делайте все возможное, чтобы избежать дублирования кода. Дублирующися код плох, потому что это означает, что есть несколько мест, где необходимо будет что-то изменить, если вам нужно изменить логику.



Представьте, что вы управляете рестораном и следите за своими продуктами: помидоры, лук, чеснок, специи и т.д. Если у вас есть несколько списков, которые вы ведете, то все они должны обновляться, когда вы подаете блюдо с помидорами в них. Если у вас есть только один список, есть только одно место для обновления!




Часто у вас есть дублирующийся код, потому что у вас есть две или более слегка отличающиеся друг от друга вещи, которые имеют много общего, но их различия вынуждают вас иметь две или более отдельных функции, которые выполняют много одинаковых функций. Удаление дублирующего кода означает создание абстракции, которая может обрабатывать этот набор разных вещей с помощью только одной функции/модуля/класса.





Правильное определение абстракции имеет решающее значение, поэтому вы должны следовать принципам SOLID, изложенным в разделе _Classes_. Плохие абстракции могут быть хуже, чем дублирующий код, поэтому будьте осторожны! Сказав это, если вы можете сделать хорошую абстракцию, сделайте это! Не повторяйте себя, иначе вы обнаружите, что обновляете несколько мест каждый раз, когда хотите что-то изменить.





**Плохо:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Хорошо:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ к содержанию](#содержание)**

### Установите объекты по умолчанию с помощью Object.assign

**Плохо:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Хорошо:**

```javascript
const menuConfig = {
  title: "Order",
  // Пользователь не включил ключ 'body'
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  config = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );

  // теперь config равен: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ к содержанию](#содержание)**

### Не используйте флаги в качестве параметров функции

Флаги говорят вашему пользователю, что эта функция делает больше, чем одну вещь. Функции должны делать одно. Разделите ваши функции, если они следуют разным путям кода на основе логического значения.

**Плохо:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Хорошо:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ к содержанию](#содержание)**

### Избегайте побочных эффектов (часть 1)

Функция создает побочный эффект, если она делает что-либо кроме принятия значения и возвращает другое значение или значения. Побочным эффектом может быть запись в файл, изменение некоторой глобальной переменной или случайная передача всех ваших денег незнакомцу.




Допустим, вам нужно иметь побочные эффекты в программе. Как и в предыдущем примере, вам может потребоваться запись в файл. То, что вы должны сделать, это централизовать, где вы делаете это. Не имейте несколько функций и классов, которые пишут в определенный файл. Есть один сервис, который делает это. Один и только один.




Суть в том, чтобы избежать распространенных ошибок, таких как совместное использование состояния между объектами без какой-либо структуры, использование изменяемых типов данных, которые могут быть записаны кем угодно, и не централизация того, где происходят ваши побочные эффекты. Если вы можете сделать это, вы будете счастливее, чем подавляющее большинство других программистов.




**Плохо:**

```javascript
// Глобальная переменная, на которую ссылается следующая функция.
// Если бы у нас была другая функция, которая использовала бы это имя, теперь это был бы массив, и он мог бы его сломать.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Хорошо:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ к содержанию](#содержание)**

### Избегайте побочных эффектов (часть 2)

В JavaScript примитивы передаются по значению, а объекты/массивы передаются по ссылке. В случае объектов и массивов, если ваша функция вносит изменения в массив корзины покупок, например, добавляя элемент для покупки, это дополнение повлияет на любую другую функцию, которая использует этот массив `cart`. Это может быть здорово, но может быть и плохо. Давайте представим себе плохую ситуацию:






Пользователь нажимает кнопку "Купить", которая вызывает функцию `purchase`, которая порождает сетевой запрос и отправляет массив `cart` на сервер. Из-за плохого сетевого соединения функция `purchase` должна повторять запрос. А теперь, что если пользователь случайно нажмет кнопку "Добавить в корзину" на элементе, который им на самом деле не нужен, до того, как начнется сетевой запрос? Если это произойдет, и начнется сетевой запрос, то эта функция покупки отправит случайно добавленный элемент, поскольку у него есть ссылка на массив корзины, который функция `addItemToCart` изменила, добавив нежелательный элемент.









Отличным решением было бы для `addItemToCart` всегда клонировать корзину, редактировать ее и возвращать клон. Это гарантирует, что никакие другие функции, удерживающие ссылку на корзину, не будут затронуты какими-либо изменениями.



Два предостережения при упоминании этого подхода:

1. Могут быть случаи, когда вы действительно хотите изменить входной объект, но когда вы примете эту практику программирования, вы обнаружите, что такие случаи довольно редки. Большинство вещей может быть переработано, чтобы не иметь побочных эффектов!



2. Клонирование больших объектов может быть очень дорогим с точки зрения производительности. К счастью, это не является большой проблемой на практике, потому что есть [отличные библиотеки](https://facebook.github.io/immutable-js/), которые позволяют такому подходу к программированию быть быстрым и не так интенсивно использовать память, как это было бы при  клонировании объектов и массивов вручную.





**Плохо:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Хорошо:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ к содержанию](#содержание)**

### Не пишите в глобальные функции

Загрязнение глобальных объектов является плохой практикой в ​​JavaScript, поскольку вы можете столкнуться с другой библиотекой, и пользователь вашего API будет неосведомлен, пока не получит исключение в работе. Давайте подумаем над примером: что если вы захотите расширить встроенный в JavaScript метод Array, чтобы иметь метод `diff`, который мог бы показать разницу между двумя массивами? Вы можете написать свою новую функцию в `Array.prototype`, но она может конфликтовать с другой библиотекой, которая пыталась сделать то же самое. Что если эта другая библиотека просто использовала `diff`, чтобы найти разницу между первым и последним элементами массива? Вот почему было бы намного лучше просто использовать классы ES2015/ES6 и просто расширить глобальный массив `Array`.









**Плохо:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Хорошо:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ к содержанию](#содержание)**

### Предпочитайте функциональное программирование над императивным

JavaScript не является функциональным языком, каким является Haskell, но он имеет функциональную природу. Функциональные языки могут быть чище и проще для тестирования. Порадуйте этот стиль программирования, когда сможете.



**Плохо:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Хорошо:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ к содержанию](#содержание)**

### Инкапсулируйте условные выражения

**Плохо:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Хорошо:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ к содержанию](#содержание)**

### Избегайте негативных условий

**Плохо:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Хорошо:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ к содержанию](#содержание)**

### Избегайте условных выражений

Это кажется невыполнимой задачей. Услышав это, большинство людей говорят: "Как я должен делать что-либо без выражения `if`?" Ответ в том, что вы можете использовать полиморфизм для достижения одной и той же задачи во многих случаях. Второй вопрос обычно таков: "Ну, это здорово, но зачем мне это делать?" Ответ - предыдущая концепция чистого кода, которую мы изучили: функция должна делать только одно. Когда у вас есть классы и функции, в которых есть операторы `if`, вы говорите своему пользователю, что ваша функция делает больше, чем одно. Помните, просто сделайте одну вещь.








**Плохо:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Хорошо:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ к содержанию](#содержание)**

### Избегайте проверки типов (часть 1)

JavaScript нетипизирован, что означает, что ваши функции могут принимать аргументы любого типа. Иногда, вас может укусить эта свобода, и становится заманчивым делать проверку типов в ваших функциях. Есть много способов избежать этого. Первое, что нужно учитывать, - это согласованные API.




**Плохо:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**Хорошо:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ к содержанию](#содержание)**

### Избегайте проверки типов (часть 2)

Если вы работаете с базовыми примитивными значениями, такими как строки и целые числа, и не можете использовать полиморфизм, но по-прежнему чувствуете необходимость проверки типов, вам следует рассмотреть возможность использования TypeScript. Это отличная альтернатива обычному JavaScript, так как предоставляет статическую типизацию поверх стандартного синтаксиса JavaScript. Проблема с ручной проверкой типов в обычном JavaScript заключается в том, что для того, чтобы сделать это правильно, требуется так много лишних слов, что ложная "безопасность типов", которую вы получаете, не восполняет потерянную читаемость. Держите JavaScript в чистоте, пишите хорошие тесты и получайте хорошие ревью кода. В противном случае, сделайте все это, но с TypeScript (который, как я уже сказал, является отличной альтернативой!).









**Плохо:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**Хорошо:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ к содержанию](#содержание)**

### Не переусердствуйте

Современные браузеры во время выполнения выполняют большую часть внутренней оптимизации. Часто, если вы оптимизируете, вы просто теряете время. [Есть хорошие ресурсы](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers), чтобы увидеть, где не хватает оптимизации. Нацельтесь на них, пока они не исправлены.





**Плохо:**

```javascript
// В старых браузерах каждая итерация с некэшированным `list.length` будет дорогостоящей
// из-за пересчета `list.length`. В современных браузерах это оптимизировано.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Хорошо:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ к содержанию](#содержание)**

### Удаляйте мертвый код

Мертвый код так же плох, как и дублирующийся код. Нет причин хранить его в своей кодовой базе. Если это не вызывается - избавьтесь от этого! Это все еще будет безопасно с вашей историей версий, если вам все еще это нужно.



**Плохо:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Хорошо:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ к содержанию](#содержание)**

## **Объекты и структуры данных**

### Используйте геттеры и сеттеры

Использование методов получения и установки данных объектов может быть лучше, чем просто поиск свойства объекта. "Почему?", спросите вы. Ну, вот неорганизованный список причин, почему:



- Если вы хотите сделать больше, чем получить свойство объекта, вам не нужно искать и менять каждый метод доступа в вашей кодовой базе.
- Делает добавление проверки простым, когда делает `set`.
- Инкапсулирует внутреннее представление.
- Легко добавить ведение журнала и обработку ошибок при получении и настройке.
- Вы можете лениво загрузить свойства вашего объекта, скажем, получить его с сервера.



**Плохо:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Хорошо:**

```javascript
function makeBankAccount() {
  // это скрытое свойство
  let balance = 0;

  // геттер, доступ к нему через возвращенный объект ниже
  function getBalance() {
    return balance;
  }

  // сеттер, доступ к нему через возвращаемый объект ниже
  function setBalance(amount) {
    // ... проверяйте перед обновлением баланса
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ к содержанию](#содержание)**

### Делате объекты закрытыми

Это может быть достигнуто через замыкания (для ES5 и ниже).

**Плохо:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Хорошо:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ к содержанию](#содержание)**

## **Classes**

### Prefer ES2015/ES6 classes over ES5 plain functions

It's very difficult to get readable class inheritance, construction, and method
definitions for classical ES5 classes. If you need inheritance (and be aware
that you might not), then prefer ES2015/ES6 classes. However, prefer small functions over
classes until you find yourself needing larger and more complex objects.

**Плохо:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Хорошо:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ к содержанию](#содержание)**

### Use method chaining

This pattern is very useful in JavaScript and you see it in many libraries such
as jQuery and Lodash. It allows your code to be expressive, and less verbose.
For that reason, I say, use method chaining and take a look at how clean your code
will be. In your class functions, simply return `this` at the end of every function,
and you can chain further class methods onto it.

**Плохо:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Хорошо:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ к содержанию](#содержание)**

### Prefer composition over inheritance

As stated famously in [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
   relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
   (Change the caloric expenditure of all animals when they move).

**Плохо:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Хорошо:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ к содержанию](#содержание)**

## **SOLID**

### Single Responsibility Principle (SRP)

As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the amount of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify
a piece of it, it can be difficult to understand how that will affect other
dependent modules in your codebase.

**Плохо:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Хорошо:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ к содержанию](#содержание)**

### Open/Closed Principle (OCP)

As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**Плохо:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Хорошо:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ к содержанию](#содержание)**

### Liskov Substitution Principle (LSP)

This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Плохо:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Хорошо:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ к содержанию](#содержание)**

### Interface Segregation Principle (ISP)

JavaScript doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with JavaScript's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in JavaScript because of
duck typing.

A good example to look at that demonstrates this principle in JavaScript is for
classes that require large settings objects. Not requiring clients to setup
huge amounts of options is beneficial, because most of the time they won't need
all of the settings. Making them optional helps prevent having a
"fat interface".

**Плохо:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Хорошо:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ к содержанию](#содержание)**

### Dependency Inversion Principle (DIP)

This principle states two essential things:

1. High-level modules should not depend on low-level modules. Both should
   depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
   abstractions.

This can be hard to understand at first, but if you've worked with AngularJS,
you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

As stated previously, JavaScript doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `requestItems` method.

**Плохо:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Хорошо:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ к содержанию](#содержание)**

## **Testing**

Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[good coverage tool](https://gotwarlost.github.io/istanbul/).

There's no excuse to not write tests. There are [plenty of good JS test frameworks](https://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single concept per test

**Плохо:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Хорошо:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ к содержанию](#содержание)**

## **Concurrency**

### Use Promises, not callbacks

Callbacks aren't clean, and they cause excessive amounts of nesting. With ES2015/ES6,
Promises are a built-in global type. Use them!

**Плохо:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Хорошо:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ к содержанию](#содержание)**

### Async/Await are even cleaner than Promises

Promises are a very clean alternative to callbacks, but ES2017/ES8 brings async and await
which offer an even cleaner solution. All you need is a function that is prefixed
in an `async` keyword, and then you can write your logic imperatively without
a `then` chain of functions. Use this if you can take advantage of ES2017/ES8 features
today!

**Плохо:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Хорошо:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ к содержанию](#содержание)**

## **Error Handling**

Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping function execution on the current stack, killing the
process (in Node), and notifying you in the console with a stack trace.

### Don't ignore caught errors

Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error to the console (`console.log`)
isn't much better as often times it can get lost in a sea of things printed
to the console. If you wrap any bit of code in a `try/catch` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**Плохо:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Хорошо:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Don't ignore rejected promises

For the same reason you shouldn't ignore caught errors
from `try/catch`.

**Плохо:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Хорошо:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ к содержанию](#содержание)**

## **Formatting**

Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are [tons of tools](https://standardjs.com/rules.html) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization

JavaScript is untyped, so capitalization tells you a lot about your variables,
functions, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**Плохо:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Хорошо:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ к содержанию](#содержание)**

### Function callers and callees should be close

If a function calls another, keep those functions vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**Плохо:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Хорошо:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ к содержанию](#содержание)**

## **Comments**

### Only comment things that have business logic complexity.

Comments are an apology, not a requirement. Good code _mostly_ documents itself.

**Плохо:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Хорошо:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ к содержанию](#содержание)**

### Don't leave commented out code in your codebase

Version control exists for a reason. Leave old code in your history.

**Плохо:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Хорошо:**

```javascript
doStuff();
```

**[⬆ к содержанию](#содержание)**

### Don't have journal comments

Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**Плохо:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Хорошо:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ к содержанию](#содержание)**

### Avoid positional markers

They usually just add noise. Let the functions and variable names along with the
proper indentation and formatting give the visual structure to your code.

**Плохо:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Хорошо:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ к содержанию](#содержание)**

## Translation

This is also available in other languages:

- ![am](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Armenia.png) **Armenian**: [hanumanum/clean-code-javascript/](https://github.com/hanumanum/clean-code-javascript)
- ![bd](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Bangladesh.png) **Bangla(বাংলা)**: [InsomniacSabbir/clean-code-javascript/](https://github.com/InsomniacSabbir/clean-code-javascript/)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Simplified Chinese**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Traditional Chinese**: [AllJointTW/clean-code-javascript](https://github.com/AllJointTW/clean-code-javascript)
- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **French**: [GavBaros/clean-code-javascript-fr](https://github.com/GavBaros/clean-code-javascript-fr)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonesia**: [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italian**: [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanish**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Spanish**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamese**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)

**[⬆ к содержанию](#содержание)**

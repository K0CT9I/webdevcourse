## Задачи

### 1. Custom Array.prototype.filter

У массивов есть встроенный метод [filter](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Array/filter), который создаёт новый массив со всеми элементами, прошедшими проверку, задаваемую в передаваемой функции.

Пример:

```js
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const result = words.filter((word) => word.length > 6);

console.log(result); // ["exuberant", "destruction", "present"]
```

Реализуйте собственную функцию фильтрации массива, в следующем формате:

```js
/**
 * Кастомная реализация метода Array.prototype.filter
 * 
 * @param {Array} array - массив
 * @param {Function} filterFn - фильтрующая функция
 * @param {Boolean} inplace - флаг "модифицируем исходный массив или нет"
 * @returns {Array} - отфильтрованный массив
 */
function filter(array, filterFn, inplace = false) {
    const filteredArray = [];

    for (let i = 0; i < array.length; i++) {
        if (filterFn(array[i])) {
            filteredArray.push(array[i]);
        }
    }

    if (inplace) {
        array.length = 0;
        for (let i = 0; i < filteredArray.length; i++) {
            array.push(filteredArray[i]);
        }
        return array;
    } else {
        return filteredArray;
    }
}
```

Примечание: гарантируется, что фильтрующая функции всегда корректная, обработку ошибок и валидацию исходных данных в решении можно не закладывать.

**N.B.** Функция не должна использовать нативный метод `Array.prototype.filter`.

### 2. Ключи и свойства

Реализуйте функцию, которая получает на вход объект, а возвращает объект, в котором в качестве ключей указаны типы, встречающиеся в исходном объекте, а в качестве значений - как часто они встречались.

Решение:

```js
function solutionFn(initialObj) {
    const resultObj = {};

    for (const key in initialObj) {
        const type = typeof initialObj[key];
        
        if (resultObj[type] === undefined) {
            resultObj[type] = 1;
        } else {
            resultObj[type]++;
        }
    }

    return resultObj;
}

```

Примечание: в решении достаточно использовать оператор `typeof`.

### 3. Больше никаких шуток про 1 + '1' === '11'

В JavaScript оператор "+" помимо сложения чисел может выполнять роль конкатенации.
Если один из операндов - строка, то второй автоматически преобразуется к строке, и они конкатенируются.

Иногда это может привести к неожиданных последствиям...

```js
console.log(('b' + 'a' + + 'a' + 'a').toLowerCase()); // 'banana'
```

Решение:

```js
function sum(leftOperand, rightOperand) {
    if (typeof leftOperand !== 'number' && typeof rightOperand !== 'number') {
        throw new Error('Operands are not numbers');
    }

    if (typeof leftOperand !== 'number') {
        throw new Error('The left operand is not a number');
    }

    if (typeof rightOperand !== 'number') {
        throw new Error('The right operand is not a number');
    }

    return leftOperand + rightOperand;
}
```

Напишите функцию `sum`, которая:
* Принимает два значения
* Проверяет, является ли каждый из них числом
* Если они оба числа, то возвращается их сумма
* Если левый операнд не является числом, то выкидывается ошибка "The left operand is not number"
* Если правый операнд не является числом, то выкидывается ошибка "The right operand is not number"
* Если оба операнда не являются числами, то выкидывается ошибка "Operands are not numbers"

### 4. CVS на минималках

CVS - система контроля версий файлов. Основное предназначение - сохранение истории изменения файлов.

Напишите функцию `getMinimalCVS`, которая будет выполнять роль CVS для некоторого массива.

Она должна принимать на вход массив и возвращать объект с четырьмя свойствами:
* `head` - функция, возвращающая последнюю версию массива
* `history` - функция, возвращаюся массив со всеми версиями массива
* `push` - функция, принимающая элемент и сохраняющая новую версию массива с добавленным элементом в конце
* `pop` - функция, сохраняющая новую версию массива без последнего элемента и возвращающая этот последний элемент

Решение:

```js
function getMinimalCVS(initialArray) {
    const history = [initialArray.slice()];

    return {
        head: function () {
            return history[history.length - 1].slice();
        },
        history: function () {
            return history.map((version) => version.slice());
        },
        push: function (item) {
            const currentVersion = history[history.length - 1].slice();
            currentVersion.push(item);
            history.push(currentVersion);
        },
        pop: function () {
            const currentVersion = history[history.length - 1].slice();
            const poppedItem = currentVersion.pop();
            history.push(currentVersion);
            return poppedItem;
        },
    };
}

const cvs = getMinimalCVS(['a', 'b', 'c']);

console.log(cvs.head());    // ['a', 'b', 'c']
console.log(cvs.pop());     // 'c'

cvs.push('d');
cvs.push('e');

console.log(cvs.head());    // ['a', 'b', 'd', 'e']
console.log(cvs.history());
/**
 * [
 *   ['a', 'b', 'c'],
 *   ['a', 'b'],
 *   ['a', 'b', 'd'],
 *   ['a', 'b', 'd', 'e']
 * ]
 */
```

### 5. Глобальный переключатель

На одном веб-проекте все названия классов делятся на дефолтные и активные.
Активные отличаются от дефолтных тем, что у них в конце добавляется суффикс `_active`.

Например, `tag` - название дефолтного класса для тега, а `tag_active` - активный класс для тега.

Напишите функию `globalToggle`, которая будет являться глобальным переключателем стилей:
* Она принимает название класса (строку)
* Находит все элементы с этим классом
* Если ни одного элемента не найдено, то прекращает работу
* Смотрит на то, является переданный класс активным или нет
* Если он был дефолтным, то меняет для всех найденных элементов этот класс на активный
* Если он был активный, то меняет для всех найденных элементов этот класс на дефолтный 

### 6. Hit Or Run

Вы пишите искусственный интеллект (ИИ) для одной пошаговой стратегии. 
ИИ в один момент времени может либо бить `hit`, либо бежать `run`.

Напишите функцию `hitOrRun`, которая:
* Принимает на вход два натуральных числа `a` и `b` (`a < b`)
* Генерирует рандомное число в промежутке `[a, b]`
* Проверяет, является ли оно простым
* Если является, то возвращает строку `run`
* Если не является, то возвращает строку `hit`

Решение:
```js
// сначала проверка на простое число
function primeNum(number) {
    if (number <= 1) return false;
    if (number <= 3) return true;

    if (number % 2 === 0 || number % 3 === 0) return false;

    for (let i = 5; i * i <= number; i += 6) {
        if (number % i === 0 || number % (i + 2) === 0) return false;
    }

    return true;
}

function hitOrRun(a, b) {
// Генерируем число в промежутке между а и b
    const randomNum = Math.floor(Math.random() * (b - a + 1) + a);

    if (primeNum(randomNum)) {
        return "run";
    } else {
        return "hit";
    }
}
```

### 7. Case Converter

В одном веб-приложении весь код бэкенда пишется в [snake_case](https://developer.mozilla.org/en-US/docs/Glossary/Snake_case) (все слова в нижнем регистре и разделяются нижним подчёркиванием), а фронтенд - в [camelCase](https://developer.mozilla.org/en-US/docs/Glossary/Camel_case) (слова не разделяются, первое слово пишется в нижнем регистре, у каждого следующего слова первая буква в верхнем регистре, остальные - в нижнем).

Чтобы обеспечить корректную передачу данных с бэкенда на фронтенд необходимо написать функцию, которая принимает на вход строку в `snake_case` и превращает её в строку в `camelCase`.

Пример:

```js

function solutionFn(snakeCaseStr) {
    return snakeCaseStr.replace(/_([a-z])/g, function (match, group1) {
        return group1.toUpperCase();
    });
}

const snakeData = 'data_in_snake_case';

const result = solutionFn(snakeData);
console.log(result); // "dataInSnakeCase"
```

Подсказка: при решении используйте встроенные [методы строк](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/String) (так быстрее и проще).

### 8. Антиспам

Одной из важных составляющих многих почтовых антиспам-систем является анализ текста письма.
В частности, если в нём содержатся определённые ключевые слова, фразы и обороты, то с высокой долей вероятности он будет отнесён к спаму.

Реализуйте функцию простейшей проверки текста на спам. Она должна иметь следующий формат:

```js
/**
 * Принимает на вход текст письма и массив ключевых слов и проверяет,
 * содержится ли хотя бы одно из ключевых слов в этом тексте
 * 
 * @param {String} text - текст, проверяемый на спам
 * @param {String[]} keywords - массив ключевых слов
 * @returns {Boolean}
 */
function isSpam(text, keywords) {
    // ваш код здесь
}
```

### 10. Задача с собеседования

Первой задачей на алгоритмическом собеседовании на должность фронтенд-разработчика обычно дают что-то несложное, для разминки.

Например, напишите функцию, которая:
* Получает на вход натуральное число
* Перемножает все цифры числа, до тех пор, пока оно не станет одноразрядным
* Возвращает итоговое одноразрядное число

Решение:

```js

function solutionFn(number) {
    if (number < 0) {
        return "Число должно быть натуральным";
    }

    if (number < 10) {
        return number; // Число уже одноразрядное
    }

    let result = 1;

    while (number > 0) {
        const digit = number % 10;
        result *= digit;
        number = Math.floor(number / 10);
    }

    return solutionFn(result);
}

console.log(solutionFn(4));     // возвращает 4, так как уже одноразрядное
console.log(solutionFn(42));    // возвращает 8, так как 4 * 2 = 8
console.log(solutionFn(999));   // возвращает 2, так как 9 * 9 * 9 = 729, 7 * 2 * 9 = 126, 1 * 2 * 6 = 12, и наконец 1 * 2 = 2 
```

Попробуйте написать такую функцию.

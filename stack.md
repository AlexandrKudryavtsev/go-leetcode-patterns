# Stack

**Stack** — это паттерн задач, где основной структурой данных для решения является стек (LIFO).

## Флаги

Обычно задача сводится к идее:

> когда встречаем X — возвращаемся к последнему незавершённому Y

Частые признаки stack-задач:

- парсинг/валидация вложенных структур:
	- скобочные выражения
	- JSON/XML/HTML
	- вложенные строки

- необходимость постоянно работать с последним добавленным/незавершённым элементом

- задачи с откатами / отменой предыдущих действий:
	- undo/redo
	- backspace
	- отмена операций

- обработка выражений:
	- calculator
	- postfix/prefix/infix notation

- DFS без рекурсии (явный stack вместо call stack)

## Примеры

### [Removing Stars From a String](https://leetcode.com/problems/removing-stars-from-a-string)

Задача: удалить каждую звездочку и ее предыдущий элемент.

Когда встречаем `*` — удаляем последний добавленный символ → stack

```go
func removeStars(s string) string {
	stack := make([]rune, len(s))
	i := 0

	for _, v := range s {
		if v == '*' {
			i--
		} else {
			stack[i] = v
			i++
		}
	}
	return string(stack[:i])
}
```

Идея: создаем стек и добавляем в него элементы. Если видим звездочку, то удаляем последний элемент и идем дальше.

### [Asteroid Collision](https://leetcode.com/problems/asteroid-collision)

Задача: по заданным правилам понять, какие астероиды выживут

Когда встречаем отрицательный астероид, нужно сравнивать его с последними положительными астероидами в стеке → stack

```go
func asteroidCollision(asteroids []int) []int {
    stack := []int{}

    for _, a := range asteroids {
        if len(stack) == 0 || a > 0 {
            stack = append(stack, a)
            continue
        }

        for len(stack) > 0 && stack[len(stack)-1] > 0 && stack[len(stack)-1] < -a {
            stack = stack[:len(stack) - 1]
        }

        if len(stack) == 0 || stack[len(stack) - 1] < 0 {
            stack = append(stack, a)
        } else if stack[len(stack) - 1] == -a  {
            stack = stack[:len(stack) - 1]
        }
    }

    return stack
}
```

Идея: в стек кладем первый и положительные элементы. Когда получаем отрицательный элемент, то итеративно обрабатываем предыдущие значения по правилам.

### [Decode String](https://leetcode.com/problems/decode-string)

Задача: раскрыть все квадратные скобки.

Когда видим квадратную скобку — обрабатываем часть строки → stack  
Работа со скобками → stack

```go
func findInnermost(s string) (int, int) {
	stack := []int{}

	for i, c := range s {
		if c == '[' {
			stack = append(stack, i)
		}

		if c == ']' {
			return stack[len(stack)-1], i
		}
	}

	return -1, -1
}


func decodeString(s string) string {
	for {
		l, r := findInnermost(s)
		if l == -1 {
			return s
		}

		numStart := l - 1

		for numStart > 0 && unicode.IsDigit(rune(s[numStart-1])) {
			numStart--
		}

		if numStart == l-1 {
			n, _ := strconv.Atoi(string(s[numStart]))
			s = s[:l-1] + strings.Repeat(s[l+1:r], n) + s[r+1:]
		} else {
			n, _ := strconv.Atoi(s[numStart:l])
			s = s[:numStart] + strings.Repeat(s[l+1:r], n) + s[r+1:]
		}
	}
}
```

Идея:

- `findInnermost` использует стек для поиска самых вложенных скобок. Индексы открывающих скобок кладутся в стек, а при первой закрывающей берется последний элемент стека и текущий индекс — это нужная пара скобок.
- Пока `findInnermost` что-либо возвращает, обрабатываем строку по правилам задачи


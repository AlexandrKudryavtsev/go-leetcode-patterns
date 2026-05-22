# Linked list

**Linked List** — это паттерн задач, где основная сложность связана с управлением указателями (`Next`) между `node` узлами.

Задачи сводятся к тому, что надо думать не про элементы, а про то, куда указывает `Next`.

## Флаги

Частые признаки linked-list задач:

- найти середину списка
- развернуть порядок элементов
- решить задачу in-place (`O(1)` по memory)
- изменить связи/порядок между элементами

## Полезные приемы

### Dummy node

Dummy node - фейковый узел перед head. 

Удобен в задачах с:

1) удалением элементов
2) вставкой элементов
3) меняющимся head

Потому что позволяет убрать special case для первого элемента.

### Fast/slow pointers

Один указатель идет по 1 шагу, второй - по 2.

```go
slow := head
fast := head

for fast != nil && fast.Next != nil {
    slow = slow.Next
    fast = fast.Next.Next
}
```

После этого цикла `slow` будет указывать на середину списка.

## Примеры

### [Reverse linked list](https://leetcode.com/problems/reverse-linked-list)

Задача: перевернуть связный список.

Нужно развернуть порядок элементов → reverse linked list

```go
func reverseList(head *ListNode) *ListNode {
    if head == nil {
        return head
    }

    cur := head
    var prev *ListNode

    for cur != nil {
        next := cur.Next
        cur.Next = prev
        prev = cur
        cur = next
    }

    return prev
}
```

Идея: создаем пустой `prev`, обходим список и каждый текущий узел направляем назад на `prev`; после этого `prev` становится головой уже перевёрнутой части.

### [Delete the Middle Node of a Linked List](https://leetcode.com/problems/delete-the-middle-node-of-a-linked-list)

Задача: удалить узел по середине связного списка

1 решение: считаем длину, заново итерируемся до среднего элемента и через `cur.Next = cur.Next.Next` удаляем

```go
func deleteMiddle(head *ListNode) *ListNode {
    cur := head
    length := 0

    for cur != nil {
        length++
        cur = cur.Next
    }

    n := length / 2

    dummy := &ListNode{Next: head}
    cur = dummy

    for i := 0; i < n; i++ {
        cur = cur.Next
    }

    cur.Next = cur.Next.Next
    
    return dummy.Next
}
```

2 решение: при помощи fast/slow ищем середину, а также дополнительно сохраняем предыдущий `slow` узел. Через `prevSlow` удаляем середину

```go
func deleteMiddle(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return nil
    }
    
    slow := head
    fast := head

    var prevSlow *ListNode

    for fast != nil && fast.Next != nil {
        prevSlow = slow
        slow = slow.Next
        fast = fast.Next.Next
    }

    prevSlow.Next = prevSlow.Next.Next

    return head
}
```

### [Odd even linked list](https://leetcode.com/problems/odd-even-linked-list)

Задача: надо переставить четные узлы в конец

Нужно изменить порядок элементов → linked list

```go
func oddEvenList(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return head
    }

    odd := head
    even := head.Next
    evenHead := even

    for even != nil && even.Next != nil {
        odd.Next = even.Next
        odd = odd.Next

        even.Next = odd.Next
        even = even.Next
    }

    odd.Next = evenHead

    return head
}
```

Идея: создаем две ветки и соединяем их в конце

- создаем новые указатели:
    - `odd` - связный список узлов, стоящих на нечетных позициях
    - `even` - связный список узлов, стоящих на четных позициях
    - `evenHead` - указатель на начало связного списка `even`
- в цикле:
    - следующее значение у `odd` - это следующее значение `even`
    - аналогично для `even`
- в конец `odd` добавляем `evenHead`

### [Maximum Twin Sum of a Linked List](https://leetcode.com/problems/maximum-twin-sum-of-a-linked-list)

Задача: найти максимальную сумму близнецовых элементов.

```go
func pairSum(head *ListNode) int {
    if head == nil {
        return 0
    }

    // находим середину
    slow := head
    fast := head

    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
    }

    middle := slow
    
    // reverse второй части
    cur := middle
    var prev *ListNode

    for cur != nil {
        next := cur.Next
        cur.Next = prev
        prev = cur
        cur = next
    }

    // находим суммы
    res := 0

    for prev != nil {
        newSum := prev.Val + head.Val

        if newSum > res {
            res = newSum
        }

        prev = prev.Next
        head = head.Next
    }
    
    return res
}
```

Идея:

- при помощи fast/slow находим середину
- переворачиваем вторую часть списка
- одновременно идем по первой и второй части списка, считаем сумму и находим максимальную

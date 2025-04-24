# Алгоритмы и структуры данных

## Содержание
1. [Структуры данных](#структуры-данных)
2. [Алгоритмы сортировки](#алгоритмы-сортировки)
3. [Алгоритмы поиска](#алгоритмы-поиска)
4. [Графы](#графы)
5. [Динамическое программирование](#динамическое-программирование)
6. [Сложность алгоритмов](#сложность-алгоритмов)

## Структуры данных

### Массивы и списки

#### Массив
```go
// Пример работы с массивом в Go
func arrayExample() {
    // Статический массив
    var arr [5]int
    arr[0] = 1
    
    // Слайс (динамический массив)
    slice := make([]int, 0, 5)
    slice = append(slice, 1, 2, 3)
    
    // Доступ к элементам
    for i, v := range slice {
        fmt.Printf("Index: %d, Value: %d\n", i, v)
    }
}
```

#### Связный список
```go
// Реализация связного списка в Go
type Node struct {
    Value int
    Next  *Node
}

type LinkedList struct {
    Head *Node
}

func (l *LinkedList) Append(value int) {
    newNode := &Node{Value: value}
    if l.Head == nil {
        l.Head = newNode
        return
    }
    
    current := l.Head
    for current.Next != nil {
        current = current.Next
    }
    current.Next = newNode
}
```

### Хэш-таблицы
```go
// Пример использования map в Go
func hashTableExample() {
    // Создание map
    m := make(map[string]int)
    
    // Добавление элементов
    m["one"] = 1
    m["two"] = 2
    
    // Получение значения
    value, exists := m["one"]
    if exists {
        fmt.Println("Value:", value)
    }
    
    // Удаление элемента
    delete(m, "one")
}
```

### Деревья

#### Бинарное дерево поиска
```go
type TreeNode struct {
    Value int
    Left  *TreeNode
    Right *TreeNode
}

func (t *TreeNode) Insert(value int) {
    if t == nil {
        return
    }
    
    if value < t.Value {
        if t.Left == nil {
            t.Left = &TreeNode{Value: value}
        } else {
            t.Left.Insert(value)
        }
    } else {
        if t.Right == nil {
            t.Right = &TreeNode{Value: value}
        } else {
            t.Right.Insert(value)
        }
    }
}
```

#### Куча (Heap)
```go
type MinHeap struct {
    elements []int
}

func (h *MinHeap) Insert(value int) {
    h.elements = append(h.elements, value)
    h.heapifyUp(len(h.elements) - 1)
}

func (h *MinHeap) heapifyUp(index int) {
    parent := (index - 1) / 2
    if index > 0 && h.elements[index] < h.elements[parent] {
        h.elements[index], h.elements[parent] = h.elements[parent], h.elements[index]
        h.heapifyUp(parent)
    }
}
```

## Алгоритмы сортировки

### Быстрая сортировка (Quick Sort)
```go
func quickSort(arr []int) []int {
    if len(arr) <= 1 {
        return arr
    }
    
    pivot := arr[len(arr)/2]
    var left, right, equal []int
    
    for _, v := range arr {
        switch {
        case v < pivot:
            left = append(left, v)
        case v > pivot:
            right = append(right, v)
        default:
            equal = append(equal, v)
        }
    }
    
    left = quickSort(left)
    right = quickSort(right)
    
    return append(append(left, equal...), right...)
}
```

### Сортировка слиянием (Merge Sort)
```go
func mergeSort(arr []int) []int {
    if len(arr) <= 1 {
        return arr
    }
    
    mid := len(arr) / 2
    left := mergeSort(arr[:mid])
    right := mergeSort(arr[mid:])
    
    return merge(left, right)
}

func merge(left, right []int) []int {
    result := make([]int, 0, len(left)+len(right))
    i, j := 0, 0
    
    for i < len(left) && j < len(right) {
        if left[i] < right[j] {
            result = append(result, left[i])
            i++
        } else {
            result = append(result, right[j])
            j++
        }
    }
    
    result = append(result, left[i:]...)
    result = append(result, right[j:]...)
    return result
}
```

## Алгоритмы поиска

### Бинарный поиск
```go
func binarySearch(arr []int, target int) int {
    left, right := 0, len(arr)-1
    
    for left <= right {
        mid := left + (right-left)/2
        if arr[mid] == target {
            return mid
        }
        if arr[mid] < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }
    
    return -1
}
```

## Графы

### Поиск в ширину (BFS)
```go
type Graph struct {
    vertices map[int][]int
}

func (g *Graph) BFS(start int) []int {
    visited := make(map[int]bool)
    queue := []int{start}
    result := []int{}
    
    for len(queue) > 0 {
        vertex := queue[0]
        queue = queue[1:]
        
        if !visited[vertex] {
            visited[vertex] = true
            result = append(result, vertex)
            
            for _, neighbor := range g.vertices[vertex] {
                if !visited[neighbor] {
                    queue = append(queue, neighbor)
                }
            }
        }
    }
    
    return result
}
```

### Поиск в глубину (DFS)
```go
func (g *Graph) DFS(start int) []int {
    visited := make(map[int]bool)
    result := []int{}
    
    var dfs func(int)
    dfs = func(vertex int) {
        visited[vertex] = true
        result = append(result, vertex)
        
        for _, neighbor := range g.vertices[vertex] {
            if !visited[neighbor] {
                dfs(neighbor)
            }
        }
    }
    
    dfs(start)
    return result
}
```

## Динамическое программирование

### Числа Фибоначчи
```go
func fibonacci(n int) int {
    if n <= 1 {
        return n
    }
    
    dp := make([]int, n+1)
    dp[0], dp[1] = 0, 1
    
    for i := 2; i <= n; i++ {
        dp[i] = dp[i-1] + dp[i-2]
    }
    
    return dp[n]
}
```

## Сложность алгоритмов

### Big-O нотация
- O(1): Константное время
  ```go
  // Пример: доступ к элементу массива
  arr := []int{1, 2, 3}
  value := arr[0] // O(1)
  ```

- O(n): Линейное время
  ```go
  // Пример: линейный поиск
  func linearSearch(arr []int, target int) bool {
      for _, v := range arr { // O(n)
          if v == target {
              return true
          }
      }
      return false
  }
  ```

- O(n²): Квадратичное время
  ```go
  // Пример: пузырьковая сортировка
  func bubbleSort(arr []int) {
      n := len(arr)
      for i := 0; i < n-1; i++ { // O(n²)
          for j := 0; j < n-i-1; j++ {
              if arr[j] > arr[j+1] {
                  arr[j], arr[j+1] = arr[j+1], arr[j]
              }
          }
      }
  }
  ```

- O(log n): Логарифмическое время
  ```go
  // Пример: бинарный поиск
  func binarySearch(arr []int, target int) int {
      left, right := 0, len(arr)-1
      for left <= right { // O(log n)
          mid := left + (right-left)/2
          if arr[mid] == target {
              return mid
          }
          if arr[mid] < target {
              left = mid + 1
          } else {
              right = mid - 1
          }
      }
      return -1
  }
  ```

### Оптимизация алгоритмов
1. Использование кэширования
2. Мемоизация
3. Разделяй и властвуй
4. Жадные алгоритмы
5. Динамическое программирование
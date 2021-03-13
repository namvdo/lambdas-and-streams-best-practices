## Lambdas and Streams best-practices

Lambda and Stream best practices extracted from the Modern Java in Action - Lambda, streams, functional and reactive programming and some others. Also, this repo includes some tips when using lambda and streams as a Java developer.

### What are lambdas and streams?

#### List of (non exhautive) intermediate operations of Stream:
| Operation        | Type           | Return Type  | Argument of the Operation | Function descriptor |
| ---------------- |----------------| -------------| ------------------------- | ------------------- |
| `filter`      | Intermediate | `Stream<T>` | `Predicate<T>` | `T -> boolean` |
| `map`      | Intermediate | `Stream<R>` | `Function<T, R>` | `T -> R` |
| `limit`      | Intermediate | `Stream<T>` | `long` | |
| `distinct`      | Intermediate | `Stream<T>` |  | |

#### List of (non exhautive) terminal operations of Stream:

| Operation        | Type           | Return Type  | Purpose |
| ---------------- |----------------| -------------| ------------------------- |
| `forEach`      | Terminal | `void` | Consumes each element from a stream and applies a lambda to each of them.|
| `count`      | Terminal | `long` | Returns the number of elements in a stream.|
| `collect`      | Terminal | `Stream<T>` | Returns a collection from a stream (such as `Map`, `List` or event `Integer`). |

### DON'T DO THIS:

```java
List<String> highCaloricDishes = new ArrayList<>();
Iterator<String> iterator = menu.iterator();
while(iterator.hasNext()) {
  Dish dish = iterator.next();
  if(dish.getCalories() > 300) {
  highCaloricDishes.add(d.getName());
  }
}
```

### DO THIS:

```java
List<String> highCaloricDish =
  menu.stream()
  .filter(dish -> dish.getCalories() > 300)
  .collect(toList());
```
---
### DON'T DO THIS:
```java
Iterator<String> iterator = menu.iterator();
while (iterator.hasNext()) {
  Dish dish = iterator.next();
  System.out.println(dish);
}
```

### DO THIS:
```java
menu.forEach(System.out::println);
```
---
### DON'T DO THIS:

```java
Set<Dish> dishes = new HashSet<>();
int count = 0;
for(Dish dish : menu) {
   if (dish.getCalories() > 300) {
      dishes.add(dish);
      count++;
   }
   if (count == 3) break;
}

```
### DO THIS:
```java
long count = menu.stream()
  .filter(dish -> dish.getCalories() > 300)
  .distinct()
  .limit(3)
  .count();
```
---
### DON'T DO THIS:

```java
List<Dish> vegetarianDishes = new ArrayList<>();
for(Dish d: menu) {
  if(d.isVegetarian()){
  vegetarianDishes.add(d);
  }
}
```

### DO THIS:
```java
List<Dish> vegetarianDishes =
  menu.stream()
  .filter(Dish::isVegetarian)
  .collect(toList());
```

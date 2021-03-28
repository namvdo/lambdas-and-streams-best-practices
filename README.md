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

### Get the name of all dishes with calories greater than 300
#### Stop doing this:
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

#### Do this:

```java
List<String> highCaloricDish =
  menu.stream()
  .filter(dish -> dish.getCalories() > 300)
  .collect(toList());
```
---
### Print all the dishes in the collection
#### Stop doing this:
```java
Iterator<String> iterator = menu.iterator();
while (iterator.hasNext()) {
  Dish dish = iterator.next();
  System.out.println(dish);
}
```

#### Do this:
```java
menu.forEach(System.out::println);
```
---
### Count the number of distinct dishes (at most 3)
#### Stop doing this:
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
#### Do this:
```java
long count = menu.stream()
  .filter(dish -> dish.getCalories() > 300)
  .distinct()
  .limit(3)
  .count();
```
---
### Get vegetarian dishes from all available dishes
#### Stop doing this:
```java
List<Dish> vegetarianDishes = new ArrayList<>();
for(Dish d: menu) {
  if(d.isVegetarian()){
  vegetarianDishes.add(d);
  }
}
```

#### Do this:
```java
List<Dish> vegetarianDishes =
  menu.stream()
  .filter(Dish::isVegetarian)
  .collect(toList());
```
---
### Get all the dishes with the calories lower than 320 (presume the collection is already sorted)
#### Stop doing this:
```java
List<Dish> dishesWithLowerThan320Calories = new ArrayList<>();
for(Dish dish : menu) {
   if (dish.getCalories() < 320) {
       dishesWithLowerThan320Calories.add(dish);
   }	
}
```
#### Do this:
```java
List<Dish> slicedMenu
 	= specialMenu.stream()
 	.takeWhile(dish -> dish.getCalories() < 320)
 	.collect(toList());
```
### Get all the dishes with the calories greater than or equal to 320 (presume the collection is already sorted)
#### Stop doing this:
```java
List<Dish> dishesWithLowerThan320Calories = new ArrayList<>();
for(Dish dish : menu) {
   if (dish.getCalories() >= 320) {
       dishesWithLowerThan320Calories.add(dish);
   }	
}
```
#### Do this:
```java
List<Dish> slicedMenu
 	= specialMenu.stream()
 	.dropWhile(dish -> dish.getCalories() < 320)
 	.collect(toList());
```
---
### Get a list of the first 3 dishes having calories greater than 300
#### Stop doing this:
```java
List<Dish> dishes = new ArrayList<>();
int count = 0;
for(Dish dish : menu) {
   if (dish.getCalories() > 300) {
      dishes.add(dish);
   }
   if (count == 3) {
      break;
   }
}
```
#### Do this:
```java
List<Dish> dishes = specialMenu
  .stream()
  .filter(dish -> dish.getCalories() > 300)
  .limit(3)
  .collect(toList()); 
```
---
### Filter dishes have calories greater than 300, skip 3 first ones and return the rest
#### Stop doing this:
```java
List<Dish> dishes = new ArrayList<>();
int skipCount = 0;
for(Dish dish : menu) {
  if (dish.getCalories() > 300 && skipCount == 3) {
     dishes.add(dish);
  }
  if (skipCount < 3) {
    skipCount++; 
  }
}
```
#### Do this:
```java
List<Dish> dishes = menu.stream()
                        .filter(dish -> dish.getCalories() > 300)
                        .skip(3)
                        .collect(Collectors.toList());
```
---
### Get the first 2 meat dishes
#### Stop doing this:
```java
List<Dish> dishes = new ArrayList<>();
int count = 0;
for(Dish dish : menu) {
  if (dish.getType() == Dish.Type.MEAT) {
    dishes.add(dish);
    count++;
  }
  if (count == 2) {
    break;
  }
}
```
#### Do this:
```java
List<Dish> dishes = menu.stream()
                        .filter(dish -> dish.getType() == Dish.Type.MEAT)
                        .limit(2)
                        .collect(Collectors.toList());
```                        
---
### Group all dishes by its type
#### Stop doing this:
```java
Map<Dish.Type, List<Dish>> typeToDish = new HashMap<>();
for(Dish dish : menu) {
   List<Dish> dishes = typeToDish.get(dish.getType());
   if (dishes == null) {
      dishes = new ArrayList<>();
   }
   dishes.add(dish);
   typeToDish.put(dish.getType(), dishes);
}
```
#### Do this:
```java
 Map<Dish.Type, List<Dish>> typeToDish = menu.stream().collect(groupingBy(Dish::getType));
```

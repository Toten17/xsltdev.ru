---
description: С функциями можно обращаться, как с любыми другими объектами, а значит, можно присваивать переменным.
---

# Присваивание к переменной

```php
<?php
$concat2 = function (string $s1, string $s2): string {
  return $s1. ' '. $s2;
};
$concat2('Hello', 'World'); //-> 'Hello World'
```

Анонимная функция назначается переменной \$concat2. С помощью функции `is_callable()` можно проверить, является ли переменная вызывающей.

```php
<?php
is_callable($concat2) // 1
```

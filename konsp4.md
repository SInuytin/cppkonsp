# Числа с плавающей точкой в C/C++

float  - 4 байта.

Диапазон значений: $[1.2\times10^{-38};3.4\times 10^{38}]$

В си используется стандарт **IEEE 754**. Число в таком формате состоит из трех частей:

**1. Знаковый бит** 1 - полож, 0 - отриц
**2. Экспонента.**
    
    + Определяет порядок числа
    + занимает 8 бит
    + экспонента хранится в смещенной форме, чтобы избежать необходимости в отдельном бите для знака экспоненты. Смещение равно $2^n -1$, где $n$ -кол-во бит экспоненты.
  
**3. Мантисса**
   
   + Определяет значащие цифры числа.
   + Занимает 23 бита
   + Мантисса хранится в нормализованной форме, где подразумевается ведущая единица перед точкой (например, $1.xxx$). Эта единица не хранится явно, что позволяет сэкономить один бит.

# Массивы в c/c++

```c
  int arr[10] - создание массива
//тип имя размер
```
Нумерация с 0, обращение за пределы массива это _undefined behavior_. 

Массивы при создании можно сразу инициализировать:
```c
//инициализация
int numbers[10] = {1,2,3}
// a[0] = 1, a[1] = 2, a[3-9] = 0
int numbers[10] = {} // можно в си++ и си 23

int arr[10 - size_t тип]
```
Массив на 10 `int` хранит ровно 40 бат.

```c
int g[10] = {[2]=3,[1]=4}// 0 4 3 0 0..,
```

Многомерные массивы:
```c
g[2][3] - двумерный массив
  ^  ^
  |  |
  y  x
```
Многомерные массивы это непрерывная область в памяти, то есть логически это:
```c
_____________
|0,0|0,1|0,2|
_____________
|1,0|1,1|1,2|
```
Но в памяти это:
```c
|0,0|0,1|0,2|1,0|1,1|1,2|
```
Размер массива должен быть константой времени компиляции.
```c
constexpr int a = 10;
int x = 10
const int b = 10;
int arr [2+3];//можно
int arr [10];//можно
int arr [a];//можно
int arr [b];//нельзя
int arr [x];//нельзя
```
VLA - как выделять массивы переменной длины
[Информация об этом](https://ru.wikipedia.org/wiki/%D0%9C%D0%B0%D1%81%D1%81%D0%B8%D0%B2_%D0%BF%D0%B5%D1%80%D0%B5%D0%BC%D0%B5%D0%BD%D0%BD%D0%BE%D0%B9_%D0%B4%D0%BB%D0%B8%D0%BD%D1%8B)

## Указатели
```c
int *q;
    ^
    указатель на адрес объекта в
    памяти

Пример:
int a = 2;
int *q;
p = &a; // теперь p хранит адрес a
    ^
    операция взятия адреса
```
взять адрес можно только у объекта в памяти.
```c
int *p;
float f;
p = &f//ошибка

p = &array[1];
*p  = 1;
^
обращение сквозь указатель
то есть не к адресу, 
а тому что там лежит
printf("%d",array[1]) //выведет 1
```
Размерность объекта это размерность программы. То есть

```c
**z = &p;
указатель ну указатель, который хранит адрес p;
```

```c
void *v -указатель  на произвольный тип
```
Черер такой указатель нельзя обратиться к данным по адресу.

Но можно так:
```c
int x = 10;
void *v = &x; // v хранит адрес переменной x
int *p = (int *)v; // Приводим void * к int *
printf("%d", *p); // Выводит 10
```

Указатель `void *` может хранить адрес любого типа данных: `int`, `float`, `char`, структуры и т.д.

Это делает его полезным для написания обобщённых функций или работы с памятью, где тип данных заранее неизвестен.

### Арифметика указателей

```c
int *p;
int q[10];
p = &q[1];
p + 2;
```
Что это значит, было:
```c
|0|1|2|3|4|...;
   ^
   *p
после:
|0|1|2|3|4|...;
       ^
       *p

```
## Динамические массивы.

```c
int *p = malloc(sizeof(int)*100)
//динамический массив на 100 интов
//вы обязаны это освободить
free(p)//освобождение памяти.
free(p)// ошибка.
```
Если `malloc` не удался он выдает `NULL`:

```c
int *p = malloc(sizeof(int)*100)
if(!p) -> error
free(p)//правильный код
```

```c
#include<stdio.h>
void increment(int *x) {//передача по ссылке
    (*x)++;
}
void zalupa(int x) {//передача по значению
    (x)++;
}

int main() {
    int a = 5;
    increment(&a); // станет 6
    printf("%d\n",a);
    zalupa(a); // останется 6
    printf("%d\n",a);
    return 0;
}
```
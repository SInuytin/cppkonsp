# Пользовательские тип данных, switch

```c
int N,K;
float a[N][K] --//VLA - нельзя в лабе
```

в лабах можно все, если не сказано иное и если вы знаете как это работает.

## enum, перечисление

```c
    enum id {enum-list} //type == int
    enum id : type {enum-list} //(since C23)

    пример:
    enum color{ RED, GREEN, BLUE} c = RED;

    enum color *cp = &c//ср указывате на тип 

    color d // нельзя
    enum color green = GREEN//можно
```
нужно сразу инициализировать `enum`, и всегда использовать ключевое слово `enum`

НО можно написать так
```c
typedef enum color color_t; //создали псевдоним
color_t g = GREEN;// можно
```

### Инициализация констант
```c
    enum foo {A,B,C = 10, D, E = 1, F, G = F + C}
    //теперь их значения в int
    A == 1, B == 2, C == 10, D == 11, E == 1, F == 2, G == 12;//all true;

    а еще можно так
    enum goo {a = 1, x = B, y = E. z = C};
    //все что записывается в enum должно быть константой времени компиляции
```

Надо `enum` можно делать все те же операции что и с `int` или с тем `type`, что он равен.

`enum` часто используют со `switch`

## switch

по сути это не несколько `if else`, а `goto`

```c
    switch ( expression ) statement
        case constant expression : 
            statment
            ...
            break;
        case another expr:
            ... 
            statement//проваливание выражениия, то есть переход к следующему case
        case third expr: {
            ...
        }//фигурные скобки не обязательны
        default: statement //если expr не равен ничему другому
```

где это можно использовать
```c
enum color = {RED, GREEN, BLUE, WHITE_RED BLACK};
switch(c)
{
    case GREEN:
        printf("green");
        break;
    case WHITE_RED:
        printf("white-");
    case RED:
        printf("red");
        break;        
    ...
}
printf("\n");
```

## Duff's device (устройство Даффа)

```c
    int main(void)
    {
        int number = 12;
        int i = 0;
        while (number-- > 0)
            ++i;
        printf("%i\n", i); // 12
        return 0;
    }
```
## loop unrolling (развертывание цикла)
```c
int main(void)
{
    int number = 12;
    int count = number / 4;

    int i = 0;
    while  (count-- > 0)
    {
        ++i; ++i; ++i; ++i;
    }

    printf("%i\n", i); // 23 + 12
    return 0;
}
```
но плохо работает если не делится нацело


НО можно сделать так.
## Duff's device (устройство Даффа)
```c
int main(void)
{
    int number =  11;
    int count = (number + 3) / 4;
    int i = 0;

    switch (number % 4)
    {
        case 0:
            do
            {
                ++i;
        case 3: ++i;
        case 2: ++i;
        case 1: ++i;
            } while(-- count > 0);
    }

    printf("%i\n", i); // 11
}
```

## struct

Структура это идейно связанный тип данных вида:
```c
    typedef struct Point1
    {
        float x;
        float y;
        float z;
    } Point

    void print(Point *p)
    {
        printf("Point{x = %f, y = %f, z = %f}\n", p->x, p->y, p->z);
    }
    int main()
    {
        Point p = {.y = 2, .x = 10, .z = 11}; // в с++ порядок важен
        p.x = 33;
        print(&p); // 33, 2, 11
        return 0;
    }

    p.x -- обращение к полю через объект
    p->x -- обращение к полю сквозь указателбь
```
### forward declaration

```c
    struct y;
    struct x { struct y *p};
    struct y { struct x *q};
```

`sizeof(Point)`= 8 + 8 + 8 = 24;

Размер структуры кратен выравниванию.

адрес переменой берется по кратности размера переменной

То как перечисленны поля в структуре влияет на то как они уложены в памяти.

выравнивание идет по наибольшему полю.

```c
#include<stddef.h>

offsetof(type) //возвращает выравнивание
```
### упаковка без выравнивания 
```c
#pragma pack(push,1)
typedef struct vec2
{

} vec2
#pragma pack(pop)
```

## union 
это пользовательский тип данных, отличие от `struct` в том что `offsetof = 0`

```c 
union floatint 
{
    float f;
    int i;
}
```
### анонимные структуры
```c
union reg32 
{
    struct 
    {
        unsigned char byte1;
        unsigned char byte2;
        unsigned char byte3;
        unsigned char byte4;
    } bytes;
    struct {
        unsigned short low;
        unsigned short high;
    } words;
    unsigned dword;
}
```
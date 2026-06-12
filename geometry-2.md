# Практика: Геомeтрия-2

## 1. Описание предметной области и сущностей
Body - абстрактный базовый класс, содержит позицию фигуры в пространстве и абстрактный метод Accept для принятия посетителя.

Ball - шар, наследник Body, имеет радиус.

RectangularCuboid - прямоугольный параллелепипед, наследник Body, имеет размеры по трём осям SizeX, SizeY, SizeZ.

Cylinder - цилиндр, наследник Body, имеет радиус и высоту SizeZ.

CompoundBody - составное тело, наследник Body, содержит список других тел (Parts).

Vector3 - вспомогательная структура, хранящая координаты X, Y, Z.

IVisitor\<T\> - интерфейс, определяющий методы Visit для каждого типа фигуры.

BoundingBoxVisitor - посетитель, реализует IVisitor\<RectangularCuboid\>, вычисляет минимальный ограничивающий параллелепипед для фигуры.

BoxifyVisitor - посетитель, реализует IVisitor\<Body\>, заменяет каждое простое тело на его ограничивающий параллелепипед.

## 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction TB
    class IVisitor~T~ {
        <<interface>>
        +Visit(Ball ball) T
        +Visit(RectangularCuboid rectangularCuboid) T
        +Visit(Cylinder cylinder) T
        +Visit(CompoundBody compoundBody) T
    }

    class Body {
        <<abstract>>
        #Vector3 Position
        +Accept(IVisitor~T~ visitor) T*
    }

    class Ball {
        +double Radius
        +Accept(IVisitor~T~ visitor) T
    }

    class RectangularCuboid {
        +double SizeX
        +double SizeY
        +double SizeZ
        +Accept(IVisitor~T~ visitor) T
    }

    class Cylinder {
        +double SizeZ
        +double Radius
        +Accept(IVisitor~T~ visitor) T
    }

    class CompoundBody {
        +IReadOnlyList~Body~ Parts
        +Accept(IVisitor~T~ visitor) T
    }

    class BoundingBoxVisitor {
        +Visit(Ball ball) RectangularCuboid
        +Visit(RectangularCuboid rectangularCuboid) RectangularCuboid
        +Visit(Cylinder cylinder) RectangularCuboid
        +Visit(CompoundBody compoundBody) RectangularCuboid
    }

    class BoxifyVisitor {
        +Visit(Ball ball) Body
        +Visit(RectangularCuboid rectangularCuboid) Body
        +Visit(Cylinder cylinder) Body
        +Visit(CompoundBody compoundBody) Body
    }

    class Vector3 {
        +double X
        +double Y
        +double Z
    }

    Body <|-- Ball : наследует
    Body <|-- RectangularCuboid : наследует
    Body <|-- Cylinder : наследует
    Body <|-- CompoundBody : наследует

    IVisitor~T~ <|.. BoundingBoxVisitor : реализует
    IVisitor~T~ <|.. BoxifyVisitor : реализует

    Body *-- Vector3 : содержит (композиция)

    CompoundBody o-- Body : содержит части (агрегация)

    BoundingBoxVisitor ..> RectangularCuboid : зависимость
    BoxifyVisitor ..> BoundingBoxVisitor : зависимость
```

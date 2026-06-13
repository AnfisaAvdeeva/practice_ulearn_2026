# Практика: HoMM

## 1. Описание предметной области и сущностей
Player - игрок. Он может побеждать армии, получать сокровища, присваивать объекты и погибать при поражении.

Interaction - обработка взаимодействия игрока с объектами карты. 

IOwnable - интерфейс объектов, могут принадлежать игроку и иметь владельца.

IHaveArmy - интерфейс объектов, содержат армию и могут участвовать в бою с игроком.

IHaveTreasure - интерфейс объектов, содержат сокровища и могут быть получены игроком.

Dwelling - объект карты, может принадлежать игроку.

Mine - объект карты, может принадлежать игроку, содержит армию и сокровища.

Creeps - объект карты, содержит армию и сокровища, не имеет владельца.

Wolves - объект карты, содержит только армию.

ResourcePile - объект карты, содержит только сокровища.

Army - сущность, боевая сила объекта.

Treasure - сущность, описывает описывающая награду игрока.


## 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    direction TB

    class IOwnable {
        <<interface>>
        +Owner: int
    }

    class IHaveArmy {
        <<interface>>
        +Army: Army
    }

    class IHaveTreasure {
        <<interface>>
        +Treasure: Treasure
    }

    class Dwelling {
        +Owner: int
    }

    class Mine {
        +Owner: int
        +Army: Army
        +Treasure: Treasure
    }

    class Creeps {
        +Army: Army
        +Treasure: Treasure
    }

    class Wolves {
        +Army: Army
    }

    class ResourcePile {
        +Treasure: Treasure
    }

    class Player {
        +Id: int
        +CanBeat(Army) bool
        +Die()
        +Consume(Treasure)
    }

    class Army {
        +Strength: int
    }

    class Treasure {
        +Value: int
    }

    class Interaction {
        <<static>>
        +Make(Player, object)
    }

    IOwnable <|.. Dwelling : реализует
    IOwnable <|.. Mine : реализует
    
    IHaveArmy <|.. Mine : реализует
    IHaveArmy <|.. Creeps : реализует
    IHaveArmy <|.. Wolves : реализует
    
    IHaveTreasure <|.. Mine : реализует
    IHaveTreasure <|.. Creeps : реализует
    IHaveTreasure <|.. ResourcePile : реализует

    Mine *-- Army : содержит
    Mine *-- Treasure : содержит
    Creeps *-- Army : содержит
    Creeps *-- Treasure : содержит
    Wolves *-- Army : содержит
    ResourcePile *-- Treasure : содержит

    Dwelling --> Player : Owner

    Interaction ..> Player : параметр
    Interaction ..> IHaveArmy : проверка
    Interaction ..> IOwnable : проверка
    Interaction ..> IHaveTreasure : проверка
    Interaction ..> Army : параметр CanBeat()
    Interaction ..> Treasure : параметр Consume()
```

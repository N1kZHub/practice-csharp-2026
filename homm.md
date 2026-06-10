# Практика "HoMM"

## Описание предметной области

В игре персонаж взаимодействует с объектами на карте. Существуют три типа взаимодействия: сражение с армией, сбор сокровищ и захват объектов. Выделены интерфейсы: `IOwnable` (захватываемые объекты), `IHasArmy` (объекты с армией), `IHasTreasure` (объекты с сокровищами).

## Диаграмма классов

```mermaid
classDiagram
    
    class Player {
        +int Id
        +int Gold
        +bool Dead
        +CanBeat(Army) bool
        +Consume(Treasure)
        +Die()
    }
    
    class Army {
        +int Power
    }
    
    class Treasure {
        +int Amount
    }
    
    class IOwnable {
        <<interface>>
        +int Owner
    }
    
    class IHasArmy {
        <<interface>>
        +Army Army
    }
    
    class IHasTreasure {
        <<interface>>
        +Treasure Treasure
    }
    
    class Dwelling {
        +int Owner
    }
    
    class Mine {
        +int Owner
        +Army Army
        +Treasure Treasure
    }
    
    class Creeps {
        +Army Army
        +Treasure Treasure
    }
    
    class Wolves {
        +Army Army
    }
    
    class ResourcePile {
        +Treasure Treasure
    }
    
    class Interaction {
        +Make(Player, object)
    }
    
    IOwnable <|.. Dwelling : реализует
    IOwnable <|.. Mine : реализует
    IHasArmy <|.. Mine : реализует
    IHasArmy <|.. Creeps : реализует
    IHasArmy <|.. Wolves : реализует
    IHasTreasure <|.. Mine : реализует
    IHasTreasure <|.. Creeps : реализует
    IHasTreasure <|.. ResourcePile : реализует
    
    Player ..> Army : использует
    Player ..> Treasure : использует
    Interaction ..> Player : управляет
    Interaction ..> IOwnable : использует
    Interaction ..> IHasArmy : использует
    Interaction ..> IHasTreasure : использует
```
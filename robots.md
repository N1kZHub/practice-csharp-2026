# Практика "Роботы"

## Описание предметной области

Применены дженерики с ковариацией и контрвариацией для создания архитектуры роботов. AI генерирует команды (ковариация `out`), Device выполняет команды (контрвариация `in`).
## Диаграмма классов

```mermaid
classDiagram
    
    class Point {
        +double X
        +double Y
    }
    
    class IMoveCommand {
        <<interface>>
        +Destination Point
    }
    
    class IShooterMoveCommand {
        <<interface>>
        +Destination Point
        +ShouldHide bool
    }
    
    class IRobotAI~out TCommand~ {
        <<interface>>
        +GetCommand() TCommand
    }
    
    class IDevice~in TCommand~ {
        <<interface>>
        +ExecuteCommand(TCommand) string
    }
    
    class Robot~TCommand~ {
        -IRobotAI~TCommand~ _ai
        -IDevice~TCommand~ _device
        +Start(int steps) IEnumerable~string~
        +Create(IRobotAI~TCommand~, IDevice~TCommand~) Robot~TCommand~$
    }
    
    class ShooterAI {
        +GetCommand() IShooterMoveCommand
    }
    
    class BuilderAI {
        +GetCommand() IMoveCommand
    }
    
    class Mover {
        +ExecuteCommand(IMoveCommand) string
    }
    
    class ShooterMover {
        +ExecuteCommand(IShooterMoveCommand) string
    }
    
    IMoveCommand <|-- IShooterMoveCommand : наследует
    IMoveCommand --> Point : использует
    IShooterMoveCommand --> Point : использует
    
    IRobotAI~IShooterMoveCommand~ <|.. ShooterAI : реализует
    IRobotAI~IMoveCommand~ <|.. BuilderAI : реализует
    
    IDevice~IMoveCommand~ <|.. Mover : реализует
    IDevice~IShooterMoveCommand~ <|.. ShooterMover : реализует
    
    Robot~TCommand~ *-- IRobotAI~TCommand~ : композиция
    Robot~TCommand~ *-- IDevice~TCommand~ : композиция
```

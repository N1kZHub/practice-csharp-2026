# Практика "Роботы"

## Описание

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
    
    class IRobotAI~TCommand~ {
        <<interface>>
        +GetCommand() TCommand
    }
    
    class IDevice~TCommand~ {
        <<interface>>
        +ExecuteCommand(TCommand) string
    }
    
    class Robot~TCommand~ {
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
    
    IMoveCommand <|-- IShooterMoveCommand : расширяет
    IMoveCommand o-- Point : агрегирует
    IShooterMoveCommand o-- Point : агрегирует
    
    IRobotAI~TCommand~ <|.. ShooterAI : реализует
    IRobotAI~TCommand~ <|.. BuilderAI : реализует
    
    IDevice~TCommand~ <|.. Mover : реализует
    IDevice~TCommand~ <|.. ShooterMover : реализует
    
    Robot~TCommand~ *-- IRobotAI~TCommand~ : композиция
    Robot~TCommand~ *-- IDevice~TCommand~ : композиция
```

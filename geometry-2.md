# Практика "Геометрия-2"

## Описание

Применён шаблон Visitor для добавления новых операций над  фигурами без изменения их классов. Реализован обобщённый интерфейс `IVisitor<TResult>` и два посетителя: `BoundingBoxVisitor` (вычисляет ограничивающий параллелепипед) и `BoxifyVisitor` (заменяет фигуры на их bounding boxes).

## Диаграмма классов

```mermaid
classDiagram
    direction TB
    
    class IVisitor~TResult~ {
        <<interface>>
        +Visit(Ball) TResult
        +Visit(RectangularCuboid) TResult
        +Visit(Cylinder) TResult
        +Visit(CompoundBody) TResult
    }
    
    class Body {
        <<abstract>>
        +Vector3 Position
        +Accept~TResult~(IVisitor~TResult~) TResult
    }
    
    class Ball {
        +double Radius
        +Accept~TResult~(IVisitor~TResult~) TResult
    }
    
    class RectangularCuboid {
        +double SizeX
        +double SizeY
        +double SizeZ
        +Accept~TResult~(IVisitor~TResult~) TResult
    }
    
    class Cylinder {
        +double SizeZ
        +double Radius
        +Accept~TResult~(IVisitor~TResult~) TResult
    }
    
    class CompoundBody {
        +IReadOnlyList~Body~ Parts
        +Accept~TResult~(IVisitor~TResult~) TResult
    }
    
    class BoundingBoxVisitor {
        +Visit(Ball) RectangularCuboid
        +Visit(RectangularCuboid) RectangularCuboid
        +Visit(Cylinder) RectangularCuboid
        +Visit(CompoundBody) RectangularCuboid
    }
    
    class BoxifyVisitor {
        +Visit(Ball) Body
        +Visit(RectangularCuboid) Body
        +Visit(Cylinder) Body
        +Visit(CompoundBody) Body
    }
    
    IVisitor~TResult~ <|.. BoundingBoxVisitor : реализует
    IVisitor~TResult~ <|.. BoxifyVisitor : реализует
    
    Body <|-- Ball : наследует
    Body <|-- RectangularCuboid : наследует
    Body <|-- Cylinder : наследует
    Body <|-- CompoundBody : наследует
    
    BoundingBoxVisitor ..> RectangularCuboid : создаёт
    BoxifyVisitor ..> RectangularCuboid : создаёт
    BoxifyVisitor ..> CompoundBody : создаёт
    CompoundBody o-- Body : содержит
```
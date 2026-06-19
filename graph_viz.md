# Практика: GraphViz

## Описание решения

Реализован паттерн Builder с Fluent API для создания графов в формате DOT. Архитектура использует композицию вместо наследования: `GraphBuilder` управляет созданием графа, а конфигураторы (`NodeConfigurator`, `EdgeConfigurator`) отвечают за настройку атрибутов через метод `With()`. Неявные преобразования обеспечивают плавные переходы между состояниями цепочки вызовов.

## Диаграмма классов

```mermaid
classDiagram
    direction TB

    class DotGraphBuilder {
        <<static>>
        +DirectedGraph(name) GraphBuilder
        +UndirectedGraph(name) GraphBuilder
    }

    class GraphBuilder {
        -Graph graph
        -GraphNode lastNode
        -GraphEdge lastEdge
        +AddNode(name) NodeConfigurator
        +AddEdge(source, dest) EdgeConfigurator
        +Build() string
        +GetGraph() Graph
    }

    class FluentBuilderBase {
        <<abstract>>
        #GraphBuilder builder
        +AddNode(name) NodeConfigurator
        +AddEdge(source, dest) EdgeConfigurator
    }

    class NodeConfigurator {
        -GraphNode node
        +With(configure) GraphBuilder
        +Build() string
    }

    class EdgeConfigurator {
        -GraphEdge edge
        +With(configure) GraphBuilder
        +Build() string
    }

    class NodeAttributes {
        -Dictionary~string, string~ attributes
        +Color(color) NodeAttributes
        +FontSize(size) NodeAttributes
        +Label(label) NodeAttributes
        +Shape(shape) NodeAttributes
    }

    class EdgeAttributes {
        -Dictionary~string, string~ attributes
        +Color(color) EdgeAttributes
        +FontSize(size) EdgeAttributes
        +Label(label) EdgeAttributes
        +Weight(weight) EdgeAttributes
    }

    class Graph {
        +AddNode(name) GraphNode
        +AddEdge(source, dest) GraphEdge
    }

    class GraphNode {
        +Name
        +Attributes
    }

    class GraphEdge {
        +SourceNode
        +DestinationNode
        +Attributes
    }

    class NodeShape {
        <<enumeration>>
        Box
        Ellipse
        Circle
        Diamond
        Record
    }

    DotGraphBuilder ..> GraphBuilder : создаёт
    GraphBuilder --> Graph : ассоциация (использует)
    FluentBuilderBase <|-- NodeConfigurator : наследует
    FluentBuilderBase <|-- EdgeConfigurator : наследует
    GraphBuilder ..> FluentBuilderBase : зависимость (создаёт конфигураторы)
    NodeConfigurator ..> NodeAttributes : конфигурирует
    EdgeConfigurator ..> EdgeAttributes : конфигурирует
    Graph ..> GraphNode : создаёт
    Graph ..> GraphEdge : создаёт
    NodeConfigurator --> GraphNode : хранит ссылку
    EdgeConfigurator --> GraphEdge : хранит ссылку

    note for GraphBuilder "Возвращает конфигураторы для продолжения цепочки"
    note for NodeConfigurator "With() возвращает GraphBuilder для продолжения цепочки"
    note for EdgeConfigurator "With() возвращает GraphBuilder для продолжения цепочки"
    note for FluentBuilderBase "Базовый класс для конфигураторов. Дублирует методы для переиспользования"
```

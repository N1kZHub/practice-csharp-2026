# Практика: GraphViz

## Описание решения

Реализован паттерн Builder с Fluent API для создания графов в формате DOT. Архитектура использует композицию вместо наследования: `GraphBuilder` управляет созданием графа, а конфигураторы (`NodeConfigurator`, `EdgeConfigurator`) отвечают за настройку атрибутов через метод `With()`. Неявные преобразования обеспечивают плавные переходы между состояниями цепочки вызовов.

## Диаграмма классов

```mermaid
classDiagram
    direction TB

    %% Точка входа
    class DotGraphBuilder {
        <<static>>
        +DirectedGraph(name) GraphBuilder
        +UndirectedGraph(name) GraphBuilder
    }

    %% Основной построитель
    class GraphBuilder {
        -Graph graph
        -GraphNode lastNode
        -GraphEdge lastEdge
        +AddNode(name) NodeConfigurator
        +AddEdge(source, dest) EdgeConfigurator
        +Build() string
        +GetGraph() Graph
    }

    %% Базовый класс для конфигураторов
    class FluentBuilderBase {
        <<abstract>>
        #GraphBuilder builder
        +AddNode(name) NodeConfigurator
        +AddEdge(source, dest) EdgeConfigurator
    }

    %% Конфигураторы
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

    %% Атрибуты
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

    %% Инфраструктура
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

    %% Enum
    class NodeShape {
        <<enumeration>>
        Box
        Ellipse
        Circle
        Diamond
        Record
    }

    %% Связи
    DotGraphBuilder ..> GraphBuilder : создаёт
    GraphBuilder --> Graph : использует
    FluentBuilderBase <|-- NodeConfigurator : наследует
    FluentBuilderBase <|-- EdgeConfigurator : наследует
    GraphBuilder --> FluentBuilderBase : создаёт
    NodeConfigurator --> NodeAttributes : конфигурирует
    EdgeConfigurator --> EdgeAttributes : конфигурирует
    GraphBuilder *-- Graph : агрегирует
    Graph --> GraphNode : создаёт
    Graph --> GraphEdge : создаёт
    NodeConfigurator --> GraphNode : хранит ссылку
    EdgeConfigurator --> GraphEdge : хранит ссылку

    %% Fluent API цепочки
    note for GraphBuilder "Возвращает конфигураторы\nдля продолжения цепочки"
    note for NodeConfigurator "With() возвращает\nGraphBuilder для\nпродолжения цепочки"
    note for EdgeConfigurator "With() возвращает\nGraphBuilder для\nпродолжения цепочки"
```
# Практика "Дифференцирование"

## Описание

Программа вычисляет производную функции одной переменной. На вход подаётся лямбда-выражение, преобразуемое в дерево узлов. Каждый узел обрабатывается по правилам математического анализа.

## Диаграмма классов

```mermaid
classDiagram
    direction LR
    
    class Algebra {
        <<static>>
        +Differentiate(Expression~Func~double, double~~) Expression~Func~double, double~~
        -Derive(Expression, ParameterExpression) Expression
    }
    
    class Expression {
        <<abstract>>
        +NodeType ExpressionType
    }
    
    class ConstantExpression {
        +Value object
    }
    
    class ParameterExpression {
        +Type Type
    }
    
    class BinaryExpression {
        +NodeType ExpressionType
        +Left Expression
        +Right Expression
    }
    
    class MethodCallExpression {
        +Method MethodInfo
        +Object Expression
        +Arguments ReadOnlyCollection~Expression~
    }
    
    class ExpressionType {
        <<enumeration>>
        Constant
        Parameter
        Addition
        Multiplication
        MethodCall
    }
    
    Algebra ..> Expression : анализирует
    Algebra ..> ParameterExpression : возвращает 1 для переменной
    Algebra ..> BinaryExpression : правило произведения
    Algebra ..> MethodCallExpression : цепное правило
    Algebra ..> ExpressionType : проверяет тип узла
    
    Expression <|-- ConstantExpression : расширяет
    Expression <|-- ParameterExpression : расширяет
    Expression <|-- BinaryExpression : расширяет
    Expression <|-- MethodCallExpression : расширяет
    
    BinaryExpression --> ExpressionType : определяет операцию
    MethodCallExpression --> ExpressionType : имеет тип Call
```
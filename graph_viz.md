# Практика: GraphViz

## 1. Описание предметной области и сущностей
Система предназначена для построения графов в формате DOT с использованием Fluent API. Она позволяет создавать ориентированные и неориентированные графы, добавлять вершины и рёбра с атрибутами, а затем генерировать текстовое представление графа в формате, совместимом с GraphViz.

INodeAttributes - интерфейс настройки атрибутов вершины: цвет, размер шрифта, метка, форма. Поддерживает цепочку вызовов.

IEdgeAttributes - интерфейс настройки атрибутов ребра: цвет, размер шрифта, метка, вес. Поддерживает цепочку вызовов.

INodeBuilder - построитель вершины до применения атрибутов. Содержит метод With для настройки, методы добавления элементов и Build.

INodeConfigured - построитель вершины после применения атрибутов. Не содержит With, предотвращает повторную настройку.

IEdgeBuilder - построитель ребра до применения атрибутов. Содержит метод With для настройки, методы добавления элементов и Build.

IEdgeConfigured - построитель ребра после применения атрибутов. Не содержит With, предотвращает повторную настройку.

DotGraphBuilder - точка входа в библиотеку. Статические методы DirectedGraph/UndirectedGraph создают граф. Методы AddNode/AddEdge добавляют элементы, Build генерирует DOT-представление.

NodeBuilder - внутренняя реализация INodeBuilder. Хранит Graph и GraphNode. With создаёт NodeAttributes и возвращает NodeConfigured.

NodeConfigured - внутренняя реализация INodeConfigured. Хранит Graph. Не имеет With. Создаёт новые билдеры через AddNode/AddEdge.

EdgeBuilder - внутренняя реализация IEdgeBuilder. Хранит Graph и GraphEdge. With создаёт EdgeAttributes и возвращает EdgeConfigured.

EdgeConfigured - внутренняя реализация IEdgeConfigured. Хранит Graph. Не имеет With. Создаёт новые билдеры через AddNode/AddEdge.

NodeAttributes - внутренняя реализация INodeAttributes. Хранит GraphNode. Записывает атрибуты в словарь вершины. Возвращает this для цепочки вызовов.

EdgeAttributes - внутренняя реализация IEdgeAttributes. Хранит GraphEdge. Записывает атрибуты в словарь ребра. Возвращает this для цепочки вызовов.

Graph - внешний класс-хранилище графа. Содержит коллекции вершин и рёбер. Методы AddNode/AddEdge для добавления, ToDotFormat для генерации DOT.

GraphNode - внешний класс вершины. Содержит словарь атрибутов и свойство Name.

GraphEdge - внешний класс ребра. Содержит словарь атрибутов, свойства SourceNode, DestinationNode и флаг Directed.

NodeShape - перечисление форм вершины: Box и Ellipse. Используется в INodeAttributes.


## 2. Диаграмма классов (Mermaid)

```mermaid
classDiagram
    class INodeAttributes {
        <<interface>>
        +Color(string) INodeAttributes
        +FontSize(int) INodeAttributes
        +Label(string) INodeAttributes
        +Shape(NodeShape) INodeAttributes
    }

    class IEdgeAttributes {
        <<interface>>
        +Color(string) IEdgeAttributes
        +FontSize(int) IEdgeAttributes
        +Label(string) IEdgeAttributes
        +Weight(double) IEdgeAttributes
    }

    class INodeBuilder {
        <<interface>>
        +With(Action~INodeAttributes~) INodeConfigured
        +AddNode(string) INodeBuilder
        +AddEdge(string,string) IEdgeBuilder
        +Build() string
    }

    class INodeConfigured {
        <<interface>>
        +AddNode(string) INodeBuilder
        +AddEdge(string,string) IEdgeBuilder
        +Build() string
    }

    class IEdgeBuilder {
        <<interface>>
        +With(Action~IEdgeAttributes~) IEdgeConfigured
        +AddNode(string) INodeBuilder
        +AddEdge(string,string) IEdgeBuilder
        +Build() string
    }

    class IEdgeConfigured {
        <<interface>>
        +AddNode(string) INodeBuilder
        +AddEdge(string,string) IEdgeBuilder
        +Build() string
    }

    class DotGraphBuilder {
        -Graph graph
        +DirectedGraph(string) DotGraphBuilder$
        +UndirectedGraph(string) DotGraphBuilder$
        +AddNode(string) INodeBuilder
        +AddEdge(string,string) IEdgeBuilder
        +Build() string
    }

    class NodeBuilder {
        -Graph graph
        -GraphNode node
        +With(Action~INodeAttributes~) INodeConfigured
        +AddNode(string) INodeBuilder
        +AddEdge(string,string) IEdgeBuilder
        +Build() string
    }

    class NodeConfigured {
        -Graph graph
        +AddNode(string) INodeBuilder
        +AddEdge(string,string) IEdgeBuilder
        +Build() string
    }

    class EdgeBuilder {
        -Graph graph
        -GraphEdge edge
        +With(Action~IEdgeAttributes~) IEdgeConfigured
        +AddNode(string) INodeBuilder
        +AddEdge(string,string) IEdgeBuilder
        +Build() string
    }

    class EdgeConfigured {
        -Graph graph
        +AddNode(string) INodeBuilder
        +AddEdge(string,string) IEdgeBuilder
        +Build() string
    }

    class NodeAttributes {
        -GraphNode node
        +Color(string) INodeAttributes
        +FontSize(int) INodeAttributes
        +Label(string) INodeAttributes
        +Shape(NodeShape) INodeAttributes
    }

    class EdgeAttributes {
        -GraphEdge edge
        +Color(string) IEdgeAttributes
        +FontSize(int) IEdgeAttributes
        +Label(string) IEdgeAttributes
        +Weight(double) IEdgeAttributes
    }

    class Graph {
        <<external>>
        +AddNode(string) GraphNode
        +AddEdge(string,string) GraphEdge
        +ToDotFormat() string
    }

    class GraphNode {
        <<external>>
        +Attributes Dictionary
        +Name string
    }

    class GraphEdge {
        <<external>>
        +Attributes Dictionary
        +SourceNode string
        +DestinationNode string
    }

    class NodeShape {
        <<enumeration>>
        Box
        Ellipse
    }

    INodeBuilder <|.. NodeBuilder : реализует
    INodeConfigured <|.. NodeConfigured : реализует
    IEdgeBuilder <|.. EdgeBuilder : реализует
    IEdgeConfigured <|.. EdgeConfigured : реализует
    INodeAttributes <|.. NodeAttributes : реализует
    IEdgeAttributes <|.. EdgeAttributes : реализует

    DotGraphBuilder --> Graph : содержит
    NodeBuilder --> Graph : содержит
    NodeBuilder --> GraphNode : содержит
    NodeConfigured --> Graph : содержит
    EdgeBuilder --> Graph : содержит
    EdgeBuilder --> GraphEdge : содержит
    EdgeConfigured --> Graph : содержит
    NodeAttributes --> GraphNode : содержит
    EdgeAttributes --> GraphEdge : содержит

    DotGraphBuilder ..> NodeBuilder : создаёт в AddNode()
    DotGraphBuilder ..> EdgeBuilder : создаёт в AddEdge()
    NodeBuilder ..> NodeConfigured : создаёт в With()
    NodeBuilder ..> NodeAttributes : создаёт в With()
    EdgeBuilder ..> EdgeConfigured : создаёт в With()
    EdgeBuilder ..> EdgeAttributes : создаёт в With()
    NodeConfigured ..> NodeBuilder : создаёт в AddNode()
    NodeConfigured ..> EdgeBuilder : создаёт в AddEdge()
    EdgeConfigured ..> NodeBuilder : создаёт в AddNode()
    EdgeConfigured ..> EdgeBuilder : создаёт в AddEdge()
    
    NodeAttributes ..> NodeShape : использует
    EdgeAttributes ..> NodeShape : использует
    INodeAttributes ..> NodeShape : использует
```

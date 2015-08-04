# Node Graph Widget #





## What is this? ##

It's a UI widget for Vaadin to generate direct connected nodes graphs.

![http://nodegraph-widget.googlecode.com/svn/wiki/images/nodegraph.png](http://nodegraph-widget.googlecode.com/svn/wiki/images/nodegraph.png)

### Goals ###

The intent of this project is:
  * Generate a Widget for easy development of connected node graph models.
  * Provide some layouts to automatic position of the nodes.

### What this widget provides ###

  * Easy way to generate a representation of nodes connected graphs.
  * Drag and drop Nodes.
  * Easy way to customize Nodes (through CSS).
  * Set of pre-defined Nodes.
  * Customizable connections between nodes (Color, Width, Connection Types - Straight lines and Bezier curves).

### What it lacks ###

  * Connection decorations like arrows and other kind of pointers.
  * Fully Bidirectional synchronization between the model and the client graph.
  * Customizable label placement.
  * Support for labels on connections.
  * Layout Support (SpringLayout,

### Dependencies ###

The NodeGraph Widget has the following dependencies:

  * [Vectomatic Lib 0.5.2](http://www.vectomatic.org/lib-gwt-svg)
  * [GWT Drag & Drop 2.6.5](http://code.google.com/p/gwt-dnd/)
  * [FlexJson 2.1](http://flexjson.sourceforge.net/)
  * [GWT Json Wrapper 0.2](http://code.google.com/p/gwt-jsonwrapper/)

## Examples ##

### How to create a new Graph Model ###

The following piece of code shows how to create a new graph model that contain some nodes with connections between them.

```
// = Create the model.
final GraphModel graphModel = GraphModelFactory.getGraphModelInstance();

// = Support for single click to select nodes.
graphModel.setSingleSelectionSupport(Boolean.TRUE);

// = Create some nodes.
Node begin = new BeginNode();

Node splitNode = new SplitNode();

Node nodeA = new DefaultNode();
nodeA.setLabel("NodeA");

Node nodeB = new DefaultNode();
nodeB.setLabel("NodeB");

Node processCreation = new ProcessCreationNode();
processCreation.setLabel("Process");

Node end = new EndNode();

// = Nodes meet nodes!.
final RelationStyle dashedBlue = new DefaultRelationStyle(GraphConstants.DOM.CSS_BLUE_VALUE, 2);
dashedBlue.setDashedStroke(5, 5);

final RelationStyle straightRed = new DefaultRelationStyle();
straightRed.strokeColor(GraphConstants.DOM.CSS_RED_VALUE).strokeWidth(3);

final RelationStyle defaultNormalBlack = new DefaultRelationStyle();

// = Tie all the stuff.
graphModel.connect(begin, splitNode, straightRed);
graphModel.connect(splitNode, nodeA, dashedBlue);
graphModel.connect(splitNode, nodeB, defaultNormalBlack);
graphModel.connect(nodeA, end, RelationTypeEnum.BEZIER);
graphModel.connect(nodeB, processCreation, RelationTypeEnum.BEZIER, new DefaultRelationStyle().strokeColor(GraphConstants.DOM.CSS_GREEN_VALUE));
graphModel.connect(processCreation, end, RelationTypeEnum.LINE);
```

### How to create a new Component Instance ###

To create a new instance of the component just create a new instance as usual of the class **GraphComponent** and pass the model into his constructor.
Then you can add the component to any normal container.

```
// = Retrive the model from somewhere...
final GraphModel graphModel = getGraphModel ... ;

// = Create the component and pass the model on its constructor.
final GraphComponent component = new GraphComponent(graphModel);
component.setSizeFull();

// = Add the component to a container...
final VerticalLayout layout = new VerticalLayout();
layout.setMargin(true);
layout.setSpacing(true);
layout.addComponent(component);
```

### How to listen for events ###

Each client Graph change event is notified to the model on the server side, and all affected properties are updated.

To listen for graph changes, add a listener to specific event types. There is three kinds of event types: _GRAPHMODEL\_EVENTS_, _NODE\_EVENTS_ and _RELATIONSHIP\_EVENTS_.

**GRAPH\_MODEL** raise (partial implementation):

  * _INITIALIZED_: Occurs once the graph was rendered in the client.

**NODE** raise (partial implementation):

  * _POSITION_: When the user drag the selected Node around the container.
  * _LABEL_: When the label node change his value.
  * _SELECTED_: When the user makes click on a Node.
  * _UNSELECTED_: When the user makes a double click on a selected Node.
  * _ENABLED_: (Not implemented yet).
  * _DISABLED_: (Not implemented yet).

**RELATION** raise:

  * _FROM_: When a connection change the source Node. (Not implemented yet).
  * _TO_: When a connection change the target Node. (Not implemented yet).

```
// = Listen for specific event occurs.
graphModel.registerCallback(NodeEventType.SELECTED, new PropertyChangeCallback() {
	
	/**
	 * {@inheritdoc}
	 */
	@Override
	public void onPropertyChange(PropertyChangeEvent propertyChangeEvent) {
		System.out.println("Property=" + propertyChangeEvent.getPropertyName());
		System.out.println("New Value:" + propertyChangeEvent.getNewValue());
		System.out.println("Old Value:" + propertyChangeEvent.getOldValue());
	}
});

// = To listen for more than one event type at the same time:
// = Define the event types to listen...
final EventType[] eventTypes = { NodeEventType.SELECTED, NodeEventType.POSITION, GraphModelEventType.INITIALIZED };

// = Register the array event types...
graphModel.registerCallbacks(eventTypes, new PropertyChangeCallback() {
	
	/**
	 * {@inheritdoc}
	 */
	@Override
	public void onPropertyChange(PropertyChangeEvent propertyChangeEvent) {
		System.out.println("Property=" + propertyChangeEvent.getPropertyName());
		System.out.println("New Value:" + propertyChangeEvent.getNewValue());
		System.out.println("Old Value:" + propertyChangeEvent.getOldValue());
	}
});
```
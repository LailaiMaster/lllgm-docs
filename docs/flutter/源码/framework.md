启动源码

```dart
WidgetsBinding
void runApp(Widget app) {
  WidgetsFlutterBinding.ensureInitialized()
    ..scheduleAttachRootWidget(app)
    ..scheduleWarmUpFrame();
}
scheduleAttachRootWidget-》attachRootWidget
attachRootwidget，将我们的根widget链接到renderTree上：
void attachRootWidget(Widget rootWidget) {
  _readyToProduceFrames = true;
  _renderViewElement = RenderObjectToWidgetAdapter<RenderBox>(
    container: renderView,
    debugShortDescription: '[root]',
    child: rootWidget,
  ).attachToRenderTree(buildOwner, renderViewElement as RenderObjectToWidgetElement<RenderBox>);
}
buildOwner
[_buildOwner]的初始化必须在[initInstances]方法中完成，因为它需要[ServicesBinding]才能正确设置
[defaultBinaryMessenger]实例。
RenderObjectToWidgetAdapter
从[RenderObject]到[Element]树的桥梁。 
给定的容器是应将[Element]树插入的[RenderObject]。
RenderObjectToWidgetElement<T> attachToRenderTree(BuildOwner owner, [ RenderObjectToWidgetElement<T> element ]) {
  if (element == null) {
    owner.lockState(() {
    ///创建element
      element = createElement();
      assert(element != null);
      element.assignOwner(owner);
    });
    ///建立构建范围，先调用的element.mount(),将标记为脏的布局rebuild
    ///element是RenderObjectToWidgetAdapter createElement创建的
    owner.buildScope(element, () {
    ///mount方法
      element.mount(null, null);
    });
    SchedulerBinding.instance.ensureVisualUpdate();
  } else {
    element._newWidget = this;
    element.markNeedsBuild();
  }
  return element;
}
RenderObjectToWidgetElement
mount
    ///mount 的目的是将element放到element树中，然后
    ///[mount]将新创建的元素添加到树中给定父级的给定插槽中。
    ///[mount]方法负责使所有子窗口小部件inflate，并根据需要调用
    ///[attachRenderObject]以便将任何关联的渲染对象附
void mount(Element parent, dynamic newSlot) {
  assert(parent == null);
  ///super renderObjectRootElement  super RenderObjectElement
  super.mount(parent, newSlot);
  _rebuild();
}
RenderObjectElement
 @override
  void mount(Element parent, dynamic newSlot) {
    super.mount(parent, newSlot);
    ///这里创建了renderObject
    _renderObject = widget.createRenderObject(this);
    ///插入到插槽中
    attachRenderObject(newSlot);
    _dirty = false;
  }
  

```




```dart
RenderObject
void layout(Constraints constraints, { bool parentUsesSize = false }) {
 
  RenderObject relayoutBoundary;
  if (!parentUsesSize || sizedByParent || constraints.isTight || parent is! RenderObject) {
    relayoutBoundary = this;
  } else {
    relayoutBoundary = (parent as RenderObject)._relayoutBoundary;
  }
  if (!_needsLayout && constraints == _constraints && relayoutBoundary == _relayoutBoundary) {
   
  _constraints = constraints;
  if (_relayoutBoundary != null && relayoutBoundary != _relayoutBoundary) {
    visitChildren(_cleanChildRelayoutBoundary);
  }
  _relayoutBoundary = relayoutBoundary;
 
  if (sizedByParent) {
  
    try {
      /// performResize
      performResize();
     
    } catch (e, stack) {
      _debugReportException('performResize', e, stack);
    }
  
  }
  RenderObject debugPreviousActiveLayout;
  
  try {
    performLayout();
    markNeedsSemanticsUpdate();
   
  } catch (e, stack) {
    _debugReportException('performLayout', e, stack);
  }
 
  _needsLayout = false;
  markNeedsPaint();
}
```



```dart
class TestRenderObjectWidget extends RenderObjectWidget{
  @override
  RenderObjectElement createElement() {
    return TestElement(this);
  }
  @override
  RenderObject createRenderObject(BuildContext context) {
    return TestRenderObject();
  }
}
class TestElement extends RenderObjectElement{
  TestElement(RenderObjectWidget widget) : super(widget);

  @override
  void insertChildRenderObject(RenderObject child, slot) {
  }
  @override
  void moveChildRenderObject(RenderObject child, slot) {
  }
  @override
  void removeChildRenderObject(RenderObject child) {
  }
}
class TestRenderObject extends RenderObject{
  @override
  void debugAssertDoesMeetConstraints() {
  }
  @override
  Rect get paintBounds => throw UnimplementedError();
  @override
  void performLayout() {
  }
  @override
  void performResize() {
  }
  @override
  Rect get semanticBounds => throw UnimplementedError();
  
}
```






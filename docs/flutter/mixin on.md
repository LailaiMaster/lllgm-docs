```plain
abstract class Base {
  a() {
    print("base a()");
  }
  b() {
    print("base b()");
  }
  c() {
    print("base c()");
  }
}
mixin A on Base {
  a() {
    print("A.a()");
    super.a();
  }
  b() {
    print("A.b()");
    super.b();
  }
}
mixin A2 on Base {
  a() {
    print("A2.a()");
    super.a();
    print("A2.a() after");
  }
}
class B extends Base {
  a() {
    print("B.a()");
    super.a();
  }
  b() {
    print("B.b()");
    super.b();
  }
  c() {
    print("B.c()");
    super.c();
  }
}
class G extends B with A, A2 {
}
void main(){
  G t = new G();
  t.a();
  t.b();
  t.c();
}
```
同方法名调用优先级 最后面的最高，
同时如果有base的话，base的优先级最低，且需要super手动调用。


mixin de super 

```plain
abstract class Base {
  Base() {
    print('Base---');
    initInstances();
  }
  void initInstances() {
    print('initInstances---');
  }
}
mixin A on Base {
  @override
  void initInstances() {
    print('initInstances--A-');
    super.initInstances();
  }
}
mixin A1 on Base {
  @override
  void initInstances() {
    print('initInstances--A1-');
    super.initInstances();
  }
}
class G extends Base with A, A1 {}
void main() {
  G t = new G();
}
```
Base---
initInstances--A1-

initInstances--A-

initInstances---


千万不要把mixin on 类比成类继承。

优先级还是后面的高，优先调用后面的，super类似连接符或者理解成前面一个混入代码。


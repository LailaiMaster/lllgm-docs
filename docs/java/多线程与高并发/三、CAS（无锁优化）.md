Compare And Set

cas(V,Expected,NewValue)

if V==Expected

    V=New

CPU原语支持


ABA问题  加版本号 时间戳

synchronzied 在转为重量锁之前 也是cas

AtomicInteger cas

LongAdder（分段锁）




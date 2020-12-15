常用的函数接口之一，表示接收一个参数，返回接口结果

这个函数接口的使用方法是`apply(Object)`

![Function的接口方法](http://cdn.mtmn.top/image-60349bfadd034c2a8f5defab26e03a59.png)

```
    R apply(T t);
    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }
    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }
    static <T> Function<T, T> identity() {
        return t -> t;
    }

```


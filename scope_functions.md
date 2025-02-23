### What are Scope functions?
## Scope functions are functions in the Kotlin standard library whose purpose is to execute a block of code within the context of an object. It provides a temporary scope when you call the function on the object with a lambda expression. In this scope, the function can access the object without its name. There are five scope functions

- let
- run
- with
- apply
- also

## How do they refer to the context object?
## Inside the lambda of a scope function, the context object could be referenced in 2 ways

## as a lambda receiver (this)
## lambda argument (it)

![](https://github.com/saeedashrafy/learn/blob/main/Screenshot%201403-12-05%20at%2007.08.51.png)
 
```
@kotlin.internal.InlineOnly
public inline fun <T, R> T.let(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block(this)
}

```

```
@kotlin.internal.InlineOnly
public inline fun <T> T.apply(block: T.() -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block()
    return this
}
```

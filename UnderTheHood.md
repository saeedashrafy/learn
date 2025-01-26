## What does @Composable mean?
#### An important thing to note is that Compose is not an annotation processor. Compose works with the aid of a Kotlin compiler plugin in the type checking and code generation phases of Kotlin: there is no annotation processor needed in order to use compose.

```
fun Example(a: () -> Unit, b: @Composable () -> Unit) {
   a() // allowed
   b() // NOT allowed
}
 
@Composable 
fun Example(a: () -> Unit, b: @Composable () -> Unit) {
   a() // allowed
   b() // allowed
}
```

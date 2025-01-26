## What does @Composable mean?
#### An important thing to note is that Compose is not an annotation processor. Compose works with the aid of a Kotlin compiler plugin in the type checking and code generation phases of Kotlin: there is no annotation processor needed in order to use compose.


### Each compose function can only be called from within another composable. This is because there’s a calling context object that we need to thread through all of the invocations.

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
## Composer 

#### We call this object the “Composer”. The implementation of the Composer contains a data structure that is closely related to a Gap Buffer. This data structure is commonly used in text editors.

#### A gap buffer represents a collection with a current index or cursor. It is implemented in memory with a flat array. That flat array is larger than the collection of data that it represents, with the unused space referred to as the gap.

![](https://miro.medium.com/v2/resize:fit:720/format:webp/0*0GgJdY76c_Kz0hs-)

### Once the composition begins, it will start inserting all required things(compose function and states) into this array.
![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*JjOJ8mrThTM90IbB7cLaxg.jpeg)

### When we have to perform recomposition, we will always start from the beginning of the array (i.e. reset the current index) and either update the item or skip it according to the states changed.
![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*J41HewaIinyfU1xP-HhLHQ.jpeg)

### If in any case, your compiler finds that the structure of the UI has changed (you might be showing some extra conditional UI/Composables), at this point we move the gap to the current position.
![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*PGRSdnS3B_Ca3-zDiG49NQ.jpeg)

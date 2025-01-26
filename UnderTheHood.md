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

### And now, add the new items in the array
![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*gHuy1hQhwj7wqB-xl2OEFA.jpeg)

> [!IMPORTANT]
> The reason we chose this data structure is because we’re making a bet that, on average, UIs don’t change structure very much. When we have dynamic UIs, they change in terms of the values but they don’t change in structure nearly as often. When they do change their structure, they typically change in big chunks, so doing this O(n) gap move is a reasonable trade-off.

### Now, let’s see this array thing with Counter example:

```
@Composable
fun Counter() {
 var count by remember { mutableStateOf(0) }
 Button(
   text="Count: $count",
   onPress={ count += 1 }
 )
}
```

```
fun Counter($composer: Composer) {
 $composer.start(123)
 var count by remember($composer) { mutableStateOf(0) }
 Button(
   $composer,
   text="Count: $count",
   onPress={ count += 1 },
 )
 $composer.end()
}
```

### When this composer executes it does the following
+ Composer.start gets called and stores a group object
+ remember inserts a group object(it is required to skip resetting the values)
+ the value that mutableStateOf returns, the state instance is stored(as it triggers the recomposition).
+ Button stores a group, followed by each of its parameters(all the nested things(in a Depth-first traversal) are also inserted in the array).
+ and finally composer.end

![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*R19RTLWCEHmA8KvNLMRilQ.jpeg)
  جچج

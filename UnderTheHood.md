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

#### It keeps track of what has changed in a composable function and determines whether it should be re-executed (recomposed) or skipped. The implementation of the Composer contains a data structure that is closely related to a Gap Buffer. This data structure is commonly used in text editors.

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

> The 123, 456, or 789 are the compiler-generated keys used to define/identify the structure of the UI. If the call to composer.start has a group with the key 456, but in the array, it was previously stored with 123, then the compiler knows that the UI has changed its structure 


### Another Example

```
@Composable fun App() {
 val result = getData()
 if (result == null) {
   Loading(...)
 } else {
   Header(result)
   Body(result)
 }
}
```

```
fun App($composer: Composer) {
 val result = getData()
 if (result == null) {
   $composer.start(123)
   Loading(...)
   $composer.end()
 } else {
   $composer.start(456)
   Header(result)
   Body(result)
   $composer.end()
 }
}
```

### Let’s assume that when this code first executes result is null. This inserts a group into the gap array and the loading screen runs.
![](https://miro.medium.com/v2/resize:fit:720/format:webp/0*CnP4GnP1Pdp20fXY)

### The second time the function runs let’s assume that result is no longer null so that the second branch of the if statement executes. This is where it gets interesting.

> [!IMPORTANT]
> The call to composer.start has a group with the key 456. The compiler sees that the group in the slot table of 123 doesn’t match, so now it knows that the UI has changed in structure.

## Positional Memoization
### Instead of tracking values by variable names, Compose associates them with their order in the UI hierarchy. This approach is called Positional Memoization.

```
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }  // Value is stored in memory
    Button(
        text = "Count: $count",
        onPress = { count++ }
    )
}
```

When Counter() is first composed:

The remember { mutableStateOf(0) } value is stored at position X in the composition tree.
When count changes and the composable recomposes:

Compose retrieves the previous value from position X and reuses it.
remember doesn’t reset because its position remains unchanged in the tree.
✅ Result: The count value persists across recompositions because Compose memorizes its position in the tree.


## Storing parameters

```
@Composable fun Google(number: Int) {
 Address(
   number=number,
   street="Amphitheatre Pkwy",
   city="Mountain View",
   state="CA"
   zip="94043"
 )
}
 
@Composable fun Address(
 number: Int,
 street: String,
 city: String,
 state: String,
 zip: String
) {
 Text("$number $street")
 Text(city)
 Text(", ")
 Text(state)
 Text(" ")
 Text(zip)
}
```

### Compose stores the parameters of a composable function in the slot table. If this is the case, looking at the example above we see some redundancies: “Mountain View” and “CA”, which are added in the address invocation, are stored again in the underlying text invocations, so these strings will be stored twice.

### We can get rid of this redundancy by adding the static parameter to Composable functions at the compiler level.
‍‍‍‍
```
fun Google(
 $composer: Composer,
 $static: Int,
 number: Int
) {
 Address(
   $composer,
   0b11110 or ($static and 0b1),
   number=number,
   street="Amphitheatre Pkwy",
   city="Mountain View",
   state="CA"
   zip="94043"
 )
}
```


### Jetpack Compose: A Declarative, Modern, and Efficient UI Toolkit for Native Android Development
Simplify UI creation with less boilerplate code, intuitive state management, and seamless integration with Kotlin, enabling faster and more flexible app development.

## What challenges does Compose address?
#### When we write code, we create modules that consist of multiple units. Coupling is the dependency among units in different modules and reflects the ways in which parts of one module influence parts of other modules. Cohesion is instead the relationship between the units within a module, and indicates how well grouped the units in the module are.
![sepration of concern](https://miro.medium.com/v2/resize:fit:1024/1*fW9xf6fwPtxxNOIntoWBeA.png)

### When writing maintainable software, our goal is to minimize coupling and maximize cohesion.
#### When we have highly coupled modules, making a change to code in one place means having to make many other changes to other modules.


#### The view model provides data to the layout. It turns out there can be a lot of dependencies
#### a lot of coupling between the view model and the layout
![viewModel](https://miro.medium.com/v2/resize:fit:1400/0*KqhjlWMPRossggev)

### exmaple of high coupling between view(xml) and viewModel

```
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable
            name="viewModel"
            type="com.example.app.UserViewModel" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <!-- Bound to ViewModel properties -->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{viewModel.userName}" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{String.valueOf(viewModel.userAge)}" />

        <!-- Directly tied to ViewModel methods -->
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="@{() -> viewModel.updateName(`Jane Doe`)}"
            android:text="Update Name" />
    </LinearLayout>
</layout>
```

### Jetpack compose is an umberall term for 7 different libraries 

+ compose.animation 
+ compose.foundation: Core layout composables for arranging UI elements (Row, Box ...)
+ compose.material
+ compose.material3
+ compose.ui : It provides the core functionalities required for layout, input handling, and graphics, which are essential for any UI toolkit
+ compose.compiler :When you write a composable function (like @Composable), the compose.compiler transforms it into a special format optimized for tracking state changes and managing recomposition efficiently.
+ compose.runtime : It determines when a composable should be created, updated (recomposed), or disposed.

### Composable functions: 
#### First, display a “Hello world!” text by adding a text element inside the onCreate method. You do this by defining a content block, and calling the Text composable function. The setContent block defines the activity's layout where composable functions are called. Composable functions can only be called from other composable functions.

```
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.material3.Text

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            Text("Hello world!")
        }
    }
}
```

### Define a composable function
#### To make a function composable, add the @Composable annotation. To try this out, define a MessageCard function which is passed a name, and uses it to configure the text element
```
// ...
import androidx.compose.runtime.Composable

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MessageCard("Android")
        }
    }
}

@Composable
fun MessageCard(name: String) {
    Text(text = "Hello $name!")
}
```

### Preview your function in Android Studio 
##### The @Preview annotation lets you preview your composable functions within Android Studio without having to build and install the app to an Android device or emulator. The annotation must be used on a composable function that does not take in parameters. For this reason, you can't preview the MessageCard function directly. Instead, make a second function named PreviewMessageCard, which calls MessageCard with an appropriate parameter. Add the @Preview annotation before @Composable

```
import androidx.compose.ui.tooling.preview.Preview

@Composable
fun MessageCard(name: String) {
    Text(text = "Hello $name!")
}

@Preview
@Composable
fun PreviewMessageCard() {
    MessageCard("Android")
}
```

### Layouts:
#### UI elements are hierarchical, with elements contained in other elements. In Compose, you build a UI hierarchy by calling composable functions from other composable functions.
### Add multiple texts
```
// ...

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MessageCard(Message("Android", "Jetpack Compose"))
        }
    }
}

data class Message(val author: String, val body: String)

@Composable
fun MessageCard(msg: Message) {
    Text(text = msg.author)
    Text(text = msg.body)
}

@Preview
@Composable
fun PreviewMessageCard() {
    MessageCard(
        msg = Message("Lexi", "Hey, take a look at Jetpack Compose, it's great!")
    )
}

```

#### This code creates two text elements inside the content view. However, since you haven't provided any information about how to arrange them, the text elements are drawn on top of each other, making the text unreadable

<img src="https://developer.android.com/static/develop/ui/compose/images/compose-tutorial/lesson2-02.png" width="300" height="600" />

### Using a Column and Row
#### The Column function lets you arrange elements vertically. Add Column to the MessageCard function.
You can use Row to arrange items horizontally and Box to stack elements
```
// ...
import androidx.compose.foundation.Image
import androidx.compose.foundation.layout.Row
import androidx.compose.ui.res.painterResource

@Composable
fun MessageCard(msg: Message) {
    Row {
        Image(
            painter = painterResource(R.drawable.profile_picture),
            contentDescription = "Contact profile picture",
        )
    
       Column {
            Text(text = msg.author)
            Text(text = msg.body)
        }
  
    }
  
}
```
<img src="https://developer.android.com/static/develop/ui/compose/images/compose-tutorial/lesson2-05.png" width="300" height="600" />

### Configure your layout
#### Your message layout has the right structure but its elements aren't well spaced and the image is too big! To decorate or configure a composable, Compose uses modifiers. They allow you to change the composable's size, layout, appearance or add high-level interactions, such as making an element clickable. You can chain them to create richer composables. You'll use some of them to improve the layout.

```
// ...
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun MessageCard(msg: Message) {
    // Add padding around our message
    Row(modifier = Modifier.padding(all = 8.dp)) {
        Image(
            painter = painterResource(R.drawable.profile_picture),
            contentDescription = "Contact profile picture",
            modifier = Modifier
                // Set image size to 40 dp
                .size(40.dp)
                // Clip image to be shaped as a circle
                .clip(CircleShape)
        )

        // Add a horizontal space between the image and the column
        Spacer(modifier = Modifier.width(8.dp))

        Column {
            Text(text = msg.author)
            // Add a vertical space between the author and message texts
            Spacer(modifier = Modifier.height(4.dp))
            Text(text = msg.body)
        }
    }
}
```

<img src= "https://developer.android.com/static/develop/ui/compose/images/compose-tutorial/lesson2-06.png" width="300" height= "600" />

### Lists
#### Create a list of messages

```
// ...
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items

@Composable
fun Conversation(messages: List<Message>) {
    LazyColumn {
        items(messages) { message ->
            MessageCard(message)
        }
    }
}

@Preview
@Composable
fun PreviewConversation() {
    ComposeTutorialTheme {
        Conversation(SampleData.conversationSample)
    }
}

```

<img src= "https://developer.android.com/static/develop/ui/compose/images/compose-tutorial/lesson4-02.png" width="300" height="600" />

#### In this code snippet, you can see that LazyColumn has an items child. It takes a List as a parameter and its lambda receives a parameter we’ve named message (we could have named it whatever we want) which is an instance of Message. In short, this lambda is called for each item of the provided List

### Lifecycle of composables 

#### When Jetpack Compose runs your composables for the first time, during initial composition, it will keep track of the composables that you call to describe your UI in a Composition. Then, when the state of your app changes, Jetpack Compose schedules a recomposition. Recomposition is when Jetpack Compose re-executes the composables that may have changed in response to state changes, and then updates the Composition to reflect any changes.

A Composition can only be produced by an initial composition and updated by recomposition. The only way to modify a Composition is through recomposition

<img src="https://developer.android.com/static/develop/ui/compose/images/lifecycle-composition.png" width="400" height="200" />

### If a composable is called multiple times, multiple instances are placed in the Composition. Each call has its own lifecycle in the Composition
###  The instance of a composable in Composition is identified by its call site. The Compose compiler considers each call site as distinct. Calling composables from multiple call sites will create multiple instances of the composable in Composition. 

```
@Composable
fun MyComposable() {
    Column {
        Text("Hello")
        Text("World")
    }
}
```
<img src="https://developer.android.com/static/develop/ui/compose/images/lifecycle-hierarchy.png" width="400" height="200" />

```
@Composable
fun LoginScreen(showError: Boolean) {
    if (showError) {
        LoginError()
    }
    LoginInput() // This call site affects where LoginInput is placed in Composition
}

@Composable
fun LoginInput() { /* ... */ }

@Composable
fun LoginError() { /* ... */ }
```

#### In the code snippet above, LoginScreen will conditionally call the LoginError composable and will always call the LoginInput composable. Each call has a unique call site and source position, which the compiler will use to uniquely identify it.

#### Even though LoginInput went from being called first to being called second, the LoginInput instance will be preserved across recompositions. Additionally, because LoginInput doesn’t have any parameters that have changed across recomposition, the call to LoginInput will be skipped by Compose.

### Add extra information to help smart recompositions 
#### Calling a composable multiple times will add it to Composition multiple times as well. When calling a composable multiple times from the same call site, Compose doesn’t have any information to uniquely identify each call to that composable, so the execution order is used in addition to the call site in order to keep the instances distinct. This behavior is sometimes all that is needed, but in some cases it can cause unwanted behavior. 

```
@Composable
fun MoviesScreen(movies: List<Movie>) {
    Column {
        for (movie in movies) {
            // MovieOverview composables are placed in Composition given its
            // index position in the for loop
            MovieOverview(movie)
        }
    }
}

```

<img src="https://developer.android.com/static/develop/ui/compose/images/lifecycle-newelement-bottom.png" width="600" height="200" />

In the example above, Compose uses the execution order in addition to the call site to keep the instance distinct in the Composition. If a new movie is added to the bottom of the list, Compose can reuse the instances already in the Composition since their location in the list haven't changed and therefore, the movie input is the same for those instances.


However, if the movies list changes by either adding to the top or the middle of the list, removing or reordering items, it'll cause a recomposition in all MovieOverview calls whose input parameter has changed position in the list. That's extremely important if, for example, MovieOverview fetches a movie image using a side effect. If recomposition happens while the effect is in progress, it will be cancelled and will start again.

![](https://developer.android.com/static/develop/ui/compose/images/lifecycle-newelement-top-all-recompose.png)

### Compose provides a way for you to tell the runtime what values you want to use to identify a given part of the tree: the key composable
#### By wrapping a block of code with a call to the key composable with one or more values passed in, those values will be combined to be used to identify that instance in the composition. The value for a key does not need to be globally unique, it needs to only be unique amongst the invocations of composables at the call site. So in this example, each movie needs to have a key that's unique among the movies; it's fine if it shares that key with some other composable elsewhere in the app.

```
@Composable
fun MoviesScreenWithKey(movies: List<Movie>) {
    Column {
        for (movie in movies) {
            key(movie.id) { // Unique ID for this movie
                MovieOverview(movie)
            }
        }
    }
}
```

### With the above, even if the elements on the list change, Compose recognizes individual calls to MovieOverview and can reuse them

### Some composables have built-in support for the key composable. For example, LazyColumn accepts specifying a custom key in the items DSL

```
@Composable
fun MoviesScreenLazy(movies: List<Movie>) {
    LazyColumn {
        items(movies, key = { movie -> movie.id }) { movie ->
            MovieOverview(movie)
        }
    }
}
```

### Skipping if the inputs haven't changed
#### During recomposition, some eligible composable functions can have their execution be skipped entirely if their inputs have not changed from the previous composition.

A composable function is eligible for skipping unless:
+ The function has a non-Unit return type
  ```
  @Composable
    fun getText(): String {
    return "Hello"
    }
  ```
+ The function is annotated with @NonRestartableComposable or @NonSkippableComposable
  ```
  @NonSkippableComposable
    @Composable
    fun MyComposable() {
     // always run
    }
  ``` 
+ If the inputs of a composable are of a type that Compose cannot recognize as Stable, the composable becomes non-skippable. This means Compose cannot reliably determine whether the input has changed or not.

### Types that are considered Stable
Some common types are recognized as Stable by Compose, even if they are not explicitly annotated with @Stable. These include:

Primitive types such as Int, Long, Boolean, Float, Char, etc.
Strings (String)
Functions (Lambdas)
These types are Immutable, and because of their immutability, they can easily be considered stable.  

### Jetpack Compose has a UI rendering pipeline that operates in three primary phases: Composition, Layout, and Drawing
+ Composition: What to show
+ Layout: Where to place it
+ Drawing: How to render it

  ![](https://developer.android.com/static/develop/ui/compose/images/compose-phases.png)

## Composition
#### In the composition phase, the Compose runtime executes composable functions and outputs a tree structure that represents your UI. This UI tree consists of layout nodes that contain all the information needed for the next phases
![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*3Xef-gRgeUi8WETr0fJSgQ.gif)

## Layout
#### Then, during the layout phase, each element in the tree measures its children, if any, and places them in the available 2D space:
![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*5XSkoGxi_rvlKVt2E6AnzQ.gif)

## Drawing
#### Finally, in the drawing phase, each node in the tree draws its pixels on the screen:
 ![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*7MsZAyH3BWkpLMzDnljEOg.gif) 

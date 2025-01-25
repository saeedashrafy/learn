# Jetpack Compose
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

<img src="https://developer.android.com/static/develop/ui/compose/images/compose-tutorial/lesson2-02.png" width="300" height="500" />

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

###Jetpack compose is an umberall term for 7 different libraries 

+ compose.animation 
+ compose.foundation
+ compose.material
+ compose.material3
+ compose.ui
+ compose.compiler 
+ compose.runtime 

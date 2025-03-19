# iOS Application Development

## Introduction

Create a new Xcode project using SwiftUI.

### Buttons

Add a button on the main screen that prints a message when tapped and modify a counter.
Show the value on the screen.

Why is the value not updated when tapping on button?
Look at the [`@State` property wrapper](https://www.hackingwithswift.com/quick-start/swiftui/what-is-the-state-property-wrapper).
How can be used in order to make the view redraw and show the right value every time when button is tapped?

> [!NOTE]
> Feel free to add any custom colors, sizes, effect in you want.

### Navigation between Multiple Views

Add a button to the main screen that will trigger a navigation to another view called `SimpleView1`.
In order to achieve this behavior you need to use [`NavigationLink`](https://www.hackingwithswift.com/articles/216/complete-guide-to-navigationview-in-swiftui).

In the new view add a `Textfield` and a simple `Text` that shows the message "Hello [text from textfield]".
How can you use string interpolation in order to combine string to show the message? (more info about `Textfield` [here](https://www.hackingwithswift.com/quick-start/swiftui/how-to-read-text-from-a-textfield))

> [!NOTE]
> Feel free to add any custom colors, sizes, effect in you want.

### Binding

On the main screen declare a new variable that should containing the text entered from the previous `Textfield`.
How can you do this?

> [!TIP]
> Use the [`@Binding` variable property wrapper](https://www.hackingwithswift.com/quick-start/swiftui/what-is-the-binding-property-wrapper).

### Notifications

On the main screen add a new `NavigationLink` that will navigate to a view called `SimpleView2`.

We want to schedule a notification.
Notifications can be scheduled locally or remotely (push notifications).
In this example we want to schedule a local notification.
In order to make things work we need to ask user for permission.
If permission is not granted the OS will not add our notification in the queue.

Add two buttons:

1. one button for requesting user permissions for notifications
1. one that schedules a notification with title `SMD`, subtitle `My first iOS notification` that is triggered 5 seconds after tapping the button

### Unwrapping

In Swift we can declare:

```swift
var name: String? = nil
```

This means that the variable may contain a `String` or might be `nil`.
Because Swift is strongly typed we need to unwrap the value before usage.

There are multiple methods to do unwrapping:

1. Force unwrapping (using `!`)
1. Optional handling
   1. `if` statement
   1. optional binding
   1. guard statement
   1. `nil`-coalescing operator (`??` giving a default value in case of `nil`)

### `UserDefaults` and `Keychain`

When you write data to `UserDefaults`, it automatically gets loaded when your app runs so that you can read it back again.
This makes using it really easy, but you need to know that it's a bad idea to store lots of data in there because it will slow loading of your app.
If you think your saved data would take up more than say `100KB`, `UserDefaults` is almost certainly the wrong choice.

[Here](https://www.hackingwithswift.com/read/12/2/reading-and-writing-basics-userdefaults) is an example of how to use it.

`Keychain` is a secure storage system for sensitive data like passwords.
It uses the `Security` framework to provide encrypted storage, ensuring the protection of confidential information from unauthorized access.
[Here](https://medium.com/@ranga.c222/how-to-save-sensitive-data-in-keychain-in-ios-using-swift-c839d0e98f9d) is an example of how to use it.

Choose one of the above method and test the functionality.
Create a new view with a `Text` and a `Button`:

- `Button`: when button is pressed store a new random value (`int`, `string`) in `Preferences` / `Keychain`
- `Text`: show the value taken from `Preferences` / `Keychain` or a default value if nothing stored

You can add a `Textfield` and store the value user types if you want.

Kill the app and open it again.
Is the text shown correctly?

### `@StateObject` and `@ObservedObject`

The main advantage of objects marked with the `@StateObject` property wrapper, is they don't get destroyed and re-instantiated at times their containing view struct redraws.

Let`s say in one view you have:
- a simple counter button with the text shown the number of taps
- a button that shown a random number

If you use the `@ObservedObject` property, the view will get redrawn and the counter will get reseted.
We do not want this.
So a better approach is to use `@StateObject` so the instance is kept between redraws.
Of course it depends where the class is initialized and many others, but for the purpose of example try ro read the example below.

Try to reproduce the example [here](https://www.avanderlee.com/swiftui/stateobject-observedobject-differences) in order to observe the differences and when it is useful to use one against another.

### Building

Building an XCode project can be realized also from CLI.

List of key information you need to choose the correct app configuration to build:

```console
xcodebuild -list -project <project-name>.xcodeproj
```

Build with:

```console
xcodebuild -workspace "App.xcworkspace" -scheme "SomeScheme" -configuration "Debug" -sdk "iphonesimulator" -destination "generic/platform=iOS Simulator" clean build
```

Get simulator device list:

```console
xcrun simctl list
```

Boot the simulator with the corresponding device id:

```console
/Applications/Xcode.app/Contents/Developer/Applications/Simulator.app/Contents/MacOS/Simulator -CurrentDeviceUDID <YOUR-DEVICE-ID>
```

or

```console
open -a Simulator --args -CurrentDeviceUDID <YOUR-DEVICE-ID>
```

Install and launch:

```console
xcrun simctl install <YOUR-DEVICE-ID> <PATH-TO-APPLICATION-BUNDLE> xcrun simctl launch <YOUR-DEVICE-ID> <BUNDLE-ID-OF-APP-BUNDLE>
```

More details about the process can be found [here](https://www.andrewhoog.com/post/how-to-build-an-ios-app-archive-via-command-line/).

[Archiving](https://www.waldo.com/blog/use-xcodebuild-command-linE) is the process of exporting & signing the app in order to generate an `.ipa` file (_iPhone Archive_) that is uploaded on AppStore Connect.
Further you can publish in TestFlight or AppStore.

## Building a Pokedex application

In the first part of the lab we learned some of the Swift particularities.
In the next part of the lab we will build a more complex app using MVVM (model, view, viewmodel):

- `Model`: represents the app's data and its business logic
- `View`: displays information to the user and enables interaction
- `ViewModel`: acts as a bridge between the view and model layers
  It contains a view's state and handles most of its display and interaction logic.

The idea of the app is to create a view with a list of pokemons.
When you click on them you will navigate in a new view and some details specific to the pokemon will be presented.

Firstly, create a new empty SwiftUI project.
To ease things a bit we will make some preparations to the project before getting started.

1. Create a new empty file and save it with the `.json` extension.
   In this file add the data from [here](https://pokeapi.co/api/v2/pokemon?limit=200).
   This will help in order not to call after the list every time.
   The response will be the same and this way we learn how to decode a JSON file.
1. Create a folder for `Model`, `View` and `ViewModel`.

### `Model`

Let's start with the `Model`.
Create a new Swift file where we will store all the models for this project.
We need 3 structs:

- Create a struct called `PokemonPage` that conforms to codable and have the field that the JSOn file contains `(count: Int, next: String, result: [Pokemon])`.
- Create a struct called `Pokemon` that conforms to `Codable`, `Identifiable` and `Equatable`.
  In order to see what field to add you can look how the JSON for field results looks like.

  **Hint**: Use a browser or [the `jq` tool](https://jqlang.org/) to show nicely JSON contents.

- Create a struct called `PokemonDetail` that conforms to `Codable`.
  We will use this struct for getting the results from https://pokeapi.co/api/v2/pokemon/1/.
  You need to add id, height, weight. (feel free to add more fields if you want)

### `ViewModel`

Create a new file called `ViewModel`.
In this file we will handle the logic of getting data, update, etc.
In order to ease things we need some helpers.
Create a new file called helper and add following lines to it.
We will use them later:
- The `decode()` function will take data from a json file and try to decode it.
- The `fetchData()` function will take an URL and get the data from the URL.

```swift
import Foundation

extension Bundle {
    func decode<T: Decodable>(file: String) -> T {
        guard let url = self.url(forResource: file, withExtension: nil) else {
            fatalError("Could not find \(file) in bundle.")
        }

        guard let data = try? Data(contentsOf: url) else {
            fatalError("Could not load \(file) from bundle.")
        }

        let decoder = JSONDecoder()

        guard let loadedData = try? decoder.decode(T.self, from: data) else {
            fatalError("Could not decode \(file) from bundle.")
        }

        return loadedData
    }

    func fetchData<T: Decodable>(url: String, model: T.Type, completion:@escaping(T) -> (), failure:@escaping(Error) -> ()) {
            guard let url = URL(string: url) else { return }

            URLSession.shared.dataTask(with: url) { (data, response, error) in
                guard let data = data else {
                    // If there is an error, return the error.
                    if let error = error { failure(error) }
                    return }

                do {
                    let serverData = try JSONDecoder().decode(T.self, from: data)
                    // Return the data successfully from the server
                    completion((serverData))
                } catch {
                    // If there is an error, return the error.
                    failure(error)
                }
            }.resume()
    }
}
```

Inside to just created `ViewModel` file we need to create 2 published variables:

- `pokemonList`
- `pokemonDetails`

#### init()

We need an `init()` function where we use our helper decode function in order to populate the pokemon list.
> [!TIP]
> The `decode()` function is returning data of type `PokemonPage`.
> The `Data` field contain result which is the list of pokemons.

#### getPokemonIndex(pokemon: Pokemon)

Add a function `getPokemonIndex()` that takes a pokemon as parameter and return an int (the ID of the pokemon)

### getDetails(pokemon: Pokemon)

Use the `fetchData()` function from the helper in order to get pokemon details.
You can populate the `pokemonDetails` variable or you can return them as a tuple.
It is your call depending on later task.

### View

Let's try to bind all together and show some data.
In the `ContentView` we need to define a `@StateObject` variable that is initialized with our `ViewModel()`.
Now we need to imagine how things will look.
We want to have a view where you see a grid of pokemons.
If you tap on the pokemon you will see pokemon details.

If we break it into smaller view we need 3 views:

1. A view called `PokemonImageView` that takes the `viewModel` as an `@ObservedObject` parameter and a pokemon.
   - Using `AsyncImage` display a circle containing pokemon image.
     You can also add a placeholder in case the response comes to slow.
   - Under the image display the name of the pokemon using `Text`.

   The pokemon image can be found [here](https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/(pokemon ID).png) but you need pokemon index for it.

1. A view that takes the `viewModel` as a `@ObservedObject` parameter and a pokemon.
   Here we will show pokemon details and the pokemon image.
   You need to use the `getDetails` function.

   **Hint**: You have multiple options here.
   You can call the function of the `onAppear` modifier for example.

A view that displays a list/grid of pokemons (it could be `PokemonImageView`).
This view takes the `ViewModel` as the `@ObservedObject` parameter.
You need to have a `ScrollView` that contains a `LazyGridView` of the pokemon list.
For display you can use the `PokemonImageView`.

## Bonus

Add a searchfield addressbar.
What changes do you need to do?
Can you integrate it in the `ViewModel` in order to stick with the pattern?

> [!TIP]
> https://www.hackingwithswift.com/quick-start/swiftui/how-to-add-a-search-bar-to-filter-your-data

## References

1. [Optional unwrapping](https://medium.com/@talhasaygili/optionals-and-unwrapping-in-swift-825dddea4fc)
1. [`@StateObject` and `@ObservedObject`](https://www.avanderlee.com/swiftui/stateobject-observedobject-differences)
1. [Build an Xcode Project on the Command-Line](https://www.waldo.com/blog/use-xcodebuild-command-line)
1. [Pokedex](https://github.com/indently/Pokedex)

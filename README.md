# From React to SwiftUI
SwiftUI is great, for many reasons. However, you might want to know how it works, coming from a React background.

- [Basic component](#basic-component)
- [State](#state)
- [Props](#props)
- [Lifecycle](#lifecycle)
- [Fragments](#fragments)
- [Style](#style)
- [Optional rendering](#optional-rendering)
- [Gestures](#gestures)
- [Children](#children)

Want to know about some other topics? [Let me know with an issue](https://github.com/eveningkid/from-react-to-swiftui/issues/new).

### Basic component
```swift
struct Home: View {
  var body: some View {
    Text("Hello")
  }
}
```
- `function/class` turns into `struct`
- `React.Component` becomes `View`, a pure UI element
- `function return/render(...)` becomes `var body: some View { ... }`

### State
#### Initialization
Use the `@State` decorator to define a state property for your component:
```swift
struct Home: View {
  @State var message = "Hello"
  
  var body: some View {
    Text(self.message)
  }
}
```
Pretty straigh-forward. Use `self.stateProperty` instead of `this.state`, just as any struct property.

#### Update
```swift
struct Home: View {
  @State var message = "Hello"
  
  var body: some View {
    Text(self.message).tapAction {
      self.message = "Goodbye"
    }
  }
}
```
No `setState`, no `setProperty`, just update the value right away and the component will re-render accordingly.

### Props
Props should be defined using component properties:
```swift
struct Home: View {
  var message: String
  
  var body: some View {
    Text(self.message)
  }
}
```
- set default values:
```swift
var message: String = "Goodbye"
```
- props here are just as immutable as in React
- props **can become** mutable by setting:
  ```swift
  // Calling component
  @State var message = "Hello"
  Home(message: $message)

  // Home
  @Binding var message: String
  ```
  - the property here is a state property, because we need to keep a reference on it as it will be mutated
  - notice the `$` in front of the `message` variable, indicating that this value is a binding (two-way binding)
  - `@Binding` indicates this relationship between the child and its parent

### Lifecycle
Pretty limited, as we only get hooks for `didMount` and `willUnmount` using `onAppear` and `onDisappear` respectively:
```swift
struct Home: View {
  var message: String
  
  var body: some View {
    Text(self.message)
      .onAppear { print("did mount") }
      .onDisappear { print("did unmount") }
  }
}
```

### Fragments
Similar to `React.Fragment`, `Group` comes handy:
```swift
struct Home: View {
  var body: some View {
    Group {
      Text("Hello")
      Text("Goodbye")
    }
  }
}
```

### Style
#### Basics
Use view modifiers to define styles:
```swift
struct Home: View {
  var body: some View {
    Text("Hello")
      .font(.title)
      .foregroundColor(.red)
      .background(.blue)
  }
}
```

#### Layout
Forget about `div`:
- use `HStack` for horizontal layout
- use `VStack` for vertical layout
- use `ZStack` for stacking views on top of each other
```swift
struct Home: View {
  var body: some View {
    // Similar to flex-direction: column
    VStack {
      // Similar to flex-direction: row
      HStack {
        Text("Hello")
        Text("World")
      }

      Text("Goodbye")
    }
  }
}
```

### Optional rendering
Instead of `value && (...)`, use `if value != nil { ... }`:
```swift
struct Home: View {
  // String? means that message could not be defined
  var message: String?
  
  var body: some View {
    Group {
      if self.message != nil {
        Text(self.message)
      }
    }
  }
}
```

### Gestures
`onClick` becomes `tapAction`:
```swift
struct Home: View {
  var body: some View {
    Text("Hello").tapAction {
      print("Tapped")
    }
  }
}
```
Many more gestures are available, yet less intuitive to implement at first.

Majid Jabrayilov wrote a really [great article about gestures in SwiftUI](https://mecid.github.io/2019/07/10/gestures-in-swiftui/), that I encourage you to read if you are curious.

### Children
This one can sound a little scary at first, but do not worry, it is only because of Swift syntax.
```swift
// !) Important part
// 1) Define the type of Content: could be View, Label...View is the most generic
struct Comments<Content>: View where Content: View {
  // 2) Add a `content: Content` property
  let content: () -> Content

  // 3) Set `content` to use `@ViewBuilder` (quite verbose isn't it)
  init(@ViewBuilder content: @escaping () -> Content) {
    self.content = content
  }
  
  var body: some View {
    VStack {
      // 4) Display 
      content()
    }
  }
}

struct Comment: View {
  var author: String
  var message: String
  
  var body: some View {
    VStack {
      Text(self.author)
      Text(self.message)
    }
  }
}

// See children in action
struct Home: View {
  var body: some View {
    Comments() {
      Comment(author: "John", message: "Hello")
      Comment(author: "Doe", message: "Goodbye")
    }
  }
}
```

## Where to go from there?
- Apple came up with a great tutorial to get started with SwiftUI
- If you prefer to enjoy great presentations right from WWDC this year:
  - [SwiftUI On All Devices](https://developer.apple.com/videos/play/wwdc2019/240)
  - [SwiftUI Essentials](https://developer.apple.com/videos/play/wwdc2019/216)
- Find more on the great [`awesome-swiftui`](https://github.com/chinsyo/awesome-swiftui) list

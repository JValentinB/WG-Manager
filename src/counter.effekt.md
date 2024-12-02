```effekt
module counter

import src/utils/dom

import ref
import string
import test

import src/utils/test
```

### Structure: pico-Elmish-React

We're using a very simplified version of a mashup of Elmish and React architectures.
You don't need to understand the structure 100%.

> [!WARNING]
> Please do the `dom` task first, otherwise you won't be able to run this code!

This first part just models a tiny part of HTML/virtual DOM parametrized on an `Ev`ent type.
```effekt
type EventHandler[Ev] { 
  OnClick(handler: Ev);
  OnChange(handler: (String) => Ev at {})
  OnMouseEnter(handler: Ev);
  OnMouseLeave(handler: Ev);
}

record Attribute (
  name: String,
  value: String
)

type Html[Ev] {
  Text(content: String);
  Element(tag: String, handler: List[EventHandler[Ev]], children: List[Html[Ev]], attributes: List[Attribute]);
}

def text[Ev](content: String) = Text[Ev](content)

def button[Ev](handler: List[EventHandler[Ev]], children: List[Html[Ev]], attributes: List[Attribute]) =
  Element("button", handler, children, attributes)

def div[Ev](handler: List[EventHandler[Ev]], children: List[Html[Ev]], attributes: List[Attribute]) =
  Element("div", handler, children, attributes)

def div[Ev](children: List[Html[Ev]], attributes: List[Attribute]): Html[Ev] = div([], children, attributes)
```

This next part is the core of the whole approach:
```
interface State[St] {
  def getState(): St
  def setState(s: St): Unit
}

/// Handle the `State` effect using a `Ref` (global mutable reference)
def statefully[R, St](r: Ref[St]) { prog: () => R / State[St] }: R =
  try { prog() } with State[St] {
    def getState() = resume(r.get())
    def setState(newState) = { r.set(newState); resume(()) }
  }


// /// Main type: each application can:
// /// - `update` based on an Ev(ent) and modify its St(ate)
// /// - `view` (render) the application, possibly modifying the St(ate)
// record Application[St, Ev](
//   update: Ev => Unit / State[St] at {},
//   view: () => Html[Ev] / State[St] at {}
// )

// /// Main runner of `Application`s.
// /// You don't need to understand how this works.
// def run[St, Ev](root: Node, init: St, app: Application[St, Ev]) = app match {
//   case Application(update, view) =>
//     val inbox = ref[List[Ev]](Nil())
//     val state = ref(init)

//     def send(ev: Ev): Unit = inbox.set(Cons(ev, inbox.get))

//     // renders the Node to the DOM
//     def render(html: Html[Ev]): Node = html match {
//       case Text(content) => createTextNode(content)
//       case Element(tag, handler, children) =>
//         val el = createElement(tag)
//         handler.foreach {
//           case OnClick(ev) => 
//             el.onClick(box { send(ev) })
//             ()
//           case OnChange(h) => ()
//         }
//         children.foreach { child => 
//           el.appendChild(child.render)
//           ()
//         }
//         el
//     }

//     // renders the app with the current state
//     def render(): Unit = {
//       val rendered = statefully(state) { view() }.render
//       root.clear;
//       root.appendChild(rendered);
//       ()
//     }

//     def loop(deadline: IdleDeadline): Unit = {
//       val messages = inbox.get.reverse
//       inbox.set(Nil())

//       if (messages.nonEmpty) {
//         messages.foreach { ev =>
//           statefully(state) { update(ev) }
//         }

//         render()
//       }
//       requestIdleCallback(box loop)
//     }

//     render()
//     requestIdleCallback(box loop)
// }
```

### Part 1: Understand what's going on

Look at the sample below. Check what kind of events are there
(this is the `Event` type corresponding to the `Model` interface)
and look at the type signatures of `Application` (above) to understand things!

Feel free to run the app and test it out: if you did the previous (`dom`) task correctly,
everything should Just Work :tm:. (Otherwise please commit, push, and tag me ASAP!) 

What is here the `St` (what type)? What is here the `Ev` (what type)?

// ```effekt
// def whatIsStHere(): String = "St is of type Int" // TODO
// def whatIsEvHere(): String = "Ev is of type Event " // TODO
// ```

// Here's the counter app:

// ```effekt
// interface Model {
//   def change(n: Int): Unit
//   def reset(): Unit
//   def count(): Int
// }

// // The "reified" Model
// type Event { 
//   Change(n: Int);
//   Reset();
// }

// // "reflection"
// def dispatch(msg: Event) = msg match {
//   case Change(n) => do change(n)
//   case Reset() => do reset()
// }

// // handle `Model` using `State`
// def counterModel[R] { prog: => R / Model }: R / State[Int] =
//   try { prog() }
//   with Model {
//     def count() = resume(do getState())
//     def change(n: Int) = {
//       do setState(do getState() + n)
//       resume(())
//     }
//     def reset() = {
//       do setState(0)
//       resume(())
//     }
//   }

// // defines the actual "component"
// def view(): Html[Event] / Model =
//   div([
//     text("Hello World!"),
//     button([ OnClick(Change(-1)) ], [ text("-1") ]),
//     text("Current value: " ++ show( do count() )),
//     button([ OnClick(Change(1)) ], [ text("+1") ]),
//     button([ OnClick(Reset()) ], [ text("Reset") ])
//   ])
// ```

### Part 2: Add "Reset" button

1. Add a new case `Reset` to the `Event` and `Model` types
2. Implement the handling of `Reset` in the `counterModel` function
3. Create the "Reset" button in the `render` function

Don't forget to test things before moving on!

### Part 3: Refactoring

Replace the `Increment` and `Decrement` events / model with a single
`Change(n: Int)` event where the argument can be either `+1` or `-1`
(to avoid some unnecessary code duplication).

### Running

Run with `effekt --backend=js-web tasks/counter.effekt.md`,
then open the `./out/counter.html` file in your browser
(there will be an [error] telling you to do so `:)` and which file to open)
You shouldn't need to change `main` below.

As always, when you're done with this task, put something in the `done` variable below to make the test pass.
// ```effekt
// def done(): String = "Done" // TODO: when you're done, set this to something non-empty :)

// def main() = {
//   // Gets the app div (or creates it if it doesn't exist yet)
//   val appDiv = getElementById("app").getOrElse { 
//     val newDiv = createElement("div").setAttribute("id", "app")
//     documentBody.appendChild(newDiv)
//   }

//   val init: Int = 0

//   // Runs the application
//   run(appDiv, init, Application(
//     box { (ev) =>
//       with counterModel
//       ev.dispatch()
//     },
//     box {
//       with counterModel
//       view()
//     }))
// }
// ```

// #### Tests

// ```effekt
// def testSuite() = suite("tasks/dom") {
//   test("part 1: what is St here?") {
//     assertNonempty(whatIsStHere())
//   }

//   test("part 1: what is Ev here?") {
//     assertNonempty(whatIsEvHere())
//   }

//   test("done?") {
//     assertNonempty(done())
//   }
// }
// ```
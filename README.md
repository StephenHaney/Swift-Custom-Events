Swift Custom Events
===================

A very simple way to implement Backbone.js style custom event listeners and triggering in Swift for iOS development.

This provides a convenient way to organize your code.  It's especially useful for game systems like keeping track of events that count towards achievements.

### Usage

Custom event listeners allow us to separate our concerns and prevent function call spaghetti.  We can keep the event agnostic while we add and remove functionality.

**I prefer creating an 'Events' property of type EventManager on each Class you wish to track.**  Alternatively, you can also inherit this code onto any classes you like.  Or, you can maintain one EventsManager object for your entire app (but listener performance may suffer with many listeners).

This system is a bit more simple and works differently than Backbone's (for one, they use JS prototypes to get their Event code onto all backbone objects).  It's intended as a starting place for custom events in Swift.

### Example

Let's create a cat class that can bug us while we're coding:

```swift
class Cat {
    let events = EventManager();

    func meow() {
        println("Cat: MRaawwweeee");
        self.events.trigger("meow");
    }
}
```

And a human class to represent ourselves:

```swift
class Human {
    func adoptCat(cat:Cat) {
        // you can pass in an anonymous code block to the event listener
        cat.events.listenTo("meow", {
            println("Human: Awww, what a cute kitty *pets cat*");
        });

        // or you can pass a function reference
        cat.events.listenTo("meow", self.dayDream);
    }

    func dayDream() {
        println("Human daydreams about owning a dog");
    }
}
```

Play out our little scene:

```swift
let zeus = Cat();
let stephen = Human();

stephen.adoptCat(zeus);
zeus.meow();
/*
 * Cat: MRaawwweeee
 * Human: Awww, what a cute kitty *pets cat*
 * Human daydreams about owning a dog
*/
```

### Disclosure:

I haven't done much performance testing as it's "fast enough" for my purposes, but this is probably much slower than using delegates.  On the other hand, it's easier and faster to implement (especially in cases with multiple listeners per event).

This is a very early system developed for my specific needs.  Feel free to use and adapt as you need, and please send any feedback or improvements.

#### Potential improvements/considerations:
- I haven't tested what happens if you remove instances of either the listener or event object from memory after wiring up a listener.
- Needs performance testing and tuning.
- Ability to remove only a specific action from listener array.

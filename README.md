Swift Custom Events
===================

A very simple way to implement Backbone.js style custom event listeners and triggering in Swift for iOS development.

This provides a convenient way to organize your code.  It's especially useful for game systems like keeping track of events that count towards achievements.

Updated for Swift 6.2 using Xcode 6.3 beta 2.

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
        self.events.trigger("meow", information: "The cat is hungry!");
    }
}
```

And a human class to represent ourselves:

```swift
class Human {
    func adoptCat(cat:Cat) {
        // you can pass in an anonymous code block to the event listener
        cat.events.listenTo("meow", action: {
            println("Human: Awww, what a cute kitty *pets cat*");
        });
        
        // or you can pass a function reference
        cat.events.listenTo("meow", action: self.dayDream);
        
        // Using the information from the trigger:
        // (notice the parameters for ponderCat)
        cat.events.listenTo("meow", action: self.ponderCat);
    }
    
    func dayDream() {
        println("Human daydreams about owning a dog");
    }
    
    func ponderCat(information:Any?) {
        if let info = information as? String {
            println("Oooh, I think I know:");
            println(info);
        }
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
 * Oooh, I think I know:
 * The cat is hungry!
*/
```

### Disclosure:

This is potentially slower than using delegates or direct function calls (see performance testing, below).  On the other hand, it's easier and faster to implement (especially in cases with multiple listeners per event).

This is a very early system developed for my specific needs.  Feel free to use and adapt as you need, and please send any feedback or improvements.

### Performance Testing:

I performed a real world comparison between using event triggers and a direct function call.  On average, the event trigger took 0.23 milliseconds to get into the action code.  Direct function calls performed in ~0.097 milliseconds.  Phew, that's quick!  More testing is needed, but I expected much worse results.  Function calls are twice as fast, but event triggers using this code are still pretty quick!  

#### Potential improvements/considerations:
- I haven't tested what happens if you remove instances of either the listener or event object from memory after wiring up a listener.
- Needs performance testing and tuning.
- Ability to remove only a specific action from listener array.

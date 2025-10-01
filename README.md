# EventDispatcher

A lightweight **event system** for Unity that allows decoupled communication between different parts of your game.
It provides a singleton based dispatcher with **event binding, unbinding, broadcasting, delayed events, and parameter passing**.


## Features

* **Singleton pattern** – Only one `EventDispatcher` instance exist at any time avoiding binds or broadcasts to the wrong instance.
* **Bind / Unbind** listeners to string-based event names.
* **Broadcast events** immediately or after a delay.
* **Pass parameters** via the `ParamList` helper class.
* **Editor-only debug tracking** (`EventDispatchTracker`) for logging dispatched events.

## Getting Started

1. Add `[Unity-Event-Dispatcher.unitypackage](https://github.com/Theo-Mestre/Unity-Event-Dispatcher/releases/)` to your Unity project.

2. Use the static methods to bind, unbind, and broadcast events anywhere in your code. The EventDispatcher will be created on the first automatically when a function is called.
You can also add an `EventDispatcher` script to an empty GameObject.

Note that the script is DontDestroyOnLoad and will persist accross different scene.

## Usage

### Binding an Event

```csharp
void OnEnable()
{
    EventDispatcher.Bind("OnPlayerScored", OnPlayerScored);
}

void OnDisable()
{
    EventDispatcher.Unbind("OnPlayerScored", OnPlayerScored);
}

private void OnPlayerScored(ParamList parameters)
{
    int points = parameters.Get<int>("score", 0);
    Debug.Log("Player scored " + points + " points!");
}
```

### Broadcasting an Event

```csharp
// Fire an event with parameters
var paramList = new ParamList()
    .Set("score", 100)
    .Set("playerId", "Player1");

EventDispatcher.Broadcast("OnPlayerScored", paramList);
```

### Broadcasting With Delay

```csharp
EventDispatcher.BroadcastDelayed("OnExplosion", new ParamList("damage", 50), 2f);
```

### Checking Bindings

```csharp
bool isBound = EventDispatcher.IsBound("OnPlayerScored");
int listenerCount = EventDispatcher.GetBindingsCount("OnPlayerScored");
```

### Clearing All Events

```csharp
EventDispatcher.ClearAllBindings();
```

## ParamList

A type-safe dictionary for passing event data.

```csharp
var parameters = new ParamList()
    .Set("health", 75)
    .Set("name", "BossEnemy");

int health = parameters.Get<int>("health", 0);
string name = parameters.Get<string>("name", "Unknown");
```

## Notes

* Event names are **string-based** – use constants to avoid typos.
* Removing all bindings with `ClearAllBindings()` resets the event system.
* Works in both play mode and across scene loads (uses `DontDestroyOnLoad`).

## Example Use Cases

* Global gameplay events (`OnGameOver`, `OnLevelComplete`).
* UI updates (`OnScoreChanged`, `OnHealthChanged`).
* Cross-scene communication without references.
* AI events or quest triggers.

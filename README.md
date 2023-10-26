# SafeAction for C#

The `SafeAction` class provides a secure wrapper around an `Action` event. It offers mechanisms for subscribing, unsubscribing, and invoking delegate methods.
This solution allows you to be sure that your class will not subscribe to the event multiple times. It is also safe to call this event without fear that the delegate described inside may contain null;
This class is placed in the namespace System for quick access, just like other delegate templates.

## Usage

### Creating a SafeAction Instance

```csharp
    public SafeAction<int> safeAction = new SafeAction<int>();
```

### Subscription/Unsubscription

In next example, we have a Character class, a CharacterFabric to produce new Characters, and an observer for our factory:

```csharp
public class CharacterFabric
{
    public SafeAction<Character> OnCharacterCreatedEvent = new();

    public Character CreateCharacter()
    {
        var character = new Character();
        OnCharacterCreatedEvent.Invoke(character);
        return character;
    }
}

public class FabricListener
{
    private CharacterFabric cf;

    public FabricListener(CharacterFabric cf)
    {
        this.cf = cf;
        cf.OnCharacterCreatedEvent += OnCharacterCreatedHandler;
    }

    public void OnCharacterCreatedHandler(Character character)
    {
        Console.WriteLine($"I heard about: {character}");
    }
}

public class Character { }
```

Based on the behavior of CharacterFabric in the CreateCharacter method, you can see that the SafeAction class can be used as a regular Action. Also, you can subscribe to this event just like a regular event.
Ley call our method:

```csharp
    public void Main(string[] args)
    {
        CharacterFabric cc = new();
        FabricListener fl = new (cc);
        cc.CreateCharacter();           //I heard about: Character
    }
```

Alternative way to subscribe:

```csharp
    cf.OnCharacterCreatedEvent.Subscribe(OnCharacterCreatedHandler);
```

The unsubscribe would look like this:

```csharp
    cf.OnCharacterCreatedEvent -= OnCharacterCreatedHandler;
```

or

```csharp
    cf.OnCharacterCreatedEvent.Unsubscribe(OnCharacterCreatedHandler);
```

### Implementation

SafeAction can also be overridden with the "=" sign by passing it a delegate of the same signature:

```csharp
    public class Program
{
    public SafeAction<int> SafeAction = new();
    public void Main(string[] args)
    {
        Action<int> action = (e) => e += 25;
        SafeAction = action;
    }
}
```

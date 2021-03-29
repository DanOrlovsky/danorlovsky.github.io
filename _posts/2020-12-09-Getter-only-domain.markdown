---
layout: post
title:  "Domain Models Without Set Properties"
date:   2020-12-09 10:45:47 -0400
categories: ef-core
---
I ran into a situation where I needed to create a domain model with a property that only allowed for getter-access.

Imagine the scenario: you have an object where a property represents a temporal state: one that cannot change.  In my case, I needed to store a Payload that was recieved in the body of a request at the time it was received.  Because of this scenario, I can't allow this property to _ever_ be changed.  But how?

My preferred approach (as of EFCore 1) would to be to make this an explicit behavior of the domain model by never exposing a ```set``` property to begin with!

When we create a backing field to a property, we can ensure you can only ever _get_ it from any other class that may use the RequestDetails class.

```csharp
public sealed class RequestDetails 
{
    public Guid Id { get; private set; }
    
    private string _payload;            // Backing field for Payload
    public string Payload => _payload;

    public RequestDetails(string payload)
    {
        Id = Guid.NewGuid();
        _payload = payload;
    }
}
```

Unfortunately, it doesn't work automagically, and we do have to utilize the modelBuilder by overriding the ```OnModelCreating``` method from the DbContext, like so:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<RequestDetails>(b => {
        b.Property(p => p.Payload)
            .HasField("_payload");
    });
}
```

Now, EF Core knows to connect your Property to the field behind it, and you don't have to worry about any other process changing a value that should never be changed.
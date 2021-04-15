---
layout: post
title:  "Mocking a Lambda Expression Using Moq"
date:   2021-04-14 9:45:00 -0500
categories: Development
---
Here is an example of how you can ```.Setup()``` a method that takes a lambda expression, and properly verify the return, using Moq.

### Links
- [Moq](https://www.nuget.org/packages/Moq) 
- [Xunit](https://www.nuget.org/packages/xunit)
- [Sample Project](https://github.com/DanOrlovsky/UnitTestingLambda)

### Setup

Here is a small class that can be used to represent a *Product*.

```csharp
    public sealed class Product
    {
        public int Id { get; private set; }

        public string Name { get; private set; }
        
        public string Description { get; private set; }

        public Product(int id, string name, string description)
        {
            Id = id;
            Name = string.IsNullOrWhiteSpace(name) ? throw new ArgumentNullException(nameof(name)) : name;
            Description = string.IsNullOrWhiteSpace(description) ? throw new ArgumentNullException(nameof(description)) : description;
        }
    }
```

This is a service that facilitates some interactions with the *Product*.  This will be our **System Under Test**, and we will be 
specifically testing the *GetByName*.  The GetByName method is meant to return all products that have the *input* parameter anywhere in
the name, as a list.

```csharp
public sealed class ProductsService
{
    private readonly IProductsRepository _productsRepository;

    public async Task<List<Product>> GetByName(string input)
    {
        return await _productsRepository
            .ToListAsync(p => p.Name.Contains(input));
    }
}
```
### Objective
Write a unit test that verifies the filter (input) does in fact return the expected results from the filter that is passed.

[Link](https://github.com/DanOrlovsky/UnitTestingLambda/blob/master/UnitTestingLambda.Data.UnitTests/Products/ProductsService_GetByName_Should.cs)
```csharp
[Fact]
public async Task Return_ExpectedProducts_AsList()
{
    // This is the filter we're going to pass to the method
    const string filter = "Pon";

    // These are the productNames we're going to expect to be returned
    const string expectedProductName1 = "A Poncho";
    const string expectedProductName2 = "Pontiac";
    
    // This is a list of Products that our lambda expression will iterate over looking for matches.
    var productsReturn = new List<Product>
    {
        new Product(1, expectedProductName1, "A good poncho"),
        new Product(2, "A product", "This thing is cool, but you'll never know it."),
        new Product(3, expectedProductName2, "It's a car!"),
    };

    // This is the expected expression. 
    // 
    // This is where we define which of the above products we expect to be returned from the expression.
    Func<Expression<Func<Product, bool>>, bool> expectedExpression = exp =>
    {
        var func = exp.Compile();
        
        return func(productsReturn[0]) &&
            !func(productsReturn[1]) &&
            func(productsReturn[2]);
    };

    // On .Setup we can use It.Is to match the expression above and return the expected results.
    _productsRepository.Setup(p => p.ToListAsync(It.Is<Expression<Func<Product, bool>>>(exp => expectedExpression(exp))))
        .ReturnsAsync(new List<Product> { productsReturn[0], productsReturn[2] })
        .Verifiable();

    // Act
    var results = await _sut.GetByName(filter);

    // Assert
    Assert.NotNull(results);
    Assert.Equal(2, results.Count);
    Assert.Equal(expectedProductName1, results[0].Name);
    Assert.Equal(expectedProductName2, results[1].Name);

    _productsRepository.Verify();
}
```
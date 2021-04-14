---
layout: post
title:  "Mocking Method That Takes Lambda Expression with Moq"
date:   2021-01-23 12:45:47 -0500
categories: Development
---
Unit testing can sometimes be a difficult concept to grasp, especially for the more junior of developers.  When I was very new to unit testing,
I ran into some scenarios that required me to mock a method that accepted a lambda, and chaos ensued. 

This is the pattern I came across and now use anytime I come across this scenario.

Just a note, this example will be using [Moq](https://www.nuget.org/packages/Moq) and [Xunit](https://www.nuget.org/packages/xunit)

And, here is a [link](https://github.com/DanOrlovsky/UnitTestingLambda) to the repo where the full solution lives.

First, I needed to create a small class with a method that I'm going to test.  It's a service class for a ```Product``` object, that gets access to the data
via a repository:

```csharp
public sealed class ProductsService
{
    private readonly IProductsRepository _productsRepository;

    /// <summary>
    ///     Creates a new instance of <see cref="ProductsService"/>
    /// </summary>
    /// <param name="productsRepository"></param>
    /// <exception cref="ArgumentNullException">
    ///     Thrown when an injected param is null.
    /// </exception>
    public ProductsService(IProductsRepository productsRepository)
    {
        _productsRepository = productsRepository ?? throw new ArgumentNullException(nameof(productsRepository));
    }

    /// <summary>
    ///     Finds products by a given filter on name.
    /// </summary>
    /// <param name="input"></param>
    /// <returns></returns>
    public async Task<List<Product>> GetByName(string input)
    {
        return await _productsRepository
            .ToListAsync(p => p.Name.Contains(input));
    }
}
```

When I unit test this method, I'm going to have to mock the call to the ```ToListAsync()``` from the repository.

Here is a sample of how it is done:

```csharp
[Fact]
public async Task Return_ExpectedProducts_AsList()
{
    // These are the productNames we're going to expect to be returned
    const string expectedProductName1 = "Poncho";
    const string expectedProductName2 = "Pontiac";
    
    // This is a list of Products that our lambda expression will iterate over looking for matches.
    var productsReturn = new List<Product>
    {
        new Product(Guid.Parse("00000000-0000-0000-0000-000000000001"), expectedProductName1, "A good poncho"),
        new Product(Guid.Parse("00000000-0000-0000-0000-000000000002"), "A product", "This thing is cool, but you'll never know it."),
        new Product(Guid.Parse("00000000-0000-0000-0000-000000000003"), expectedProductName2, "It's a car!"),
    };

    // This is the expected expression, which provides the return we expect from the method we are mocking.
    Func<Expression<Func<Product, bool>>, bool> expectedExpression = exp =>
    {
        var func = exp.Compile();
        return func(productsReturn[0]) &&
            !func(productsReturn[1]) &&
            func(productsReturn[2]);
    };

    _productsRepository.Setup(p => p.ToListAsync(It.Is<Expression<Func<Product, bool>>>(exp => expectedExpression(exp))))
        .ReturnsAsync(new List<Product> { productsReturn[0], productsReturn[2] })
        .Verifiable();

    var result = await _sut.GetByName("Pon");

    Assert.NotNull(result);
    Assert.Equal(2, result.Count);
}
```
We're doing well with testing. We've established a unit testing framework for our app that will test the following:

* Methods in our classes
* Output from controller methods
* Model data types within `ViewResult` objects

Let's learn one final tool for testing, using a test database to run **integration tests**.

## Integration Testing

An integration test is designed to verify data flow across an app. Now that we know our models and controllers work independently of each other, we want to check that they work together properly.

### Integration Testing Setup

Let's set up a test database and a `DbContext` class to connect to it. We'll have to do this in a slightly different way than we did in the C# unit since we are using Entity. Our connection string is now located in the `ToDoListContext` class. So, instead of resetting the `ConnectionString` property in the `DBConfiguration` class, we need to tell our repo to reference a `DbContext` that represents our test database.

* Create a database called _todolist\_test_. Let it be a duplicate of the database we've been using. In this case, it contains one table named `Items`, with an `ItemId` column (type: `int`, set primary key, set identity) and a `Description` column (type: `nvarchar(MAX)`, deselect _"Allow nulls"_)

* In the test project, create a _Models_ folder. Inside that folder, create a file called _TestDbContext.cs_ with the following code:

<div class="filename">ToDoList.Tests/Models/TestDbContext</div>
```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoList.Models
{
    public class TestDbContext : ToDoListContext
    {
        public override DbSet<Item> Items { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder options)
        {
            options.UseMySql(@"Server=localhost;database=todolist_test;uid=root;pwd=root;");
        }
    }
}
```

_**Note:** Make sure the `DbSet<>` in your original `DbContext` file is set to a `virtual` property, otherwise the `override` will not be accepted._

* This `TestDbContext` class extends `ToDoListContext` so it can be called in place of `ToDoListContext` in our methods.

* Likewise, we `override` the default `DbSet` inherited from `ToDoListContext`. We do this because we don't want to use `ToDoListContext`'s data set, we want to use a separate data set specific to `TestDbContext`.

* We also override the `OnConfiguring()` method `TestDbContext` inherited from `ToDoListContext` to specifically connect to the test database (notice the portion of the connection string reading `database=todolist_test`). As discussed in the [documentation](https://docs.microsoft.com/en-us/ef/core/miscellaneous/configuring-dbcontext), `OnConfiguring()` is a method used to associate a context (like our `TestDbContext`) with a specific data store (like our test database).

### Connecting to the Database in Tests

Now that we have a `DbContext` class to connect to our database, let's make a change to _EFItemRepository_ so that we can connect to this database in our tests. We'll add the following constructor to the `EFItemRepository` class:

<div class="filename">EFItemRepository</div>
```csharp
...
ToDoListContext db;
public EFItemRepository()
{
    db = new ToDoListContext();
}
public EFItemRepository(ToDoListDbContext thisDb)
{
    db = thisDb;
}...
```

### Overloading Constructors

You'll notice that we've actually added _two_ constructors. You see, our application now has two different database options it can use: the actual production database and the test database. As such, we also have two different constructors; one that accepts an argument, and one that does not. Here's how it works:

* If we don't provide an argument when instantiating new instances of `EFItemRepository`, the constructor _without_ an argument in its method signature is used.

* If we _do_ provide an argument when instantiating a new instance, the constructor _with_ an argument in its method signature is automatically used.


In short, we're essentially telling our repo _"Default to using the production database, unless we specifically give you a different (test) one as an argument!"_

Defining multiple constructors for different possibilities like this is called **constructor overloading**.

But wait, neither of the constructors accept a `TestDbContext` as an argument - one accepts nothing and the other accepts an instance of `ToDoListContext`! Well, remember how we created `TestDbContext` to extend `ToDoListContext`? Because `TestDbContext` inherits from `ToDoListContext`, we can implicitly cast it as a `ToDoListContext` type!

We could change our parameter type to `TestDbContext`, but keeping it as type `ToDoListContext` helps keep our code modular. If we wanted to specify other databases for our code to use, we'd simply have to create additional classes that extend the `ToDoListContext`, and we wouldn't have to update our `EFItemRepository` constructor again.

Now let's instantiate this test database in our tests. Add the following code to the _ItemsControllerTest.cs_ file.

```csharp
EFItemRepository db = new EFItemRepository(new TestDbContext());
```

Whenever we want to create a test that interacts with our test database, we simply need to instantiate a new `ItemsController` with the new `db` variable as the argument. For example:

```csharp
ItemsController controller = new ItemsController(db);
```

Here were specifying that we want the controller to use an instance of `EFItemRepository` that references the test database.

With that, we're all set up!

## Writing Integration Tests

Here's a test that evaluates our `Create()` controller method:

<div class="filename">ItemsControllerTest.cs</div>
```csharp
...
        [TestMethod]
        public void DB_CreatesNewEntries_Collection()
        {
            // Arrange
            ItemsController controller = new ItemsController(db);
            Item testItem = new Item();
            testItem.Description = "TestDb Item";

            // Act
            controller.Create(testItem);
            var collection = (controller.Index() as ViewResult).ViewData.Model as List<Item>;

            // Assert
            CollectionAssert.Contains(collection, testItem);
        }
...
```

In this case we're beginning our test name with `DB_...` so that at a glance we can see that this test interacts with our database when we see it in the Test Results.

Now that we're using a test database, we can't forget to include a teardown. You can review the teardown from the lesson in the [C# coursework](https://www.learnhowtoprogram.com/c/behavior-driven-development-with-c/model-testing).

Here's a hint, we need to create a `DeleteAll()` method of some kind in _EFItemRepository.cs_

Happy testing!

---

**[<i class="glyphicon glyphicon-folder-open"></i>  Example GitHub Repo for To Do List with Testing](https://github.com/epicodus-lessons/ToDoList-.NET-wk1-wk2/tree/8b9104c76fc1742a1dc9aa212bbce6f7ec22a646)**

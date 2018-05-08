# BlazorDB
In memory, persisted to localstorage, database for .net Blazor browser framework

## Warning
This library like Blazor itself is experimental and API is likely to change.

## Docs

### Install
So far the the API is very simplestic but works.

First add a reference to the nuget package:

```
Install-Package BlazorDB -Version 0.0.2
```

or

```
dotnet add package BlazorDB --version 0.0.2
```

Then in Program.cs add Blazor DB to the dependency injection services:

```
var serviceProvider = new BrowserServiceProvider(services =>
{
    services.AddBlazorDB(options =>
    {
        options.LogDebug = true;
        options.Assembly = typeof(Program).Assembly;
    });
});
new BrowserRenderer(serviceProvider).AddComponent<App>("app");
```

Set `LogDebug` to see debug output in the browser console.

### Setup

Create at least one model and context for example:

Person.cs:

```
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

if the field `public int Id { get; set; }` exists it will be managed by BlazorDB

and a context, for example, Context.cs:
```
public class Context : StorageContext
{
    public StorageSet<Person> People { get; set; }
}
```

### Usage

See the full example in the sample app: https://github.com/chanan/BlazorDB/blob/master/src/Sample/Pages/Index.cshtml
Inject your context into your component:

```
@using Sample.Models
@inject Context Context
```

Create a model and add it to your Context:

```
var person = new Person { FirstName = "John", LastName = "Smith" };
Context.People.Add(person);
```

Do not set the Id field. It will be assigned by BlazorDB.

Call SaveChanges:

```
Context.SaveChanges();
```

Once `SaveChanges()` has been called, you may close the browser and return later, the data will be reloaded from localStorage.

You may query the data using linq for example:

```
private Person Person { get; set; }
void onclickGetPerson()
{
    var query = from person in Context.People
                where person.Id == 1
                select person;
    Person = query.Single();
    StateHasChanged();
}
```
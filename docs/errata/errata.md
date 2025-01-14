**Errata** (21 items)

If you find any mistakes, then please [raise an issue in this repository](https://github.com/markjprice/apps-services-net7/issues) or email me at markjprice (at) gmail.com.

> Note that some of the simpler typos listed below have been fixed for copies purchased after about mid-April 2023. For example, the [single-quotes instead of backticks issue](#page-467---adding-a-chat-page-to-the-mvc-website), and the [wrong file extensions (some `.cshtml` files should be `.razor` files)](#page-571---blazor-routing-to-page-components).

> Microsoft has changed their domain for documentation from `https://docs.microsoft.com` to `https://learn.microsoft.com` with an automatic redirect so all links in my books that use the `docs` domain should still work.

- [Page 16 - Installing other extensions](#page-16---installing-other-extensions)
- [Page 56 - Managing data with Transact-SQL](#page-56---managing-data-with-transact-sql)
- [Page 82 - Defining the Northwind database model](#page-82---defining-the-northwind-database-model)
  - [Category class changes](#category-class-changes)
  - [NorthwindDb class changes](#northwinddb-class-changes)
- [Page 102 - Creating a class library for the data context using SQL Server](#page-102---creating-a-class-library-for-the-data-context-using-sql-server)
- [Page 138 - Performing CRUD operations with Cosmos SQL API](#page-138---performing-crud-operations-with-cosmos-sql-api)
- [Page 200 - Testing an AutoMapper configuration](#page-200---testing-an-automapper-configuration)
- [Page 411 - Using an ASP.NET Core MVC project as a GraphQL client](#page-411---using-an-aspnet-core-mvc-project-as-a-graphql-client)
- [Page 417 - Understanding Strawberry Shake - Creating a console app client](#page-417---understanding-strawberry-shake---creating-a-console-app-client)
- [Page 467 - Adding a chat page to the MVC website](#page-467---adding-a-chat-page-to-the-mvc-website)
- [Page 571 - Blazor routing to page components](#page-571---blazor-routing-to-page-components)
- [Page 578 - Building Blazor components](#page-578---building-blazor-components)
- [Page 587 - Building and testing a Blazor alert component](#page-587---building-and-testing-a-blazor-alert-component)
- [Page 600 - Building a local storage service](#page-600---building-a-local-storage-service)
- [Page 613 - Exploring Radzen Blazor components](#page-613---exploring-radzen-blazor-components)
- [Page 621 - Building a web service for Northwind entities](#page-621---building-a-web-service-for-northwind-entities)
- [Page 623 - Using the Radzen tabs, image, and icon components](#page-623---using-the-radzen-tabs-image-and-icon-components)
- [Page 627 - Using the Radzen HTML editor component](#page-627---using-the-radzen-html-editor-component)
- [Page 631 - Using the Radzen chart component](#page-631---using-the-radzen-chart-component)
- [Page 633 - Using the Radzen form components](#page-633---using-the-radzen-form-components)
- [Page 634 - Using the Radzen form components](#page-634---using-the-radzen-form-components)
- [Page 657 - Creating a virtual Android device for local app testing](#page-657---creating-a-virtual-android-device-for-local-app-testing)

# Page 16 - Installing other extensions

In the table, the **C# for Visual Studio Code** extension says it is "powered by OmniSharp". This is true up to the most recent release version `1.25.9`. But if you install the pre-release version `2.0.x` then it does not include OmniSharp any more.

To follow the instructions in the book, if you have installed the pre-release version `2.0.x` then on the **C#** extension page, I recommend that you click the button **Switch to Release Version** to revert back to the current release version `1.25.9`.

# Page 56 - Managing data with Transact-SQL

The link to the SQL language reference is broken in the PDF version due to a line break. It should be: 
https://learn.microsoft.com/en-us/sql/t-sql/language-reference.

# Page 82 - Defining the Northwind database model

> Thanks to [Bob Molloy](https://github.com/BobMolloy) for raising this [issue on 31 December 2022](https://github.com/markjprice/apps-services-net7/issues/5).

## Category class changes

In Step 7, I show the class that represents a `Category` in the Northwind database that is generated by the `dotnet-ef`, as shown in the following code:
```cs
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
using Microsoft.EntityFrameworkCore;

namespace Northwind.Console.EFCore.Models
{
  [Index("CategoryName", Name = "CategoryName")]
  public partial class Category
  {
    public Category()
    {
      Products = new HashSet<Product>();
    }

    [Key]
    public int CategoryId { get; set; }

    [StringLength(15)]
    public string CategoryName { get; set; } = null!;

    [Column(TypeName = "ntext")]
    public string? Description { get; set; }

    [Column(TypeName = "image")]
    public byte[]? Picture { get; set; }

    [InverseProperty("Category")]
    public virtual ICollection<Product> Products { get; set; }
  }
}
```
The current `dotnet-ef` tool generates slightly different output, for example, it uses a file-scoped namespace declaration to avoid indenting and it initializes the `Products` property to a `List<T>` instead of a `HashSet<T>`, as shown in the following code:
```cs
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
using Microsoft.EntityFrameworkCore;

namespace Northwind.Console.EFCore.Models;

[Index("CategoryName", Name = "CategoryName")]
public partial class Category
{
  [Key]
  public int CategoryId { get; set; }

  [StringLength(15)]
  public string CategoryName { get; set; } = null!;

  [Column(TypeName = "ntext")]
  public string? Description { get; set; }

  [Column(TypeName = "image")]
  public byte[]? Picture { get; set; }

  [InverseProperty("Category")]
  public virtual ICollection<Product> Products { get; } = new List<Product>();
}
```

## NorthwindDb class changes

In Step 9, I show the class that represents the Northwind database that was generated by the `dotnet-ef` tool. The current `dotnet-ef` tool generates slightly different output, for example, it uses a file-scoped namespace declaration to avoid indenting and it does not set each `DbSet<T>` property to the null-forgiving operator, as shown in the following code:
```cs
// older version of dotnet-ef did this:
public virtual DbSet<Category> Categories { get; set; } = null!;

// current version of dotnet-ef does this:
public virtual DbSet<Category> Categories { get; set; }
```

We asked the compiler to treat warnings as errors so we cannot build until the compiler knows that all those 25+ properties are safe to use as `null`. We could manually add the null-forgiving operator, or use a find and replace regular expression to do it, or customize the T4 template used by the `dotnet-ef` tool to set all `DbSet<T>` properties to the null-forgiving operator, but the easiest thing to do is simply disable null warnings for this file by adding the following statement to the top of the class file, as shown in the following code:
```cs
#nullable disable
```

Microsoft recommends either setting `DbSet<T>` properties to the null-forgiving operator, or calling the `Set<T>` method defined in the `DbContext` base class to implement them as a read-only property, as shown in the following code:
```cs
public virtual DbSet<Category> Categories => Set<Category>();
```

**Working with Nullable Reference Types - DbContext and DbSet**
https://learn.microsoft.com/en-us/ef/core/miscellaneous/nullable-reference-types#dbcontext-and-dbset

> Thanks to [charlygg](https://github.com/charlygg) for suggesting using the `Set<T>` method in a comment on [issue on 1 January 2023](https://github.com/markjprice/apps-services-net7/issues/5#issuecomment-1368614033).

# Page 102 - Creating a class library for the data context using SQL Server

In Step 1, I wrote, "In Visual Studio Code, select `Northwind.Common.DataContext.SqlServer` as the active OmniSharp project." 

This works if you installed the most recent release version `1.25.9`. But if you installed the pre-release version `2.0.x` then it does not include OmniSharp any more.

To follow the instructions in the book, if you have installed the pre-release version `2.0.x` then on the **C#** extension page, I recommend that you click the button **Switch to Release Version** to revert back to the current release version `1.25.9`.

# Page 138 - Performing CRUD operations with Cosmos SQL API

In Step 8, a long block of code tries to copy `Product` instances and their related `Category` and `Supplier` entities into Cosmos DB. Although the code uses `Where` to filter only products that have a non-null category and supplier, the compiler is not smart enough to detect this, and gives warnings about potential `null` value assignment. 

The following statement:
```cs
category = new CategoryCosmos
```
Can be changed to the following:
```cs
// If the related category is null, store null,
// else store the category mapped to Cosmos model.
category = p.Category == null ? null :
  new CategoryCosmos
```

And the following statement:
```cs
supplier = new SupplierCosmos
```
Can be changed to the following:
```cs
supplier = p.Supplier == null ? null :
  new SupplierCosmos
```

This change has been made in the 2nd edition, found at the following link: https://github.com/markjprice/apps-services-net8/blob/main/code/Chapter04/Northwind.CosmosDb.SqlApi/Program.Methods.cs#L149

# Page 200 - Testing an AutoMapper configuration

> Thanks to Doug Murphy for raising this issue via email.

In the note at the top of page 200, I wrote, "For the entity models, we used records because they will be immutable. But an 
instance of `Summary` will be created and then its members populated automatically by AutoMapper, so it must be a normal mutable class with public properties that can be set."

But records are not always immutable. They are immutable in this scenario due to the way I defined them. It is possible to define mutable records. For example:
```cs
// As defined in the book. This will not work because it // only has a 
// constructor with two parameters. AutoMapper needs to call a default 
// constructor (no parameters) and then set the properties.
public record Summary(string? FullName, decimal Total);

// This works because it has a default constructor and due to the init 
// keywords. After constructing an instance, the properties can be set,
// but after that, the properties cannot be changed.
public record Summary 
{
  public string? FullName { get; init; }
  public decimal Total { get; init; }
}
```

In the next edition, I will use the `init` style to define the `Summary` record.

# Page 411 - Using an ASP.NET Core MVC project as a GraphQL client

> Thanks to [mdevol58](https://github.com/mdevol58) for raising this [issue on 14 August 2023](https://github.com/markjprice/apps-services-net7/issues/18).

In Step 12, I wrote, "In the `Controllers` folder, in `HomeController.cs`, import the namespace for working with text encodings and for our Northwind entity models".

I should have written, "In the `Controllers` folder, in `HomeController.cs`, import the namespace for working with text 
encodings and for the local project models", and add the missing import statement for the models, as shown in the following code:
```cs
using Northwind.Mvc.GraphQLClient.Models; // IndexViewModel, ResponseProducts and so on
using System.Text; // Encoding
```

# Page 417 - Understanding Strawberry Shake - Creating a console app client

In Step 4, you add references to two StrawberryShake packages. I had hoped that version 13 would be released by now, but it is still in preview for one of those packages and is not available at all for the other. I therefore recommend that you use version 12.16.0, as shown in the following markup:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="7.0.0" />
  <PackageReference Include="Microsoft.Extensions.Http" Version="7.0.0" />
  <PackageReference Include="StrawberryShake.CodeGeneration.CSharp.Analyzers" Version="12.16.0" />
  <PackageReference Include="StrawberryShake.Transport.Http" Version="12.16.0" />
</ItemGroup>
```

> ChilliCream recommends that you always use the same versions when referencing multiple of their packages to ensure they work together properly.

In Step 9, you add a file named `seafoodProducts.graphql`. After creating this file, review the project file to check for an entry to explicitly remove this file from the build process, as shown in the following markup: 
```xml
<ItemGroup>
  <GraphQL Remove="seafoodProducts.graphql" />
</ItemGroup>
```

There must be at least one .graphql file for the Strawberry Shake tool to be able to generate its code automatically. So this entry will prevent the Strawberry Shake tool from generating its code and you will later get compile errors. You should delete or comment out that entry, as shown in the following markup:
```xml
<!--<ItemGroup>
  <GraphQL Remove="seafoodProducts.graphql" />
</ItemGroup>-->
```

# Page 467 - Adding a chat page to the MVC website

In Step 2, in a JavaScript file named `chat.js`, I tell the reader to add statements to add events handlers for the **Register** and **Send** buttons, and so on. One of those statements uses a backtick \` to enable JavaScript interpolated strings that use curly brackets `{}` for dynamic placeholders. 

But after submitting final drafts in a Word document, a Packt process replaced the backtick characters with single straight quote `'` characters. This disabled the interpolated strings. I had even added a comment to explain to the reader that they should use a backtick character but the Packt process replaced that too! 

Incorrect code in the print book in the comment and string value:
```cs
// note the use of backtick ' to enable a formatted string
li.textContent =
  'To ${received.to}, From ${received.from}: ${received.body}';
```

The statement should be as follows:

```cs
// note the use of backtick ` to enable a formatted string
li.textContent =
  `To ${received.to}, From ${received.from}: ${received.body}`;
```

Luckily, the code was correct in the GitHub repository:
https://github.com/markjprice/apps-services-net7/blob/main/vs4win/Chapter13/Northwind.SignalR.Service.Client.Mvc/wwwroot/js/chat.js#L28

# Page 571 - Blazor routing to page components

`MainLayout.cshtml` should be `MainLayout.razor`.

# Page 578 - Building Blazor components

> Thanks to [mdevol58](https://github.com/mdevol58) for raising two potential issues on [16 August 2023](https://github.com/markjprice/apps-services-net7/issues/19) and [17 August 2023](https://github.com/markjprice/apps-services-net7/issues/20).

In Step 17, the two elements added are already HTML5-compliant and were copied from [Bootstrap's official template](https://getbootstrap.com/docs/5.2/getting-started/introduction/). But if you need them to be XHTML-compliant then they should be self-closing i.e. they should end with `/>` instead of just `>`, as shown in the following markup:
```html
<meta name="viewport" content="width=device-width, initial-scale=1" />
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-gH2yIJqKdNHPEq0n4Mqa/HGKIhSkIHeL5AyhkYV8i59U5AR6csBvApHHNl/vI1Bx" crossorigin="anonymous" />
```

The second issue that mdevol58 found caused the **Delete Database** dialog not to display. I was not able to reproduce the same error so I cannot confirm the fix. If you have the same issue, try changing the attribute name from `crossorigin` to `crossOrigin`. The official documentation does not say camelCase is required.

# Page 587 - Building and testing a Blazor alert component

> Thanks to [Bob Molloy](https://github.com/BobMolloy) for raising this [issue on 8 February 2023](https://github.com/markjprice/apps-services-net7/issues/9).

In Step 3, the filename `_Imports.cshtml` should be `_Imports.razor`.

# Page 600 - Building a local storage service

In Step 9, the filename `_Imports.cshtml` should be `_Imports.razor`.

# Page 613 - Exploring Radzen Blazor components

> Thanks to [Bob Molloy](https://github.com/BobMolloy) for raising this [issue on 14 March 2023](https://github.com/markjprice/apps-services-net7/issues/11).

In Step 10, the filename `_Imports.cshtml` should be `_Imports.razor`. Also, the text should say, "add statements to import the Radzen and Radzen Blazor namespaces", not just one statement.

# Page 621 - Building a web service for Northwind entities

> Thanks to [Bob Molloy](https://github.com/BobMolloy) for raising this [issue on 14 March 2023](https://github.com/markjprice/apps-services-net7/issues/12).

In Step 6, the statement to include the products is missing a close bracket. It should be as shown:
```cs
app.MapGet("api/categories", (
  [FromServices] NorthwindContext db) => 
    Results.Json(
      db.Categories.Include(c => c.Products)))
  .WithName("GetCategories")
  .Produces<Category[]>(StatusCodes.Status200OK);
```

The GitHub repository code solution used a slightly older version of this code. It has now been updated to match the book, as shown at the following link:
https://github.com/markjprice/apps-services-net7/blob/main/vs4win/Chapter17/Northwind.BlazorLibraries/Server/Program.cs#L44

# Page 623 - Using the Radzen tabs, image, and icon components

> Thanks to [Bob Molloy](https://github.com/BobMolloy) for raising this [issue on 15 March 2023](https://github.com/markjprice/apps-services-net7/issues/13).

In Step 5, `MainLayout.cshtml` should be `MainLayout.razor`.

# Page 627 - Using the Radzen HTML editor component

In Step 3, `MainLayout.cshtml` should be `MainLayout.razor`.

# Page 631 - Using the Radzen chart component

In Step 5, `MainLayout.cshtml` should be `MainLayout.razor`.

# Page 633 - Using the Radzen form components

In Step 1, the statement to define and endpoint for employees manually sets JSON options, as shown in the following code:
```cs
app.MapGet("api/employees/", (
  [FromServices] NorthwindContext db) =>
    Results.Json(db.Employees, jsonOptions))
  .WithName("GetEmployees")
  .Produces<Employee[]>(StatusCodes.Status200OK);
```

It should use the globally configure JSON options, as shown in the following code:
```cs
app.MapGet("api/employees/", (
  [FromServices] NorthwindContext db) =>
    Results.Json(db.Employees))
  .WithName("GetEmployees")
  .Produces<Employee[]>(StatusCodes.Status200OK);
```

# Page 634 - Using the Radzen form components

In Step 2, `MainLayout.cshtml` should be `MainLayout.razor`.

# Page 657 - Creating a virtual Android device for local app testing

> Thanks to [Bob Molloy](https://github.com/BobMolloy) for raising this [issue on 29 March 2023](https://github.com/markjprice/apps-services-net7/issues/15).

In Step 5, **Save** should be **Create**. Also in *Figure 18.2*, the caption of the button should be **Create** instead of **Save**. (I must have edited an existing device instead of creating a new one when taking the screenshot which caused this mistake.)

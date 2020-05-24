---
title: "Blazor WebAssembly Client App Getting Started"
date: 2020-05-24T16:00:00-04:00
categories:
  - blog
  - quickstart
tags:
  - Blazor
  - Blazor WebAssembly
  - Client Web App
---

This post is a simple quick start on how to setup a Blazor WebAssembly Client App. Detailed instructions are available in this Microsoft Learn module: [Build a web app with Blazor WebAssembly and Visual Studio Code](https://docs.microsoft.com/en-us/learn/modules/build-blazor-webassembly-visual-studio-code/). I am only demonstrating a summary of what you can learn from that module. 

If you are entirely new to Blazor be sure to check out the [Blazor homepage](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) and [Introduction to ASP.NET Core Blazor](https://docs.microsoft.com/en-us/aspnet/core/blazor/?view=aspnetcore-3.1).

## Get up and going in less than 10 minutes

This quickstart will walk you thru the following steps:

1. Install the necessary extensions for VS Code
2. Create a new `blazorwasm` project
3. Add a new `razor` file to your project
4. Hook the new `razor` page up to a nav menu
5. Wire up the new razor page with some basic functionality
6. Run and test your application

Doing this with VS Code is pretty easy to setup and you should be able to be up and going in 10 minutes or less if you are comfortable working with `VS Code` and `dotnet`.

## Getting Started

When using VS Code, to get started with a Blazor WebAssembly there are a few extensions that are needed to get started.

1. Install .NET Core 3.1 by downloading the latest version from [https://dotnet.microsoft.com/download/dotnet-core/3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) for your operating system
2. In [VS Code](https://code.visualstudio.com/) install the following extensions:
   1. [C# for Visual Studio Code (powered by OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
   2. [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)
3. At the time of this writing you will need to enable a preview feature for the JavaScript Debugger extension
   1. Right click on the **JavaScript Debugger (Nightly)** extension and go to `Extension Settings`
   2. Scroll and find `Debug > JavaScript: Use Preview`
   3. Check `Use the new in-preview JavaScript debugger for Node.js and Chrome`

## Create a new Blazor WebAssembly app

When it comes to creating Blazor apps, there are a few templates you can choose from as noted at [ASP.NET Core Blazor templates](https://docs.microsoft.com/en-us/aspnet/core/blazor/templates?view=aspnetcore-3.1). In this exercise we are going to use the `blazorwasm` template to create a Blazor WebAssembly app.

1. Open a terminal or command prompt window
2. In the command prompt or terminal enter the following 
  ```bash
  dotnet new blazorwasm -o CICalc
  ```
3. Navigate to the `CICalc` sub folder
4. In the command prompt or termianl enter
  ```bash
  dotnet run
  ```
5. Open a web browser and navigate to `https://localhost:5001`
6. Confirm that the app runs correctly with the `blazorwasm` template

## Add a new page to the app

1. Navigate to the folder where `CICalc` project was created and enter `code .` to launch VS Code
2. Expand the folders in the project explorer
3. Right-click on `Pages` and select `New File`
4. Name the new page `CompoundInterest.razor`
5. Open the newly created `razor` file
6. At the top of the file add the following
  ```csharp
    @page "/compoundinterest"

    <h1>Compound Interest</h1>
  ```
7. Open the `Shared` folder
8. Open `NavMenu.razor`
9. Add a fourth list item that will point to the newly created razor page
  ```csharp
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="compoundinterest">
            <span class="oi oi-plus" aria-hidden="true"></span> Compound Interest
        </NavLink>
    </li>
  ```
10. In the VS Code terminal enter `dotnet run` to launch the application
11. Open a web browser and navigate to `https://localhost:5001`
12. Click on the `Conversions` menu to confirm it redirects to the newly created page

## Adding code to the page

1. In the `CompoundInterest.razor` file add the following code just beneath the `<h1>`
  ```csharp
    <table class="table">
        <tr>
            <th>Initial Principal</th>
            <th></th>
        </tr>
        <tr>
            <th>Years</th>
            <th></th>
        </tr>
        <tr>
            <th>Annual Interest Rate (%)</th>
            <th></th>
        </tr>
        <tr>
            <th>Total:</th>
            <th></th>
        </tr>
    </table>
    <button class="btn btn-primary">Calculate</button>
  ```
2. Next we add the inline code to the page in an `@code` block just beneath the HTML added in the prior step. Code can either be inline in the same razor file or can be in a separate `.cs` file entirely. In this example we will add inline
  ```csharp
    @code
    {
        private double Principal { get; set; } = 5000;
        private double InterestRate { get; set; } = 5;
        private int Years { get; set; } = 10;
        private double total { get; set; } = 0;
        private string Total { get; set; }

        private void Calculate()
        {
            var total = Principal * Math.Pow(1 + InterestRate / (1200.0), Years * 12);
            Total = total.ToString("C");
        }
    }
  ```
3. Now that the code is in place the bindings need to be added to hook up the HTML to the C# code. The updated HTML will look like this, note the addition of `@bind` and `@onclick`
  ```csharp
    <table class="table">
        <tr>
            <th>Initial Principal</th>
            <th><input @bind="Principal" /></th>
        </tr>
        <tr>
            <th>Years</th>
            <th><input @bind="Years" /></th>
        </tr>
        <tr>
            <th>Annual Interest Rate (%)</th>
            <th><input @bind="InterestRate" /></th>
        </tr>
        <tr>
            <th>Total:</th>
            <th>@Total</th>
        </tr>
    </table>
    <button class="btn btn-primary" @onclick="Calculate">Calculate</button>
  ```
4. Run the app using `dotnet run` in the terminal and test out the functionality

## Next Steps

The next steps are to become aquainted with Blazor and what all it has to offer. Below are some [references](#references) to start with. If you are used to coding in `C#` the good news is you should be able to pick up working with Blazor pretty quickly. [Introduction to ASP.NET Core Blazor](https://docs.microsoft.com/en-us/aspnet/core/blazor/?view=aspnetcore-3.1) is a good place to start to get a sense of everything. 

Moving on from there the [Razor syntax reference for ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-3.1) is the next place to have bookmarked to dive into this. For the simple code used in this article the following were used in the razor syntax which are only a few of the many options available:

* [Directives](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-3.1#directives)
  * [@code](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-3.1#code)
  * [@page](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-3.1#page)
* [Directive Attributes](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-3.1#directive-attributes)
  * [@bind](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-3.1#bind)
  * [@on{EVENT}](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-3.1#onevent)

## References

* [Build a web app with Blazor WebAssembly and Visual Studio Code](https://docs.microsoft.com/en-us/learn/modules/build-blazor-webassembly-visual-studio-code/)
* [Blazor homepage](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor)
* [Introduction to ASP.NET Core Blazor](https://docs.microsoft.com/en-us/aspnet/core/blazor/?view=aspnetcore-3.1)
* [Razor syntax reference for ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-3.1)
* [Host and deploy ASP.NET Core Blazor WebAssembly](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/blazor/webassembly?view=aspnetcore-3.1)
* [Project structure for Blazor apps](https://docs.microsoft.com/en-us/dotnet/architecture/blazor-for-web-forms-developers/project-structure)
* [Build Progressive Web Applications with ASP.NET Core Blazor WebAssembly](https://docs.microsoft.com/en-us/aspnet/core/blazor/progressive-web-app?view=aspnetcore-3.1&tabs=visual-studio) 
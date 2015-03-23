title: NancyFx  
author:  
  name: Mike Ward  
  twitter: mikeward_aa  
  url: http://mike-ward.net  
output: index.html  
style: style.css
--
# Nancy
## ASP.NET's Super Duper Happy Framework

--

--
### Demo

<https://dotnetfiddle.net/YkgrV6>

--
### Why Nancy?

![fit](nancy-sinatra.jpg)

--
### Built to Run Anywhere

One of the core concepts in Nancy is hosts

Hosts act as adapters for a hosting environment and Nancy

Runs on existing technologies 

* ASP.NET
* WCF
* OWIN
* Integrated in any given application

--
### The super-duper-happy-path

“It just works”

* Modules automatically discovered
* Multiple view engines per application
* Automatic dependency injection

--
### Easily Customizable

* Use any IOC container
* Tweak routes
* Add to the pipeline

--
### Low Ceremony

* Minimal code
* No boiler plate

--
### Low Friction

* Stays out of the way
* Required configuration minimal
* Naming is obvious

--
### Demo

<https://dotnetfiddle.net/yWSvP8>

--
### Bootstrapper

To change the runtime behavior of Nancy write a custom bootstrapper

    public class CustomBootstrapper : DefaultNancyBootstrapper
    {
        protected override void ApplicationStartup(
            TinyIoCContainer container, 
            IPipelines pipelines)
        {
             // your customization goes here
        }
    }
 
--
### TinyIoC

* Default implementation uses TinyIoC
* Auto-resolves concreate types
* Auto-registers interfaces

--
### Other IoC's

* TinyIoC be swapped out with a container of your choice
* All major containers available through NuGet

--
### Views

* You get a lot of choices...
* For most cases, it just works.

--
### Resolving Views from Routes

    Get["/customers"] = p => View["customers.html", someModel];

    Get["/customers"] = p => View["customers.html", someModel];

    Get["data/customers"] = p => View["customers.html", someModel];

-- 
### Resolving Views from Models

    Get["/customers"] = p => View[customersModel];

1. Strip off `Model` if there
2. Look in Views folder
3. Look in `/customers` folder

-- 
### Custom View Conventions

    public class CustomBootstrapper : DefaultNancyBootstrapper
    {
        protected override void ApplicationStartup(
            TinyIoCContainer container, 
            Nancy.Bootstrapper.IPipelines pipelines)
        {
            Conventions
                .ViewLocationConventions
                .Add((viewName, model, context) =>
            {
                return string.Concat("custom/", viewName);
            });
        }
    }

--
### Roll Your Own

* Implement `IConvention`
* Add to `ViewLocationConventions` property

--
### View Engines

You get a lot of choices...

* Super Simple View Engine
* Razor
* Spark
* DotLiquid
* Markdown
* ...

--
### Super Simple View Engine

    @Master['MasterPage']
    
    @Section['Content']
        <p>This content from the index page<p>
        <h3>Partials</h3>
        <p>Partial view with no model.</p>
        <div id="login">@Partial['login'];</div>
        <p>Partial with a sub-model passed in.</p>
        <p>Iterate with Each</p>
        <div id="users">@Partial['user', Model.Users];</div>
        <h3>Encoding</h3>
        <p>Model output can also be encoded:</p>
        <p>@!Model.NaughtyStuff</p>
    @EndSection

--
### Razor

    PM> Install-Package Nancy.ViewEngines.Razor

`@Model` not `@model` like in ASP.NET MVC

--
### Routes


--
### Model Binding


--
### Testing


--
### Content Negotiation


--
### Static Content


--
### Authentication


--
### Diagnostics


--
### In Conclusion


--
### Slides

<https://github.com/mike-ward/talk-nancyfx>

Use the `gh-pages` branch.

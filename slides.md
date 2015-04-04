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
### Slides

> <http://mike-ward.net/talk-nancyfx>

> <https://github.com/mike-ward/talk-nancyfx>

--

--
### Demo

<https://dotnetfiddle.net/YkgrV6>

--
### Why Nancy?

![fit](nancy-sinatra.jpg)

--
### Sinatra (Ruby)

    #!/usr/bin/env ruby
    require 'sinatra'
     
    get '/' do
      redirect to('/hello/World')
    end
     
    get '/hello/:name' do
      "Hello #{params[:name]}!"
    end

--
### Built to Run Anywhere

One of the core concepts in Nancy is hosts

Hosts are adapters for hosting environments and Nancy

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

To change the runtime behavior of Nancy, write a custom bootstrapper

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

    Get["data/customers"] = 
        p => View["customers.html", someModel];

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

    // captures routes like /hello/nancy sent as a GET request
    
    Get["/hello/{name}"] = parameters => {
        return "Hello " + parameters.name;
    };
    
    // captures routes like /favoriteNumber/1234, 
    // but not /favoriteNumber/asdf as a GET request
    
    Get["/favoriteNumber/{value:int}"] = parameters => {
        return "Favorite number is " + parameters.value + "?";
    };

--
### Routes (continued)

    // captures routes like /products/1034 
    // sent as a DELETE request
    
    Delete[@"/products/(?<id>[\d]{1,7})"] = parameters => {
        return 200;
    };
    
    // captures routes like /users/192/add/moderator 
    // sent as a POST request
    
    Post["/users/{id}/add/{category}"] = parameters => {
        return HttpStatusCode.OK;
    };

--
### Routes (Contraints)

  int
, long
, decimal
, guid
, bool
, alpha
, datetime
, datetime(format)
, min(minimum)
, max(maximum)
, range(minimum, maximum)
, minlength(length)
, maxlength(length)
, length(minimum, maximum)

#### Custom Contraints

    RouteSegmentConstraintBase<T>
    
    ParameterizedRouteSegmentConstraintBase<T>

--
### Model Binding

    var model = this.Bind<Foo>();

#### Serialization

- collect from the body (JSON, XML, Forms)
- collect from captured parameters
- collect from query strings

#### Deserialization

- JSON and XML
- Roll your own

--
### Testing

    [Fact]
    public void Should_return_status_ok_when_route_exists()
    {
        var bootstrapper = new DefaultNancyBootstrapper();
        var browser = new Browser(bootstrapper);
    
        var result = browser.Get("/", with => {
            with.HttpRequest();
        });
    
        Assert.Equal(HttpStatusCode.OK, result.StatusCode);
    }

--
### Being Assertive

    [Fact]
    public void Should_redirect_to_login_with_error_details_incorrect()
    {
        var bootstrapper = new DefaultNancyBootstrapper();
        var browser = new Browser(bootstrapper);
    
        var response = browser.Post("/login/", (with) => {
            with.HttpRequest();
            with.FormValue("Username", "username");
            with.FormValue("Password", "wrongpassword");
        });
    
        response.ShouldHaveRedirectedTo(
            "/login?error=true&username=username");
    }

--
### Assert Content

    [Fact]
    public void Should_display_error_message_when_error_passed()
    {
        var bootstrapper = new DefaultNancyBootstrapper();
        var browser = new Browser(bootstrapper);
    
        var response = browser.Get("/login", (with) => {
            with.HttpRequest();
            with.Query("error", "true");
        });
    
        response.Body["#errorBox"]
                .ShouldExistOnce()
                .And.ShouldBeOfClass("floatingError")
                .And.ShouldContain(
                    "invalid",
                    StringComparison.InvariantCultureIgnoreCase);
    }

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

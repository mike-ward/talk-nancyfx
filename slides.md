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

-- nancy-home-page

--
### Demo

<https://dotnetfiddle.net/YkgrV6>

--
### Why Nancy?

![fit](nancy-sinatra.jpg)

--
### Sinatra (Ruby)

```ruby
#!/usr/bin/env ruby
require 'sinatra'
 
get '/' do
  redirect to('/hello/World')
end
 
get '/hello/:name' do
  "Hello #{params[:name]}!"
end
```

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

![sdhp](sdhp.gif)

--
### It just works

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

```cs
public class CustomBootstrapper : DefaultNancyBootstrapper
{
	protected override void ApplicationStartup(
		TinyIoCContainer container, 
		IPipelines pipelines)
	{
		 // your customization goes here
	}
}
```
 
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

```cs
Get["/customers"] = p => View["customers.html"];
```

1. Look in the Views folder
2. Look in the `\customers` folder
 
-- 
### Resolving Views from Models

```cs
Get["/customers"] = p => View[customersModel];
```

1. Strip off `Model` if there
2. Look in Views folder
3. Look in `/customers` folder

-- 
### Custom View Conventions

```cs
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
```

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

```xml
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
```

--
### Razor

```dos
PM> Install-Package Nancy.ViewEngines.Razor
```

`@Model` not `@model` like in ASP.NET MVC

--
### Routes

```cs
// captures routes like /hello/nancy sent as a GET request

Get["/hello/{name}"] = parameters => 
    "Hello " + parameters.name;

// captures routes like /favoriteNumber/1234, 
// but not /favoriteNumber/asdf as a GET request

Get["/favoriteNumber/{value:int}"] = parameters => 
	"Favorite number is " + parameters.value + "?";
```

--
### Routes (continued)

```cs
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
```

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

```cs
RouteSegmentConstraintBase<T>

ParameterizedRouteSegmentConstraintBase<T>
```

--
### Model Binding

```cs
var model = this.Bind<Foo>();
```

**Serialization**

- collect from the body (JSON, XML, Forms)
- collect from captured parameters
- collect from query strings

**Deserialization**

- JSON and XML
- Roll your own

--
### Testing


![nancy.testing](nancy.testing.png)

--
### Headless Testing

```cs
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
```

--
### Being Assertive

```cs
[Fact]
public void Should_redirect_to_login_error_incorrect()
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
```

--
### Assert Content

```cs
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
```

--
### Content Negotiation

```cs
Get["/"] = parameters => new MyModel();
```
    
The content negotiation pipeline will inspect the incoming Accept headers and determine which of the requested media types is the most suitable and format the response accordingly.

--
### File Extension Hotwiring

```cs
Get["/ratpack"] = parameters => new RatPack();
```

- /ratpack.json => json response
- /ratpack.xml => xml response

--
### Static Content

**TL;DR: stick stuff in `/Content` .. done.**


--
### Define your own Convention(s)

```cs
public class CustomBoostrapper : DefaultNancyBootstrapper
{
	protected override void 
		ConfigureConventions(NancyConventions conventions)
	{
		base.ConfigureConventions(conventions);

		conventions.StaticContentsConventions.Add(
			StaticContentConventionBuilder.AddDirectory
			("assets", @"contentFolder/subFolder")
		);
	}
}
```

--
### Authentication

Short Answer: No built-in authentication

Longer Answer: Extension points allow you to add authentication that fits your business needs.

--
### Supported Authentication Packages

- Forms `Nancy.Authentication.Forms`
- Basic `Nancy.Authentication.Basic`
- Stateless `Nancy.Authentication.Stateless`

--
### Authentication Example

```cs
public class ShowStudyModule: NancyModule
{
  public ShowStudyModule()
  {
	  this.RequiresAuthentication();
	  this.RequiresClaims(new[] {"admin"});

	  Get["/show/{studyid}"] = p => View["show", p.studyid];
	  ...
```
--
### Authentication Example Extended

```cs
public class ShowStudyModule: NancyModule
{
  public ShowStudyModule(ILicense license)
  {
	  this.RequiresAuthentication();
	  this.RequiresClaims(new[] {"admin"});
	  this.RequiresLicense(license);

	  Get["/show/{studyid}"] = p => View["show", p.studyid];
	  ...
```

--
### Diagnostics

```cs
protected override 
    DiagnosticsConfiguration DiagnosticsConfiguration =>
    new DiagnosticsConfiguration { Password = @"secret" };
```

-- diagnostics-page

--
### Open Source

![opensource](opensource.png)

<https://github.com/NancyFx>

--
### Why Nancy?

**Because it Rocks!**

![rocking](rocking.jpg)

Oh, and all that other stuff I said.


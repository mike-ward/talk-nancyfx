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
 
 


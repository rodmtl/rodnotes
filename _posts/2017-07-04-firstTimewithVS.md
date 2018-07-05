---
layout: post
title:  ".NET Dev 101"
date:   2018-07-04 03:46:36 -0400
categories: VS2017 dev
---
## First time with Visual Studio?
### First time with c#?

Go to - <https://my.visualstudio.com> using your MSDN account.

1. Download visual studio. This will take acouple of hours at least.
2. Activate Pluralsight benefit.

<https://app.pluralsight.com/library/courses/c-sharp-fundamentals-with-visual-studio-2015/table-of-contents>

<https://app.pluralsight.com/library/courses/csharp-from-scratch/table-of-contents>

<https://app.pluralsight.com/library/courses/csharp-from-scratch-part2/table-of-contents>


----

## .net MVC Developer Resources:

#### ASP.net

<https://app.pluralsight.com/library/courses/tfs-visual-studio-2017-continuous-delivery-devops/table-of-contents>

<https://app.pluralsight.com/library/courses/full-stack-dot-net-developer-fundamentals>

<https://app.pluralsight.com/library/courses/full-stack-dot-net-developer>


#### .net core (<https://en.wikipedia.org/wiki/ASP.NET_Core>)
<https://app.pluralsight.com/library/courses/aspdotnetcore-web-application-building/table-of-contents>

----

## New developer joining your team:

- Get the solution from VSTS. 
- Have the Telerik license assigned or any third party framework the project will use
- Machine is new? Install plugind and all 3rd party tools
- Get and setup yor DB Access:
	Server, user
	

----

##	Dev tricks and tips:
### _mostly for MVC with Telerik Kendo UI Framework_

- All the actions in controllers must have the Authorize atribute.
- Keep controllers as small as possible. (Use Services to code Data transformations, etc.)
- Keep buttons style and layout the same as the already built forms (Design consistency)
- When accessing data, try to code on the repo class and return IQueryable objects. Convert to lists only when the data is filtered. (This will help performance, useful when dealing with huge amounts of data)
- Always (or at least try) use view models to pass info to a view.
- On grids: 
    - Always create custom sorts and filter attributes.
    - sort data on server and client.
    - Verifiy that the query is efficient. Run a SQL profiler if necessary.
- Before use standar MVC controls check if there is an equivalent kendo MVC control (Gives you more options on the client side).
- Keep at minimum the JS on the views, instead you can create JS files(Sevices) and include it in the bundle.
- Access DB only trhough the unit of work class. Use dependency injection in all the classes. (Currently using ninject)
- In forms always add tabindex property to the controls in order to able navigation with the tab key.
- Kendo UI Icons (For buttons): <https://docs.telerik.com/kendo-ui/styles-and-layout/icons-web>
- Principal action on the form should always be on prymary color.
- If you are developing intranet Apps all JS and CSS must be local referenced and bundled in the .net app


----

### Kendo Grid Error Handling

Changed exception catch on global.asax in case is an exception rised by an Ajax Call (Grids)

{% highlight javascript %}
// If we're an ajax request, and doing a 302, then we actually need to do a 401
if (context.Request.IsAjaxRequest())
{                    
	return;
}
{% endhighlight %}


Created a new Global JS function to catch No connection Error on all Grids. 

{% highlight javascript %}
function grid_error(e) {
    if (e.xhr.status === 404 || e.xhr.status === 401 || e.xhr.status === 500) //Not found
        alert("Something went wrong - Please contact your system administrator.");    
    if (e.errors) {
        var message = "Error: ";
        // Create a message containing all errors.
        $.each(e.errors, function (key, value) {
            if ('errors' in value) {
                $.each(value.errors, function () {
                    message += this + "\n";
                });
            }
        });
        // Display the message.
        alert(message);
        // Cancel the changes.
        var grid = $("#gridLocations").data("kendoGrid");
        grid.cancelChanges();
        //console.log(message);
        alert(e.errorThrown + " - Please contact your system administrator.");
    }
    
}
{% endhighlight %}



Setup all Grids to use the new Error Catch Js Function. 

{% highlight c# %}
.Events(events => events.Error("grid_error")) // Handle the "error" event.
{% endhighlight %}


+ Fix All get Methods in controllers to return an Error to the UI.
	- Check each controller. Catch Portion should:
	    - Errors need to be logged and added to the model state.
	    - Return jsons with the Model state. 


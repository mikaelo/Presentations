# Understanding your superpowers

## Raw

Use voting app

- show running as processes
- running as containers
- w/ a function
- w/ kubenetes

on docker
- single-phase vs two phase (build inside a container)
- self-contained vs base image (related but not coupled)


Concerns:

- Deployment
- Files
- Networking
- Relationships
- Monitoring/Diagnostics

## Personal Intro



## Agenda

First of all - what is an Application Model? That's probably a new term for a lot of people.

Secondly have can you think systematically about cloud runtimes and deployment environments.

And hopefully by the end you can understand runtime environments for easily and more expertly design applications.

## Topic Intro

## Demo: Applying Systematic Thinking - The Process

Narrative: let's think about the needs and requirements of the application and try to map those to code.

We'll start with the humble process - because you are probably used to running applications on your computer.

Let's start very basic - I need the application to actually start - all of the files and depenendencies needs to be in place. 

secondly, this is a website so I need to be able to accept web traffic. I also need to talk to some other websites to get data. 

You can think about connections in and connections out a little differently.

- For a connection in you need to have an address - other people need to be able to find you. This is most commonly going to be a port for HTTP.
- For an outgoing connection you need to know the address - how do you reach the other service. You might also need some credentials 

next, what about configuration?

We will want a way to make some of our application behaviors configurable. It also might be a good way to communicate things like what addresses we're using to talk to other services.

and lastly, diagnostics. We need some kind of logging - that way we can diagnose problems with a running app, or being able to collect data about what kinds of failures occur.

---

So let's map these concerns to our code and tools - and then to understand how these 4 concepts work:

- Deployment
- Communication
- Configuration 
- Diagnostics

For deployment - we have to look no further that `dotnet publish`. It's possible no explained the difference between `dotnet run` and `dotnet publish` before clearly. `dotnet run` runs your application *in-place* - using your content files from where they already are on disk and using .dlls from the where they are downloaded by nuget. 

When `dotnet run` builds your application code - you can find the binaries in the `bin/` folder. But if you copy this folder somewhere else it won't work. `dotnet publish` on the other hand creates a runnable output of your application that can be used portably.

But we're not done talking about deployment yet. `dotnet publish` assumes that a compatible version of .NET Core is installed where you're going run. If you want to you can use the `--runtime` argument to include the runtime in the output. We call this a self-contained publish.

Ultimately the thing you want to run will be a set of files on disk. Deployment is about making sure you can reliably produce the right set of files.

Next - let's talk about incoming HTTP - we need the application to listen. That code is provided as part of ASP.NET Core - this code interfaces with the operating system to handle HTTP requests by opening a port.

Which port? This is something that ASP.NET Core provides some opinions about. By default ASP.NET Core will listen on port 5000 - we can configure this with some settings. Our concern when running web applications as processes is to avoid a conflict, and to remember all of the various numbers we picked.

Let's talk about configuration - we'll use for a few things:

- potentially configuing the framework itself
- configuring connections to other services (URLs connection strings)
- any other settings or data we need to provide the application

Again, ASP.NET Core provides some default opinions about configuration. The configuration system can gather and compose configuration data from across different formats. You get some configuration files on disk as part of the template, and the command line and environment variable providers for config by default.

In terms of our relationship with OS our choices are for each configuration key to provide it via the command-line, environment variables, or configuration files.

There are cases that are good for all of these options. I'll talk about CLI and environment variables together because they have similar tradeoffs. environment variables are a little bit easier to compose typically.

environment variables are good for infromation that you don't want to source control next to the app like secrets, or deployment-specific configuration. environment variables are clumsy when your data is highly structured since they are just key value pairs. The set of environment variables are read into configuration when the process starts are aren't expected to change for the lifetime of the process.

configuration files are better when you have highly structued data (like json), that you want to source control next to the application. It's a good choice for data that you don't want to hardcode in application code, or that doesn't neatly translate to key-value-pairs. Additional configuration files can change. For instance the logging system in asp.net core will watch for configuration changes and update the log verbosity.

We also wanted to talk about logging and diagnostics. 


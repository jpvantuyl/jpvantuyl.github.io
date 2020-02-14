---
layout: post
title: "All the flavors of config"
date: 2020-02-14 -0800
comments: false
categories: [configuration]
---

Shipping quality software means decoupling the data that flows through that software from the actual deployed bits.  I work in C# every day and our compiled binaries depend on a whole lot of moving parts.  The fact that these parts can move makes them more valuable.

## User data

A river of user data flows in through our web pages and out to a database.  Not a shocker here.  Everyone does it.  Our tool of choice is a SQL database like MSSQL or DB2 if its on the mainframe backend side.  A tiny portion of the data is configuration data where the user can tweak some settings that affect their user experience.  There might be other feature flags deeper in the system that change how that user config data is treated.

## Web.config Files

Most of the apps I work on are old-school ASP.NET web projects.  That means we do a lot of our configuration in a config file that ships with the software.  The Web.config file has the obvious `<AppSettings>` section where you can write key/value pairs.  Each of these is weakly typed.  If you want a Boolean or a date you'll need to interpret it in your code.  

The strength of this is that it's simple to understand and operate.  The biggest downside is that a change in your software means needing to ship a new config file and restart the process to read it.  This kind of change can only be shipped by developers through the build pipeline.

## Database Support Data

We have a reasonable amount of data that is read in from the database.  Things like magic strings, lists of zip codes, etc.  These categories of support data are really useful to share amongst all our apps and since they're almost always read-only getting them from a database makes sense.

The only people who write to the database are our operations team and they can do it at any time in any environment.

## CMS Content

We have a Content Management System (CMS) that allows non-developers to change the words on our website.  Pretty typical of a CMS.  We also use it to serve up other content like JavaScript and CSS.  This keeps the distribution and presentation of that content wholely outside of our usual .NET developer teams.  We can have front-end developers with those skills collaborate with stakeholders on those changes.  Because content isn't compiled we can have a more ad hoc distribution pipeline.

## Feature Flags

Being able to enable/disable features and update configuration in the environment at runtime has been hugely beneficial.  We have a simple key/value pair store hooked up to each of our environments.  There's a nice approval mechanism for each change so we have to have two sets of eyes on each change.  In lower environments our QA team owns the configs, in production it's owned by the BA team.  And the config provider inside .NET caches the configuration so we don't make too many calls over the network.

## Conway's Configuration

If you haven't heard of [Conway's Law](https://en.wikipedia.org/wiki/Conway%27s_law) it's the idea that the (software) systems of an organization will tend to mirror their communication structure.  Application configuration is an interesting lense to use to examine an organization.

Coordination between development teams is often required to make the same configuration changes in the Web.config files.  Its not a shared configuration so if a communication point is missed there might be an app that didn't switch on the feature.  Because these configs are part of a packaged deployment we'll have developers and QA confirm that they're working correctly.  Making a system-wide change might take a developer on four different teams talking to each other and to a QA person to make the change simultaneously.

Conversely, support data from the database can be changed by operations without all of that communicating.  Its a faster path to changes but a bit riskier because we don't take as much time to double check things.  The same system-wide change from above might take one ops person and a QA person.  But there's also no opting out of the change; with support data changes everyone is along for the ride so it'd better be a safe change.

## Reverse Conway

To maximize productivity and optimize for happiness we can attempt the reverse Conway maneuver.  We can build a software system that works well with specific communication flows and hopefully the teams using it will adapt.  This might feel a bit dirty and manipulative but if you believe in Conway's law that software will tend to mirror team communication then you'd have to acknowledge that the tools you choose for your organization _will_ have an impact on team dynamics.  The big question is whether you approach tool choice with intentionality.

So what kind of outcomes do we want from a configuration tool?  A shared-nothing approach a la Microservices builds for speed of release and assumes that each system configuration point is located in exactly one spot.  In that model, I guess all configuration stuff would be accessible via an API?  I like shared-nothing configuration to be an option because at least half of the time the config settings for my app are very specific to my domain and not interesting to other teams.  I need a system that doesn't have too much noise.

The other half of the time a feature flag, URL, or other setting _will_ be shared.  Or it'll start off in one team who's getting started early and eventually get promoted to a system-wide configuration point.  So I want the ability to see configuration points that other teams have so I can pillage their configs and avoid writing/maintaining that stuff myself.  Of course, I'd always like to be able to fork my config off and go my own way so having an override mechanism would be excellent.  And if I go that route I'd like my choices to be easily discoverable by other teams.

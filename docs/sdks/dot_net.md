---
id: dot_net_sdk
title: .net SDK
---

In this guide we explain how to use feature toggles in a .NET application using Unleash-hosted. We will be using the open source Unleash [.net Client SDK](https://github.com/Unleash/unleash-client-dotnet).

> **Important details**
>
>Make sure you have the following details available:
>
>- **API URL** – Where you should connect your client SDK
>- **API Secret** – Your API secret required to connect to your instance. 
>You can find this information in your “Instance admin” available in your Unleash management UI. 

## Step 1: Install client SDK

First we must add Unleash Client SDK as a dependency to your project. Below is an example of how you would add it via the .Net cli. Please see [NuGet](https://www.nuget.org/packages/Unleash.Client/) for other alternatives.

```sh
dotnet add package unleash.client
```

## Step 2: Create a new Unleash Instance

Next we must initialize a new instance of the Unleash Client.

```csharp
var settings = new UnleashSettings()
{
  AppName = "dot-net-client",
  Environment = "local",
  UnleashApi = new Uri("API URL"),
  CustomHttpHeaders = new Dictionary()
  {
    {"Authorization","API secret" }
  }
};

IUnleash unleash = new DefaultUnleash(settings);
```

In your app you typically just want one instance of Unleash, and inject that where you need it. 

You should change the URL and the Authorization header (API secret) with the correct values for your instance, which you may locate under “Instance admin” in the menu.

## Step 3: Use the feature toggle

Now that we have initialized the client SDK we can start using feature toggles defined in Unleash in our application. To achieve this we have the “isEnabled” method available, which will allow us to check the value of a feature toggle. This method will return true or false based on whether the feature should be enabled or disabled for the current request. 

```csharp
if (unleash.IsEnabled("Demo")) 
{
  //do some magic
} 
else 
{
  //do old boring stuff
}
```

Pleas note the client SDK will synchronize with the Unleash-hosted API on initialization, and thus it can take a few milliseconds the first time before the client has the correct state. You can use the *SynchronousInitialization* option to block the client until it has successfully synced with the server. 

Read more about the [Unleash architecture](https://www.unleash-hosted.com/articles/our-unique-architecture) to learn how it works in more details

## Step 4: Provide Unleash Context

It is the client SDK that computes whether a feature toggle should be considered enabled or disabled for  specific use request. This is the job of the activation strategies, which are implemented in the client SDK.

The activation strategies is an implementation of rules based on data, which you provide as part of the Unleash Context.

**a) As argument to the isEnabled call**

The simplest way to provide the Unleash Context is as part of the “isEnabled” call:

```csharp
var context = new UnleashContext
{
  UserId = "61"
};

unleash.IsEnabled("someToggle", context);
```

b) Via a UnleashContextProvider

This is a bit more advanced approach, where you configure an unleash-context provider. By doing this, you do not have to rebuild or to pass the unleash-context object to every place you are calling `unleash.IsEnabled`. You can read more, and get [examples about this option on GitHub](https://github.com/Unleash/unleash-client-dotnet#unleashcontextprovider).

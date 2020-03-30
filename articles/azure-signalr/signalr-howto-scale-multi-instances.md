---
title: Skala med flera instanser - Azure SignalR-tjänst
description: I många skalningsscenarier måste kunden ofta etablera flera instanser och konfigurera för att använda dem tillsammans för att skapa en storskalig distribution. Fragmentering kräver till exempel stöd för flera instanser.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158153"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Hur skalar du SignalR-tjänsten med flera instanser?
Den senaste SignalR Service SDK stöder flera slutpunkter för SignalR Service-instanser. Du kan använda den här funktionen för att skala samtidiga anslutningar eller använda den för meddelanden mellan regioner.

## <a name="for-aspnet-core"></a>För ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hur lägger man till flera slutpunkter från config?

Config med `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` nyckel eller för SignalR Service anslutningssträng.

Om nyckeln börjar `Azure:SignalR:ConnectionString:`med ska den `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`vara `Name` `EndpointType` i format `ServiceEndpoint` , var och är egenskaper för objektet och är tillgängliga från kod.

Du kan lägga till anslutningssträngar för flera instanser med följande `dotnet` kommandon:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hur lägger man till flera slutpunkter från koden?

En `ServicEndpoint` klass introduceras för att beskriva egenskaperna för en Slutpunkt för Azure SignalR-tjänst.
Du kan konfigurera flera instansslutpunkter när du använder Azure SignalR Service SDK via:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Hur man anpassar slutpunkt router?

Som standard använder SDK [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) för att plocka upp slutpunkter.

#### <a name="default-behavior"></a>Standardbeteende 
1. Routning av klientbegäran

    När `/negotiate` klient med appservern. Som standard väljer SDK **slumpmässigt** en slutpunkt från uppsättningen tillgängliga tjänstslutpunkter.

2. Routning av servermeddelanden

    När *skicka meddelande till en viss **anslutning***, och målanslutningen dirigeras till aktuell server, går meddelandet direkt till den anslutna slutpunkten. Annars sänds meddelandena till varje Azure SignalR-slutpunkt.

#### <a name="customize-routing-algorithm"></a>Anpassa routningsalgoritm
Du kan skapa en egen router när du har särskild kunskap för att identifiera vilka slutpunkter meddelandena ska gå till.

En anpassad router definieras nedan som ett `east-` exempel när grupper `east`som börjar med alltid går till slutpunkten med namnet:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Ett annat exempel nedan, som åsidosätter standardförhandsförhöret, för att välja slutpunkter beror på var appservern finns.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Glöm inte att registrera routern till DI-behållaren med:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>För ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hur lägger man till flera slutpunkter från config?

Config med `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` nyckel eller för SignalR Service anslutningssträng.

Om nyckeln börjar `Azure:SignalR:ConnectionString:`med ska den `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`vara `Name` `EndpointType` i format `ServiceEndpoint` , var och är egenskaper för objektet och är tillgängliga från kod.

Du kan lägga till anslutningssträngar för flera instanser i: `web.config`

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hur lägger man till flera slutpunkter från koden?

En `ServicEndpoint` klass introduceras för att beskriva egenskaperna för en Slutpunkt för Azure SignalR-tjänst.
Du kan konfigurera flera instansslutpunkter när du använder Azure SignalR Service SDK via:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Hur man anpassar router?

Den enda skillnaden mellan ASP.NET SignalR och ASP.NET Core SignalR är `GetNegotiateEndpoint`http-kontexttypen för . För ASP.NET SignalR är det av [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) typ.

Nedan är det anpassade förhandla exempel för ASP.NET SignalR:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Glöm inte att registrera routern till DI-behållaren med:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Konfiguration i scenarier mellan regioner

Objektet `ServiceEndpoint` har `EndpointType` en egenskap `primary` `secondary`med värde eller .

`primary`Slutpunkter är att föredra slutpunkter för att ta emot klienttrafik och anses ha mer tillförlitliga nätverksanslutningar. `secondary` slutpunkter anses ha mindre tillförlitliga nätverksanslutningar och används endast för att ta server till klienttrafik, till exempel sändning av meddelanden, inte för att ta klient till servertrafik.

I fall mellan regioner kan nätverket vara instabilt. För en appserver i *östra USA*kan slutpunkten för SignalR-tjänsten i samma region i *östra USA* konfigureras som `primary` och slutpunkter i andra regioner som markerats som `secondary`. I den här konfigurationen kan tjänstslutpunkter i andra regioner **ta emot** meddelanden från den här östra **usa-appservern,** men det kommer inte att finnas några klienter mellan regioner dirigerade till den här appservern. *East US* Arkitekturen visas i diagrammet nedan:

![Cross-Geo Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

När en `/negotiate` klient försöker med appservern, med standardroutern, väljer SDK `primary` **slumpmässigt** en slutpunkt från uppsättningen tillgängliga slutpunkter. När den primära slutpunkten inte är tillgänglig väljer SDK `secondary` sedan **slumpmässigt** från alla tillgängliga slutpunkter. Slutpunkten markeras som **tillgänglig** när anslutningen mellan servern och tjänstslutpunkten är vid liv.

I scenario över flera regioner, `/negotiate` när en klient försöker med appservern i `primary` östra *USA*, som standard returnerar den alltid slutpunkten som finns i samma region. När alla slutpunkter i *östra USA* inte är tillgängliga omdirigeras klienten till slutpunkter i andra regioner. Avsnittet Över fail-over nedan beskriver scenariot i detalj.

![Normal Förhandla](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Växla över

När `primary` alla slutpunkter inte är tillgängliga `/negotiate` väljer klienten `secondary` från de tillgängliga slutpunkterna. Den här felväxlingsmekanismen kräver att `primary` varje slutpunkt ska fungera som slutpunkt till minst en appserver.

![Växla över](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du konfigurerar flera instanser i samma program för skalning, fragmentering och scenarier mellan regioner.

Flera slutpunkter stöder kan också användas i hög tillgänglighet och katastrofåterställning scenarier.

> [!div class="nextstepaction"]
> [Konfigurera SignalR-tjänst för haveriberedskap och hög tillgänglighet](./signalr-concept-disaster-recovery.md)

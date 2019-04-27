---
title: Så här skalar med flera instanser för Azure SignalR Service
description: I många scenarier för skalning måste kunden ofta att etablera flera instanser och konfigurera om du vill använda dem tillsammans för att skapa en storskalig distribution. Horisontell partitionering kräver till exempel stöd för flera instanser.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809186"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Så här skalar du SignalR Service med flera instanser?
Senaste SignalR Service SDK har stöd för flera slutpunkter för SignalR Service-instanser. Du kan använda den här funktionen för att skala samtidiga anslutningar eller använda det för meddelanden över flera regioner.

## <a name="for-aspnet-core"></a>För ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hur du lägger till flera slutpunkter från konfig?

Konfigurationen med nyckeln `Azure:SignalR:ConnectionString` eller `Azure:SignalR:ConnectionString:` för SignalR Service-anslutningssträngen.

Om nyckeln börjar med `Azure:SignalR:ConnectionString:`, den måste vara i formatet `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, där `Name` och `EndpointType` är egenskaper för den `ServiceEndpoint` objekt och är tillgängliga från koden.

Du kan lägga till flera anslutningssträngar för instansen med hjälp av följande `dotnet` kommandon:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hur du lägger till flera slutpunkter från kod?

En `ServicEndpoint` klass introduceras för att beskriva egenskaperna för en Azure SignalR Service-slutpunkt.
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

### <a name="how-to-customize-endpoint-router"></a>Hur du anpassar endpoint router?

Som standard använder SDK den [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) att hämta slutpunkter.

#### <a name="default-behavior"></a>Standardbeteende 
1. Routning av begäran i klienten

    När klienten `/negotiate` med app-servern. Som standard SDK **väljer slumpmässigt** en slutpunkt från uppsättningen med tillgängliga tjänstslutpunkter.

2. Server meddelanderoutning

    När * skicka meddelandet till en specifik ** anslutning *** och målanslutningen dirigeras till den aktuella servern, meddelandet går direkt till den anslutna slutpunkten. I annat fall är meddelanden skickade till varje Azure SignalR-slutpunkt.

#### <a name="customize-routing-algorithm"></a>Anpassa routning algoritmen
Du kan skapa egna router när du har särskilda kunskaper för att identifiera vilka slutpunkter som meddelanden ska gå att.

En anpassad router definieras nedan som exempel när grupper som börjar med `east-` alltid gå till den slutpunkt som heter `east`:

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

Ett annat exempel nedan, som åsidosätter standardinställningarna förhandla beteende, för att välja slutpunkterna som beror på där appservern finns.

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

Glöm inte att registrera routern till DI behållaren med hjälp av:

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

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hur du lägger till flera slutpunkter från konfig?

Konfigurationen med nyckeln `Azure:SignalR:ConnectionString` eller `Azure:SignalR:ConnectionString:` för SignalR Service-anslutningssträngen.

Om nyckeln börjar med `Azure:SignalR:ConnectionString:`, den måste vara i formatet `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, där `Name` och `EndpointType` är egenskaper för den `ServiceEndpoint` objekt och är tillgängliga från koden.

Du kan lägga till flera instans anslutningssträngar till `web.config`:

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hur du lägger till flera slutpunkter från kod?

En `ServicEndpoint` klass introduceras för att beskriva egenskaperna för en Azure SignalR Service-slutpunkt.
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

### <a name="how-to-customize-router"></a>Hur du anpassar router?

Den enda skillnaden mellan ASP.NET SignalR och ASP.NET Core SignalR är typen av http-kontexten för `GetNegotiateEndpoint`. För ASP.NET SignalR, är det av [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) typen.

Nedan visas anpassat förhandla exempel för ASP.NET SignalR:

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

Glöm inte att registrera routern till DI behållaren med hjälp av:

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

## <a name="configuration-in-cross-region-scenarios"></a>Konfigurationen i interregionala scenarier

Den `ServiceEndpoint` objekt har en `EndpointType` egenskapen med värdet `primary` eller `secondary`.

`primary` slutpunkter är prioriterade slutpunkter att ta emot klienttrafik och anses ha mer tillförlitliga nätverksanslutningar; `secondary` slutpunkter anses ha mindre tillförlitliga nätverksanslutningar och används endast för servern för att möjliggöra att klienttrafik, till exempel sänder meddelanden, inte för att möjliggöra klienten servertrafik.

I interregionala fall kan nätverket vara instabil. För en applikationsserver i *USA, östra*, SignalR Service-slutpunkt finns i samma *USA, östra* region kan konfigureras som `primary` och slutpunkter i andra regioner markeras som `secondary`. I den här konfigurationen tjänstslutpunkter i andra regioner kan **får** meddelanden från den här *USA, östra* app-servern, men det kommer att inga **interregionala** klienter dirigeras till den här appservern. Arkitekturen visas i diagrammet nedan:

![Cross-Geo Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

När en klient försöker `/negotiate` med appservern med standardrouter, SDK **väljer slumpmässigt** en slutpunkt från uppsättningen med tillgängliga `primary` slutpunkter. När slutpunkten är tillgänglig, SDK sedan **väljer slumpmässigt** från alla tillgängliga `secondary` slutpunkter. Slutpunkten är markerad som **tillgängliga** när anslutningen mellan servern och tjänstslutpunkten är aktiv.

I interregionala scenariot när en klient försöker `/negotiate` med appservern finns i *USA, östra*, genom standard den alltid returnerar de `primary` slutpunkt finns i samma region. När alla *USA, östra* slutpunkter är inte tillgängliga, klienten ska omdirigeras till slutpunkter i andra regioner. Round nedan beskriver scenariot i detalj.

![Normal Negotiate](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Redundansväxling

När alla `primary` slutpunkter är inte tillgängliga, klientens `/negotiate` hämtar från de tillgängliga `secondary` slutpunkter. Den här mekanismen för redundansväxling kräver att varje slutpunkt ska fungera som `primary` slutpunkten till minst en app-servern.

![Redundansväxling](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Nästa steg

I den här guiden kommer du lärt dig om hur du konfigurerar flera instanser i samma program för skalning, horisontell partitionering och interregionala scenarier.

Har stöd för flera slutpunkter kan också användas i scenarier för hög tillgänglighet och katastrofåterställning.

> [!div class="nextstepaction"]
> [Konfigurera SignalR Service för haveriberedskap och hög tillgänglighet](./signalr-concept-disaster-recovery.md)

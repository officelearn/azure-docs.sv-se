---
title: Kommunikation med ASP.NET Core-tjänst | Microsoft Docs
description: Lär dig hur du använder ASP.NET Core tillståndslösa och tillståndskänsliga Reliable Services.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939783"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core i Azure Service Fabric Reliable Services

ASP.NET Core är ett ramverk med öppen källkod och plattformsoberoende. Det här ramverket är utformat för att skapa molnbaserade, internet-anslutna program, till exempel webbappar, IoT-appar och mobila serverdelar.

Den här artikeln är utförliga instruktioner för värdtjänster ASP.NET Core i Service Fabric Reliable Services med hjälp av den **Microsoft.ServiceFabric.AspNetCore.** uppsättning NuGet-paket.

En inledande självstudiekurs om ASP.NET Core i Service Fabric och anvisningar om utvecklingsmiljön konfigurera finns i [självstudien: Skapa och distribuera ett program med en ASP.NET Core Web API-klientdelstjänst och en tillståndskänslig backend-tjänst](service-fabric-tutorial-create-dotnet-app.md).

Resten av den här artikeln förutsätter att du redan är bekant med ASP.NET Core. Om inte, Läs igenom den [grunderna i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core i Service Fabric-miljö

Både ASP.NET Core och Service Fabric-appar kan köras på .NET Core eller fullständiga .NET Framework. Du kan använda ASP.NET Core på två olika sätt i Service Fabric:
 - **Värdbaserad som en körbar gäst**. Det här sättet används främst för att köra befintliga ASP.NET Core-program i Service Fabric utan kodändringar.
 - **Körs i en tillförlitlig tjänst**. På så sätt kan bättre integrering med Service Fabric-körningen och tillståndskänslig ASP.NET Core services.

Resten av den här artikeln beskriver hur du använder ASP.NET Core i en tillförlitlig tjänst genom komponenterna för ASP.NET Core-integrering som levereras med Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Service Fabric som är värd för tjänsten

I Service Fabric kan köra en eller flera instanser och/eller repliker av din tjänst i en *tjänsten värdprocess*: en körbar fil som kör koden för tjänsten. Du, som en tjänst författare äger värdprocessen service och Service Fabric aktiveras och övervakar det åt dig.

Traditionella ASP.NET (upp till MVC 5) är direkt kopplade till IIS via System.Web.dll. ASP.NET Core ger en åtskillnad mellan webbservern och ditt webbprogram. Den här separationen kan webbprogram kan flyttas mellan olika servrar. Det gör också till att *egenvärdbaserade*. Det innebär att du kan starta en webbserver i en egen process, till skillnad från en process som ägs av dedikerade web server-programvara, till exempel IIS.

Om du vill kombinera ett Service Fabric-tjänst och ASP.NET, antingen som en körbar Gäst eller i en tillförlitlig tjänst måste du kunna starta ASP.NET i din tjänst värdprocess. ASP.NET Core som egen värd kan du göra detta.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Värd för ASP.NET Core i en tillförlitlig tjänst
Vanligtvis egenhanterad ASP.NET Core-program skapar en WebHost i startpunkten för ett program, till exempel den `static void Main()` -metod i `Program.cs`. I det här fallet är WebHost livscykel bunden till processen livscykel.

![Värd för ASP.NET Core i en process][0]

Men startpunkt för programmet är inte rätt ställe för att skapa en WebHost i en tillförlitlig tjänst. Det beror på att programmet startpunkt bara används för att registrera en tjänsttyp med Service Fabric-körningen så att den kan skapa instanser av den typ av tjänst. WebHost ska skapas i en tillförlitlig tjänst själva. Instanser av tjänsten och/eller repliker kan gå igenom flera supportlivscykel inom värdprocess service. 

En tillförlitlig tjänstinstans representeras av din tjänstklass som härleds från `StatelessService` eller `StatefulService`. Kommunikationsstack för en tjänst finns i en `ICommunicationListener` implementering i din tjänstklass. Den `Microsoft.ServiceFabric.AspNetCore.*` NuGet-paket som innehåller implementeringar av `ICommunicationListener` som starta och hantera ASP.NET Core WebHost för Kestrel eller HTTP.sys i en tillförlitlig tjänst.

![Diagram som värd för ASP.NET Core i en tillförlitlig tjänst][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
Den `ICommunicationListener` implementeringar för Kestrel och HTTP.sys i den `Microsoft.ServiceFabric.AspNetCore.*` NuGet-paket har liknande användningsmönster. Men de utföra något annorlunda åtgärder som är specifika för varje webbserver. 

Båda kommunikationslyssnarna innehåller en konstruktor som använder följande argument:
 - **`ServiceContext serviceContext`**: Det här är den `ServiceContext` objekt som innehåller information om tjänsten som körs.
 - **`string endpointName`**: Det här är namnet på en `Endpoint` konfigurationen i ServiceManifest.xml. Det är främst där två kommunikationslyssnarna skiljer sig åt. HTTP.sys *kräver* en `Endpoint` konfiguration, medan Kestrel inte.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Det här är en lambda som du implementerar i som du skapar och returnera ett `IWebHost`. Du kan konfigurera `IWebHost` på sätt som du vanligtvis använder i ett ASP.NET Core-program. Lambda får en URL som har genererats åt dig, beroende på vilka alternativ för Service Fabric-integrering som du använder och `Endpoint` konfiguration som du anger. Du kan ändra eller Använd URL: en för att starta webbservern.

## <a name="service-fabric-integration-middleware"></a>Mellanprogram för Service Fabric-integrering
Den `Microsoft.ServiceFabric.AspNetCore` NuGet-paketet innehåller den `UseServiceFabricIntegration` tilläggsmetod på `IWebHostBuilder` som lägger till Service Fabric-medvetna mellanprogram. Den här mellanprogram konfigurerar Kestrel eller HTTP.sys `ICommunicationListener` att registrera en unik tjänst-URL med Service Fabric Naming-tjänst. Sedan kontrolleras klientbegäranden för att se till att klienter ansluter till en tjänst. 

Det här steget är nödvändigt att förhindra att klienter ansluter av misstag till fel-tjänsten. Det beror i en delad-host-miljö, till exempel Service Fabric, flera webbprogram kan köras på samma fysiska eller virtuella datorn men Använd inte unika värdnamn. Det här scenariot beskrivs närmare i nästa avsnitt.

### <a name="a-case-of-mistaken-identity"></a>Ett ärende med felaktiga identitet
Tjänsten repliker, oavsett protokoll, lyssna på en unik IP:port kombination. När en replik för tjänsten har startats lyssnar på en slutpunkt för IP:port rapporterar den slutpunktsadressen till Service Fabric Naming-tjänst. Det, kan klienter eller andra tjänster identifiera den. Om tjänster använder dynamiskt tilldelade programportar, kan en tjänsterepliken tillfälligtvis använda samma IP:port slutpunkten för en annan tjänst tidigare på samma fysiska eller virtuella datorn. Detta kan orsaka en klient ansluta av misstag till fel-tjänsten. Det här scenariot kan bero på följande sekvens av händelser inträffar:

 1. Tjänsten A lyssnar på 10.0.0.1:30000 via HTTP. 
 2. Klienten matchar tjänst A och hämtar adress 10.0.0.1:30000.
 3. Tjänsten A flyttar till en annan nod.
 4. Tjänsten B är placerad på 10.0.0.1 och använder tillfälligtvis samma port 30000.
 5. Klienten försöker ansluta till tjänsten A med cachelagrade adress 10.0.0.1:30000.
 6. Klienten är nu ansluten till tjänsten B är inte märker det. är anslutna till tjänsten fel.

Detta kan orsaka fel vid slumpmässigt tidpunkter som kan vara svårt att diagnostisera.

### <a name="using-unique-service-urls"></a>Med hjälp av unik tjänst-URL: er
För att förhindra att de här felen services publicera en slutpunkt på Naming Service med en unik identifierare och sedan validera den unika identifieraren under klientbegäranden. Det här är en samordnad åtgärd mellan tjänster i en betrodd miljö för icke-fientlig-klient. Det ger inte säker tjänst-autentisering i en miljö fientlig organisationer.

I en betrodd miljö, mellanprogrammet som läggs till av den `UseServiceFabricIntegration` metoden lägger automatiskt till en unik identifierare till den adress som skickas till tjänsten Naming. Kontrollera att identifieraren för varje begäran. Om det ID: t inte matchar returnerar mellanprogrammet omedelbart ett svar med HTTP 410 sidan finns inte.

Tjänster som använder en dynamiskt tilldelad port bör se användning av den här mellanprogram.

Tjänster som använder en fast unik port har inte det här problemet i en integrerad miljö. En fast unik port används vanligtvis för utåtriktade tjänster som behöver en känd port för klientprogram att ansluta till. Till exempel använder de flesta internet-riktade webbprogram port 80 eller 443 för web browser anslutningar. I det här fallet bör inte den unika identifieraren aktiveras.

Följande diagram visar begärandeflödet med mellanprogram aktiverat:

![Service Fabric ASP.NET Core-integrering][2]

Både Kestrel och HTTP.sys `ICommunicationListener` implementeringar använder den här mekanismen i exakt samma sätt. Även om HTTP.sys internt kan skilja förfrågningar baserat på unika URL-sökvägar med hjälp av den underliggande **HTTP.sys** port funktionen, som fungerar för delning *inte* används av HTTP.sys `ICommunicationListener`implementering. Det beror på att det resulterar i HTTP 503-fel och HTTP 404-fel statuskoder i scenariot ovan. Som i sin tur gör det svårt för klienter för att fastställa syftet med felet, som HTTP 503-fel och HTTP 404 brukar användas för att indikera andra fel. 

Därför både Kestrel och HTTP.sys `ICommunicationListener` implementeringar som standard mellanprogram som tillhandahålls av den `UseServiceFabricIntegration` tilläggsmetod. Klienter behöver därför bara att utföra en service endpoint igen lösa åtgärd på 410 för HTTP-svar.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys Reliable Services
Du kan använda HTTP.sys Reliable Services genom att importera den **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet-paketet. Det här paketet innehåller `HttpSysCommunicationListener`, en implementering av `ICommunicationListener`. `HttpSysCommunicationListener` kan du skapa en ASP.NET Core WebHost i en tillförlitlig tjänst med hjälp av HTTP.sys som webbservern.

HTTP.sys bygger på den [Windows http-Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Den här API: N används den **HTTP.sys** kerneldrivrutinen att bearbeta begäranden för HTTP och dirigera dem till processer som körs webbprogram. På så sätt kan flera processer på samma fysiska eller virtuella datorn för värdbaserade webbprogram på samma port skiljas åt med antingen en unik URL-sökväg eller värdnamn. De här funktionerna är användbara i Service Fabric som värd för flera webbplatser i samma kluster.

>[!NOTE]
>HTTP.sys-implementering fungerar endast på Windows-plattformen.

Följande diagram illustrerar hur HTTP.sys använder den **HTTP.sys** kerneldrivrutinen på Windows för delning av port:

![HTTP.sys-diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys i en tillståndslös tjänst
Att använda `HttpSys` i en tillståndslös tjänst åsidosätta den `CreateServiceInstanceListeners` metoden och returnera en `HttpSysCommunicationListener` instans:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys i en tillståndskänslig tjänst

`HttpSysCommunicationListener` för närvarande är inte avsedd för användning i tillståndskänsliga tjänster på grund av problem med den underliggande **HTTP.sys** port funktionen för delning. Mer information finns i följande avsnitt på dynamisk porttilldelning med HTTP.sys. För tillståndskänsliga tjänster är Kestrel föreslagna webbservern.

### <a name="endpoint-configuration"></a>Konfiguration av tjänstslutpunkt

En `Endpoint` krävs konfiguration för webbservrar som använder Windows http-Server API: et, inklusive HTTP.sys. Webbservrar som använder API: et Windows http-Server måste först reservera URL-Adressen med HTTP.sys (detta görs normalt med den [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) verktyget). 

Den här åtgärden kräver förhöjd behörighet som dina tjänster inte har som standard. ”Http” eller ”https” alternativ för den `Protocol` egenskapen för den `Endpoint` konfigurationen i ServiceManifest.xml används specifikt för att instruera Service Fabric-körning för att registrera en URL med HTTP.sys för din räkning. Detta sker med hjälp av den [ *starkt jokertecken* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL-prefix.

Till exempel för att reservera `http://+:80` för en tjänst, Använd följande konfiguration i ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Och namnet på slutpunkten måste skickas till den `HttpSysCommunicationListener` konstruktorn:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Använda HTTP.sys med en statisk port
Om du vill använda en statisk port med HTTP.sys, ange portnumret i den `Endpoint` konfiguration:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Använda HTTP.sys med en dynamisk port
Om du vill använda en dynamiskt tilldelad port med HTTP.sys utelämna den `Port` -egenskapen i den `Endpoint` konfiguration:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

En dynamisk port som allokerats av en `Endpoint` konfigurationen ger endast en port *per värdprocess*. Den aktuella Service Fabric-värdmodellen kan flera instanser av tjänsten och/eller repliker ska finnas i samma process. Det innebär att var och en delar samma port när tilldelade rakt igenom den `Endpoint` konfiguration. Flera **HTTP.sys** instanser kan dela en port med hjälp av den underliggande **HTTP.sys** port funktionen för delning. Men det stöds inte av `HttpSysCommunicationListener` på grund av komplikationer införs för klientbegäranden. För användning av dynamisk port är Kestrel föreslagna webbservern.

## <a name="kestrel-in-reliable-services"></a>Kestrel Reliable Services
Du kan använda Kestrel i Reliable Services genom att importera den **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet-paketet. Det här paketet innehåller `KestrelCommunicationListener`, en implementering av `ICommunicationListener`. `KestrelCommunicationListener` kan du skapa en ASP.NET Core WebHost i en tillförlitlig tjänst med hjälp av Kestrel som webbservern.

Kestrel är en plattformsoberoende webbserver för ASP.NET Core utifrån libuv, ett plattformsoberoende asynkrona i/o-bibliotek. Till skillnad från HTTP.sys använder Kestrel inte en centraliserad endpoint-hanteraren. Till skillnad från HTTP.sys stöder Kestrel inte heller portdelning mellan flera processer. Varje instans av Kestrel måste använda en unik port.

![Kestrel diagram][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel i en tillståndslös tjänst
Att använda `Kestrel` i en tillståndslös tjänst åsidosätta den `CreateServiceInstanceListeners` metoden och returnera en `KestrelCommunicationListener` instans:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel i en tillståndskänslig tjänst
Att använda `Kestrel` i en tillståndskänslig tjänst åsidosätta den `CreateServiceReplicaListeners` metoden och returnera en `KestrelCommunicationListener` instans:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

I det här exemplet är en singleton-instans av `IReliableStateManager` har angetts till behållaren WebHost beroende inmatning. Det här inte är absolut nödvändigt, men du kan använda `IReliableStateManager` och tillförlitliga samlingar i åtgärden för MVC kontrollantmetoder.

En `Endpoint` namn *inte* till `KestrelCommunicationListener` i en tillståndskänslig tjänst. Detta förklaras i detalj i följande avsnitt.

### <a name="configure-kestrel-to-use-https"></a>Konfigurera Kestrel för användning av HTTPS
När du aktiverar HTTPS med Kestrel i din tjänst måste du ange lyssnar på flera sätt. Uppdatera den `ServiceInstanceListener` att använda en *EndpointHttps* slutpunkt och lyssna på en viss port (till exempel port 443). När du konfigurerar webbvärd för att använda Kestrel webbserver, måste du konfigurera Kestrel för att lyssna efter IPv6-adresser på alla nätverksgränssnitt: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Ett fullständigt exempel i en självstudiekurs finns [konfigurera Kestrel att använda HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfiguration av tjänstslutpunkt
En `Endpoint` konfiguration är inte nödvändigt att använda Kestrel. 

Kestrel är en enkel fristående server. Till skillnad från HTTP.sys (eller HttpListener) behöver inte en `Endpoint` konfigurationen i ServiceManifest.xml eftersom det ingen krävs registrering av URL: en innan du börjar. 

#### <a name="use-kestrel-with-a-static-port"></a>Använda Kestrel med en statisk port
Du kan konfigurera en statisk port i den `Endpoint` konfigurationen av ServiceManifest.xml för användning med Kestrel. Även om detta inte är absolut nödvändigt, erbjuder två potentiella fördelar:
 - Om porten inte faller inom programportsintervallet, har filen öppnats genom brandväggen OS av Service Fabric.
 - URL: en som tillhandahålls via `KestrelCommunicationListener` kommer att använda den här porten.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Om en `Endpoint` är konfigurerad, dess namn måste skickas till den `KestrelCommunicationListener` konstruktorn: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Om ServiceManifest.xml inte använder en `Endpoint` konfiguration, utelämnar namn i den `KestrelCommunicationListener` konstruktor. I det här fallet använder en dynamisk port. Se nästa avsnitt för mer information om detta.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Använda Kestrel med en dynamisk port
Kestrel kan inte använda automatisk porttilldelning från den `Endpoint` konfigurationen i ServiceManifest.xml. Det beror på automatisk port tilldelning från en `Endpoint` konfigurationen tilldelar en unik port per *värd processen*, och en enda värdprocess kan innehålla flera Kestrel instanser. Detta fungerar inte med Kestrel eftersom den inte stöder delning av port. Varje instans Kestrel måste därför vara öppna på en unik port.

Om du vill använda dynamisk porttilldelning med Kestrel utelämna den `Endpoint` konfigurationen i ServiceManifest.xml helt och hållet, och inte skicka ett slutpunktnamn för att den `KestrelCommunicationListener` konstruktorn på följande sätt:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

I den här konfigurationen `KestrelCommunicationListener` väljer automatiskt en ledig port från portintervallet för programmet.

## <a name="service-fabric-configuration-provider"></a>Konfigurationsprovidern för Service Fabric
Konfiguration av i ASP.NET Core är baserad på nyckel / värde-par som upprättats av konfigurationsprovidern. Läs [konfigurationen i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) att förstå mer om allmänna ASP.NET Core stöd för konfiguration.

Det här avsnittet beskrivs hur Service Fabric-konfigurationsprovider kan integreras med ASP.NET Core-konfigurationen genom att importera den `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-paketet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration Start-tillägg
När du har importerat den `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-paketet som du behöver registrera konfigurationskälla för Service Fabric med ASP.NET Core konfigurations-API. Du gör detta genom att kontrollera **AddServiceFabricConfiguration** tillägg i den `Microsoft.ServiceFabric.AspNetCore.Configuration` namnområde mot `IConfigurationBuilder`.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

ASP.NET Core-tjänst kan nu komma åt Service Fabric-konfigurationsinställningar, precis som andra programinställningar. Du kan till exempel använda mönstret alternativ för att läsa in inställningarna till starkt typifierad objekt.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Viktiga standardmappningen
Som standard innehåller Service Fabric-konfigurationsprovider paketnamn, avsnittsnamn och egenskapsnamnet. Tillsammans utgör de konfigurationsnyckeln ASP.NET Core på följande sätt:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Exempel: Om du har ett konfigurationspaket med namnet `MyConfigPackage` med följande innehåll Konfigurationsvärdet blir tillgängligt på ASP.NET Core `IConfiguration` via *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric-konfigurationsalternativ
Konfigurationsprovidern för Service Fabric stöder också `ServiceFabricConfigurationOptions` ändra standardbeteendet för mappning av nyckeln.

#### <a name="encrypted-settings"></a>Krypterade inställningar
Service Fabric stöder krypterade inställningar som gör det Service Fabric-konfigurationstjänst. Krypterade inställningar inte dekrypteras till ASP.NET Core `IConfiguration` som standard. Krypterade värden lagras det i stället. Men om du vill dekryptera värdet som ska lagra i ASP.NET Core IConfiguration kan du ange den *DecryptValue* flaggan till false i den `AddServiceFabricConfiguration` tillägg, enligt följande:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Flera konfigurationspaket
Service Fabric stöder flera konfigurationspaket. Som standard ingår paketnamnet i konfigurationsnyckeln. Men du kan ange den `IncludePackageName` flaggan till false, enligt följande:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mappning av anpassade nyckel och värde extrahering ifyllnad av data
Konfigurationsprovidern för Service Fabric stöder också mer avancerade scenarier för att anpassa viktiga mappningen med `ExtractKeyFunc` och extrahera värden med anpassade `ExtractValueFunc`. Du kan även ändra hela processen för att fylla i data från Service Fabric-konfigurationen till ASP.NET Core-konfigurationen med hjälp av `ConfigAction`.

I följande exempel visas hur du använder `ConfigAction` att anpassa ifyllnad av data:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Uppdateringar för
Konfigurationsprovidern för Service Fabric stöder också konfigurationsuppdateringar. Du kan använda ASP.NET Core `IOptionsMonitor` att ta emot ändringsmeddelanden och sedan använda `IOptionsSnapshot` läses in på nytt konfigurationsdata. Mer information finns i [ASP.NET Core alternativ](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Dessa alternativ stöds som standard. Ingen ytterligare kodning krävs för att aktivera uppdateringar.

## <a name="scenarios-and-configurations"></a>Scenarier och konfigurationer
Det här avsnittet innehåller en kombination av webbserver, portkonfiguration, Service Fabric-integreringsalternativ och övriga inställningar som vi rekommenderar för att felsöka följande scenarier:
 - Externt exponeras tillståndslös ASP.NET Core-tjänster
 - Endast internt ASP.NET Core tillståndslösa tjänster
 - Endast internt ASP.NET Core tillståndskänsliga tjänster

En **externt exponeras service** är ett program som Exponerar en slutpunkt som anropas från utanför klustret, vanligtvis via en belastningsutjämnare.

En **interna** service är en vars slutpunkten är endast anropas från klustret.

> [!NOTE]
> Tillståndskänsliga Tjänsteslutpunkter bör inte Allmänt exponeras för internet. Kluster bakom belastningsutjämnare som inte känner till Service Fabric service lösning, till exempel Azure Load Balancer kommer inte att exponera tillståndskänsliga tjänster. Det beror på att belastningsutjämnaren kan inte att hitta och dirigera trafik till lämplig tillståndskänslig tjänst-repliken. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externt exponeras tillståndslös ASP.NET Core-tjänster
Kestrel är föreslagna webbservern för frontend-tjänster som visar extern, internet-ansluten HTTP-slutpunkter. På Windows får HTTP.sys dela port-funktion, vilket kan du ha flera webbtjänster på samma uppsättning noder som använder samma port. I det här scenariot åtskiljs webbtjänsterna genom värdnamn eller sökväg, utan att behöva en frontend proxy eller gateway för att tillhandahålla routning av HTTP.
 
När du är exponerade för internet, bör en tillståndslös tjänst använda en välkänd och stabil slutpunkt som kan nås via en belastningsutjämnare. Du ger den här URL: en till ditt programs användare. Vi rekommenderar följande konfiguration:

|  |  | **Anteckningar** |
| --- | --- | --- |
| Webbserver | Kestrel | Kestrel är den primära webbservern, eftersom den stöds i Windows och Linux. |
| Portkonfiguration | statisk | En känd statisk port ska ställas in på den `Endpoints` konfiguration av ServiceManifest.xml, till exempel 80 för HTTP och port 443 för HTTPS. |
| ServiceFabricIntegrationOptions | Ingen | Använd den `ServiceFabricIntegrationOptions.None` alternativ när du konfigurerar Service Fabric-integrering middleware, så att tjänsten inte försöker verifiera inkommande förfrågningar för en unik identifierare. Externa användare av ditt program vet inte unika identifieringsinformation som använder mellanprogrammet. |
| Antal instanser | -1 | I vanliga användningsfall instansantalet inställningen ska vara inställd på *-1*. Det gör du så att en instans är tillgänglig på alla noder som tar emot trafik från en belastningsutjämnare. |

Om flera tjänster som externt exponeras delar samma uppsättning noder kan använda du HTTP.sys med ett unikt men stabil URL-sökväg. Du kan göra detta genom att modifiera den URL som anges när du konfigurerar IWebHost. Observera att detta endast gäller HTTP.sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Endast internt tillståndslös ASP.NET Core-tjänst
Tillståndslösa tjänster som endast anropas från i klustret ska använda unika URL: er och dynamiskt tilldelade portar för att säkerställa samarbete mellan flera tjänster. Vi rekommenderar följande konfiguration:

|  |  | **Anteckningar** |
| --- | --- | --- |
| Webbserver | Kestrel | Men du kan använda HTTP.sys för interna tillståndslösa tjänster, är Kestrel bästa servern så att flera instanser av tjänsten att dela en värd.  |
| Portkonfiguration | dynamiskt tilldelade | Flera kopior av en tillståndskänslig tjänst kan dela en värdprocess eller värdoperativsystemet och därför måste unika portar. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Den här inställningen förhindrar felaktiga identitet problemet som beskrivs ovan med dynamisk porttilldelning. |
| InstanceCount | valfri | Ange instansantalet kan ställas in till ett värde krävs för att driva tjänsten. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Endast internt tillståndskänslig ASP.NET Core-tjänst
Tillståndskänsliga tjänster som endast anropas från i klustret ska använda dynamiskt tilldelade portar för samarbete mellan flera tjänster. Vi rekommenderar följande konfiguration:

|  |  | **Anteckningar** |
| --- | --- | --- |
| Webbserver | Kestrel | Den `HttpSysCommunicationListener` är inte avsedd för användning av tillståndskänsliga tjänster där repliker dela en värdprocess. |
| Portkonfiguration | dynamiskt tilldelade | Flera kopior av en tillståndskänslig tjänst kan dela en värdprocess eller värdoperativsystemet och därför måste unika portar. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Den här inställningen förhindrar felaktiga identitet problemet som beskrivs ovan med dynamisk porttilldelning. |

## <a name="next-steps"></a>Nästa steg
[Felsöka ditt Service Fabric-program med Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png

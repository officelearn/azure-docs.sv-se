---
title: Servicekommunikation med ASP.NET Core
description: Lär dig hur du använder ASP.NET Core i tillståndslösa och tillståndskänsliga Azure Service Fabric Reliable Services-program.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639640"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core i Azure Service Fabric Reliable Services

ASP.NET Core är ett ramverk med öppen källkod och plattformsoberoende. Det här ramverket är utformat för att skapa molnbaserade, internetanslutna program, till exempel webbappar, IoT-appar och mobila bakåtslutningar.

Den här artikeln är en djupgående guide till ASP.NET Core-tjänster i Service Fabric Reliable Services med hjälp av **Microsoft.ServiceFabric.AspNetCore.** uppsättning NuGet-paket.

En inledande självstudiekurs om ASP.NET Core i Service Fabric och instruktioner om hur du konfigurerar utvecklingsmiljön finns i [Självstudiekurs: Skapa och distribuera ett program med en ASP.NET frontend-tjänst för Core Web API och en tillståndskänslig backend-tjänst](service-fabric-tutorial-create-dotnet-app.md).

Resten av den här artikeln förutsätter att du redan är bekant med ASP.NET Core. Om inte, läs igenom [grunderna i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core i serviceinfrastrukturmiljön

Både ASP.NET Core- och Service Fabric-appar kan köras på .NET Core eller full .NET Framework. Du kan använda ASP.NET Core på två olika sätt i Service Fabric:
 - **Värden som körbar gäst.** På så sätt används främst för att köra befintliga ASP.NET Core-program på Service Fabric utan kodändringar.
 - **Kör in i en pålitlig tjänst.** På så sätt kan bättre integration med Service Fabric-körningen och möjliggör tillståndskänsliga ASP.NET Core-tjänster.

Resten av den här artikeln förklarar hur du använder ASP.NET Core inuti en tillförlitlig tjänst, via de ASP.NET Core-integrationskomponenter som levereras med Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Service Fabric-tjänstvärd

I Service Fabric körs en eller flera instanser och/eller repliker av tjänsten i en *tjänstvärdprocess:* en körbar fil som kör din tjänstkod. Du som tjänstförfattare äger tjänstvärdprocessen och Service Fabric aktiverar och övervakar den åt dig.

Traditionella ASP.NET (upp till MVC 5) är tätt kopplad till IIS via System.Web.dll. ASP.NET Core ger en separation mellan webbservern och ditt webbprogram. Denna separation gör att webbapplikationer kan vara bärbara mellan olika webbservrar. Det gör också att webbservrar att vara *självvärd.* Det innebär att du kan starta en webbserver i din egen process, i motsats till en process som ägs av dedikerad webbserverprogramvara, till exempel IIS.

Om du vill kombinera en Service Fabric-tjänst och ASP.NET, antingen som gästkörbar eller i en tillförlitlig tjänst, måste du kunna starta ASP.NET i din servicevärdprocess. ASP.NET Core självhosting kan du göra detta.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting ASP.NET Core i en pålitlig tjänst
Vanligtvis skapar självvärdbaserade ASP.NET Core-program ett webbhotell i ett programs startpunkt, `static void Main()` till `Program.cs`exempel metoden i . I det här fallet är livscykeln för WebHost bunden till livscykeln för processen.

![Hosting ASP.NET Core i en process][0]

Men ansökan startpunkten är inte rätt plats att skapa en WebHost i en pålitlig tjänst. Det beror på att programmets startpunkt endast används för att registrera en tjänsttyp med Service Fabric-körningen, så att den kan skapa instanser av den tjänsttypen. WebHost bör skapas i en tillförlitlig tjänst själv. Inom tjänstvärdprocessen kan tjänstinstanser och/eller repliker gå igenom flera livscykelr. 

En reliable service-instans representeras av din `StatelessService` serviceklass som härstammar från eller `StatefulService`. Kommunikationsstacken för en tjänst finns `ICommunicationListener` i en implementering i din serviceklass. NuGet-paketen `Microsoft.ServiceFabric.AspNetCore.*` innehåller implementeringar av `ICommunicationListener` den starten och hantera ASP.NET Core WebHost för antingen Kestrel eller HTTP.sys i en tillförlitlig tjänst.

![Diagram för ASP.NET Core i en pålitlig tjänst][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
Implementeringarna `ICommunicationListener` för Kestrel och HTTP.sys i NuGet-paketen `Microsoft.ServiceFabric.AspNetCore.*` har liknande användningsmönster. Men de utför lite olika åtgärder som är specifika för varje webbserver. 

Båda kommunikationsavlyssnare ger en konstruktör som tar följande argument:
 - **`ServiceContext serviceContext`**: Det `ServiceContext` här är objektet som innehåller information om den tjänst som körs.
 - **`string endpointName`**: Det här är `Endpoint` namnet på en konfiguration i ServiceManifest.xml. Det är främst där de två kommunikationslyssnare skiljer sig åt. HTTP.sys *requires* kräver `Endpoint` en konfiguration, medan Kestrel inte gör det.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Detta är en lambda som du genomför, `IWebHost`där du skapar och returnerar en . Det låter dig `IWebHost` konfigurera hur du normalt skulle i en ASP.NET Core-program. Lambda innehåller en URL som genereras för dig, beroende på vilka `Endpoint` integrationsalternativ för Service Fabric du använder och vilken konfiguration du anger. Du kan sedan ändra eller använda webbadressen för att starta webbservern.

## <a name="service-fabric-integration-middleware"></a>Mellanprogram för integrering av serviceinfrastruktur
`Microsoft.ServiceFabric.AspNetCore` NuGet-paketet innehåller `UseServiceFabricIntegration` tilläggsmetoden på `IWebHostBuilder` den som lägger till Service Fabric-medvetna mellanprogram. Detta mellanprogram konfigurerar Kestrel eller `ICommunicationListener` HTTP.sys för att registrera en unik tjänst-URL med tjänsten Fabric Naming Service. Den validerar sedan klientbegäranden för att säkerställa att klienter ansluter till rätt tjänst. 

Det här steget är nödvändigt för att förhindra att klienter av misstag ansluter till fel tjänst. Det beror på att i en miljö med delad värd, till exempel Service Fabric, kan flera webbprogram köras på samma fysiska eller virtuella dator men inte använda unika värdnamn. Det här scenariot beskrivs mer i detalj i nästa avsnitt.

### <a name="a-case-of-mistaken-identity"></a>Ett fall av felaktig identitet
Tjänstrepliker, oavsett protokoll, lyssnar på en unik IP:port-kombination. När en tjänstreplik har börjat lyssna på en IP:port-slutpunkt rapporterar den slutpunktsadressen till tjänst fabric-namngivningstjänsten. Där kan kunder eller andra tjänster upptäcka det. Om tjänster använder dynamiskt tilldelade programportar kan en tjänstreplik av en tillfällighet använda samma IP:port-slutpunkt för en annan tjänst som tidigare finns på samma fysiska eller virtuella dator. Detta kan leda till att en klient av misstag ansluter till fel tjänst. Det här scenariot kan uppstå om följande händelseförlopp inträffar:

 1. Tjänst A lyssnar på 10.0.0.1:30000 över HTTP. 
 2. Klienten löser Service A och får adress 10.0.0.1:30000.
 3. Tjänst A flyttas till en annan nod.
 4. Service B placeras på 10.0.0.1 och använder av samma port 30000 av samma port.
 5. Klienten försöker ansluta till tjänst A med cachelagrade adress 10.0.0.1:30000.
 6. Klienten är nu ansluten till tjänst B, utan att inse att den är ansluten till fel tjänst.

Detta kan orsaka buggar slumpmässiga tider som kan vara svåra att diagnostisera.

### <a name="using-unique-service-urls"></a>Använda unika tjänstadresser
För att förhindra dessa fel kan tjänster bokföra en slutpunkt till namngivningstjänsten med en unik identifierare och sedan validera den unika identifieraren under klientbegäranden. Detta är en samarbetsåtgärd mellan tjänster i en icke-fientlig klient betrodd miljö. Det ger inte säker tjänstautentisering i en fientlig klientmiljö.

I en betrodd miljö lägger mellanprogramet `UseServiceFabricIntegration` som läggs till med metoden automatiskt en unik identifierare till adressen som publiceras på namngivningstjänsten. Den validerar den identifieraren på varje begäran. Om identifieraren inte matchar returnerar mellanprogramet omedelbart ett HTTP 410 Gone-svar.

Tjänster som använder en dynamiskt tilldelad port bör använda sig av denna mellanprogram.

Tjänster som använder en fast unik port har inte det här problemet i en kooperativ miljö. En fast unik port används vanligtvis för externt införnde av tjänster som behöver en välkänd port för klientprogram att ansluta till. De flesta internetinriktade webbapplikationer använder till exempel port 80 eller 443 för webbläsaranslutningar. I det här fallet bör den unika identifieraren inte aktiveras.

I följande diagram visas begäran flödet med mellanprogram aktiverat:

![Integrering av serviceinfrastruktur ASP.NET kärnintegration][2]

Både Kestrel och `ICommunicationListener` HTTP.sys implementationer använder denna mekanism på exakt samma sätt. Även om HTTP.sys internt kan skilja begäranden baserat på unika URL-sökvägar med hjälp *not* av den underliggande `ICommunicationListener` http.sys-portdelningsfunktionen, används inte den funktionen av HTTP.sys-implementeringen. **HTTP.sys** Det beror på att det resulterar i HTTP 503 och HTTP 404 felstatuskoder i det scenario som beskrivs tidigare. Det i sin tur gör det svårt för klienter att avgöra syftet med felet, eftersom HTTP 503 och HTTP 404 ofta används för att indikera andra fel. 

Således standardiserar både Kestrel- och `ICommunicationListener` HTTP.sys-implementeringar `UseServiceFabricIntegration` på mellanprogram som tillhandahålls av tilläggsmetoden. Klienterna behöver därför bara utföra en tjänstslutpunkt för att lösa åtgärden igen för HTTP 410-svar.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys i tillförlitliga tjänster
Du kan använda HTTP.sys i Reliable Services genom att importera **paketet Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet. Det här `HttpSysCommunicationListener`paketet innehåller `ICommunicationListener`, en implementering av . `HttpSysCommunicationListener`kan du skapa en ASP.NET Core WebHost inuti en tillförlitlig tjänst med hjälp av HTTP.sys som webbserver.

HTTP.sys bygger på [Windows HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Det här API:et använder **drivrutinen http.sys** för att bearbeta HTTP-begäranden och dirigera dem till processer som kör webbprogram. Detta gör att flera processer på samma fysiska eller virtuella dator kan vara värd för webbprogram på samma port, oenyguererade av antingen en unik URL-sökväg eller värdnamn. Dessa funktioner är användbara i Service Fabric för att vara värd för flera webbplatser i samma kluster.

>[!NOTE]
>HTTP.sys-implementeringen fungerar bara på Windows-plattformen.

I följande diagram visas hur HTTP.sys använder **drivrutinen HTTP.sys** i Windows för portdelning:

![HTTP.sys-diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys i en tillståndslös tjänst
Om `HttpSys` du vill använda i `CreateServiceInstanceListeners` en tillståndslös tjänst åsidosätter du metoden och returnerar en `HttpSysCommunicationListener` instans:

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

`HttpSysCommunicationListener`är för närvarande inte avsedd för användning i tillståndskänsliga tjänster på grund av komplikationer med den underliggande HTTP.sys-portdelningsfunktionen. **HTTP.sys** Mer information finns i följande avsnitt om dynamisk portallokering med HTTP.sys. För tillståndskänsliga tjänster är Kestrel den föreslagna webbservern.

### <a name="endpoint-configuration"></a>Konfiguration av slutpunkt

En `Endpoint` konfiguration krävs för webbservrar som använder Windows HTTP Server API, inklusive HTTP.sys. Webbservrar som använder Windows HTTP Server API måste först reservera sin URL med HTTP.sys (detta sker normalt med [netsh-verktyget).](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 

Den här åtgärden kräver förhöjda privilegier som dina tjänster inte har som standard. Alternativen "http" eller "https" `Protocol` för `Endpoint` egenskapen för konfigurationen i ServiceManifest.xml används specifikt för att instruera Service Fabric-körningen att registrera en URL med HTTP.sys för din räkning. Det gör detta genom att använda den [*starka jokertecken*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL prefix.

Om du till `http://+:80` exempel vill reservera för en tjänst använder du följande konfiguration i ServiceManifest.xml:

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

Och slutpunktsnamnet måste skickas `HttpSysCommunicationListener` till konstruktorn:

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
Om du vill använda en statisk port med `Endpoint` HTTP.sys anger du portnumret i konfigurationen:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Använda HTTP.sys med en dynamisk port
Om du vill använda en dynamiskt tilldelad `Port` port med `Endpoint` HTTP.sys utelämnar du egenskapen i konfigurationen:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

En dynamisk port som `Endpoint` allokeras av en konfiguration ger bara en port *per värdprocess*. Den aktuella Service Fabric-värdmodellen gör att flera tjänstinstanser och/eller repliker kan vara värd för samma process. Det innebär att var och en delar `Endpoint` samma port när den allokeras via konfigurationen. Flera **HTTP.sys-instanser** kan dela en port med hjälp av den underliggande http.sys-portdelningsfunktionen. **HTTP.sys** Men det stöds inte `HttpSysCommunicationListener` av på grund av de komplikationer den introducerar för kundförfrågningar. För dynamisk portanvändning är Kestrel den föreslagna webbservern.

## <a name="kestrel-in-reliable-services"></a>Kestrel i pålitliga tjänster
Du kan använda Kestrel i Reliable Services genom att importera **Paketet Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet. Det här `KestrelCommunicationListener`paketet innehåller `ICommunicationListener`, en implementering av . `KestrelCommunicationListener`kan du skapa en ASP.NET Core WebHost inuti en pålitlig tjänst med hjälp av Kestrel som webbserver.

Kestrel är en plattformsoberoende webbserver för ASP.NET Core. Till skillnad från HTTP.sys använder Kestrel inte en centraliserad slutpunktshanterare. Till skillnad från HTTP.sys stöder Kestrel inte portdelning mellan flera processer. Varje instans av Kestrel måste använda en unik port. Mer information om Tornstrel finns i [implementeringsdetaljerna](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Kestreldiagram][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel i en statslös tjänst
Om `Kestrel` du vill använda i `CreateServiceInstanceListeners` en tillståndslös tjänst åsidosätter du metoden och returnerar en `KestrelCommunicationListener` instans:

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
Om `Kestrel` du vill använda i `CreateServiceReplicaListeners` en tillståndskänslig tjänst åsidosätter du metoden och returnerar en `KestrelCommunicationListener` instans:

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

I det här exemplet tillhandahålls `IReliableStateManager` en singleton-instans av webhost beroendeinjektionsbehållaren. Detta är inte absolut nödvändigt, men `IReliableStateManager` det gör att du kan använda och tillförlitliga samlingar i din MVC controller åtgärdsmetoder.

Ett `Endpoint` konfigurationsnamn anges `KestrelCommunicationListener` *inte* i en tillståndskänslig tjänst. Detta förklaras mer i detalj i följande avsnitt.

### <a name="configure-kestrel-to-use-https"></a>Konfigurera Kestrel för användning av HTTPS
När du aktiverar HTTPS med Kestrel i tjänsten måste du ange flera lyssningsalternativ. Uppdatera `ServiceInstanceListener` om du vill använda en *slutpunktHttps-slutpunkt* och lyssna på en viss port (till exempel port 443). När du konfigurerar webbhotell för att använda Kestrel-webbservern måste du konfigurera Kestrel för att lyssna efter IPv6-adresser i alla nätverksgränssnitt: 

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

Ett fullständigt exempel i en självstudiekurs finns i [Konfigurera Kestrel för att använda HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfiguration av slutpunkt
En `Endpoint` konfiguration krävs inte för att använda Kestrel. 

Kestrel är en enkel fristående webbserver. Till skillnad från HTTP.sys (eller HttpListener) behöver den ingen `Endpoint` konfiguration i ServiceManifest.xml eftersom den inte kräver URL-registrering innan den startas. 

#### <a name="use-kestrel-with-a-static-port"></a>Använd Kestrel med en statisk port
Du kan konfigurera en `Endpoint` statisk port i konfigurationen av ServiceManifest.xml för användning med Kestrel. Även om detta inte är absolut nödvändigt, erbjuder det två potentiella fördelar:
 - Om porten inte faller i programportintervallet öppnas den via OS-brandväggen av Service Fabric.
 - Webbadressen som du `KestrelCommunicationListener` får via kommer att använda den här porten.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Om `Endpoint` en är konfigurerad måste dess `KestrelCommunicationListener` namn skickas till konstruktorn: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Om ServiceManifest.xml inte använder `Endpoint` en konfiguration utelämnar `KestrelCommunicationListener` du namnet i konstruktorn. I det här fallet kommer den att använda en dynamisk port. Mer information om detta finns i nästa avsnitt.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Använd Kestrel med en dynamisk port
Kestrel kan inte använda den automatiska porttilldelningen från konfigurationen `Endpoint` i ServiceManifest.xml. Det beror på att automatisk `Endpoint` porttilldelning från en konfiguration tilldelar en unik port per *värdprocess*och en enda värdprocess kan innehålla flera Kestrel-instanser. Detta fungerar inte med Kestrel eftersom det inte stöder portdelning. Därför måste varje Kestrel-instans öppnas på en unik port.

Om du vill använda dynamisk porttilldelning `Endpoint` med Kestrel utelämnar du konfigurationen i ServiceManifest.xml helt och skickar inte ett slutpunktsnamn till `KestrelCommunicationListener` konstruktorn enligt följande:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

I den `KestrelCommunicationListener` här konfigurationen väljer du automatiskt en oanvänd port från programportområdet.

För HTTPS bör den ha slutpunkten konfigurerad med HTTPS-protokoll utan en port som anges i ServiceManifest.xml och skicka slutpunktsnamnet till KestrelCommunicationListener-konstruktorn.


## <a name="service-fabric-configuration-provider"></a>Konfigurationsleverantör för tjänstinfrastruktur
Appkonfigurationen i ASP.NET Core baseras på nyckelvärdespar som upprättats av konfigurationsleverantören. Läs [Konfiguration i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) för att förstå mer om allmänt ASP.NET Core-konfigurationsstöd.

I det här avsnittet beskrivs hur konfigurationsleverantören för Service Fabric `Microsoft.ServiceFabric.AspNetCore.Configuration` integreras med ASP.NET Core-konfigurationen genom att importera NuGet-paketet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Starttillägg för AddServiceFabricConfiguration
När du `Microsoft.ServiceFabric.AspNetCore.Configuration` har importerat NuGet-paketet måste du registrera serviceinfrastrukturkonfigurationskällan med ASP.NET Core-konfigurations-API. Du gör detta genom att kontrollera **AddServiceFabricConfiguration-tillägg** i `Microsoft.ServiceFabric.AspNetCore.Configuration` namnområdet mot `IConfigurationBuilder`.

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

Nu kan ASP.NET Core-tjänsten komma åt konfigurationsinställningarna för Service Fabric, precis som alla andra programinställningar. Du kan till exempel använda alternativmönstret för att läsa in inställningar i starkt skrivna objekt.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Standardnyckelmappning
Som standard innehåller konfigurationsprovidern för Service Fabric paketnamn, avsnittsnamn och egenskapsnamn. Tillsammans utgör dessa konfigurationsnyckeln ASP.NET Core enligt följande:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Om du till exempel har `MyConfigPackage` ett konfigurationspaket med namnet med följande innehåll är `IConfiguration` konfigurationsvärdet tillgängligt på ASP.NET Core via *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Konfigurationsalternativ för Service Fabric
Konfigurationsprovidern för `ServiceFabricConfigurationOptions` Service Fabric stöder också för att ändra standardbeteendet för nyckelmappning.

#### <a name="encrypted-settings"></a>Krypterade inställningar
Service Fabric stöder krypterade inställningar, liksom konfigurationsleverantören för Service Fabric. De krypterade inställningarna dekrypteras inte för `IConfiguration` att ASP.NET Core som standard. De krypterade värdena lagras där i stället. Men om du vill dekryptera värdet för att lagra i ASP.NET Core IConfiguration kan du ställa `AddServiceFabricConfiguration` in flaggan *Dekryptera* på false i tillägget enligt följande:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Flera konfigurationspaket
Service Fabric stöder flera konfigurationspaket. Som standard ingår paketnamnet i konfigurationsnyckeln. Men du kan `IncludePackageName` ställa in flaggan till false, enligt följande:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Anpassad nyckelmappning, värdeutvinning och datapopulation
Konfigurationsprovidern för Service Fabric stöder också `ExtractKeyFunc` mer avancerade scenarier för `ExtractValueFunc`att anpassa nyckelmappningen med och extrahera värdena med . Du kan till och med ändra hela processen med att fylla i `ConfigAction`data från Service Fabric-konfigurationen till ASP.NET Core-konfigurationen med hjälp av .

Följande exempel illustrerar hur `ConfigAction` du använder för att anpassa datapopulationen:
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

### <a name="configuration-updates"></a>Konfigurationsuppdateringar
Konfigurationsprovidern för Service Fabric stöder också konfigurationsuppdateringar. Du kan använda ASP.NET `IOptionsMonitor` Core för att ta `IOptionsSnapshot` emot ändringsmeddelanden och sedan använda för att läsa in konfigurationsdata igen. Mer information finns [i ASP.NET Core-alternativ](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Dessa alternativ stöds som standard. Ingen ytterligare kodning behövs för att aktivera konfigurationsuppdateringar.

## <a name="scenarios-and-configurations"></a>Scenarier och konfigurationer
Det här avsnittet innehåller en kombination av webbserver, portkonfiguration, service fabric-integrationsalternativ och tillägg som vi rekommenderar att du felsöker följande scenarier:
 - Externt exponerade ASP.NET Kärntillståndslösa tjänster
 - Endast interna ASP.NET kärntillståndstjänster
 - Interna ASP.NET Grundläggande tillståndskänsliga tjänster

En **externt exponerad tjänst** är en tjänst som exponerar en slutpunkt som anropas från utanför klustret, vanligtvis via en belastningsutjämnare.

En **intern** tjänst är en tjänst vars slutpunkt endast anropas inifrån klustret.

> [!NOTE]
> Tillståndskänsliga tjänstslutpunkter i allmänhet inte bör utsättas för Internet. Kluster bakom belastningsutjämnare som inte känner till serviceinfrastrukturlösningen, till exempel Azure Load Balancer, kan inte exponera tillståndskänsliga tjänster. Det beror på att belastningsutjämnaren inte kan hitta och dirigera trafik till lämplig tillståndskänslig tjänstreplik. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externt exponerade ASP.NET Kärntillståndslösa tjänster
Kestrel är den föreslagna webbservern för frontend-tjänster som exponerar externa, internet-vända HTTP-slutpunkter. I Windows kan HTTP.sys tillhandahålla portdelningsfunktioner, vilket gör att du kan vara värd för flera webbtjänster på samma uppsättning noder med samma port. I det här fallet är webbtjänsterna differentierade efter värdnamn eller sökväg, utan att förlita sig på en frontend-proxy eller gateway för att tillhandahålla HTTP-routning.
 
När den utsätts för internet bör en tillståndslös tjänst använda en välkänd och stabil slutpunkt som kan nås via en belastningsutjämnare. Du ska ange den här webbadressen till programmets användare. Vi rekommenderar följande konfiguration:

|  |  | **Obs!** |
| --- | --- | --- |
| Webbserver | Kestrel | Kestrel är den föredragna webbservern, eftersom den stöds i Windows och Linux. |
| Portkonfiguration | statiskt | En välkänd statisk port bör konfigureras `Endpoints` i konfigurationen av ServiceManifest.xml, till exempel 80 för HTTP eller 443 för HTTPS. |
| ServiceFabricIntegrationOptions | Inget | Använd `ServiceFabricIntegrationOptions.None` alternativet när du konfigurerar Middleware för integrering av Service Fabric, så att tjänsten inte försöker validera inkommande begäranden om en unik identifierare. Externa användare av ditt program kommer inte att veta den unika identifierande information som mellanprogram använder. |
| Antal instanser | -1 | I vanliga användningsfall ska inställningen för instansantal ställas in *på -1*. Detta görs så att en instans är tillgänglig på alla noder som får trafik från en belastningsutjämnare. |

Om flera externt exponerade tjänster delar samma uppsättning noder kan du använda HTTP.sys med en unik men stabil URL-sökväg. Du kan åstadkomma detta genom att ändra webbadressen som anges när du konfigurerar IWebHost. Observera att detta endast gäller HTTP.sys.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Tillståndslös tjänst endast för internt ASP.NET
Tillståndslösa tjänster som endast anropas inifrån klustret bör använda unika webbadresser och dynamiskt tilldelade portar för att säkerställa samarbete mellan flera tjänster. Vi rekommenderar följande konfiguration:

|  |  | **Obs!** |
| --- | --- | --- |
| Webbserver | Kestrel | Även om du kan använda HTTP.sys för interna tillståndslösa tjänster är Kestrel den bästa servern för att tillåta flera tjänstinstanser att dela en värd.  |
| Portkonfiguration | dynamiskt tilldelad | Flera repliker av en tillståndskänslig tjänst kan dela en värdprocess eller värdoperativsystem och behöver därför unika portar. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Med dynamisk porttilldelning förhindrar den här inställningen det felaktiga identitetsproblemet som beskrivs tidigare. |
| InstansCount | valfri | Inställningen för instansantal kan ställas in på alla värden som krävs för att driva tjänsten. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Tillståndskänslig ASP.NET core-tjänsten internt
Tillståndskänsliga tjänster som endast anropas inifrån klustret bör använda dynamiskt tilldelade portar för att säkerställa samarbete mellan flera tjänster. Vi rekommenderar följande konfiguration:

|  |  | **Obs!** |
| --- | --- | --- |
| Webbserver | Kestrel | Den `HttpSysCommunicationListener` är inte avsedd för användning av tillståndskänsliga tjänster där repliker delar en värdprocess. |
| Portkonfiguration | dynamiskt tilldelad | Flera repliker av en tillståndskänslig tjänst kan dela en värdprocess eller värdoperativsystem och behöver därför unika portar. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Med dynamisk porttilldelning förhindrar den här inställningen det felaktiga identitetsproblemet som beskrivs tidigare. |

## <a name="next-steps"></a>Nästa steg
[Felsöka ditt Service Fabric-program med Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png

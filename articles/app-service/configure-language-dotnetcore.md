---
title: Konfigurera Windows ASP.NET Core-appar
description: Lär dig hur du konfigurerar en ASP.NET Core-app i de interna Windows-instanserna av App Service. Den här artikeln visar de vanligaste konfigurations åtgärderna.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908149"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Konfigurera en Windows ASP.NET Core-app för Azure App Service

> [!NOTE]
> Information om ASP.NET i .NET Framework finns i [Konfigurera en ASP.net-app för Azure App Service](configure-language-dotnet-framework.md)

ASP.NET Core appar måste distribueras till Azure App Service som kompilerade binärfiler. Verktyget Visual Studio Publishing skapar lösningen och distribuerar sedan de kompilerade binärfilerna direkt, medan App Service distributions motor distribuerar kod databasen först och kompilerar sedan binärfilerna. Information om Linux-appar finns i [Konfigurera en linux ASP.net Core-app för Azure App Service](containers/configure-language-dotnetcore.md).

Den här guiden innehåller viktiga begrepp och instruktioner för ASP.NET Core utvecklare. Om du aldrig har använt Azure App Service ska du först följa snabb starten för [ASP.net](app-service-web-get-started-dotnet.md) och [ASP.net Core med SQL Database själv studie kursen](app-service-web-tutorial-dotnetcore-sqldb.md) .

## <a name="show-supported-net-core-runtime-versions"></a>Visa de .NET Core runtime-versioner som stöds

I App Service har Windows-instanser redan alla .NET Core-versioner som stöds installerade. Om du vill visa de .NET Core Runtime och SDK-versioner som är tillgängliga går du till `https://<app-name>.scm.azurewebsites.net/DebugConsole` och kör följande kommando i den webbläsarbaserade konsolen:

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>Ange .NET Core-version

Ange mål ramverket i projekt filen för ditt ASP.NET Core-projekt. Mer information finns i [Välj den .net Core-version som ska användas](https://docs.microsoft.com/dotnet/core/versions/selection) i .net Core-dokumentationen.

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [Ange inställningar för appar](configure-common.md#configure-app-settings) utanför appens kod. Sedan kan du komma åt dem i valfri klass med hjälp av standard mönstret för ASP.NET Core beroende inmatning:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Om du konfigurerar en app-inställning med samma namn i App Service och i *appsettings.jspå*, till exempel, har App Service värdet företräde framför *appsettings.js* svärdet. Med värdet Local *appsettings.json* kan du felsöka appen lokalt, men App Service-värdet låter appen köras i produkt med produktions inställningar. Anslutnings strängar fungerar på samma sätt. På så sätt kan du behålla dina program hemligheter utanför din kod lagrings plats och få till gång till lämpliga värden utan att ändra koden.

> [!NOTE]
> Observera att [hierarkiska konfigurations data](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) i *appsettings.jspå* nås med hjälp av den `:` avgränsare som är standard för .net Core. Om du vill åsidosätta en viss hierarkisk konfigurations inställning i App Service anger du namnet på appens inställning med samma avgränsat format i nyckeln. Du kan köra följande exempel i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Distribuera lösningar för flera projekt

När en Visual Studio-lösning innehåller flera projekt inkluderar publicerings processen i Visual Studio redan att välja det projekt som ska distribueras. När du distribuerar till App Service distributions motor, t. ex. med git eller med ZIP-distribution, med build-automatisering aktiverat, väljer App Service distributions motor den första webbplatsen eller det webb programs projekt som hittas som App Service-appen. Du kan ange vilket projekt App Service ska använda genom att ange `PROJECT` appens inställning. Kör till exempel följande i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

ASP.NET Core tillhandahåller en [inbyggd Logging-Provider för App Service](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service). I *program.cs* för ditt projekt lägger du till providern i programmet via `ConfigureLogging` tilläggs metoden, som du ser i följande exempel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Du kan sedan konfigurera och generera loggar med [standard mönstret för .net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Mer information om hur du felsöker ASP.NET Core appar i App Service finns i [felsöka ASP.net Core på Azure App service och IIS](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>Sidan Hämta detaljerade undantag

När din ASP.NET Core-app genererar ett undantag i Visual Studio-felsökaren, visar webbläsaren en detaljerad undantags sida, men i App Service sidan ersätts av ett allmänt **HTTP 500-** fel eller **så uppstod ett fel när din begäran bearbetades.** . Om du vill visa sidan detaljerad undantag i App Service lägger du till `ASPNETCORE_ENVIRONMENT` appens inställning i din app genom att köra följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din app-logik behöver veta om användarnas begär Anden är krypterade eller inte, konfigurerar du de vidarebefordrade rubrikernas mellanprogram i *startup.cs*:

- Konfigurera mellanprogram med [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) för att vidarebefordra- `X-Forwarded-For` och- `X-Forwarded-Proto` rubrikerna i `Startup.ConfigureServices` .
- Lägg till privata IP-adressintervall i de kända nätverken så att mellanprogram kan lita på App Service belastningsutjämnare.
- Anropa metoden [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) i `Startup.Configure` innan du anropar andra middlewares.

Om du placerar alla tre element tillsammans ser koden ut som i följande exempel:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Mer information finns i [konfigurera ASP.net Core att arbeta med proxyservrar och belastningsutjämnare](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core app med SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

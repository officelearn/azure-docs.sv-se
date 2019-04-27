---
title: Konfigurera ASP.NET Core - appar i Azure App Service | Microsoft Docs
description: Lär dig hur du konfigurerar ASP.NET Core-appar att fungera i Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852320"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurera en Linux ASP.NET Core-app för Azure App Service

ASP.NET Core-appar måste distribueras som kompilerade binärfiler. Visual Studio-Publiceringsverktyget bygger lösningen och distribuerar sedan kompilerade binärfilerna direkt, medan App Service-distributionsmotorn distribuerar kodlagringsplatsen först och sedan kompilerar binärfilerna.

Den här guiden innehåller viktiga begrepp och instruktioner för ASP.NET Core utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service, följer du de [ASP.NET Core-Snabbstart](quickstart-dotnetcore.md) och [ASP.NET Core med SQL Database-självstudier](tutorial-dotnetcore-sqldb-app.md) första.

## <a name="show-net-core-version"></a>Visa .NET Core-version

För att visa den aktuella versionen av .NET Core, kör du följande kommando den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

För att visa alla .NET Core-versioner, kör du följande kommando den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Ange .NET Core-version

Kör följande kommando den [Cloud Shell](https://shell.azure.com) att ange version .NET Core till 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [konfigurera appinställningar](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) utanför din Appkod. Du kan använda dem med hjälp av Standardmönstret för ASP.NET:

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Om du konfigurerar en appinställning med samma namn i App Service och i *Web.config*, App Service-värdet har företräde framför Web.config-värdet. Web.config-värdet kan du felsöka appen lokalt men App Service-värdet kan din app i produkten med inställningar för produktion. Anslutningssträngar fungerar på samma sätt. På så sätt kan du hålla dina programhemligheter utanför din kodlagringsplats och få åtkomst till lämpliga värden utan att ändra koden.

## <a name="get-detailed-exceptions-page"></a>Få detaljerad undantagssidan

När din ASP.NET-app genererar ett undantag i Visual Studio-felsökaren, visas en sida med detaljerade undantag, men i App Service sidan ersätts av en allmän **HTTP 500** fel eller **ett fel uppstod när din begäran behandlas.** meddelande. För att visa undantagssidan detaljerad i App Service, lägger du till den `ASPNETCORE_ENVIRONMENT` appinställningen till din app genom att köra följande kommando i den <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver veta om användaren begär är krypterade eller inte, konfigurera vidarebefordras rubriker mellanprogram i *Startup.cs*:

- Konfigurera mellanprogram med [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) att vidarebefordra den `X-Forwarded-For` och `X-Forwarded-Proto` rubriker i `Startup.ConfigureServices`.
- Lägga till privata IP-adressintervall till kända nätverk, så att mellanprogrammet kan lita på App Service-belastningsutjämnare.
- Anropa den [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) -metod i `Startup.Configure` innan du anropar andra middlewares.

Sammanfoga alla tre element koden ser ut som i följande exempel:

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

Mer information finns i [Konfigurera ASP.NET Core för att arbeta med proxyservrar och belastningsutjämnare](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Distribuera lösningar med flera projekt

När du distribuerar en ASP.NET-lagringsplats till distributionsmotorn med en *.csproj* fil i rotkatalogen motorn distribuerar-projektet. När du distribuerar en ASP.NET-lagringsplats med en *.sln* fil i rotkatalogen motorn hämtar den första webbplatsen eller Webbprogramprojektet påträffas som App Service-appen. Det är möjligt för engine inte att välja projektet som du vill.

Om du vill distribuera en lösning för flera projekt kan du ange projektet som ska använda i App Service på två olika sätt:

### <a name="using-deployment-file"></a>Med hjälp av filen .deployment

Lägg till en *.deployment* till Lagringsplatsens rot och Lägg till följande kod:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Använda appinställningar

I den <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, lägga till en appinställning i App Service-appen genom att köra följande CLI-kommando. Ersätt  *\<appens namn->*,  *\<resource-group-name >*, och  *\<projekt-name >* med lämpliga värden .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core-app med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux vanliga frågor och svar](app-service-linux-faq.md)
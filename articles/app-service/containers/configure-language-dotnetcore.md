---
title: Konfigurera Linux ASP.NET Core-appar
description: Lär dig hur du konfigurerar en fördefinierad ASP.NET Core behållare för din app. Den här artikeln visar de vanligaste konfigurations åtgärderna.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: cab99b9d20ce8a3190eb9aa59650dab32fca324d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768426"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurera en Linux ASP.NET Core-app för Azure App Service

ASP.NET Core appar måste distribueras som kompilerade binärfiler. Verktyget Visual Studio Publishing skapar lösningen och distribuerar sedan de kompilerade binärfilerna direkt, medan App Service distributions motor distribuerar kod databasen först och kompilerar sedan binärfilerna.

Den här guiden innehåller viktiga begrepp och instruktioner för ASP.NET Core utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service, följer du anvisningarna [ASP.net Core snabb start](quickstart-dotnetcore.md) och [ASP.net Core med SQL Database själv studie kursen](tutorial-dotnetcore-sqldb-app.md) först.

## <a name="show-net-core-version"></a>Visa .NET Core-version

Om du vill visa den aktuella .NET Core-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla .NET Core-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Ange .NET Core-version

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ange .net Core-versionen till 2,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [Ange inställningar för appar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) utanför appens kod. Sedan kan du komma åt dem i valfri klass med hjälp av standard mönstret för ASP.NET Core beroende inmatning:

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Om du konfigurerar en app-inställning med samma namn i App Service och i *appSettings. JSON*, har App Service-värdet företräde framför värdet *appSettings. JSON* . Med det lokala *appSettings. JSON* -värdet kan du felsöka appen lokalt, men App Service-värdet låter appen köras i produkt med produktions inställningar. Anslutnings strängar fungerar på samma sätt. På så sätt kan du behålla dina program hemligheter utanför din kod lagrings plats och få till gång till lämpliga värden utan att ändra koden.

## <a name="get-detailed-exceptions-page"></a>Sidan Hämta detaljerade undantag

När ASP.NET-appen genererar ett undantag i Visual Studio-felsökaren visar webbläsaren en detaljerad undantags sida, men i App Service sidan ersätts av ett allmänt **HTTP 500-** fel eller så **uppstod ett fel när din begäran bearbetades.** som meddelande. Om du vill visa den detaljerade undantags sidan i App Service lägger du till inställningen `ASPNETCORE_ENVIRONMENT` app i appen genom att köra följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din app-logik behöver veta om användarnas begär Anden är krypterade eller inte, konfigurerar du de vidarebefordrade rubrikernas mellanprogram i *startup.cs*:

- Konfigurera mellanprogram med [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) för att vidarebefordra `X-Forwarded-For` och `X-Forwarded-Proto` huvuden i `Startup.ConfigureServices`.
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

## <a name="deploy-multi-project-solutions"></a>Distribuera lösningar för flera projekt

När du distribuerar en ASP.NET-lagringsplats till distributions motorn med en *. CSPROJ* -fil i rot katalogen, distribuerar motorn projektet. När du distribuerar en ASP.NET-lagringsplats med en *. SLN* -fil i rot katalogen, väljer motorn den första webbplatsen eller det webb programs projekt som hittas som App Service-appen. Det är möjligt att motorn inte väljer det projekt som du vill använda.

Om du vill distribuera en lösning med flera projekt kan du ange det projekt som ska användas i App Service på två olika sätt:

### <a name="using-deployment-file"></a>Använda. Deployment-fil

Lägg till en *. Deployment* -fil till lagrings platsens rot och Lägg till följande kod:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Använda appinställningar

I <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>lägger du till en app-inställning i App Service-appen genom att köra följande CLI-kommando. Ersätt *\<app-name >* , *\<resurs-grupp-namn >* och *\<projekt namn >* med lämpliga värden.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ASP.NET Core app med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service Linux](app-service-linux-faq.md)

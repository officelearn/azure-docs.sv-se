---
title: Konfigurera Linux-ASP.NET Core-appar
description: Lär dig hur du konfigurerar en förbyggd ASP.NET Core-behållaren för din app. Den här artikeln visar de vanligaste konfigurationsuppgifterna.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255911"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurera en Linux-ASP.NET Core-app för Azure App Service

ASP.NET Core-appar måste distribueras som kompilerade binärfiler. Visual Studio-publiceringsverktyget skapar lösningen och distribuerar sedan de kompilerade binärfilerna direkt, medan apptjänstdistributionsmotorn distribuerar koddatabasen först och sammanställer sedan binärfilerna.

Den här guiden innehåller viktiga begrepp och instruktioner för ASP.NET Core-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service följer [du snabbstarten ASP.NET Core](quickstart-dotnetcore.md) och ASP.NET Core med [självstudiekursen SQL Database](tutorial-dotnetcore-sqldb-app.md) först.

## <a name="show-net-core-version"></a>Visa .NET Core-versionen

Om du vill visa den aktuella .NET Core-versionen kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla NET Core-versioner som stöds kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Ange .NET Core-version

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ställa in .NET Core-versionen till 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Anpassa byggautomation

Om du distribuerar din app med Git- eller zip-paket med build automation aktiverat, steg apptjänstens automatisering genom följande sekvens:

1. Kör anpassat skript om `PRE_BUILD_SCRIPT_PATH`det anges av .
1. Kör `dotnet restore` för att återställa NuGet-beroenden.
1. Kör `dotnet publish` för att skapa en binär för produktion.
1. Kör anpassat skript om `POST_BUILD_SCRIPT_PATH`det anges av .

`PRE_BUILD_COMMAND`och `POST_BUILD_COMMAND` är miljövariabler som är tomma som standard. Om du vill köra förbyggningskommandon definierar du `PRE_BUILD_COMMAND`. Om du vill köra kommandon `POST_BUILD_COMMAND`efter build definierar du .

I följande exempel anges de två variablerna till en serie kommandon, avgränsade med kommatecken.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Ytterligare miljövariabler för att anpassa byggautomatisering finns i [Oryx-konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Mer information om hur App Service körs och bygger ASP.NET Core-appar i Linux finns i [Oryx-dokumentation: Så här identifieras och skapas .NET Core-appar](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ställa in appinställningar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) utanför appkoden. Sedan kan du komma åt dem i alla klasser med hjälp av standard ASP.NET Core beroende injektion mönster:

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

Om du konfigurerar en appinställning med samma namn i App Service och i *appsettings.json*har värdet App Service företräde framför *värdet appsettings.json.* Med värdet för lokala *appsettings.json* kan du felsöka appen lokalt, men med apptjänstvärdet kan du köra appen i produkten med produktionsinställningar. Anslutningssträngar fungerar på samma sätt. På så sätt kan du hålla dina programhemligheter utanför koddatabasen och komma åt lämpliga värden utan att ändra koden.

## <a name="get-detailed-exceptions-page"></a>Sidan Få detaljerade undantag

När din ASP.NET-app genererar ett undantag i Felsökningsprogrammet i Visual Studio visar webbläsaren en detaljerad undantagssida, men i Apptjänsten ersätts den sidan av ett allmänt **HTTP 500-fel** eller **Ett fel uppstod vid bearbetning av din begäran.** . Om du vill visa den detaljerade `ASPNETCORE_ENVIRONMENT` undantagssidan i App Service lägger du till appinställningen i appen genom att köra följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver veta om användarbegäranden är krypterade eller inte konfigurerar du Middleware för vidarebefordrade rubriker i *Startup.cs:*

- Konfigurera mellanprogram med [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) `X-Forwarded-For` för `X-Forwarded-Proto` att `Startup.ConfigureServices`vidarebefordra och rubrikerna i .
- Lägg till privata IP-adressintervall i kända nätverk, så att mellanprogram kan lita på apptjänstens belastningsutjämnare.
- Anropa [metoden UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) `Startup.Configure` innan du anropar andra mellanprogram.

När du sätter ihop alla tre elementen ser koden ut som följande exempel:

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

## <a name="deploy-multi-project-solutions"></a>Distribuera lösningar för flera projekt

När du distribuerar en ASP.NET databas till distributionsmotorn med en *CSPROJ-fil* i rotkatalogen distribuerar motorn projektet. När du distribuerar en ASP.NET databas med en *SLN-fil* i rotkatalogen väljer motorn den första webbplatsen eller webbprogramprojektet som apptjänstappen hittas. Det är möjligt för motorn att inte välja det projekt du vill ha.

Om du vill distribuera en lösning för flera projekt kan du ange vilket projekt som ska användas i App Service på två olika sätt:

### <a name="using-deployment-file"></a>Använda DISTRIBUTIONSFIL

Lägg till en *DISTRIBUTIONSFIL* i databasroten och lägg till följande kod:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Använda appinställningar

Lägg till en appinställning i apptjänstappen i <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>genom att köra följande CLI-kommando. Ersätt * \<appnamn>, * * \<>och * * \<projektnamn>* med lämpliga värden.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: ASP.NET Core-appen med SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service Linux](app-service-linux-faq.md)

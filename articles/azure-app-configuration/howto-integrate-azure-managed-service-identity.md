---
title: Autentisera med Azure-hanterade identiteter
titleSuffix: Azure App Configuration
description: Autentisera till Azure App-konfiguration med hjälp av Azure-hanterade identiteter
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: bf97a1eae758778efc8d800666af4a5fcb574429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056829"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrera med hanterade Azure-identiteter

Azure Active [Directory-hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md) förenklar hantering av hemligheter för ditt molnprogram. Med en hanterad identitet kan din kod använda tjänsthuvudnamnet som skapats för Azure-tjänsten som körs på. Du använder en hanterad identitet i stället för en separat autentiseringsuppgifter som lagras i Azure Key Vault eller en lokal anslutningssträng. 

Azure App Configuration och dess .NET Core-, .NET Framework- och Java Spring-klientbibliotek har skapat identitetsstöd inbyggt i dem. Även om du inte behöver använda den, eliminerar den hanterade identiteten behovet av en åtkomsttoken som innehåller hemligheter. Koden kan komma åt App Configuration Store med endast tjänstslutpunkten. Du kan bädda in den här webbadressen direkt i koden utan att avslöja någon hemlighet.

Den här artikeln visar hur du kan dra nytta av den hanterade identiteten för att komma åt App-konfigurationen. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter [skapar du först en ASP.NET Core-app med appkonfiguration.](./quickstart-aspnet-core-app.md)

Den här artikeln visar också hur du kan använda den hanterade identiteten tillsammans med App Configuration's Key Vault-referenser. Med en enda hanterad identitet kan du sömlöst komma åt både hemligheter från Key Vault och konfigurationsvärden från AppKonfiguration. Om du vill utforska den här funktionen, avsluta [Använd Key Vault Referenser med ASP.NET Core](./use-key-vault-references-dotnet-core.md) först.

Du kan använda vilken kodredigerare som helst för att göra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som finns på Windows-, macOS- och Linux-plattformarna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Bevilja en hanterad identitetsåtkomst till appkonfiguration.
> * Konfigurera appen så att den använder en hanterad identitet när du ansluter till appkonfiguration.
> * Du kan också konfigurera appen så att den använder en hanterad identitet när du ansluter till Key Vault via en appkonfigurationsnyckelvalvsreferens.

## <a name="prerequisites"></a>Krav

Du behöver följande för att kunna slutföra den här självstudiekursen:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell konfigurerat](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Lägga till en hanterad identitet

Om du vill konfigurera en hanterad identitet i portalen skapar du först ett program och aktiverar sedan funktionen.

1. Skapa en App Services-instans i [Azure-portalen](https://portal.azure.com) som vanligt. Gå till den i portalen.

1. Bläddra ned till gruppen **Inställningar** i den vänstra rutan och välj **Identitet**.

1. På fliken **System som tilldelats** växlar du **Status** till **På** och väljer **Spara**.

1. Svar **Ja** när du uppmanas att aktivera systemtilldelade hanterade identitet.

    ![Ange hanterad identitet i App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Bevilja åtkomst till App Configuration

1. I [Azure-portalen](https://portal.azure.com)väljer du **Alla resurser** och väljer appkonfigurationsarkivet som du skapade i snabbstarten.

1. Välj **Åtkomstkontroll (IAM)**.

1. Välj **Lägg till** i användargränssnittet för lägg **till rolltilldelningskort** på fliken **Kontrollera åtkomst.**

1. Under **Roll**väljer du **App Configuration Data Reader**. Under **Tilldela åtkomst till**väljer du **Apptjänst** under **Systemtilldelade hanterade identitet**.

1. Under **Prenumeration**väljer du din Azure-prenumeration. Välj apptjänstresursen för din app.

1. Välj **Spara**.

    ![Lägga till en hanterad identitet](./media/add-managed-identity.png)

1. Valfritt: Om du också vill bevilja åtkomst till Key Vault följer du anvisningarna i [Ge nyckelvalvautentisering med en hanterad identitet](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Använda en hanterad identitet

1. Lägg till en referens till *Azure.Identity-paketet:*

    ```cli
    dotnet add package Azure.Identity
    ```

1. Hitta slutpunkten i appkonfigurationsarkivet. Den här URL:en visas på fliken **Access-nycklar** för butiken i Azure-portalen.

1. Öppna *appsettings.json*och lägg till följande skript. Ersätt * \<service_endpoint>*, inklusive parenteserna, med URL:en till appkonfigurationsarkivet. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Öppna *Program.cs*och lägg till en `Azure.Identity` referens `Microsoft.Azure.Services.AppAuthentication` till och namnområden:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Om du bara vill komma åt värden som `CreateWebHostBuilder` lagras direkt `config.AddAzureAppConfiguration()` i appkonfiguration uppdaterar du metoden genom att ersätta metoden.

    > [!IMPORTANT]
    > `CreateHostBuilder`ersätts `CreateWebHostBuilder` i .NET Core 3.0.  Välj rätt syntax baserat på din miljö.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. Om du vill använda både appkonfigurationsvärden och key vault-referenser uppdaterar *du Program.cs* som visas nedan. Den här koden `KeyVaultClient` skapar `AzureServiceTokenProvider` en ny med hjälp av `UseAzureKeyVault` en och skickar den här referensen till ett anrop till metoden.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>());
    ```
    ---

    Du kan nu komma åt Key Vault-referenser precis som alla andra appkonfigurationsnyckelr. Config-providern använder `KeyVaultClient` den som du har konfigurerat för att autentisera till Key Vault och hämta värdet.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Distribuera från lokal Git

Det enklaste sättet att aktivera lokal Git-distribution för din app med Kudu-byggservern är att använda [Azure Cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivera lokal Git med Kudu
Om du inte har en lokal git-databas för din app måste du initiera en. Om du vill initiera en lokal git-databas kör du följande kommandon från appens projektkatalog:

```cmd
git init
git add .
git commit -m "Initial version"
```

Om du vill aktivera lokal Git-distribution för [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) din app med Kudu-byggservern körs du i Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Det här kommandot ger dig något som liknar följande utdata:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Distribuera projektet

Lägg till en Azure-fjärrkontroll i din lokala Git-databas i det _lokala terminalfönstret._ Ersätt _ \<url>_ med webbadressen till Git-fjärrkontrollen som du fick från [Aktivera lokal Git med Kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När du uppmanas att ange ett lösenord anger du lösenordet som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user). Använd inte lösenordet som du använder för att logga in på Azure-portalen.

```bash
git push azure master
```

Du kan se körningsspecifik automatisering i utdata, till exempel `npm install` MSBuild för ASP.NET, `pip install` för Node.js och Python.

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till din webbapp med hjälp av en webbläsare för att kontrollera att innehållet distribueras.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Använd hanterad identitet på andra språk

App Configuration-providrar för .NET Framework och Java Spring har också inbyggt stöd för hanterad identitet. Du kan använda butikens URL-slutpunkt i stället för dess fullständiga anslutningssträng när du konfigurerar en av dessa leverantörer. 

Du kan till exempel uppdatera konsolappen .NET Framework som skapats i snabbstarten för att ange följande inställningar i filen *App.config:*

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lagt till en Azure-hanterad identitet för att effektivisera åtkomsten till appkonfiguration och förbättra autentiseringshanteringen för din app. Om du vill veta mer om hur du använder Appkonfiguration fortsätter du till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)

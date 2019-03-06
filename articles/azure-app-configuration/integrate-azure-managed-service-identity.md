---
title: Självstudie om att integrera med hanterade Azure-identiteter | Microsoft Docs
description: I den här självstudien lär du dig att använda hanterade Azure-identiteter för att autentisera med och få åtkomst till Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 874522b6b4ca3739e0736d4f70f76bb82cad25f9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957359"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Självstudier: Integrera med hanterade Azure-identiteter

[Hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för Azure Active Directory förenklar hanteringen av hemligheter för ditt molnprogram. Med en hanterad identitet kan du konfigurera din kod till att använda det tjänsthuvudnamn som har skapats för den Azure-beräkningstjänst som det körs på i stället för separata autentiseringsuppgifter som lagras i Azure Key Vault eller en lokal anslutningssträng. Azure App Configuration och dess klientbibliotek för .NET Core, .NET och Java har inbyggt MSI-stöd. Du måste inte använda MSI, men det eliminerar behovet av en åtkomsttoken som innehåller hemligheter. Din kod behöver bara känna till tjänstslutpunkten för ett appkonfigurationsarkiv för att kunna komma åt det, och du kan bädda in den här URL:en i koden direkt utan risk för att exponera någon hemlighet.

Den här självstudien visar hur du kan dra nytta av MSI för att komma åt App Configuration. Den bygger på den webbapp som introducerades i snabbstarterna. Slutför avsnittet om att [skapa en ASP.NET Core-app med App Configuration](./quickstart-aspnet-core-app.md) först innan du fortsätter.

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är dock ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ge en hanterad identitet åtkomst till App Configuration
> * Konfigurera din app till att använda en hanterad identitet vid anslutning till App Configuration

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra den här självstudiekursen behöver du följande:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Azure Cloud Shell konfigurerat](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Lägga till en hanterad identitet

För att konfigurera en hanterad identitet i portalen skapar du först ett program som vanligt och aktiverar sedan funktionen.

1. Skapa en app i [Azure-portalen](https://aka.ms/azconfig/portal) som vanligt. Gå till den i portalen.

2. Rulla ned till gruppen **Inställningar** i det vänstra navigeringsfältet och välj **Identitet**.

3. På fliken **Systemtilldelad** växlar du **Status** till **På** och klickar på **Spara**.

    ![Ange hanterad identitet i App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Bevilja åtkomst till App Configuration

1. I [Azure-portalen](https://aka.ms/azconfig/portal) klickar du på **Alla resurser** och det appkonfigurationsarkiv som du skapade i snabbstarten.

2. Välj **Åtkomstkontroll (IAM)**.

3. På fliken **Kontrollera åtkomst** klickar du på **Lägg till** i användargränssnittet för kortet **Lägg till en rolltilldelning**.

4. Ange **Roll** till *Deltagare* och **Tilldela åtkomst till** till *App Service* (under *Systemtilldelad hanterad identitet*).

5. Ange **Prenumeration** till din Azure-prenumeration och välj App Service-resursen för appen.

6. Klicka på **Spara**.

    ![Lägga till hanterad identitet](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Använda en hanterad identitet

1. Öppna *appsettings.json*, lägg till följande och ersätt *<service_endpoint>* (inklusive vinkelparenteser) med URL:en till ditt appkonfigurationsarkiv:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Öppna *Program.cs* och uppdatera metoden `CreateWebHostBuilder` genom att ersätta metoden `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Distribuera från lokal Git

Det enklaste sättet att aktivera lokal Git-distribution för din app med Kudu build-servern är att använda Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivera lokal Git med Kudu

För att aktivera lokal Git-distribution för din app med Kudu build-servern kör du [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) i Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Om du vill skapa en Git-aktiverad app i stället kör du [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) i Cloud Shell med parametern `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Kommandot `az webapp create` bör ge utdata som ser ut ungefär så här:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Distribuera projektet

I det _lokala terminalfönstret_ kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt _\<url>_ med URL:en för den fjärranslutna Git-lagringsplats som du fick från [Enable Git for your app](#enable-local-git-with-kudu) (Aktivera Git för din app).

```bash
git remote add azure <url>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) när du blir ombedd att ange lösenord och inte lösenordet du använde när du loggade in på Azure-portalen.

```bash
git push azure master
```

Du kan se körningsspecifik automatisering i utdata, till exempel MSBuild för ASP.NET, `npm install` för Node.js och `pip install` för Python.

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till webbappen med hjälp av en webbläsare för att verifiera att innehållet distribueras.

```bash
http://<app_name>.azurewebsites.net
```

![app som körs i App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Använd hanterad identitet på andra språk

App Configuration-providrar för .NET Framework och Java Spring har också inbyggt stöd för hanterad identitet. I de här fallen kan du helt enkelt använda appkonfigurationsarkivets URL-slutpunkt i stället för dess fullständiga anslutningssträng när du konfigurerar en provider. Exempel: för den .NET Framework-konsolapp som skapades i snabbstarten anger du följande inställningar i filen *App.config*:

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

I den här självstudien har du lagt till en hanterad Azure-tjänstidentitet för att effektivisera åtkomsten till App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Om du vill lära dig mer om att använda App Configuration fortsätter du till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)

---
title: Självstudie för att integrera med Azure hanterade identiteter | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure hanterade identiteter för att autentisera med och få åtkomst till Azure-Appkonfiguration
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
ms.openlocfilehash: 6e161bcf2c38db0fd614eac1a211218785179ec3
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620792"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Självstudier: Integrera med Azure hanterade identiteter

Azure Active Directory [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) att förenkla hanteringen av hemligheter för ditt molnprogram. Med en hanterad identitet kan du konfigurera din kod att använda tjänstens huvudnamn som skapades för den körs på Azure compute-tjänsten. Du kan använda en hanterad identitet i stället för en separat autentiseringen som lagras i Azure Key Vault eller en lokal anslutningssträng. 

Konfiguration av Azure och dess .NET Core, .NET och Java Spring klientbibliotek medföljer managed service identity (MSI) support inbyggd. Även om du inte behöver använda den, eliminerar behovet av en åtkomsttoken som innehåller hemligheter MSI. Koden har veta endast tjänstens slutpunkt för en appkonfiguration lagra för att kunna komma åt den. Du kan bädda in den här URL: en i din kod direkt utan problem med att exponera alla hemlighet.

Den här självstudien visar hur du kan dra nytta av MSI för att komma åt App Configuration. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter Slutför [skapa en ASP.NET Core-app med Appkonfiguration](./quickstart-aspnet-core-app.md) första.

Du kan använda valfri Kodredigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ tillgängligt på Windows, macOS och Linux-plattformar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ge en hanterad identitet tillgång till Appkonfiguration.
> * Konfigurera din app om du vill använda en hanterad identitet när du ansluter till Appkonfiguration.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna slutföra den här självstudiekursen:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell konfigurerade](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Lägga till en hanterad identitet

Om du vill konfigurera en hanterad identitet i portalen du först skapa ett program som vanligt och sedan aktivera funktionen.

1. Skapa en app i den [Azure-portalen](https://aka.ms/azconfig/portal) som vanligt. Gå till den i portalen.

2. Rulla ned till den **inställningar** gruppen i den vänstra rutan och välj **identitet**.

3. På den **systemtilldelad** fliken, växla **Status** till **på** och välj **spara**.

    ![Ange hanterad identitet i App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Bevilja åtkomst till App Configuration

1. I den [Azure-portalen](https://aka.ms/azconfig/portal)väljer **alla resurser** och välj app-konfigurationsarkivet som du skapade i snabbstarten.

2. Välj **Åtkomstkontroll (IAM)**.

3. På den **Kontrollera åtkomst** fliken **Lägg till** i den **Lägg till rolltilldelning** kortet Användargränssnittet.

4. Under **rollen**väljer **deltagare**. Under **tilldela åtkomst till**väljer **Apptjänst** under **System tilldelade hanterad identitet**.

5. Under **prenumeration**, Välj din Azure-prenumeration. Välj den App Service-resursen för din app.

6. Välj **Spara**.

    ![Lägga till en hanterad identitet](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Använda en hanterad identitet

1. Öppna *appsettings.json*, och Lägg till följande skript. Ersätt *< service_endpoint >*, inklusive parenteser med URL-Adressen till din app konfiguration av lagring:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Öppna *Program.cs*, och uppdatera den `CreateWebHostBuilder` metoden genom att ersätta den `config.AddAzureAppConfiguration()` metoden.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Distribuera från lokal Git

Det enklaste sättet att aktivera lokal Git-distribution för din app med Kudu build-servern är att använda Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivera lokal Git med Kudu

Om du vill aktivera lokal Git-distribution för din app med Kudu build-servern kör [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) i Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Om du vill skapa en Git-aktiverad app i stället kör [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) i Cloud Shell med den `--deployment-local-git` parametern.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Den `az webapp create` kommandot ger dig något som liknar följande utdata:

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

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När du uppmanas att ange ett lösenord anger du lösenordet som du skapade i [konfigurera en distributionsanvändare](#configure-a-deployment-user). Använd inte lösenordet du använde för att logga in på Azure-portalen.

```bash
git push azure master
```

Du kan se runtime-specifika automation i utdata, till exempel MSBuild för ASP.NET, `npm install` för Node.js, och `pip install` för Python.

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till webbappen med en webbläsare för att kontrollera att innehållet distribueras.

```bash
http://<app_name>.azurewebsites.net
```

![App som körs i App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Använd hanterad identitet på andra språk

App Configuration-providrar för .NET Framework och Java Spring har också inbyggt stöd för hanterad identitet. I dessa fall kan du använda din app configuration store URL-slutpunkt i stället för dess fullständiga anslutningssträngen när du konfigurerar en provider. Till exempel för .NET Framework konsolappen skapade i snabbstarten, anger du följande inställningar i den *App.config* fil:

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

I den här självstudien har du lagt till en hanterad Azure-tjänstidentitet för att effektivisera åtkomsten till App Configuration och förbättra hanteringen av autentiseringsuppgifter för din app. Om du vill veta mer om hur du använder Appkonfiguration, fortsätter du att Azure CLI-exempel.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)

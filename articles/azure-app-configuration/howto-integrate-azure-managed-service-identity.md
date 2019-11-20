---
title: Integrera med Azure Managed Identities | Microsoft Docs
description: Lär dig hur du använder Azure Managed Identities för att autentisera med och få till gång till Azure App konfiguration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b0c6e39aebe7864ab132805b78aa7be2d61c5160
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185146"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrera med hanterade Azure-identiteter

Azure Active Directory [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) bidrar till att förenkla hanteringen av hemligheter för ditt moln program. Med en hanterad identitet kan du konfigurera din kod för att använda tjänstens huvud namn som skapades för den Azure-tjänst som den körs på. Du använder en hanterad identitet i stället för en separat autentiseringsuppgift som lagras i Azure Key Vault eller en lokal anslutnings sträng. 

Azure App konfiguration och dess .NET Core-, .NET Framework-och Java våren-klient bibliotek levereras med det hanterade identitets stödet inbyggt i dem. Även om du inte behöver använda den, eliminerar den hanterade identiteten behovet av en åtkomsttoken som innehåller hemligheter. Din kod kan komma åt appens konfigurations Arkiv enbart med tjänstens slut punkt. Du kan bädda in den här URL: en i koden direkt utan att oroa dig för att exponera någon hemlighet.

Den här självstudien visar hur du kan dra nytta av den hanterade identiteten för att komma åt appens konfiguration. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter måste du först [skapa en ASP.net Core-app med app-konfigurationen](./quickstart-aspnet-core-app.md) .

Dessutom visar den här själv studie kursen hur du kan använda den hanterade identiteten tillsammans med app-konfigurationens Key Vault referenser. På så sätt kan du sömlöst komma åt hemligheter som lagras i Key Vault och konfigurations värden i app-konfigurationen. Om du vill utforska den här funktionen kan du avsluta [använda Key Vault referenser med ASP.net Core](./use-key-vault-references-dotnet-core.md) först.

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, MacOS-och Linux-plattformarna.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Bevilja en hanterad identitets åtkomst till app-konfigurationen.
> * Konfigurera appen så att den använder en hanterad identitet när du ansluter till app-konfigurationen.
> * Du kan också konfigurera appen så att den använder en hanterad identitet när du ansluter till Key Vault via en app-konfiguration Key Vault referens.

## <a name="prerequisites"></a>Krav

Du behöver följande för att kunna slutföra den här självstudiekursen:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell kon figurer ATS](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Lägga till en hanterad identitet

Om du vill konfigurera en hanterad identitet i portalen skapar du först ett program som normalt och aktiverar sedan funktionen.

1. Skapa en App Services-instans i [Azure Portal](https://portal.azure.com) som du vanligt vis gör. Gå till den i portalen.

2. Rulla ned till **inställnings** gruppen i det vänstra fönstret och välj **identitet**.

3. Växla **status** till **på på** fliken **systemtilldelad** och välj **Spara**.

4. Svara **Ja** när du uppmanas att aktivera systemtilldelad hanterad identitet.

    ![Ange hanterad identitet i App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Bevilja åtkomst till App Configuration

1. I [Azure Portal](https://portal.azure.com)väljer du **alla resurser** och väljer det konfigurations Arkiv för app som du skapade i snabb starten.

1. Välj **Åtkomstkontroll (IAM)** .

1. På fliken **kontrol lera åtkomst** väljer du **Lägg till** i användar gränssnittet **Lägg till Roll tilldelnings** kort.

1. Under **roll**väljer du **deltagare**. Under **tilldela åtkomst till**väljer du **App Service** under **systemtilldelad hanterad identitet**.

1. Under **prenumeration**väljer du din Azure-prenumeration. Välj App Service resurs för din app.

1. Välj **Spara**.

    ![Lägga till en hanterad identitet](./media/add-managed-identity.png)

1. Valfritt: om du vill ge åtkomst till Key Vault också följer du anvisningarna i [tillhandahålla Key Vault autentisering med en hanterad identitet](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Använda en hanterad identitet

1. Hitta URL: en till appens konfigurations Arkiv genom att gå till konfigurations skärmen i Azure Portal och sedan klicka på fliken **åtkomst nycklar** .

1. Öppna *appSettings. JSON*och Lägg till följande skript. Ersätt *\<service_endpoint >* , inklusive hakparenteser, med URL: en till appens konfigurations lager. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Om du bara vill komma åt värden som lagras direkt i appens konfiguration öppnar du *program.cs*och uppdaterar metoden `CreateWebHostBuilder` genom att ersätta `config.AddAzureAppConfiguration()`-metoden.

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

1. Om du vill använda konfigurations värden för appar och Key Vault referenser öppnar du *program.cs*och uppdaterar `CreateWebHostBuilder`-metoden som visas nedan. Detta skapar en ny `KeyVaultClient` som använder en `AzureServiceTokenProvider` och skickar den här referensen till ett anrop till `UseAzureKeyVault`-metoden.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    Du kan nu komma åt Key Vault referenser precis som andra konfigurations nycklar för appar. Konfigurations leverantören använder `KeyVaultClient` som du konfigurerade för att autentisera till Key Vault och hämta värdet.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Distribuera från lokal Git

Det enklaste sättet att aktivera lokal Git-distribution för din app med kudu build-servern är att använda Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivera lokal Git med Kudu
Om du inte har en lokal git-lagringsplats för din app måste du initiera en. Det gör du genom att köra följande kommandon från appens projekt katalog:

```cmd
git init
git add .
git commit -m "Initial version"
```

Om du vill aktivera lokal Git-distribution för din app med kudu build-servern kör du [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) i Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Om du vill skapa en git-aktiverad app i stället kör du [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) i Cloud Shell med parametern `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Kommandot `az webapp create` ger dig något som liknar följande utdata:

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

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När du uppmanas att ange ett lösen ord anger du det lösen ord som du skapade i [Konfigurera en distributions användare](#configure-a-deployment-user). Använd inte det lösen ord du använder för att logga in på Azure Portal.

```bash
git push azure master
```

Du kan se körnings viss automatisering i utdata, till exempel MSBuild för ASP.NET, `npm install` för Node. js och `pip install` för python.

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till din webbapp med hjälp av en webbläsare för att kontrol lera att innehållet har distribuerats.

```bash
http://<app_name>.azurewebsites.net
```

![App som körs i App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Använd hanterad identitet på andra språk

App Configuration-providrar för .NET Framework och Java Spring har också inbyggt stöd för hanterad identitet. I dessa fall använder du programmets URL-slutpunkt i appens konfiguration i stället för dess fullständiga anslutnings sträng när du konfigurerar en provider. Om du till exempel vill skapa en app för .NET Framework-konsolen som skapats i snabb starten anger du följande inställningar i filen *app. config* :

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
I den här självstudien har du lagt till en Azure-hanterad identitet för att effektivisera åtkomst till app-konfigurationen och förbättra hantering av autentiseringsuppgifter för din app. Om du vill veta mer om hur du använder app-konfiguration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)

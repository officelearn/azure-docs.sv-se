---
title: På ett säkert sätt sparar hemliga programinställningar för en webbapp – Azure Key Vault | Microsoft Docs
description: Hur att på ett säkert sätt spara hemliga programinställningar, till exempel autentiseringsuppgifter för Azure eller från tredje part API-nycklar med ASP.NET core Key Vault-providern, användaren hemlighet eller .NET 4.7.1 configuration builders
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: cawa
ms.openlocfilehash: 6b60e03c8888ad2c9726116f1f3b2e49d9a4e1e8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722755"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>På ett säkert sätt spara hemliga programinställningar för ett webbprogram

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du sparar inställningar för hemliga program för Azure-program på ett säkert sätt.

Alla webbservrar traditionellt programkonfiguration inställningarna sparas i konfigurationsfiler, till exempel Web.config. Den här metoden leder till att checka in hemliga inställningar, till exempel molnautentiseringsuppgifter i offentliga källkontrollsystem som GitHub. Under tiden kan det vara svårt att följa bästa praxis för säkerhet på grund av det arbetet som krävs för att ändra källkoden och konfigurera om inställningar för webbprogramutveckling.

För att säkerställa utvecklingsprocessen är säker, skapas verktyg och ramverk bibliotek för att spara hemliga programinställningar på ett säkert sätt med minimala kodändringar eller inga ändringar i koden källa.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET och .NET core-program

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Spara inställningarna för hemliga i användaren hemlighet store som ligger utanför kontrollen källmapp
Om du gör en snabb prototyp eller om du inte har tillgång till internet, börja med att flytta din hemliga inställningar utanför kontrollen källmappen till användaren hemlighet store. Hemlig användararkivet är en fil som sparats användaren profiler i mappen, så att hemligheter inte checkas in till källkontroll. Följande diagram visar hur [användaren hemlighet](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) fungerar.

![Användaren hemlighet håller hemliga inställningar utanför källkontroll](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Om du använder .NET core-konsolprogram kan du använda Key Vault för att spara din hemlighet på ett säkert sätt.

### <a name="save-secret-settings-in-azure-key-vault"></a>Spara inställningarna för hemliga i Azure Key Vault
Om du utvecklar ett projekt och behöver dela källkoden på ett säkert sätt använda [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Skapa ett Nyckelvalv i Azure-prenumerationen. Fyll i alla obligatoriska fält i Användargränssnittet och på *skapa* längst ned på bladet

    ![Skapa Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Ge som du och dina gruppmedlemmar som har åtkomst till Key Vault. Om du har en stor grupp kan du skapa en [Azure Active Directory-grupp](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) och lägga till den säkerhetsgrupp åtkomstbehörighet till Key Vault. I den *hemlighet behörigheter* listrutan Kontrollera *hämta* och *lista* under *hemlighet hanteringsåtgärder*.

    ![Lägg till åtkomstprincip för Nyckelvalvet](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Lägg till din hemlighet till Key Vault på Azure-portalen. Kapslade konfigurationsinställningar, Ersätt ””: med ”--” så att det hemliga Key Vault-namnet är giltigt. ':' får inte vara namnet på ett Key Vault hemliga.

    ![Lägga till Key Vault-hemlighet](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Installera den [autentisering för Azure-Services-tillägget för Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354). Appen kan komma åt Key Vault med Visual Studio-inloggningen identitet via det här tillägget.

5. Lägg till följande NuGet-paketen i projektet:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
6. Lägg till följande kod i filen Program.cs:

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((ctx, builder) =>
            {
                var keyVaultEndpoint = GetKeyVaultEndpoint();
                if (!string.IsNullOrEmpty(keyVaultEndpoint))
                {
                    var azureServiceTokenProvider = new AzureServiceTokenProvider();
                    var keyVaultClient = new KeyVaultClient(
                        new KeyVaultClient.AuthenticationCallback(
                            azureServiceTokenProvider.KeyVaultTokenCallback));
                            builder.AddAzureKeyVault(
                            keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                        }
                    })
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
7. Lägg till Key Vault-Webbadressen launchsettings.json filen. Miljövariabeln *KEYVAULT_ENDPOINT* definieras i den kod som du lade till i steg 6.

    ![Lägg till Key Vault-Webbadressen som en miljövariabel för projektet](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Starta felsökningen av projektet. Den bör köras.

## <a name="aspnet-and-net-applications"></a>ASP.NET och .NET-program

.NET 4.7.1 stöder Key Vault och hemlighet configuration builders, vilket säkerställer att hemligheter kan flyttas utanför källmapp kontroll utan att kodändringar krävs.
Att fortsätta, [ladda ned .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) och migrera ditt program om det använder en äldre version av .NET framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Spara inställningarna för hemliga i en hemlighetsfilen som ligger utanför kontrollen källmapp
Om du skriver en snabb prototyp och inte vill gå med det här alternativet om du vill etablera Azure-resurser.

1. Installera följande NuGet-paketet i projektet
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic
    ```

2. Skapa en fil som liknar Följ. Spara den under en plats utanför projektmappen.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Definiera hemlighetsfilen för att vara en konfiguration builder i Web.config-filen. Placera det här avsnittet innan du *appSettings* avsnittet.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Ange appSettings avsnittet använder hemliga configuration builder. Kontrollera att det finns inga poster för hemliga inställningen med ett värde för dummy.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Felsök din app. Den bör köras.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Spara inställningarna för hemliga i ett Azure Key Vault
Följ instruktionerna i ASP.NET core-avsnittet och konfigurera ett Key Vault för ditt projekt.

1. Installera följande NuGet-paketet i projektet
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Definiera Key Vault configuration builder i Web.config. Placera det här avsnittet innan du *appSettings* avsnittet. Ersätt *vaultName* är Key Vault-namn om Key Vault är i offentlig Azure eller fullständiga URI om du använder nationellt moln.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. Ange appSettings avsnittet använder Key Vault configuration builder. Kontrollera att det finns inga poster för hemliga inställningen med ett värde för dummy.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Starta felsökningen av projektet. Den bör köras.

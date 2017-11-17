---
title: "Spara hemliga programinställningar för ett webbprogram på ett säkert sätt | Microsoft Docs"
description: "Hur på ett säkert sätt spara hemliga programinställningar, till exempel autentiseringsuppgifter för Azure eller tredje parts API med hjälp av ASP.NET core Key Vault-providern, användaren hemlighet eller .NET 4.7.1 configuration builders"
services: visualstudio
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: 
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cawa
ms.openlocfilehash: 61540aeb0f1ac7c55b064c81494208e58aeaa2e0
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>På ett säkert sätt spara hemliga programinställningar för ett webbprogram

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du sparar på ett säkert sätt hemliga programmet konfigurationsinställningar för Azure-program.

Alla web traditionellt programkonfigurationen inställningarna sparas i konfigurationsfiler, till exempel Web.config. Den här övningen leder till att checka in hemliga inställningar, till exempel molnet autentiseringsuppgifter till offentliga kontrollen källsystem som Github. Under tiden kan det vara svårt att följa bästa praxis för säkerhet på grund av det arbetet som krävs för att ändra källkoden och konfigurera inställningar för webbprogramutveckling.

Om du vill säkerställa att utvecklingsprocessen är säker, skapas verktygsuppsättning och framework bibliotek om du vill spara hemliga programinställningar på ett säkert sätt med minimal eller ingen källa kodändring.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET och .NET core-program

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Spara inställningarna för hemliga i användaren hemlighet store som ligger utanför kontrollen källmapp
Om du gör en snabb prototyp eller om du inte har tillgång till internet, börja med flytta inställningarna hemliga utanför källmapp kontroll till användaren hemlighet store. Hemlig användararkivet är en fil som sparats under användarens profileraren mapp så hemligheter inte kontrolleras till källkontroll. Följande diagram visar hur [användaren hemlighet](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?tabs=visual-studio#SecretManager) fungerar.

![Användaren hemlighet håller hemliga inställningar utanför källkontroll](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Om du kör .NET core-konsolprogram kan du använda Nyckelvalv för att spara ditt hemliga på ett säkert sätt.

### <a name="save-secret-settings-in-azure-key-vault"></a>Spara inställningarna för hemliga i Azure Key Vault
Om du utvecklar ett grupprojekt och behöver dela källkoden på ett säkert sätt använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Skapa ett Nyckelvalv i din Azure-prenumeration. Fyll i alla obligatoriska fält i Användargränssnittet och på *skapa* längst ned på bladet

    ![Skapa Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Bevilja du och dina gruppmedlemmar åtkomst till Nyckelvalvet. Om du har en stor grupp, kan du skapa en [Azure Active Directory-grupp](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-groups-create-azure-portal) och lägga till den säkerhetsgrupp åtkomstbehörighet till Nyckelvalvet. I den *hemlighet behörigheter* listrutan Kontrollera *hämta* och *lista* under *hemlighet hanteringsåtgärder*.

    ![Lägg till princip för Key Vault-åtkomst](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Lägga till ditt hemliga Key Vault på Azure-portalen. Kapslade konfigurationsinställningar, Ersätt ':' med '--' så Key Vault hemliga namnet är giltigt. ':' får inte vara i ett Nyckelvalv hemliga.

    ![Lägg till Key Vault hemlighet](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Installera den [Azure Services Authentication tillägget för Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354). Appen har åtkomst till Nyckelvalvet med Visual Studio-inloggning identitet via det här tillägget.

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
7. Lägg till din Webbadressen till Nyckelvalvet launchsettings.json filen. Miljövariabeln *KEYVAULT_ENDPOINT* har definierats i den kod som du lade till i steg 6.

    ![Lägga till Webbadressen till Nyckelvalvet som en miljövariabel för projektet](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Starta felsökningen av projektet. Det ska köras.

## <a name="aspnet-and-net-applications"></a>ASP.NET och .NET-program

.NET 4.7.1 stöder Key Vault och hemlighet configuration builders, vilket säkerställer att hemligheter kan flyttas utanför källmapp kontroll utan kodändringar.
Att fortsätta, [hämta .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) och migrera dina program om det använder en äldre version av .NET framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Spara hemliga inställningar i en hemlig fil som ligger utanför kontrollen källmapp
Gå med det här alternativet om du skriver en snabb prototyp och inte vill att etablera Azure-resurser.

1. Installera följande NuGet-paket i projektet
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic.1.0.0-alpha1.nupkg
    ```

2. Skapa en fil som liknar följande. Spara den under en plats utanför projektmappen.

    ```xml

       <root>
              <secrets ver="1.0">
                     <secret name="secret1" value="foo_one" />
                        <secret name="secret2" value="foo_two" />
                       </secrets>
      </root>
      ```

3. Definiera den hemliga fil att en konfiguration builder i Web.config-filen. Placera det här avsnittet innan *appSettings* avsnitt.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Ange appSettings avsnittet använder hemliga configuration builder. Kontrollera att det finns någon post för inställningen hemliga med ett värde för dummy.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Felsöka appen. Det ska köras.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Spara hemliga inställningar i en Azure Key Vault
Följ instruktionerna från ASP.NET core avsnittet för att konfigurera ett Nyckelvalv för projektet.

1. Installera följande NuGet-paket i projektet
```
Microsoft.Configuration.ConfigurationBuilders.Azure.1.0.0-alpha1.nupkg
```

2. Definiera Key Vault configuration builder i Web.config. Placera det här avsnittet innan *appSettings* avsnitt. Ersätt *vaultName* ska Key Vault-namn om Key Vault är offentlig Azure eller fullständig URI om du använder suveräna molnet.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="KeyVault" vaultName="Test911" clientId="13c7e116-eb8d-4c12-920a-0093e5f6e33e" clientSecret="8EnMWiciE0wNinNGW7mbylZ3BnaNu7ZeafaC3x+wdCc=" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3.  Ange appSettings avsnittet använder Key Vault configuration builder. Kontrollera att det finns någon post för inställningen hemliga med ett värde för dummy.

    ```xml
    <appSettings configBuilders="AzureKeyVault">
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
        <add key="secret" value="" />
    </appSettings>
    ```

4. Starta felsökningen av projektet. Det ska köras.

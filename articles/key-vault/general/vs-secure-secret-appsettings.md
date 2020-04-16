---
title: Spara hemliga programinställningar för ett webbprogram på ett säkert sätt – Azure Key Vault | Microsoft-dokument
description: Så här sparar du hemliga programinställningar som Azure-autentiseringsuppgifter eller API-nycklar från tredje part med ASP.NET grundläggande Key Vault Provider-, User Secret- eller .NET 4.7.1-konfigurationsbyggare
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: bcacd5d2ed9e325383ec7ae75002ae0a6213111c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429764"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Spara hemliga programinställningar för ett webbprogram på ett säkert sätt

## <a name="overview"></a>Översikt
I den här artikeln beskrivs hur du på ett säkert sätt sparar hemliga programkonfigurationsinställningar för Azure-program.

Traditionellt sparas alla konfigurationsinställningar för webbprogram i konfigurationsfiler som Web.config. Den här metoden leder till att du kontrollerar hemliga inställningar som molnautentiseringsuppgifter till styrsystem för offentliga källor som GitHub. Under tiden kan det vara svårt att följa bästa säkerhetspraxis på grund av de omkostnader som krävs för att ändra källkod och konfigurera om utvecklingsinställningar.

För att säkerställa att utvecklingsprocessen är säker skapas verktygs- och rambibliotek för att spara programhemliga inställningar på ett säkert sätt med minimal eller ingen källkodsändring.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET- och .NET Core-program

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Spara hemliga inställningar i User Secret-arkivet som ligger utanför källkontrollmappen
Om du gör en snabb prototyp eller om du inte har tillgång till internet, börja med att flytta dina hemliga inställningar utanför källa kontrollmapp till User Secret store. User Secret Store är en fil som sparats under mappen användare profiler, så hemligheter inte checkas in till källkontroll. Följande diagram visar hur [Användarhemlighet](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) fungerar.

![Användarhemlighet håller hemliga inställningar utanför källkontrollen](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Om du kör .NET core console-programmet använder du Key Vault för att spara hemligheten på ett säkert sätt.

### <a name="save-secret-settings-in-azure-key-vault"></a>Spara hemliga inställningar i Azure Key Vault
Om du utvecklar ett projekt och behöver dela källkoden på ett säkert sätt använder du [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Skapa ett Key Vault i din Azure-prenumeration. Fyll i alla obligatoriska fält i användargränssnittet och klicka på *Skapa* längst ned på bladet

    ![Skapa Azure Key Vault](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Ge dig och dina gruppmedlemmar tillgång till Key Vault. Om du har ett stort team kan du skapa en [Azure Active Directory-grupp](../../active-directory/active-directory-groups-create-azure-portal.md) och lägga till säkerhetsgruppsåtkomsten till Nyckelvalvet. I listrutan *Hemliga behörigheter* markerar du *Hämta* och *lista* under *Hemliga hanteringsåtgärder*.
Om du redan har skapat din webbapp ger du webbappen åtkomst till Key Vault så att den kan komma åt nyckelvalvet utan att lagra hemlig konfiguration i Appinställningar eller filer. Sök efter din webbapp efter namnet och lägg till den på samma sätt som du ger användarna åtkomst.

    ![Lägg till åtkomstprincip för Key Vault](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Lägg till din hemlighet i Key Vault på Azure-portalen. För kapslade konfigurationsinställningar ersätter du ':' med '--' så att det hemliga namnet för Key Vault är giltigt. ':' är inte tillåtet att vara i namn av en Key Vault hemlighet.

    ![Lägg till Key Vault-hemlighet](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Innan Visual Studio 2017 V15.6 vi brukade rekommendera att installera Azure Services autentiseringstillägg för Visual Studio. Men det är föråldrad nu som funktionaliteten är integrerad i Visual Studio . Om du använder en äldre version av visual Studio 2017 föreslår vi därför att du uppdaterar till minst VS 2017 15.6 eller uppåt så att du kan använda den här funktionen internt och komma åt key-valvet från att använda själva Visual Studio-inloggningsidentiteten.
    >

4. Lägg till följande NuGet-paket i projektet:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Lägg till följande kod i Program.cs-fil:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
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
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Lägg till url:en för Key Vault i filen launchsettings.json. Miljövariabelnamnet *KEYVAULT_ENDPOINT* definieras i koden som du lade till i steg 6.

    ![Lägga till URL för Nyckelvalv som en projektmiljövariabel](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Börja felsöka projektet. Det bör köras framgångsrikt.

## <a name="aspnet-and-net-applications"></a>ASP.NET- och .NET-program

.NET 4.7.1 stöder konfigurationsbyggare för Key Vault och Secret, vilket säkerställer att hemligheter kan flyttas utanför källkontrollmappen utan kodändringar.
Om du vill fortsätta [laddar du ned .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) och migrerar programmet om det använder en äldre version av .NET-ramverket.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Spara hemliga inställningar i en hemlig fil som ligger utanför källkontrollmappen
Om du skriver en snabb prototyp och inte vill etablera Azure-resurser går du med det här alternativet.

1. Installera följande NuGet-paket i projektet
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
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

3. Definiera den hemliga filen som en konfigurationsbyggare i filen Web.config. Placera det här avsnittet före *avsnittet appSettings.*

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Ange avsnittet appSettings använder den hemliga konfigurationsverktyget. Kontrollera att det finns en post för den hemliga inställningen med ett dummy-värde.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Felsöka appen. Det bör köras framgångsrikt.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Spara hemliga inställningar i ett Azure Key Vault
Följ instruktionerna från ASP.NET kärnavsnitt för att konfigurera ett Nyckelvalv för projektet.

1. Installera följande NuGet-paket i projektet
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Definiera konfigurationsverktyget för Nyckelvalv i Web.config. Placera det här avsnittet före *avsnittet appSettings.* Ersätt *vaultName* till att vara Key Vault-namnet om ditt Key Vault finns i offentliga Azure eller fullständig URI om du använder Sovereign-molnet.

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
3. Ange avsnittet AppSettings använder konfigurationsverktyget för Nyckelvalv. Kontrollera att det finns någon post för den hemliga inställningen med ett dummy-värde.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Börja felsöka projektet. Det bör köras framgångsrikt.

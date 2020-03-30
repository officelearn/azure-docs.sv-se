---
title: Lägg till Support för Key Vault i ditt ASP.NET projekt med Visual Studio – Azure Key Vault | Microsoft-dokument
description: Använd den här självstudien för att lära dig hur du lägger till Stöd för Key Vault i ett ASP.NET eller ASP.NET Core-webbprogram.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 1c12cf4a7bd097c6d33d032065734b477920644b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456995"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Lägg till Key Vault i webbprogrammet med hjälp av Visual Studio Connected Services

I den här självstudien får du lära dig hur du enkelt lägger till allt du behöver för att börja använda Azure Key Vault för att hantera dina hemligheter för webbprojekt i Visual Studio, oavsett om du använder ASP.NET Core eller någon typ av ASP.NET projekt. Genom att använda funktionen Anslutna tjänster i Visual Studio kan du låta Visual Studio automatiskt lägga till alla NuGet-paket och konfigurationsinställningar som du behöver för att ansluta till Key Vault i Azure.

Mer information om de ändringar som Anslutna tjänster gör i projektet för att aktivera Key Vault, finns i [Key Vault Connected Service - Vad hände med mitt ASP.NET 4.7.1-projekt](#how-your-aspnet-framework-project-is-modified) eller [Key Vault Connected Service - Vad hände med mitt ASP.NET Core-projekt](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Krav

- **En Azure-prenumeration**. Om du inte har en prenumeration registrerar du dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 version 16.3** eller senare, eller **Visual Studio 2017 version 15.7** med **arbetsbelastningen webbutveckling** installerad. [Ladda ned det nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- För ASP.NET (inte Core) med Visual Studio 2017 behöver du .NET Framework 4.7.1 eller senare Utvecklingsverktyg, som inte installeras som standard. Om du vill installera dem startar du Visual Studio Installer, väljer **Ändra**och väljer sedan **Enskilda komponenter**, expanderar sedan ASP.NET **och webbutveckling**till höger och väljer **.NET Framework 4.7.1 Development Tools**.
- Ett ASP.NET 4.7.1 eller senare, eller ASP.NET Core 2.0 eller senare webbprojekt öppet.

## <a name="add-key-vault-support-to-your-project"></a>Lägg till Stöd för Key Vault i projektet

Innan du börjar kontrollerar du att du är inloggad i Visual Studio. Logga in med samma konto som du använder för din Azure-prenumeration. Öppna sedan ett ASP.NET 4.7.1 eller senare, eller ASP.NET Core 2.0-webbprojekt och gör följande:

1. Högerklicka på det projekt som du vill lägga till stöd för Key Vault i **i Solution Explorer**och välj Lägg **till** > **ansluten tjänst**.
   Sidan Ansluten tjänst visas med tjänster som du kan lägga till i projektet.
1. Välj Säkra hemligheter med **Azure Key Vault**på menyn för tillgängliga tjänster .

   ![Välj "Säkra hemligheter med Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Välj den prenumeration du vill använda och välj sedan ett nytt eller befintligt Nyckelvalv. Om du väljer det nya Nyckelvalvet visas en **redigeringslänk.** Välj den för att konfigurera ditt nya Key Vault.

   ![Välj din prenumeration](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. Ange det namn som du vill använda för Key Vault i **Redigera Azure Key Vault.**

1. Välj en befintlig **resursgrupp**eller välj att skapa en ny med ett automatiskt genererat unikt namn.  Om du vill skapa en ny grupp med ett annat namn kan du använda [Azure-portalen](https://portal.azure.com)och sedan stänga sidan och starta om för att läsa in listan över resursgrupper igen.
1. Välj den **plats** där nyckelvalvet ska skapas. Om ditt webbprogram finns i Azure väljer du den region som är värd för webbprogrammet för optimal prestanda.
1. Välj en **prisnivå**. Mer information finns i [Prissättning av Nyckelvalv](https://azure.microsoft.com/pricing/details/key-vault/).
1. Välj **OK** för att acceptera konfigurationsalternativen.
1. När du har valt ett befintligt Nyckelvalv eller har konfigurerat ett nytt Nyckelvalv väljer du **Lägg till** tjänsten Lägg till i fliken Ansluten tjänst på fliken Azure **Key Vault** i Visual Studio.
1. Välj länken **Hantera hemligheter som lagras i den här key vault-länken** för att öppna sidan **Hemligheter** för key vault. Om du stängt sidan eller projektet kan du navigera till den i [Azure-portalen](https://portal.azure.com) genom att välja **Alla tjänster** och under **Säkerhet**välja **Nyckelvalv**och sedan välja ditt Nyckelvalv.
1. I avsnittet Nyckelvalv för det nyckelvalv som du skapade väljer du **Hemligheter**och sedan **generera/importera**.

   ![Generera/importera en hemlighet](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Ange en hemlighet, till exempel *MySecret* och ge den vilket strängvärde som helst som ett test, välj sedan knappen **Skapa.**

   ![Skapa en hemlighet](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (valfritt) Ange en annan hemlighet, men den här gången lägga den i en kategori genom att namnge den *Secrets - MySecret*. Den här syntaxen anger en kategori "Hemligheter" som innehåller en hemlig "MySecret".

Nu kan du komma åt dina hemligheter i kod. Nästa steg är olika beroende på om du använder ASP.NET 4.7.1 eller ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Få tillgång till dina hemligheter i kod (ASP.NET Core)

1. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. På fliken **Bläddra** letar du reda på och installerar dessa två NuGet-paket: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och för .NET Core 2, lägg till [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) eller för .NET Core 3, lägg till[Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core).

1. För .NET Core 2 `Program.cs` väljer du `BuildWebHost` fliken och ändrar definitionen i klassen Program till följande:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
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
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   Använd följande kod för .NET Core 3.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Öppna en av sidfilerna nästa gång, till exempel *Index.cshtml.cs* och skriv följande kod:
   1. Inkludera en `Microsoft.Extensions.Configuration` hänvisning till detta med hjälp av direktiv:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Lägg till konfigurationsvariabeln.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Lägg till den här konstruktorn eller ersätt den befintliga konstruktorn med följande:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Uppdatera `OnGet`-metoden. Uppdatera platshållarvärdet som visas här med det hemliga namn som du skapade i ovanstående kommandon.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Om du vill bekräfta värdet vid `ViewData["Message"]` körning lägger du till kod som ska visas i *Cshtml-filen* för att visa hemligheten i ett meddelande.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Du kan köra appen lokalt för att kontrollera att hemligheten har hämtats från Key Vault.

## <a name="access-your-secrets-aspnet"></a>Få tillgång till dina hemligheter (ASP.NET)

Du kan ställa in konfigurationen så att filen web.config har ett dummy-värde i elementet `appSettings` som ersätts av det verkliga värdet vid körning. Du kan sedan komma `ConfigurationManager.AppSettings` åt detta via datastrukturen.

1. Redigera filen web.config.  Leta reda på appenSettings-taggen, lägg till ett attribut `configBuilders="AzureKeyVault"`och lägg till en rad:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Redigera `About` metoden i *HomeController.cs*för att visa värdet för bekräftelse.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Kör appen lokalt under felsökningskryparen, växla till fliken **Om** och kontrollera att värdet från Nyckelvalvet visas.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resursgruppen. Detta tar bort Nyckelvalvet och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPNAMN:** anger du namnet på resursgruppen och väljer **Ta bort**.

## <a name="troubleshooting"></a>Felsökning

Om key vault körs på ett annat Microsoft-konto än det du är inloggad på Visual Studio (till exempel visas Key Vault på ditt arbetskonto, men Visual Studio använder ditt privata konto) får du ett felmeddelande i din Program.cs-fil , att Visual Studio inte kan få tillgång till Key Vault. Så här åtgärdar du problemet:

1. Gå till [Azure-portalen](https://portal.azure.com) och öppna ditt Key Vault.

1. Välj **Åtkomstprinciper**och lägg sedan **till åtkomstprincip**och välj det konto som du är inloggad med som huvudnamn.

1. Välj > **Filkontoinställningar**i Visual Studio . **File**
Välj **Lägg till ett konto** i avsnittet Alla **konton.** Logga in med det konto som du har valt som huvudman för din åtkomstpolicy.

1. Välj **Verktygsalternativ** > **Options**och leta efter **Azure Service Authentication**. Välj sedan det konto som du just har lagt till i Visual Studio.

Nu, när du felsöker ditt program, ansluter Visual Studio till det konto som key vault finns på.

## <a name="how-your-aspnet-core-project-is-modified"></a>Så här ändras ditt ASP.NET Core-projekt

I det här avsnittet identifieras de exakta ändringar som gjorts i ett ASP.NET projekt när du lägger till den anslutna tjänsten Key Vault med Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Lade till referenser för ASP.NET Core

Påverkar projektfilen .NET-referenser och NuGet-paketreferenser.

| Typ | Referens |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Lade till filer för ASP.NET Core

- `ConnectedService.json`som registrerar viss information om leverantören av Ansluten tjänst, version och en länk till dokumentationen.

### <a name="project-file-changes-for-aspnet-core"></a>Projektfiländringar för ASP.NET Core

- Lade till itemgroup `ConnectedServices.json` och fil för anslutna tjänster.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.json förändringar för ASP.NET Core

- Lade till följande miljövariabelposter i både IIS Express-profilen och profilen som matchar ditt webbprojektnamn:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Ändringar på Azure för ASP.NET Core

- Skapade en resursgrupp (eller använde en befintlig).
- Skapade ett nyckelvalv i den angivna resursgruppen.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Så här ändras ditt ASP.NET Framework-projekt

I det här avsnittet identifieras de exakta ändringar som gjorts i ett ASP.NET projekt när du lägger till den anslutna tjänsten Key Vault med Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Lade till referenser för ASP.NET Framework

Påverkar projektfilen .NET-referenser `packages.config` och (NuGet-referenser).

| Typ | Referens |
| --- | --- |
| .NET; NuGet (nuget) | Microsoft.Azure.KeyVault |
| .NET; NuGet (nuget) | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet (nuget) | Microsoft.Rest.ClientRuntime |
| .NET; NuGet (nuget) | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Lade till filer för ASP.NET Framework

- `ConnectedService.json`som registrerar viss information om leverantören av Ansluten tjänst, version och en länk till dokumentationen.

### <a name="project-file-changes-for-aspnet-framework"></a>Projektfiländringar för ASP.NET Framework

- Lade till filen Connected Services ItemGroup och ConnectedServices.json.
- Referenser till .NET-sammansättningar som beskrivs i avsnittet [Tillagda referenser.](#added-references-for-aspnet-framework)

### <a name="webconfig-or-appconfig-changes"></a>web.config eller app.config ändringar

- Lade till följande konfigurationsposter:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Ändringar på Azure för ASP.NET Framework

- Skapade en resursgrupp (eller använde en befintlig).
- Skapade ett nyckelvalv i den angivna resursgruppen.

## <a name="next-steps"></a>Nästa steg

Om du har följt den här självstudien konfigureras dina behörigheter för Key Vault för att köras med din egen Azure-prenumeration, men det kanske inte är önskvärt för ett produktionsscenario. Du kan skapa en hanterad identitet för att hantera Key Vault-åtkomst för din app. Se [Tillhandahålla nyckelvalvsautentisering med en hanterad identitet](/azure/key-vault/managed-identity).

Läs mer om utveckling av Key Vault genom att läsa [Key Vault Developer's Guide](key-vault-developers-guide.md).

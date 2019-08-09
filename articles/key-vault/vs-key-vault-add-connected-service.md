---
title: Lägg till Key Vault stöd för ditt ASP.NET-projekt med Visual Studio-Azure Key Vault | Microsoft Docs
description: I den här självstudien får du lära dig hur du lägger till Key Vault support till ett ASP.NET-eller ASP.NET Core-webbprogram.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 27c21171c2a53cb739215dcae070b94c8610a490
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880928"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Lägg till Key Vault i ditt webb program med hjälp av Visual Studio Connected Services

I den här självstudien får du lära dig hur du enkelt kan lägga till allt du behöver för att börja använda Azure Key Vault för att hantera dina hemligheter för webb projekt i Visual Studio, oavsett om du använder ASP.NET Core eller någon typ av ASP.NET-projekt. Genom att använda funktionen anslutna tjänster i Visual Studio kan du låta Visual Studio automatiskt lägga till alla NuGet-paket och konfigurations inställningar som du behöver för att ansluta till Key Vault i Azure.

Mer information om de ändringar som anslutna tjänster gör i projektet för att aktivera Key Vault finns i [Key Vault Connected service – vad hände med mitt ASP.NET 4.7.1-projekt](#how-your-aspnet-framework-project-is-modified) eller [Key Vault Connected service – vad hände med mitt ASP.net Core-projekt](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Förutsättningar

- **En Azure-prenumeration**. Om du inte har någon prenumeration kan du registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/).
- **Visual studio 2019 version 16,3 Preview 1** eller senare, eller **Visual Studio 2017 version 15,7** med arbets belastningen **webb utveckling** installerad. [Ladda ned det nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- För ASP.NET (inte kärnan) med Visual Studio 2017 behöver du .NET Framework 4.7.1 eller senare utvecklingsverktyg, som inte installeras som standard. Installera dem genom att starta Visual Studio Installer, välja **ändra**och sedan välja **enskilda komponenter**, sedan expandera **ASP.net och webb utveckling**på den högra sidan och välja **.NET Framework 4.7.1 utvecklingsverktyg** .
- En ASP.NET 4.7.1 eller senare, eller ASP.NET Core 2,0-webbprojektet öppnas.

## <a name="add-key-vault-support-to-your-project"></a>Lägg till Key Vault stöd för ditt projekt

Innan du börjar ska du kontrol lera att du är inloggad i Visual Studio. Logga in med samma konto som du använder för din Azure-prenumeration. Öppna sedan en ASP.NET 4.7.1 eller senare eller ASP.NET Core 2,0-webbprojektet och utför följande steg:

1. I **Solution Explorer** väljer du **Lägg till** > **Ansluten tjänst**.
   Sidan Ansluten tjänst visas med tjänster som du kan lägga till i projektet.
1. På menyn med tillgängliga tjänster väljer du **skydda hemligheter med Azure Key Vault**.

   ![Välj "säkra hemligheter med Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Välj den prenumeration som du vill använda och välj sedan en ny eller befintlig Key Vault. Om du väljer det nya Key Vault visas en **redigerings** länk. Välj den för att konfigurera din nya Key Vault.

   ![Välj din prenumeration](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. I **redigera Azure Key Vault**anger du det namn som du vill använda för Key Vault.

1. Välj en befintlig **resurs grupp**eller Välj att skapa en ny med ett automatiskt genererat unikt namn.  Om du vill skapa en ny grupp med ett annat namn kan du använda [Azure Portal](https://portal.azure.com)och sedan stänga sidan och starta om för att läsa in listan över resurs grupper igen.
1. Välj den **plats** där du vill skapa Key Vault. Om ditt webb program finns i Azure väljer du den region som är värd för webb programmet för optimala prestanda.
1. Välj en **pris nivå**. Mer information finns i [Key Vault prissättning](https://azure.microsoft.com/pricing/details/key-vault/).
1. Välj **OK** för att acceptera konfigurations alternativen.
1. När du har valt en befintlig Key Vault eller konfigurerat en ny Key Vault går du till fliken **Azure Key Vault** i Visual Studio och väljer **Lägg till** för att lägga till den anslutna tjänsten.
1. Välj den **Hantera hemligheter som lagras i den här Key Vault** -länken för att öppna sidan **hemligheter** för din Key Vault. Om du stängde sidan eller projektet kan du navigera till den i [Azure Portal](https://portal.azure.com) genom att välja **alla tjänster** och under **säkerhet**väljer du **Key Vault**, och sedan väljer du Key Vault.
1. I avsnittet Key Vault för nyckel valvet som du skapade väljer du **hemligheter**och sedan **generera/importera**.

   ![Generera/importera en hemlighet](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Ange en hemlighet, till exempel *hemligt* , och ge det valfritt sträng värde som ett test och välj sedan knappen **skapa** .

   ![Skapa en hemlighet](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. valfritt Ange en annan hemlighet, men den här gången lägger den till en kategori genom att ge den namnet *hemligheter – hemligt*. Den här syntaxen anger en kategori "hemligheter" som innehåller en hemlig "hemlig".

Nu kan du komma åt dina hemligheter i kod. Nästa steg varierar beroende på om du använder ASP.NET 4.7.1 eller ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Få åtkomst till dina hemligheter i kod

1. I Solution Explorer högerklickar du på projektet och väljer **Hantera NuGet-paket**. På fliken **Bläddra** letar du reda på och installerar dessa två NuGet-paket: [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och [Microsoft. Azure. nyckel valv](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

1. `Program.cs` Välj fliken och ersätt program klassen med följande kod:

   ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

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
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

1. Nästa öppna `About.cshtml.cs` fil och skriv följande kod:
   1. Inkludera en referens till `Microsoft.Extensions.Configuration` med instruktionen:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Lägg till den här konstruktorn:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. `OnGet` Uppdatera metoden. Uppdatera plats hållarens värde som visas här med det hemliga namn som du skapade i kommandona ovan.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Kör appen lokalt genom att bläddra till sidan om. Du bör se att ditt hemliga värde hämtades.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resursgruppen. Detta tar bort Key Vault och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **Skriv resurs gruppens namn:** anger du namnet på resurs gruppen och väljer **ta bort**.

## <a name="how-your-aspnet-core-project-is-modified"></a>Så här ändras ditt ASP.NET Core-projekt

Det här avsnittet identifierar de exakta ändringar som gjorts i ett ASP.NET-projekt när du lägger till den Key Vault anslutna tjänsten med Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Tillagda referenser för ASP.NET Core

Påverkar projekt filens .NET-referenser och NuGet-paket referenser.

| type | Referens |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Filer har lagts till för ASP.NET Core

- `ConnectedService.json`tillagt, som innehåller information om den anslutna tjänst leverantören, versionen och en länk till dokumentationen.

### <a name="project-file-changes-for-aspnet-core"></a>Projekt fil ändringar för ASP.NET Core

- De anslutna tjänsterna ItemGroup och `ConnectedServices.json` File har lagts till.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings. JSON-ändringar för ASP.NET Core

- Följande miljövariabel poster har lagts till i både den IIS Express profilen och den profil som matchar ditt webb projekt namn:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Ändringar på Azure för ASP.NET Core

- Skapade en resurs grupp (eller använt en befintlig).
- Skapade en Key Vault i den angivna resurs gruppen.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Så här ändras ditt ASP.NET Framework-projekt

Det här avsnittet identifierar de exakta ändringar som gjorts i ett ASP.NET-projekt när du lägger till den Key Vault anslutna tjänsten med Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Tillagda referenser för ASP.NET Framework

Påverkar projekt filens .net-referenser `packages.config` och (NuGet-referenser).

| type | Referens |
| --- | --- |
| Nettotid NuGet | Microsoft.Azure.KeyVault |
| Nettotid NuGet | Microsoft.Azure.KeyVault.WebKey |
| Nettotid NuGet | Microsoft.Rest.ClientRuntime |
| Nettotid NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Filer har lagts till för ASP.NET Framework

- `ConnectedService.json`tillagt, som registrerar viss information om den anslutna tjänst leverantören, versionen och en länk till dokumentationen.

### <a name="project-file-changes-for-aspnet-framework"></a>Projekt fil ändringar för ASP.NET Framework

- Har lagt till de anslutna tjänsterna ItemGroup och ConnectedServices. JSON-filen.
- Referenser till .NET-sammansättningar som beskrivs i avsnittet [tillagda referenser](#added-references-for-aspnet-framework) .

### <a name="webconfig-or-appconfig-changes"></a>ändringar i Web. config eller app. config

- Följande konfigurations poster har lagts till:

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Ändringar i Azure för ASP.NET Framework

- Skapade en resurs grupp (eller använt en befintlig).
- Skapade en Key Vault i den angivna resurs gruppen.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du Key Vault utveckling genom att läsa [Key Vault Developer ' s guide](key-vault-developers-guide.md).

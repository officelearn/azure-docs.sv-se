---
title: Lägger till stöd för Key Vault i ASP.NET-projektet med Visual Studio | Microsoft Docs
description: Använd den här självstudiekursen för att lära dig hur du lägger till stöd för Key Vault i ett ASP.NET eller ASP.NET Core-webbprogram.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: c90ef26c0170db67b1d422701b6969ca3f9c9e38
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958524"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Lägg till Key Vault i ditt webbprogram med hjälp av Visual Studio Connected Services

I den här självstudien får lära du dig att enkelt lägga till allt du behöver för att börja använda Azure Key Vault för att hantera dina hemligheter för webbprojekt i Visual Studio om du använder ASP.NET Core eller alla typer av ASP.NET-projekt. Du kan ha Visual Studio automatiskt lägga till alla NuGet-paket och konfigurationsinställningar som du behöver ansluta till Key Vault i Azure med hjälp av funktionen Connected Services i Visual Studio 2017. 

Mer information om ändringarna att Connected Services gör i ditt projekt för att aktivera Key Vault finns [Key Vault Connected Service - vad hände med mitt ASP.NET 4.7.1 projekt](vs-key-vault-aspnet-what-happened.md) eller [Key Vault Connected Service - vad hände med mitt ASP.NET Core-projekt](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Förutsättningar

- **En Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 version 15.7** med arbetsbelastningen **Webbutveckling** installerad. [Ladda ned det nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- För ASP.NET (inte Core) behöver du utvecklingsverktygen .NET Framework 4.7.1 som inte installeras som standard. Starta installationsprogrammet för Visual Studio för att installera dem, Välj **ändra**, och välj sedan **enskilda komponenter**, sedan till höger, expandera **ASP.NET och webbutveckling**, och välj **utvecklingsverktyg för .NET Framework 4.7.1**.
- En ASP.NET 4.7.1 eller ASP.NET Core 2.0 webbprojekt som är öppna.

## <a name="add-key-vault-support-to-your-project"></a>Lägg till stöd för Key Vault i projektet

1. I **Solution Explorer** väljer du **Lägg till** > **Ansluten tjänst**.
   Sidan Ansluten tjänst visas med tjänster som du kan lägga till i projektet.
1. Meny med olika tjänster, Välj **skydda hemligheter med Azure Key Vault**.

   ![Välj ”säker hemligheter med Azure Key Vault”](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Om du har loggat in till Visual Studio och har en Azure-prenumeration som är associerad med ditt konto, visas en sida med en listruta med dina prenumerationer. Kontrollera att du har loggat in Visual Studio och att konton som du har loggat in med samma konto som du använder för din Azure-prenumeration.

1. Välj den prenumeration som du vill använda och välj sedan en ny eller befintlig Key Vault eller Välj länken Redigera för att ändra det automatiskt genererade namnet.

   ![Välj din prenumeration](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Ange namnet som du vill använda för Key Vault.

   ![Byt namn på Key Vault och välj en resursgrupp](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Välj en befintlig resursgrupp eller välja att skapa en ny med ett automatiskt genererade unika namnet.  Om du vill skapa en ny grupp med ett annat namn kan du använda den [Azure-portalen](https://portal.azure.com), och sedan stänga sidan och startas om för att läsa in listan över resursgrupper.
1. Välj den region där du vill skapa Key Vault. Om ditt webbprogram finns i Azure, väljer du den region som är värd för webbprogram för optimala prestanda.
1. Välj en prismodell. Mer information finns i [priser för Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Välj OK om du vill acceptera konfigurationsalternativ.
1. Välj **Lägg till** att skapa Key Vault. Skapandeprocessen misslyckas om du väljer ett namn som redan används.  Om detta händer, Använd den **redigera** länk för att byta namn på Key Vault och försök igen.

   ![Att lägga till ansluten tjänst i projektet](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Lägg nu till en hemlighet i Key Vault i Azure. Gå till rätt plats i portalen genom att klicka på länken för hantera hemligheter som lagras i den här Key Vault. Om du har stängt sidan eller projektet du kan navigera till den i den [Azure-portalen](https://portal.azure.com) genom att välja **alla tjänster**under **Security**, Välj **Key Vault**, välj sedan det Nyckelvalv som du nyss skapade.

   ![Gå till portalen](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Valvet du skapade i avsnittet Key Vault för nyckeln, Välj **hemligheter**, sedan **generera/importera**.

   ![Generera/importera en hemlighet](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Ange en hemlighet, till exempel ”MinHemlighet” och ge den ett värde som ett test, och välj sedan den **skapa** knappen.

   ![Skapa en hemlighet](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (valfritt) Ange en annan hemlighet, men nu placerar den i en kategori genom att namnge den ”hemligheter--MinHemlighet”. Den här syntaxen anger en kategori ”hemligheter” som innehåller en hemlighet ”MinHemlighet”.
 
Du kan nu komma åt dina hemligheter i kod. Nästa steg är olika beroende på om du använder ASP.NET 4.7.1 eller ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Få åtkomst till dina hemligheter i kod (ASP.NET Core-projekt)

Anslutningen till Key Vault har ställts in vid start av en klass som implementerar [Microsoft.AspNetCore.Hosting.IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) med hjälp av ett sätt att utöka appkonfigurering som beskrivs i [förbättra en app från en extern sammansättningen i ASP.NET Core med IHostingStartup](/aspnet/core/fundamentals/host/platform-specific-configuration). Start-klassen använder två miljövariabler som innehåller anslutningsinformationen för Key Vault: ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED, inställd på true och ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT, ange den nya nyckeln Vault-URL. Dessa läggs till i filen launchsettings.json när du kör den **Lägg till Connected Service** processen.

Öppna dina hemligheter:

1. I Visual Studio kan i ASP.NET Core-projektet du nu referera till dessa hemligheter med hjälp av följande uttryck i koden:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. På sidan .cshtml, säger About.cshtml, lägga till den @inject direktiv längst upp i filen för att ställa in en variabel du kan använda för att få åtkomst till Key Vault-konfigurationen.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Du kan bekräfta att värdet för hemligheten är tillgänglig genom att visa den på en av sidorna som ett test. Använd @config att referera till variabeln config.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Skapa och köra webbprogrammet, gå till sidan om och se ”hemlighet”-värde.

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Få åtkomst till dina hemligheter i kod (ASP.NET 4.7.1 projekt)

Anslutningen till ditt Nyckelvalv har ställts in av klassen ConfigurationBuilder med hjälp av information som har lagts till i din web.config-fil när du kör den **Lägg till Connected Service** processen.

Öppna dina hemligheter:

1. Ändra web.config enligt följande. Nycklarna är platshållare som kommer att ersättas av AzureKeyVault ConfigurationBuilder med värdena för hemligheter i Key Vault.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. Lägg till följande rader för att hämta hemligheten och lagra den i ViewBag i HomeController, i metoden om domänkontrollanten.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. I vyn About.cshtml lägger du till följande om du vill visa värdet för hemligheten (endast för testning).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

1. Kör appen lokalt för att kontrollera att du kan läsa hemligt värde du angav i Azure-portalen, inte dummy värdet från konfigurationsfilen.

Därefter publicera din app till Azure.

## <a name="publish-to-azure-app-service"></a>Publicera till Azure App Service

1. Högerklicka på projektnoden och välj **publicera**. En skärm visas där det står **Välj ett mål för publicera**. Till vänster, Välj **Apptjänst**, och sedan **Skapa ny**.

   ![Publicera i App Service](media/vs-key-vault-add-connected-service/AppServicePublish1.PNG)

1. På den **skapa App Service** skärmbild och se till att prenumerationen och resursgruppen är samma som du skapade i Key Vault och väljer **skapa**.

   ![Skapa App Service](media/vs-key-vault-add-connected-service/AppServicePublish2.PNG)

1. När webbappen har skapats kan den **publicera** skärmen visas. Observera URL-Adressen för din publicerade webbprogram som finns i Azure. Om du ser **ingen** bredvid **Key Vault**, du måste fortfarande ange vilka Key Vault för att ansluta till att App Service. Välj den **lägga till Key Vault** koppla och välj det Nyckelvalv som du skapade.

   ![Lägg till Key Vault](media/vs-key-vault-add-connected-service/AppServicePublish3.PNG)

   Om du ser **hantera Nyckelvalv**, du kan klicka på som om du vill visa de aktuella inställningarna behörighet att redigera, eller gör ändringar i dina hemligheter i Azure Portal.

1. Nu kan välja länken webbplats-URL att gå till ditt webbprogram i webbläsaren. Kontrollera att du ser det korrekta värdet från Key Vault.

Grattis, du har bekräftat att webbappen kan använda Key Vault för att komma åt säkert lagrade hemligheter när du kör i Azure.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resursgruppen. Detta tar bort Nyckelvalvet och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Läs mer om Key Vault-utveckling genom att läsa den [Utvecklarguide för Key Vault](key-vault-developers-guide.md)
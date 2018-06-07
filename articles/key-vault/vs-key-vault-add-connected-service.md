---
title: Lägga till stöd för Key Vault din ASP.NET-projekt med Visual Studio | Microsoft Docs
description: Använd den här självstudiekursen för att du lär dig hur du lägger till stöd för Key Vault till ett webbprogram för ASP.NET eller ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: b4fed559b6364149170dc8b1da421c9c3ee1203c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635771"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Lägg till Nyckelvalvet i ditt webbprogram med hjälp av Visual Studio anslutna Services

I kursen får lära du dig att enkelt lägga till allt du behöver för att börja använda Azure Key Vault för att hantera din hemliga för webbprojekt i Visual Studio om du använder ASP.NET Core eller någon typ av ASP.NET-projekt. Du kan ha Visual Studio automatiskt lägga till alla NuGet-paket och konfigurationsinställningar som du behöver ansluta till Nyckelvalvet i Azure med hjälp av funktionen anslutna i Visual Studio-2017. 

Mer information om ändringarna anslutna tjänster gör i projektet att aktivera Key Vault finns [Key Vault anslutna Service - vad hände med min ASP.NET 4.7.1 projektet](vs-key-vault-aspnet-what-happened.md) eller [Key Vault anslutna Service - vad hände med ASP.NET Core projektet](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Förutsättningar

- **En Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 version 15.7** med den **Web Development** arbetsbelastning som har installerats. [Hämta nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- För ASP.NET (inte Core) måste .NET Framework 4.7.1 Development Tools inte är installerade som standard. Starta installationsprogrammet för Visual Studio för att installera dem, Välj **ändra**, och välj sedan **enskilda komponenter**, höger, expandera **ASP.NET och web development**, och välj **utvecklingsverktyg för .NET Framework 4.7.1**.
- Ett ASP.NET 4.7.1 eller ASP.NET Core 2.0 webbprojekt som är öppna.

## <a name="add-key-vault-support-to-your-project"></a>Lägga till stöd för Key Vault på ditt projekt

1. I **Solution Explorer**, Välj **Lägg till** > **ansluten Service**.
   Sidan ansluten visas med tjänster som du kan lägga till i projektet.
1. I menyn med tillgängliga tjänster, Välj **Secure hemligheter med Azure Key Vault**.

   ![Välj ”säker hemligheter med Azure Key Vault”](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Om du har loggat in på Visual Studio och har en Azure-prenumeration som är kopplad till ditt konto, visas en sida med en listruta med dina prenumerationer.
1. Välj den prenumeration som du vill använda och välj sedan en ny eller befintlig Key Vault eller Välj länken Redigera för att ändra det automatiskt genererade namnet.

   ![Välj din prenumeration](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Ange namnet som du vill använda för Nyckelvalvet.

   ![Byt namn på Nyckelvalvet och välja en resursgrupp](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Välj en befintlig resursgrupp eller välja att skapa en ny med ett namn som skapas automatiskt unqiue.  Om du vill skapa en ny grupp med ett annat namn, kan du använda den [Azure Portal](https://portal.azure.com), och sedan stänga sidan och starta om för att läsa in listan över resursgrupper.
1. Välj den region där du vill skapa Nyckelvalvet. Om ditt webbprogram finns i Azure, väljer du den region som är värd för webbprogrammet för optimal prestanda.
1. Välj en prisnivå modell. Mer information finns i [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).
1. Klicka på OK för att godkänna konfigurationsalternativ.
1. Välj **Lägg till** att skapa Nyckelvalvet. Processen kan misslyckas om du väljer ett namn som redan används.  I så fall kan du använda den **redigera** länken för att byta namn på Nyckelvalvet och försök igen.

   ![Lägger till anslutna service i projektet](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Lägg till en hemlighet i ditt Nyckelvalv i Azure. Klicka på länken för att hantera hemligheter som lagras i den här Key Vault för att få till rätt plats i portalen. Om du har stängt sidan eller projektet kan du gå till den i den [Azure-portalen](https://portal.azure.com) genom att välja **alla tjänster**under **säkerhet**, Välj **Key Vault**, Välj Nyckelvalv som du nyss skapade.

   ![Gå till portalen](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Valvet du skapade i avsnittet Key Vault för nyckeln, Välj **hemligheter**, sedan **generera/Import**.

   ![Generera/importera en hemlighet](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Ange en hemlighet, till exempel ”MinHemlighet”, och ge det ett värde som ett test och välj sedan den **skapa** knappen.

   ![Skapa en hemlighet](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (valfritt) Ange ett annat hemligt, men nu placera den i en kategori genom att namnge den ”hemligheter--MinHemlighet”. Den här syntaxen anger kategori ”hemligheter” som innehåller en hemlighet ”MinHemlighet”.
 
Du kan nu komma åt din hemliga i kod. Nästa steg är olika beroende på om du använder ASP.NET 4.7.1 eller ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Åtkomst till din hemliga i koden (ASP.NET Core projekt)

1. I Visual Studio kan i projektet ASP.NET Core du nu referera till dessa hemligheter med hjälp av följande uttryck i koden:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. På sidan .cshtml, säger About.cshtml, lägga till den @inject direktivet längst upp i filen för att ställa in en variabel du kan använda för att komma åt Key Vault-konfigurationen.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Du kan bekräfta att värdet för hemligheten som är tillgänglig genom att visa på en av sidorna som ett test. Använd @config att referera till den config-variabeln.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Skapa och köra webbprogrammet, gå till sidan om och se ”secret”-värdet.

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Åtkomst till din hemliga i koden (ASP.NET 4.7.1 projekt)

1. Ändra web.config på följande sätt. Nycklarna är platshållare som ersätts av AzureKeyVault ConfigurationBuilder med värden för hemligheter i Nyckelvalvet.

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

1. Lägg till följande rader för att hämta hemligheten och lagra den på ViewBag i HomeController i metoden om domänkontrollanten.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. I vyn About.cshtml lägger du till följande om du vill visa värdet för hemligt (endast för testning).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Grattis, du nu har bekräftat att ditt webbprogram kan använda Key Vault för att komma åt säkert lagrade hemligheter.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när de inte längre behövs. Detta tar bort Key Vault och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information om Key Vault-utveckling genom att läsa den [Key Vault-utvecklare](key-vault-developers-guide.md)
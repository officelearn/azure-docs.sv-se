---
title: Kom igång med Key Vault anslutna Service i Visual Studio (ASP.NET-projekt) | Microsoft Docs
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
ms.openlocfilehash: cd305801f10c899682aa6d751e48f30b6e8303fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787404"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Kom igång med Key Vault anslutna Service i Visual Studio (ASP.NET-projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-key-vault-aspnet-get-started.md)
> - [Vad hände](vs-key-vault-aspnet-what-happened.md)

Den här artikeln innehåller ytterligare vägledning när du har lagt till Key Vault till ett ASP.NET MVC-projektet till den **Lägg till anslutna tjänster** i Visual Studio. Om du inte redan har lagt till tjänsten till ditt projekt kan du göra det när som helst genom att följa instruktionerna i [lägga till Nyckelvalvet i ditt webbprogram med hjälp av Visual Studio anslutna Services](vs-key-vault-add-connected-service.md).

Se [vad hände med ASP.NET-projekt?](vs-key-vault-aspnet-core-what-happened.md) för ändringar i ditt projekt när du lägger till tjänsten anslutna.

## <a name="after-you-connect"></a>När du har anslutit

1. Lägga till en hemlighet i ditt Nyckelvalv i Azure. Klicka på länken för att komma till rätt plats i portalen: **hantera hemligheter som lagras i den här Key Vault**. Om du har stängt sidan eller projektet kan du gå till den i den [Azure-portalen](https://portal.azure.com) genom att välja **alla tjänster**under **säkerhet**, Välj **Key Vault**, Välj Nyckelvalv som du nyss skapade.

   ![Gå till portalen](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Valvet du skapade i avsnittet Key Vault för nyckeln, Välj **hemligheter**, sedan **generera/Import**.

   ![Generera/importera en hemlighet](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Ange en hemlighet som **MinHemlighet**, och ge det ett värde som ett test och välj sedan den **skapa** knappen.

   ![Skapa en hemlighet](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (valfritt) Ange ett annat hemligt, men nu placera den i en kategori genom att namnge den **hemligheter--MinHemlighet**. Den här syntaxen anger en kategori **hemligheter** som innehåller en hemlighet **MinHemlighet**.

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

Grattis, du har nu aktiverats ditt webbprogram till att använda Nyckelvalv för att komma åt säkert lagrade hemligheter.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när de inte längre behövs. Detta tar bort Key Vault och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

# <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du utvecklar med Key Vault i den [Key Vault-utvecklare](key-vault-developers-guide.md)
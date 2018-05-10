---
title: Kom igång med Key Vault anslutna Service i Visual Studio (ASP.NET Core projekt) | Microsoft Docs
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
ms.openlocfilehash: 64d1a404eac955f47308f01edd56b3d008e250a0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Kom igång med Key Vault anslutna Service i Visual Studio (ASP.NET Core projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-key-vault-aspnet-core-get-started.md)
> - [Vad hände](vs-key-vault-aspnet-core-what-happened.md)

Den här artikeln innehåller ytterligare vägledning när du har lagt till Key Vault till ett ASP.NET Core projekt via den **Lägg till anslutna tjänster** i Visual Studio. Om du inte redan har lagt till tjänsten till ditt projekt kan du göra det när som helst genom att följa instruktionerna i [lägga till Nyckelvalvet i ditt webbprogram med hjälp av Visual Studio anslutna Services](vs-key-vault-add-connected-service.md).

Se [vad hände med ASP.NET Core projektet?](vs-key-vault-aspnet-core-what-happened.md) för ändringar i ditt projekt när du lägger till tjänsten anslutna.

## <a name="after-you-connect"></a>När du har anslutit

1. Lägga till en hemlighet i ditt Nyckelvalv i Azure. Klicka på länken för att hantera hemligheter som lagras i den här Key Vault för att få till rätt plats i portalen. Om du har stängt sidan eller projektet kan du gå till den i den [Azure-portalen](https://portal.azure.com) genom att välja **alla tjänster**under **säkerhet**, Välj **Key Vault**, Välj Nyckelvalv som du nyss skapade.

   ![Gå till portalen](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Valvet du skapade i avsnittet Key Vault för nyckeln, Välj **hemligheter**, sedan **generera/Import**.

   ![Generera/importera en hemlighet](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Ange en hemlighet, till exempel ”MinHemlighet”, och ge det ett värde som ett test och välj sedan den **skapa** knappen.

   ![Skapa en hemlighet](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (valfritt) Ange ett annat hemligt, men nu placera den i en kategori genom att namnge den **hemligheter--MinHemlighet**. Den här syntaxen anger en kategori **hemligheter** som innehåller en hemlighet **MinHemlighet.**
1. I Visual Studio-projekt kan du nu referera dessa hemligheter med hjälp av följande uttryck i koden:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

Grattis, du har nu aktiverats ditt webbprogram till att använda Nyckelvalv för att komma åt säkert lagrade hemligheter.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när de inte längre behövs. Detta tar bort Key Vault och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

# <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du utvecklar med Key Vault i den [Key Vault-utvecklare](key-vault-developers-guide.md)
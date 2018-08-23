---
title: Kom igång med Key Vault Connected Service i Visual Studio (ASP.NET Core-projekt) | Microsoft Docs
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
ms.openlocfilehash: e591771ee9c9cb12d9ec2ff61ec7f5a76691c8c7
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42059917"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Kom igång med Key Vault Connected Service i Visual Studio (ASP.NET Core-projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-key-vault-aspnet-core-get-started.md)
> - [Vad hände](vs-key-vault-aspnet-core-what-happened.md)

Den här artikeln innehåller ytterligare vägledning när du har lagt till Key Vault till ett ASP.NET Core-projekt i den **Lägg till Connected Services** i Visual Studio. Om du inte redan har lagt till tjänsten i projektet kan du göra det när som helst genom att följa instruktionerna i [lägga till Key Vault i ditt webbprogram med hjälp av Visual Studio Connected Services](vs-key-vault-add-connected-service.md).

Se [vad hände med mitt ASP.NET Core-projekt?](vs-key-vault-aspnet-core-what-happened.md) för ändringar som görs i ditt projekt när du lägger till den anslutna tjänsten.

## <a name="after-you-connect"></a>När du har anslutit

1. Lägga till en hemlighet i Key Vault i Azure. Gå till rätt plats i portalen genom att klicka på länken för hantera hemligheter som lagras i den här Key Vault. Om du har stängt sidan eller projektet du kan navigera till den i den [Azure-portalen](https://portal.azure.com) genom att välja **alla tjänster**under **Security**, Välj **Key Vault**, välj sedan det Nyckelvalv som du nyss skapade.

   ![Gå till portalen](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Valvet du skapade i avsnittet Key Vault för nyckeln, Välj **hemligheter**, sedan **generera/importera**.

   ![Generera/importera en hemlighet](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Ange en hemlighet, till exempel ”MinHemlighet” och ge den ett värde som ett test, och välj sedan den **skapa** knappen.

   ![Skapa en hemlighet](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (valfritt) Ange en annan hemlighet, men nu placerar den i en kategori genom att namnge den **hemligheter--MinHemlighet**. Den här syntaxen anger en kategori **hemligheter** som innehåller en hemlighet **MinHemlighet.**
1. Du kan nu referera dessa hemligheter med hjälp av följande uttryck i kod i Visual Studio-projektet:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

Grattis, nu har du aktiverat webbappen du använder Key Vault för att komma åt säkert lagrade hemligheter.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort resursgruppen. Detta tar bort Nyckelvalvet och relaterade resurser. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

# <a name="next-steps"></a>Nästa steg

Läs mer om hur du utvecklar med Key Vault i den [Utvecklarguide för Key Vault](key-vault-developers-guide.md)
---
title: Kör dina Azure-funktioner från ett paket | Microsoft Docs
description: Har Azure Functions-körningen kör dina funktioner genom att montera en paketfil för distribution som innehåller dina projektfiler för funktionen app.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: glenga
ms.openlocfilehash: a0e643397372e5b132119a7c23f251ecec876916
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346585"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Kör dina Azure-funktioner från en paketfil

> [!NOTE]
> Funktioner som beskrivs i den här artikeln är inte tillgängliga för funktioner i Linux.

I Azure, kan du köra dina funktioner direkt från en paketfil för distribution i din funktionsapp. Ett annat alternativ är att distribuera dina filer i den `d:\home\site\wwwroot` katalog på din funktionsapp.

Den här artikeln beskriver fördelarna med att köra dina funktioner från ett paket. Den visar även hur du aktiverar den här funktionen i din funktionsapp.

## <a name="benefits-of-running-from-a-package-file"></a>Fördelar med att köra en paketfil från
  
Det finns flera fördelar med att köra en paketfil från:

+ Minskar risken för filkopiering låsning problem.
+ Kan distribueras till en produktionsapp (omstart).
+ Du kan vara säker på filer som körs i din app.
+ Förbättrar prestandan vid [Azure Resource Manager-distributioner](functions-infrastructure-as-code.md).
+ Kan minska kallstart gånger, särskilt för JavaScript-funktioner med stora npm-paketet träd.

Mer information finns i [det här meddelandet](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Aktivera funktioner för att köra från ett paket

Om du vill aktivera funktionsappen att köra från ett paket, du lägger bara till en `WEBSITE_RUN_FROM_PACKAGE` att ställa in din funktionsappinställningarna. Den `WEBSITE_RUN_FROM_PACKAGE` inställningen kan ha ett av följande värden:

| Värde  | Beskrivning  |
|---------|---------|
|**`<url>`**  | Platsen för en specifik paketfil som du vill köra. När du använder Blob storage, bör du använda en privat behållare med en [signatur för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) att aktivera Functions-körningen får tillgång till paketet. Du kan använda den [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ladda upp paketfiler till Blob storage-kontot.         |
| **`1`**  | Kör en paketfil i från den `d:\home\data\SitePackages` mapp på din funktionsapp. Det här alternativet kräver mappen också ha en fil med namnet `packagename.txt`. Den här filen innehåller bara namnet på den paketfil i mappen, utan några blanksteg. |

Nedan visas en funktionsapp som konfigurerats för att köras från en .zip-fil som finns i Azure Blob storage:

![WEBSITE_RUN_FROM_ZIP appinställning](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> För närvarande stöds endast .zip-paketfilerna.

## <a name="integration-with-zip-deployment"></a>Integrering med zip-distribution

[ZIP-distribution] [ Zip deployment for Azure Functions] är en funktion i Azure App Service som låter dig distribuera ditt funktionsappsprojekt till den `wwwroot` directory. Projektet kommer som en .zip-fil i distributionen. Samma API: er som kan användas för att distribuera paketet till den `d:\home\data\SitePackages` mapp. Med den `WEBSITE_RUN_FROM_PACKAGE` app inställningens värde av `1`, zip-distribution API: er kopiera paketet till den `d:\home\data\SitePackages` mappen i stället för att extrahera filerna till `d:\home\site\wwwroot`. Det skapar också de `packagename.txt` fil. Funktionsappen körs sedan från paketet efter en omstart och `wwwroot` skrivskyddas. Mer information om zip-distribution finns i [Zip-distribution för Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfrompackage-setting"></a>Att lägga till inställningen WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md

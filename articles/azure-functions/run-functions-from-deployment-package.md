---
title: Kör dina Azure-funktioner från ett paket
description: Låt Azure Functions köra dina funktioner genom att montera en distributionspaketfil som innehåller dina funktionsappprojektfiler.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365279"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Köra dina Azure-funktioner från en paketfil

I Azure kan du köra dina funktioner direkt från en distributionspaketfil i din funktionsapp. Det andra alternativet är att `d:\home\site\wwwroot` distribuera dina filer i katalogen för din funktionsapp.

I den här artikeln beskrivs fördelarna med att köra dina funktioner från ett paket. Den visar också hur du aktiverar den här funktionen i din funktionsapp.

> [!IMPORTANT]
> När du distribuerar dina funktioner till en Linux-funktionsapp i en [Premium-plan](functions-scale.md#premium-plan)bör du alltid köra från paketfilen och [publicera din app med hjälp av Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Fördelar med att köra från en paketfil
  
Det finns flera fördelar med att köra från en paketfil:

+ Minskar risken för problem med filkopieringslåsning.
+ Kan distribueras till en produktionsapp (med omstart).
+ Du kan vara säker på de filer som körs i appen.
+ Förbättrar prestanda för [Azure Resource Manager-distributioner](functions-infrastructure-as-code.md).
+ Kan minska kallstartstider, särskilt för JavaScript-funktioner med stora npm-paketträd.

Mer information finns i [det här meddelandet](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Aktivera funktioner som ska köras från ett paket

Om du vill att din funktionsapp ska `WEBSITE_RUN_FROM_PACKAGE` kunna köras från ett paket lägger du bara till en inställning i funktionsappinställningarna. Inställningen `WEBSITE_RUN_FROM_PACKAGE` kan ha något av följande värden:

| Värde  | Beskrivning  |
|---------|---------|
| **`1`**  | Rekommenderas för funktionsappar som körs i Windows. Kör från en paketfil i mappen i funktionsappen. `d:\home\data\SitePackages` Om du inte [distribuerar med zip-distribution](#integration-with-zip-deployment)krävs det `packagename.txt`här alternativet att mappen också har en fil med namnet . Den här filen innehåller bara namnet på paketfilen i mappen, utan blanktecken. |
|**`<URL>`**  | Plats för en specifik paketfil som du vill köra. När du använder Blob-lagring bör du använda en privat behållare med en [SAS (Shared Access Signature)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att aktivera funktionskörningen för åtkomst till paketet. Du kan använda [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att överföra paketfiler till ditt Blob-lagringskonto. När du anger en URL måste du också [synkronisera utlösare](functions-deployment-technologies.md#trigger-syncing) när du har publicerat ett uppdaterat paket. |

> [!CAUTION]
> När du kör en funktionsapp i Windows ger alternativet extern URL sämre kallstartsprestanda. När du distribuerar din funktionsapp `WEBSITE_RUN_FROM_PACKAGE` `1` till Windows bör du ställa in och publicera med zip-distribution.

Följande visar en funktionsapp som konfigurerats för att köras från en ZIP-fil som finns i Azure Blob-lagring:

![WEBSITE_RUN_FROM_ZIP appinställning](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> För närvarande stöds endast ZIP-paketfiler.

## <a name="integration-with-zip-deployment"></a>Integrering med zip-distribution

[Zip-distribution][Zip deployment for Azure Functions] är en funktion i Azure App Service som `wwwroot` låter dig distribuera ditt funktionsappprojekt till katalogen. Projektet är paketerat som en ZIP-distributionsfil. Samma API:er kan användas för `d:\home\data\SitePackages` att distribuera paketet till mappen. Med `WEBSITE_RUN_FROM_PACKAGE` värdet för `1`appinställningen för kopierar API:erna för zip-distribution ditt paket till `d:\home\data\SitePackages` mappen i stället för att extrahera filerna till `d:\home\site\wwwroot`. Det skapar också `packagename.txt` filen. Efter en omstart monteras paketet `wwwroot` som ett skrivskyddat filsystem. Mer information om zip-distribution finns i [Zip-distribution för Azure Functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Lägga till WEBSITE_RUN_FROM_PACKAGE-inställningen

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Felsökning

- Kör från `wwwroot` paketet gör skrivskyddad, så du får ett felmeddelande när du skriver filer till den här katalogen.
- Tar- och gzip-format stöds inte.
- Den här funktionen komponerar inte med lokal cache.
- För förbättrad kallstartsprestanda använder du`WEBSITE_RUN_FROM_PACKAGE`det lokala zip-alternativet ( =1).
- Kör från paket är inkompatibel`SCM_DO_BUILD_DURING_DEPLOYMENT=true`med distributionsanpassningsalternativet ( ), byggsteget ignoreras under distributionen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md

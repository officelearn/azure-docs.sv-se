---
title: Köra Azure Functions från ett paket | Microsoft Docs
description: Kör den Azure Functions körningen av dina funktioner genom att montera en distributions paket fil som innehåller dina projektfiler för Function-appen.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: 549768473460dcb97b66c3589d71c02039220605
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389953"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Köra Azure Functions från en paketfil

I Azure kan du köra dina funktioner direkt från en distributions paket fil i din Function-app. Det andra alternativet är att distribuera dina filer i katalogen `d:\home\site\wwwroot` i din Function-app.

I den här artikeln beskrivs fördelarna med att köra funktioner från ett paket. Den visar också hur du aktiverar den här funktionen i Function-appen.

> [!IMPORTANT]
> När du distribuerar dina funktioner till en Linux Function-app i en [Premium-plan](functions-scale.md#premium-plan)bör du alltid köra från paket filen och [publicera din app med hjälp av Azure Functions Core tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Fördelar med att köra från en paket fil
  
Det finns flera fördelar med att köra från en paket fil:

+ Minskar risken för fil kopierings låsnings problem.
+ Kan distribueras till en produktions-app (med omstart).
+ Du kan vara säker på vilka filer som körs i din app.
+ Förbättrar prestandan för [Azure Resource Manager distributioner](functions-infrastructure-as-code.md).
+ Kan minska kall start tider, särskilt för JavaScript-funktioner med stora NPM paket träd.

Mer information finns i [det här meddelandet](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Aktivera funktioner för att köra från ett paket

Om du vill att din Function-app ska kunna köras från ett paket lägger du bara till en `WEBSITE_RUN_FROM_PACKAGE`-inställning i appens inställningar. Inställningen `WEBSITE_RUN_FROM_PACKAGE` kan ha något av följande värden:

| Värde  | Beskrivning  |
|---------|---------|
| **`1`**  | Rekommenderas för Function-appar som körs i Windows. Kör från en paket fil i mappen `d:\home\data\SitePackages` i din Function-app. Om du inte [distribuerar med zip Deploy](#integration-with-zip-deployment)kräver det här alternativet att mappen också har en fil med namnet `packagename.txt`. Den här filen innehåller bara namnet på paket filen i mappen utan blank steg. |
|**`<url>`**  | Platsen för en angiven paketfil som du vill köra. När du använder Blob Storage bör du använda en privat behållare med en [signatur för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att göra det möjligt för functions-körningen att komma åt paketet. Du kan använda [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att ladda upp paketfiler till ditt Blob Storage-konto.         |

> [!CAUTION]
> När du kör en Function-app i Windows ger det externa URL-alternativet sämre kall start prestanda. När du distribuerar din Function-app till Windows ska du ange `WEBSITE_RUN_FROM_PACKAGE` till `1` och publicera med zip-distribution.

Följande visar en Function-app som kon figurer ATS för att köras från en. zip-fil som finns i Azure Blob Storage:

![Inställning för WEBSITE_RUN_FROM_ZIP-app](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> För närvarande stöds endast. zip-paketfiler.

## <a name="integration-with-zip-deployment"></a>Integrering med zip-distribution

[Zip-distribution][Zip deployment for Azure Functions] är en funktion i Azure App Service som gör att du kan distribuera ditt app-projekt till katalogen `wwwroot`. Projektet paketeras som en. zip-distributions fil. Samma API: er kan användas för att distribuera paketet till mappen `d:\home\data\SitePackages`. Med värdet för appen `WEBSITE_RUN_FROM_PACKAGE` för `1` kopierar API: erna för zip-distribution ditt paket till mappen `d:\home\data\SitePackages` i stället för att extrahera filerna till `d:\home\site\wwwroot`. Det skapar också filen `packagename.txt`. Efter en omstart monteras paketet till `wwwroot` som ett skrivskyddat fil system. Mer information om zip-distribution finns i [zip-distribution för Azure Functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Lägger till inställningen WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Felsöka

- Kör från paket gör `wwwroot` skrivskyddat, så att du får ett fel meddelande när du skriver filer till den här katalogen.
- Tar-och gzip-format stöds inte.
- Den här funktionen skapar inte lokal cache.
- För förbättrad kall start prestanda använder du det lokala zip-alternativet (`WEBSITE_RUN_FROM_PACKAGE` = 1).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md

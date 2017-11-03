---
title: "Anropa REST-slutpunkter med HTTP + Swagger-koppling för Logikappar i Azure | Microsoft Docs"
description: "Ansluta till REST-slutpunkter från logikappar via Swagger med HTTP + Swagger koppling"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 3e9229d94e96aad7b769d0e55d208d856e3b80bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-http--swagger-action"></a>Kom igång med HTTP + Swagger åtgärd

Du kan skapa en förstklassig koppling till valfri REST-slutpunkt via en [Swagger-dokument](https://swagger.io) när du använder HTTP + Swagger åtgärd i logik app arbetsflödet. Du kan också utöka logikappar för att anropa en REST-slutpunkt förstklassigt logik App Designer upplevelse.

Information om hur du skapar logikappar med kopplingar finns [skapa en ny logikapp](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Använd HTTP + Swagger som en utlösare eller en åtgärd

HTTP- + Swagger utlösa och åtgärd som fungerar på samma sätt som den [HTTP-åtgärd](connectors-native-http.md) men ge en bättre upplevelse i logik App Designer genom att exponera API-strukturen och utdata från den [Swagger-metadata](https://swagger.io). Du kan också använda HTTP + Swagger-koppling som en utlösare. Om du vill implementera en avsökning utlösare följer mönstret avsökning som beskrivs i [skapa anpassade API: er för att anropa andra API: er, tjänster och system från logikappar](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Lär dig mer om [logik app utlösare och åtgärder](connectors-overview.md).

Här är ett exempel på hur till Använd HTTP + Swagger-åtgärden eftersom en åtgärd i ett arbetsflöde i en logikapp.

1. Välj den **nytt steg** knappen.
2. Välj **lägga till en åtgärd**.
3. Skriv i sökrutan åtgärd **swagger** till listan HTTP + Swagger-åtgärd.
   
    ![Välj HTTP + Swagger åtgärd](./media/connectors-native-http-swagger/using-action-1.png)
4. Ange URL för en Swagger-dokument:
   
   * Om du vill arbeta från logik App Designer URL: en måste vara en HTTPS-slutpunkt och har CORS aktiverat.
   * Om Swagger-dokument inte uppfylla detta krav kan du använda [Azure Storage med CORS aktiverat](#hosting-swagger-from-storage) att spara dokumentet.
5. Klicka på **nästa** att läsa och rendera från Swagger-dokument.
6. Lägga till i alla parametrar som krävs för HTTP-anropet.
   
    ![Slutföra HTTP-åtgärden](./media/connectors-native-http-swagger/using-action-2.png)
7. Spara och publicera din logikapp genom att klicka på **spara** designer i verktygsfältet.

### <a name="host-swagger-from-azure-storage"></a>Värden Swagger från Azure Storage
Du kanske vill referera till en Swagger-dokument som inte finns eller som inte uppfyller säkerhet och cross-origin krav för designer. Lös problemet, kan du lagra Swagger-dokument i Azure Storage och aktivera CORS för att referera till dokumentet.  

Här följer stegen för att skapa, konfigurera och lagra Swagger-dokument i Azure Storage:

1. [Skapa ett Azure storage-konto med Azure Blob storage](../storage/common/storage-create-storage-account.md). Om du vill utföra det här steget kan du ange behörigheter till **offentlig åtkomst**.

2. Aktivera CORS på blob. 

   Du kan använda för att automatiskt konfigurera den här inställningen, [detta PowerShell-skript](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Överför Swagger-filen till blob. 

   Du kan utföra denna åtgärd från den [Azure-portalen](https://portal.azure.com) eller från ett verktyg som [Azure Lagringsutforskaren](http://storageexplorer.com/).

4. Referera till en HTTPS-länk till dokument i Azure Blob storage. 

   Länken använder det här formatet:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Teknisk information
Nedan visas information för utlösare och åtgärder som den här HTTP + Swagger anslutningen stöder.

## <a name="http--swagger-triggers"></a>HTTP- + Swagger-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definieras i en logikapp. [Mer information om utlösare.](connectors-overview.md) HTTP- + Swagger koppling har en utlösare.

| Utlösare | Beskrivning |
| --- | --- |
| HTTP- + Swagger |Gör ett HTTP-anrop och returnera svar innehållet |

## <a name="http--swagger-actions"></a>HTTP- + Swagger-åtgärder
En åtgärd är en åtgärd som utförs av arbetsflödet som definieras i en logikapp. [Mer information om åtgärder.](connectors-overview.md) HTTP- + Swagger koppling har en möjlig åtgärd.

| Åtgärd | Beskrivning |
| --- | --- |
| HTTP- + Swagger |Gör ett HTTP-anrop och returnera svar innehållet |

### <a name="action-details"></a>Åtgärdsinformation
HTTP- + Swagger connector medföljer en möjlig åtgärd. Följande är information om var och en av åtgärderna, sina obligatoriska och valfria inmatningsfält och detaljer om motsvarande utdata som är associerade med deras användning.

#### <a name="http--swagger"></a>HTTP- + Swagger
Göra en utgående HTTP-begäran med hjälp av Swagger-metadata.
En asterisk (*) innebär ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Metoden * |Metoden |HTTP-verbet som ska användas. |
| URI * |URI: N |URI för HTTP-begäran. |
| Rubriker |Rubriker |Ett JSON-objekt med HTTP-rubriker för att inkludera. |
| Innehåll |Brödtext |Texten på HTTP-begäran. |
| Autentisering |Autentisering |Autentisering som ska användas för begäran. Mer information finns i [HTTP-anslutningen](connectors-native-http.md#authentication). |

**Information för utdata**

HTTP-svar

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Rubriker |Objektet |Svarsrubriker |
| Innehåll |Objektet |Objektet Response |
| Statuskod |int |HTTP-statuskod |

### <a name="http-responses"></a>HTTP-svar
När du anropar olika åtgärder, kan du få vissa svar. Följande är en tabell som visar motsvarande svar och beskrivningar.

| Namn | Beskrivning |
| --- | --- |
| 200 |OKEJ |
| 202 |Godkänt |
| 400 |Felaktig begäran |
| 401 |Behörighet saknas |
| 403 |Tillåts inte |
| 404 |Det gick inte att hitta |
| 500 |Internt serverfel. Okänt fel uppstod. |

- - -
## <a name="next-steps"></a>Nästa steg

* [Skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md)
* [Sök efter andra kopplingar](apis-list.md)
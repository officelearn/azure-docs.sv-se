---
title: Anropa REST-slutpunkter från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som kommunicerar med REST-slutpunkter med hjälp av HTTP- + Swagger anslutning i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
tags: connectors
ms.topic: article
ms.date: 07/18/2016
ms.openlocfilehash: bb89c76fb19bff74d39d919c8d2e65d430cb0566
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817759"
---
# <a name="call-rest-endpoints-with-http--swagger-connector-in-azure-logic-apps"></a>Anropa REST-slutpunkter med HTTP + Swagger anslutning i Azure Logic Apps

Du kan skapa en första klassens koppling till valfri REST-slutpunkt via en [Swagger-dokument](https://swagger.io) när du använder HTTP + Swagger åtgärden i logikappens arbetsflöde. Du kan också utöka logikappar för att anropa valfri REST-slutpunkt med en förstklassig Logic App Designer.

Läs hur du skapar logikappar med kopplingar i [skapa en ny logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Använd HTTP + Swagger som en utlösare eller en åtgärd

HTTP + Swagger utlösa och åtgärden som fungerar på samma sätt som den [HTTP-åtgärd](connectors-native-http.md) men ger en bättre upplevelse i Logic App Designer genom att exponera API struktur och utdata från den [Swagger-metadata](https://swagger.io). Du kan också använda HTTP + Swagger-anslutningsapp som en utlösare. Om du vill implementera en avsökning utlösare följer avsökningen mönster som beskrivs i [skapa anpassade API: er för att anropa andra API: er, tjänster och system från logikappar](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Läs mer om [logic app-utlösare och åtgärder](connectors-overview.md).

Här är ett exempel på hur till Använd HTTP + Swagger-åtgärden eftersom en åtgärd i ett arbetsflöde i en logikapp.

1. Välj den **nytt steg** knappen.
2. Välj **Lägg till en åtgärd**.
3. Skriv i sökrutan åtgärd **swagger** till listan över HTTP + Swagger-åtgärd.
   
    ![Välj HTTP + Swagger åtgärd](./media/connectors-native-http-swagger/using-action-1.png)
4. Ange en Swagger-dokument:
   
   * Om du vill arbeta från Logic App Designer, URL: en måste vara en HTTPS-slutpunkt och har CORS aktiverat.
   * Om Swagger-dokument inte uppfyller detta krav kan använda du Azure Storage med CORS aktiverat dokumentet ska lagras.
5. Klicka på **nästa** att läsa och rendera från Swagger-dokument.
6. Lägg till i alla parametrar som krävs för HTTP-anrop.
   
    ![Fullständig HTTP-åtgärd](./media/connectors-native-http-swagger/using-action-2.png)
7. Spara och publicera din logikapp genom att klicka på **spara** på verktygsfältet för appdesignern.

### <a name="host-swagger-from-azure-storage"></a>Värden Swagger från Azure Storage
Du kanske vill referera till en Swagger-dokument som inte finns eller som inte uppfyller de krav på säkerhet och resursdelning för korsande ursprung för designer. För att lösa problemet, kan du lagra Swagger-dokument i Azure Storage och aktivera CORS att referera till dokumentet.  

Här följer stegen för att skapa, konfigurera och lagra Swagger-dokument i Azure Storage:

1. [Skapa ett Azure storage-konto med Azure Blob storage](../storage/common/storage-create-storage-account.md). Om du vill utföra det här steget kan du ange behörigheter **offentlig åtkomst**.

2. Aktivera CORS i blobben. 

   Du kan använda för att automatiskt konfigurera den här inställningen, [den här PowerShell.skript](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Överför Swagger-filen till blob. 

   Du kan utföra denna åtgärd från den [Azure-portalen](https://portal.azure.com) eller från ett verktyg som [Azure Storage Explorer](http://storageexplorer.com/).

4. Referera till en HTTPS-länk till dokumentet i Azure Blob storage. 

   Länken använder det här formatet:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Teknisk information
Följande visas information om utlösare och åtgärder som den här HTTP + Swagger anslutningsappen stöder.

## <a name="http--swagger-triggers"></a>HTTP + Swagger-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definieras i en logikapp. [Mer information om utlösare.](connectors-overview.md) HTTP + Swagger-anslutningsappen har en utlösare.

| Utlösare | Beskrivning |
| --- | --- |
| HTTP + Swagger |Ett HTTP-anrop och gå tillbaka svarsinnehållet |

## <a name="http--swagger-actions"></a>HTTP + Swagger-åtgärder
En åtgärd är en åtgärd som utförs av arbetsflödet som definieras i en logikapp. [Läs mer om åtgärder.](connectors-overview.md) HTTP + Swagger-anslutningsappen har en möjlig åtgärd.

| Åtgärd | Beskrivning |
| --- | --- |
| HTTP + Swagger |Ett HTTP-anrop och gå tillbaka svarsinnehållet |

### <a name="action-details"></a>Åtgärdsinformation
HTTP + Swagger connector levereras med en möjlig åtgärd. Följande är information om var och en av åtgärderna som deras obligatoriska och valfria inmatningsfält och motsvarande utdatainformation som är associerade med deras användning.

#### <a name="http--swagger"></a>HTTP + Swagger
Skapa en utgående HTTP-begäran med hjälp av Swagger-metadata.
En asterisk (*) innebär ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Metoden * |metod |HTTP-verb som använder. |
| URI: N * |URI |URI för HTTP-begäran. |
| Rubriker |Rubriker |En JSON-objekt för HTTP-huvuden att inkludera. |
| Innehåll |brödtext |HTTP-begärandetexten. |
| Authentication |autentisering |Autentisering som ska användas för begäran. Mer information finns i den [HTTP-anslutningsappen](connectors-native-http.md#authentication). |

**Utdatainformation**

HTTP-svar

| Namn på egenskap | Datatyp | Beskrivning |
| --- | --- | --- |
| Rubriker |objekt |Svarshuvud |
| Innehåll |objekt |Svarsobjekt |
| Statuskod |int |HTTP-statuskod |

### <a name="http-responses"></a>HTTP-svar
När du anropar olika åtgärder, kan du få vissa svar. Följande är en tabell som visar motsvarande svar och beskrivningar.

| Namn | Beskrivning |
| --- | --- |
| 200 |Ok |
| 202 |Accepterad |
| 400 |Felaktig förfrågan |
| 401 |Behörighet saknas |
| 403 |Förbjudna |
| 404 |Kunde inte hittas |
| 500 |Internt serverfel. Okänt fel uppstod. |

- - -
## <a name="next-steps"></a>Nästa steg

* [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Sök efter andra anslutningar](apis-list.md)

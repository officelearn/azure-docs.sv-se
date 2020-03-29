---
title: Ansluta till REST-slutpunkter från Azure Logic Apps
description: Övervaka REST-slutpunkter i automatiserade uppgifter, processer och arbetsflöden med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787377"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Anropa REST-slutpunkter med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda HTTP + Swagger-kopplingen kan du automatisera arbetsflöden som regelbundet anropar alla REST-slutpunkter via en [Swagger-fil](https://swagger.io) genom att skapa logikappar. HTTP + Swagger-utlösaren och åtgärden fungerar på samma sätt som [HTTP-utlösaren och åtgärden,](connectors-native-http.md) men ger en bättre upplevelse i Logic App Designer genom att exponera API-strukturen och utdata som beskrivs av Swagger-filen. Om du vill implementera en avsökningsutlösare följer du avsökningsmönstret som beskrivs i [Skapa anpassade API:er för att anropa andra API:er, tjänster och system från logikappar](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL:en för Swagger-filen (inte OpenAPI) som beskriver mål-REST-slutpunkten

  Normalt måste REST-slutpunkten uppfylla dessa kriterier för att kopplingen ska fungera:

  * Swagger-filen måste finnas på en HTTPS-URL som är allmänt tillgänglig.

  * Swagger-filen måste ha [CORS (Cross-Origin Resource Sharing)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) aktiverat.

  Om du vill referera till en Swagger-fil som inte finns eller som inte uppfyller kraven för säkerhet och korsnamn kan du [överföra Swagger-filen till en blob-behållare i ett Azure-lagringskonto](#host-swagger)och aktivera CORS på det lagringskontot så att du kan referera till filen.

  Exemplen i det här avsnittet använder [Cognitive Services Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview), som kräver ett Cognitive [Services-konto och åtkomstnyckel](../cognitive-services/cognitive-services-apis-create-account.md).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Logikappen där du vill anropa målslutpunkten. Om du vill börja med HTTP + Swagger-utlösaren [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda http + swagger-åtgärden startar du logikappen med valfri utlösare. I det här exemplet används HTTP + Swagger-utlösaren som det första steget.

## <a name="add-an-http--swagger-trigger"></a>Lägga till en HTTP + Swagger-utlösare

Den här inbyggda utlösaren skickar en HTTP-begäran till en URL för en Swagger-fil som beskriver ett REST API och returnerar ett svar som innehåller filens innehåll.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din tomma logikapp i Logic App Designer.

1. På designern, i sökrutan, ange "swagger" som filter. Välj **HTTP + Swagger-utlösaren** i listan **Utlösare.**

   ![Välj HTTP + Swagger-utlösare](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. I rutan **SWAGGER ENDPOINT URL** anger du URL:en för Swagger-filen och väljer **Nästa**.

   I det här exemplet används Swagger-URL:en som finns i regionen Västra USA för [Cognitive Services Face API:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Ange URL för Swagger-slutpunkt](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. När designern visar de åtgärder som beskrivs av Swagger-filen väljer du den åtgärd som du vill använda.

   ![Operationer i Swagger-filen](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Ange värdena för utlösarparametrarna, som varierar beroende på den valda åtgärden, som du vill inkludera i slutpunktsanropet. Ställ in upprepningen för hur ofta du vill att utlösaren ska anropa slutpunkten.

   I det här exemplet byter du namn på utlösaren till "HTTP + Swagger-utlösaren: Face - Detect" så att steget har ett mer beskrivande namn.

   ![Åtgärdsinformation](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer önskade parametrar.

   Mer information om autentiseringstyper som är tillgängliga för HTTP + Swagger finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Fortsätt att skapa logikappens arbetsflöde med åtgärder som körs när utlösaren utlöses.

1. När du är klar ska du komma ihåg att spara logikappen. Välj **Spara**i designerverktygsfältet .

## <a name="add-an-http--swagger-action"></a>Lägga till en HTTP + Swagger-åtgärd

Den här inbyggda åtgärden gör en HTTP-begäran till URL:en för Swagger-filen som beskriver ett REST API och returnerar ett svar som innehåller filens innehåll.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna logikappen i Logic App Designer.

1. Under det steg där du vill lägga till http + swagger-åtgärden väljer du **Nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. På designern, i sökrutan, ange "swagger" som filter. Välj http **+ Swagger-åtgärden** i listan **Åtgärder.**

    ![Välj HTTP + Swagger-åtgärd](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. I rutan **SWAGGER ENDPOINT URL** anger du URL:en för Swagger-filen och väljer **Nästa**.

   I det här exemplet används Swagger-URL:en som finns i regionen Västra USA för [Cognitive Services Face API:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Ange URL för Swagger-slutpunkt](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. När designern visar de åtgärder som beskrivs av Swagger-filen väljer du den åtgärd som du vill använda.

   ![Operationer i Swagger-filen](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Ange värdena för åtgärdsparametrarna, som varierar beroende på den valda åtgärden, som du vill inkludera i slutpunktsanropet.

   Det här exemplet har inga parametrar, men byter namn på åtgärden till "HTTP + Swagger-åtgärd: Face - Identify" så att steget har ett mer beskrivande namn.

   ![Åtgärdsinformation](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer önskade parametrar.

   Mer information om autentiseringstyper som är tillgängliga för HTTP + Swagger finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. När du är klar ska du komma ihåg att spara logikappen. Välj **Spara**i designerverktygsfältet .

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Värdswagger i Azure Storage

Du kan referera till en Swagger-fil som inte finns eller som inte uppfyller kraven för säkerhet och korsnamn genom att ladda upp filen till blob-behållaren i ett Azure-lagringskonto och aktivera CORS på det lagringskontot. Så här skapar, konfigurerar och lagrar du Swagger-filer i Azure Storage:

1. [Skapa ett Azure-lagringskonto](../storage/common/storage-create-storage-account.md).

1. Aktivera nu CORS för blobben. På ditt lagringskontos meny väljer du **CORS**. Ange dessa värden på fliken **Blob-tjänst** och välj sedan **Spara**.

   | Egenskap | Värde |
   |----------|-------|
   | **Tillåtna ursprung** | `*` |
   | **Tillåtna metoder** | `GET`, `HEAD`, `PUT` |
   | **Tillåtna rubriker** | `*` |
   | **Exponerade rubriker** | `*` |
   | **Max ålder** (i sekunder) | `200` |
   |||

   Även om det här exemplet använder [Azure-portalen](https://portal.azure.com)kan du använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/)eller konfigurera den här inställningen automatiskt med hjälp av det här exemplet [PowerShell-skriptet](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Skapa en blob-behållare](../storage/blobs/storage-quickstart-blobs-portal.md). I fönstret **Översikt** i behållaren väljer du **Ändra åtkomstnivå**. Välj **Blob (anonym läsåtkomst endast för blobbar)** i listan **Offentlig åtkomstnivå** och välj **OK**.

1. [Ladda upp Swagger-filen till blob-behållaren](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), antingen via [Azure-portalen](https://portal.azure.com) eller [Azure Storage Explorer](https://storageexplorer.com/).

1. Om du vill referera till filen i blob-behållaren använder du en HTTPS-länk som följer det här formatet, som är skiftlägeskänsligt:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Referens för anslutningsapp

Här finns mer information om utdata från en HTTP + Swagger-utlösare eller åtgärd. HTTP + Swagger-anropet returnerar den här informationen:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| Headers | objekt | Rubrikerna från begäran |
| body | objekt | JSON-objekt | Objektet med brödtextinnehållet från begäran |
| statuskod | int | Statuskoden från begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | OK |
| 202 | Accepterad |
| 400 | Felaktig begäran |
| 401 | Behörighet saknas |
| 403 | Förbjudet |
| 404 | Hittades inte |
| 500 | Internt serverfel. Okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)

---
title: Ansluta till REST-slutpunkter från Azure Logic Apps
description: Övervaka REST-slutpunkter i automatiserade uppgifter, processer och arbetsflöden med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541647"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Anropa REST-slutpunkter med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och inbyggda HTTP + Swagger-anslutningstjänsten kan du automatisera arbetsflöden som regelbundet anropa valfri REST-slutpunkt via en [Swagger-fil](https://swagger.io) genom att skapa logikappar. HTTP + Swagger utlösa och åtgärden som fungerar på samma sätt som den [HTTP-utlösare och åtgärd](connectors-native-http.md) men ge en bättre upplevelse i Logic App Designer genom att exponera API struktur och utdata som beskrivs av Swagger-fil. För att implementera en avsökning utlösare, följer du avsökningen mönster som beskrivs i [skapa anpassade API: er för att anropa andra API: er, tjänster och system från logikappar](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL för Swagger-filen som beskriver REST-slutpunkt för mål

  REST-slutpunkten måste normalt uppfylla det här villkoret för anslutningstjänsten ska fungera:

  * Swagger-filen måste finnas på en HTTPS-URL som är allmänt tillgänglig.

  * Swagger-filen måste ha [Cross-Origin Resource Sharing (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) aktiverat.

  Om du vill referera till en swaggerfil som inte finns eller som inte uppfyller de krav på säkerhet och resursdelning för korsande ursprung, kan du [överför Swagger-filen till en blobbehållare i Azure-lagringskonton](#host-swagger), och aktivera CORS på det lagringskontot så att du kan referera till filen.

  Exemplen i det här avsnittet använder de [Cognitive Services, Ansikts-API](https://docs.microsoft.com/azure/cognitive-services/face/overview), vilket kräver en [Cognitive Services-kontot och åtkomstnyckeln](../cognitive-services/cognitive-services-apis-create-account.md).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Logikapp från där du vill anropa mål-slutpunkten. Börja med HTTP + Swagger utlöser, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Att använda HTTP + Swagger-åtgärden, börjar din logikapp med en utlösare som du vill. Det här exemplet används HTTP + Swagger-utlösare som det första steget.

## <a name="add-an-http--swagger-trigger"></a>Lägg till en HTTP- + Swagger utlösare

Den här inbyggda utlösaren skickar en HTTP-begäran till en URL för en Swagger-fil som beskriver ett REST-API och returnerar ett svar som innehåller den filinnehåll.

1. Logga in på [Azure Portal](https://portal.azure.com). Öppna din tom logikapp i Logic App Designer.

1. På designern i sökrutan anger du ”swagger” som filter. Från den **utlösare** väljer den **HTTP + Swagger** utlösaren.

   ![Välj HTTP + Swagger utlösa](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. I den **SLUTPUNKTS-URL för SWAGGER** , ange URL: en för Swagger-filen och väljer **nästa**.

   Det här exemplet används en Swagger-URL som finns i regionen västra USA för den [Cognitive Services, Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Ange URL för Swagger-slutpunkten](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. När designverktyget visar åtgärderna som beskrivs av Swagger-filen, väljer du den åtgärd som du vill använda.

   ![Åtgärder i Swagger-fil](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Ange värden för parametrarna utlösare, som varierar baserat på den valda åtgärden som du vill ska ingå i slutpunkten för anropet. Ställ in upprepningen för hur ofta du vill att utlösaren att anropa slutpunkten.

   Det här exemplet byter namn på utlösare för ”HTTP + Swagger utlösa: Ansiktsigenkänning – identifiera ”så att steget innehåller ett mer beskrivande namn.

   ![Åtgärdsinformation](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Om du vill lägga till andra tillgängliga parametrar, öppna den **Lägg till ny parameter** och välj de parametrar som du vill.

   Mer information om typer av autentisering som är tillgängliga för HTTP- + Swagger finns i [autentisera HTTP-utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Fortsätt att skapa din logikapp arbetsflöde med åtgärder som körs när den utlöses.

1. När du är klar, Kom ihåg att spara din logikapp. På verktygsfältet för appdesignern väljer **spara**.

## <a name="add-an-http--swagger-action"></a>Lägg till en HTTP- + Swagger åtgärd

Den här inbyggda åtgärden gör en HTTP-begäran till URL för Swagger-filen som beskriver ett REST-API och returnerar ett svar som innehåller den filinnehåll.

1. Logga in på [Azure Portal](https://portal.azure.com). Öppna logikappen i Logic App Designer.

1. Under steget där du vill lägga till HTTP- + Swagger väljer **nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. Klicka på plustecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. På designern i sökrutan anger du ”swagger” som filter. Från den **åtgärder** väljer den **HTTP + Swagger** åtgärd.

    ![Välj HTTP + Swagger åtgärd](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. I den **SLUTPUNKTS-URL för SWAGGER** , ange URL: en för Swagger-filen och väljer **nästa**.

   Det här exemplet används en Swagger-URL som finns i regionen västra USA för den [Cognitive Services, Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Ange URL för Swagger-slutpunkten](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. När designverktyget visar åtgärderna som beskrivs av Swagger-filen, väljer du den åtgärd som du vill använda.

   ![Åtgärder i Swagger-fil](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Ange värden för åtgärdsparametrar, som varierar baserat på den valda åtgärden som du vill ska ingå i slutpunkten för anropet.

   Det här exemplet har inga parametrar, men byter namn på åtgärden som ska ”HTTP + Swagger åtgärd: Ansiktsigenkänning – identifiera ”så att steget innehåller ett mer beskrivande namn.

   ![Åtgärdsinformation](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Om du vill lägga till andra tillgängliga parametrar, öppna den **Lägg till ny parameter** och välj de parametrar som du vill.

   Mer information om typer av autentisering som är tillgängliga för HTTP- + Swagger finns i [autentisera HTTP-utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. När du är klar, Kom ihåg att spara din logikapp. På verktygsfältet för appdesignern väljer **spara**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Värden Swagger i Azure Storage

Du kan referera till en Swagger-fil som inte finns eller som inte uppfyller de krav på säkerhet och resursdelning för korsande ursprung genom att ladda upp filen till blob-behållare i ett Azure storage-konto och aktivera CORS på det lagringskontot. Följ dessa steg för att skapa, konfigurera och lagra filer för Swagger i Azure Storage:

1. [Skapa ett Azure Storage-konto](../storage/common/storage-create-storage-account.md).

1. Nu aktivera CORS för bloben. På lagringskontots meny väljer **CORS**. På den **Blobtjänst** fliken, ange dessa värden och därefter **spara**.

   | Egenskap | Värde |
   |----------|-------|
   | **Tillåtna ursprung** | `*` |
   | **Tillåtna metoder** | `GET`, `HEAD`, `PUT` |
   | **Tillåtna huvuden** | `*` |
   | **Exponerade rubriker** | `*` |
   | **Maximal ålder** (i sekunder) | `200` |
   |||

   Även om det här exemplet används den [Azure-portalen](https://portal.azure.com), du kan använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/), eller konfigurera automatiskt den här inställningen med hjälp av det här exemplet [PowerShell-skript](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Skapa en blobbehållare](../storage/blobs/storage-quickstart-blobs-portal.md). För behållaren **översikt** väljer **ändra åtkomstnivå**. Från den **offentlig åtkomstnivå** väljer **Blob (anonym läsåtkomst endast för blobbar)** , och välj **OK**.

1. [Överför Swagger-filen till blobbehållaren](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), antingen via den [Azure-portalen](https://portal.azure.com) eller [Azure Storage Explorer](https://storageexplorer.com/).

1. Använd en HTTPS-länk som följer den här format, vilket är skiftlägeskänsligt för att hänvisa till filen i blob-behållaren:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Referens för anslutningsapp

Här finns mer information om utdata från en HTTP- + Swagger utlösare eller åtgärd. HTTP + Swagger-anrop returnerar den här informationen:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| Rubriker | object | Rubriker i begäran |
| body | object | JSON-objekt | Objektet med brödtext i begäran |
| Statuskod | int | Statuskoden i begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | Ok |
| 202 | Accepterat |
| 400 | Felaktig förfrågan |
| 401 | Behörighet saknas |
| 403 | Förbjudna |
| 404 | Det gick inte att hitta |
| 500 | Internt serverfel. Okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
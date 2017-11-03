---
title: 'Beskriver anpassade API: er och kopplingar med Postman - Azure Logic Apps | Microsoft Docs'
description: "Skapa Postman samlingar för att beskriva, gruppen och ordna dina anpassade API: er och kopplingar"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Beskriv anpassade API: er och anpassade kopplingar med Postman

För att utveckla [anpassade API: er](../logic-apps/logic-apps-create-api-app.md) och [anpassade kopplingar](../logic-apps/custom-connector-overview.md) snabbare och enklare, kan du skapa [Postman](https://www.getpostman.com/) samlingar, i stället för OpenAPI dokument för att beskriva dina API: er och kopplingar. Postman samlingar hjälper dig att sortera och gruppera relaterade API-begäranden. Du kan till exempel använda Postman när du skapar kopplingar för Logikappar i Azure, Microsoft Flow eller Microsoft PowerApps. 

Den här kursen visar hur du skapar en [Postman samling](https://www.getpostman.com/docs/postman/collections/creating_collections) med hjälp av den [identifiera språk API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) i [Azure kognitiva Services Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) som exemplet. Detta API identifierar språk, sentiment och viktiga fraser i text som du skickar till API: et.

## <a name="prerequisites"></a>Krav

* Om du har använt Postman, [installera appen Postman](https://www.getpostman.com/apps).

* En Azure-prenumeration. Om du inte har en prenumeration kan du starta med en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan registrera dig för en [betala per användning prenumeration](https://azure.microsoft.com/pricing/purchase-options/).

* Om du har en Azure-prenumeration, registrering för Text Analytics-API: er genom att slutföra [uppgift 1 här](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md). 

## <a name="create-a-postman-collection"></a>Skapa en samling för Postman

Innan du kan skapa en samling, skapa en HTTP-begäran för API-slutpunkt. 

### <a name="create-an-http-request-for-your-api"></a>Skapa en HTTP-begäran för ditt API

1. Öppna appen Postman så att du kan skapa en [HTTP-begäran](https://www.getpostman.com/docs/postman/sending_api_requests/requests) för API-slutpunkt. Mer information finns i Postman [begär dokumentationen](https://www.getpostman.com/docs/postman/sending_api_requests/requests).

   1. På den **Builder** , Välj HTTP-metoden, ange URL: en begäran för API-slutpunkt, och välj ett authorization protocol eventuella. 
   När du är klar kan du välja **Params**.

      Den här kursen kan använda du inställningarna i det här exemplet:

      ![Skapa-förfrågan: ”HTTP-metoden”, ”URL-begäran”, ”tillstånd”](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | Parameter | Föreslaget värde | 
      | --------- | --------------- | 
      | **HTTP-metoden** | POST | 
      | **URL-begäran** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **Auktorisering** | ”Ingen autentisering” | | 
      ||| 

   2. Du kan nu ange nyckel / värde-par att använda som frågan eller sökväg parametrar i begärande-URL. Postman kombinerar de här objekten tillsammans till en frågesträng.
   När du är klar väljer **huvuden**.

      ![Begäran fortsatte: ”parametrar”](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | Parameter | Föreslaget värde | 
      | --------- | --------------- | 
      | **Parametrar** | **Nyckeln**: ”numberOfLanguagesToDetect” </br>**Värdet**: ”1” | 
      ||| 

   3. Ange nyckel-värdepar för begärandehuvudet. 
   Ange valfri sträng som du vill använda för huvudets namn. Du kan välja i listrutan för vanliga HTTP-rubriker. När du är klar väljer **brödtext**. 
   
      ![Begäran fortsatte: ”rubriker”](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | Parameter | Värde | 
      | --------- | ----- | 
      | **Rubriker** | **Nyckeln**: ”Ocp-Apim-prenumeration-Key” </br>**Värdet**: *din API-prenumeration-nyckel*, som du hittar i kognitiva Services-konto <p>**Nyckeln**: ”Content-Type” </br> **Värdet**: ”application/json” | 
      ||| 

   4. Ange innehåll som du vill skicka i begärandetexten. 
   Om du vill kontrollera att begäran fungerar genom att få tillbaka ett svar, Välj **skicka**. 
   
      ![Begäran fortsatte: ”text”](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | Parameter | Föreslaget värde | 
      | --------- | --------------- |    
      | **Brödtext** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      Svaret innehåller det fullständiga svaret från API: et, inklusive resultatet eller, om det finns några fel.

      ![Få svar på begäranden](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. När du har kontrollerat att din begäran fungerar sparar din begäran till en Postman-samling. 

   1. Välj **Spara**. 
      
      ![Välj ”Spara”](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. Under **spara begära**, ange en **namn för begäran** och eventuellt en **begära beskrivning**. 

      > [!NOTE]
      > Din anpassade anslutningen används dessa värden senare. Kontrollera så att du anger samma värden som du senare använda för ditt anpassade API sammanfattning och beskrivning.

   3. Välj **+ skapa samlingen** och ange ett samlingsnamn. 
   Välj kryssmarkeringen, skapas en mapp för insamling och välj sedan **spara *din-Postman-samling-name***.

      ![Spara begäran](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>Spara begäran-svar

När du har sparat din begäran, kan du spara svaret. På så sätt kan svaret visas som ett exempel när du läser in begäran senare.

1. Ovanför svar-fönstret Välj **spara svar**. 

   ![Spara svar](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   Anpassade kopplingar stöder endast ett svar per begäran. 
   Om du sparar flera svar per begäran, används bara det första.

2. Ange ett namn för ditt exempel och välj **spara exempel**.

3. Fortsätta att skapa samlingen Postman med eventuella ytterligare begäranden och -svar.

### <a name="export-your-postman-collection"></a>Exportera samlingen Postman

1. När du är klar kan du exportera samlingen till en JSON-fil.

   ![Exportera samlingen](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. Välj den **samling v1** exportera format och bläddra till den plats där du vill spara JSON-filen.

   > [!NOTE]
   > För närvarande fungerar bara V1 för anpassade kopplingar.

   ![Välj exportformat: ”v1 samlingen”](./media/custom-connector-api-postman-collection/09-export-format.png)
   
Du kan nu använda samlingen Postman för att skapa anpassade API: er och kopplingar. När du exporterar samlingen kan importera du JSON-filen till Logic Apps, flöde eller PowerApps.

> [!IMPORTANT]
> Om du skapar en anpassad koppling från en Postman samling, se till att du tar bort den `Content-type` huvudet från åtgärder och utlösare. Måltjänsten, till exempel flödet lägger automatiskt till det här sidhuvudet. Ta också bort autentiseringshuvuden i den `securityDefintions` avsnittet från åtgärder och utlösare.

## <a name="next-steps"></a>Nästa steg

* [Logic Apps: Registrera anpassade kopplingar](../logic-apps/logic-apps-custom-connector-register.md)
* [Flöde: Registrera ditt connector](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: Registrera ditt connector](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [Anpassad koppling vanliga frågor och svar](../logic-apps/custom-connector-faq.md)

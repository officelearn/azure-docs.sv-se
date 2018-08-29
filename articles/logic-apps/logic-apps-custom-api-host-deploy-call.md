---
title: 'Distribuera och anropa webb-API: er och REST API: er från Azure Logic Apps | Microsoft Docs'
description: 'Distribuera och anropa webb-API: er och REST API: er för integrering arbetsflöden i Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, stepsic, LADocs
ms.topic: article
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.date: 05/26/2017
ms.openlocfilehash: 0d53c8355fadf53c81676a1fe3c71f8e0b046630
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126576"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Distribuera och anropa anpassade API: er från arbetsflöden i Azure Logic Apps

När du [skapa anpassade API: er](./logic-apps-create-api-app.md) för användning i logikapparbetsflöden, måste du distribuera dina API: er innan du kan anropa dem. Du kan distribuera dina API: er som [webbappar](../app-service/app-service-web-overview.md), men överväga att distribuera dina API: er som [API apps](../app-service/app-service-web-tutorial-rest-api.md), som gör ditt jobb enklare när du skapar, köra och förbruka API: er i molnet och lokalt. Du behöver ändra någon kod i dina API: er – distribuera bara din kod till en API-app. Du kan vara värd för dina API: er på [Azure App Service](../app-service/app-service-web-overview.md), en platform-as-a-service (PaaS) som tillhandahåller mycket skalbar och enkelt API som är värd för.

Även om den bästa upplevelsen kan du anropa API: er från en logikapp, lägga till [OpenAPI (tidigare Swagger) metadata](http://swagger.io/specification/) som beskriver ditt API: s åtgärder och parametrar. OpenAPI-filen kan ditt API integrera enkelt och fungerar bättre med logic apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Distribuera ditt API som en webbapp eller en API-app

Innan du kan anropa ditt anpassade API från en logikapp måste du distribuera ditt API som en webbapp eller en API-app till Azure App Service. Även om du vill göra OpenAPI fil som kan läsas av Logic Apps Designer, ställer du in egenskaperna för API-definition och aktivera [cross-origin resource sharing (CORS)](../app-service/app-service-web-overview.md) för din webbapp eller API-app.

1. I den [Azure-portalen](https://portal.azure.com), Välj din webbapp eller API-app.

2. I app-menyn som öppnas under **API**, Välj **API-definition**. Ange den **API-definitionsplats** i URL: en för din swagger.json OpenAPI-fil.

   Webbadressen visas vanligtvis i det här formatet: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Länka till OpenAPI-fil för ditt anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Under **API**, Välj **CORS**. Ställa in CORS-principen för **tillåtna ursprung** till **' *'** (Tillåt alla).

   Den här inställningen tillåter förfrågningar från Logic App Designer.

   ![Tillåta begäranden från Logic App Designer till ditt anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Mer information finns i [vara värd för en RESTful-API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Anropa ditt anpassade API från logic app-arbetsflöden

När du har konfigurerat egenskaperna för API-definition och CORS ska utlösare och åtgärder för ditt anpassade API vara tillgängliga för dig att inkludera i logikappens arbetsflöde. 

*  Om du vill visa webbplatser med OpenAPI-URL: er, kan du bläddra till din prenumeration webbplatser i Logic Apps Designer.

*  Du kan visa tillgängliga åtgärder och indata genom att peka på ett OpenAPI-dokument med den [HTTP + Swagger åtgärd](../connectors/connectors-native-http-swagger.md).

*  För att anropa API: er, inklusive API: er som inte har eller exponera ett OpenAPI-dokument kan du alltid skapa en begäran med den [HTTP-åtgärden](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Nästa steg

* [Översikt över anpassad anslutningsapp](../logic-apps/custom-connector-overview.md)
---
title: "Distribuera och anropa webb-API: er och REST API: er från Logikappar i Azure | Microsoft Docs"
description: "Distribuera och anropa webb-API: er & REST API: er för integrering arbetsflöden i Azure Logic Apps"
keywords: "webb-API: er, REST API: er, kopplingar, arbetsflöden, system integreringar, autentisera"
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: c7a240bf5b7ed5e7780b90f438d2e336ee79f0b3
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="deploy-and-call-custom-apis-from-logic-app-workflows"></a>Distribuera och anropa anpassade API: er från logik app arbetsflöden

När du [skapa anpassade API: er](./logic-apps-create-api-app.md) för användning i logik app arbetsflöden, måste du distribuera dina API: er innan du kan anropa dem. Du kan distribuera dina API: er som [webbappar](../app-service/app-service-web-overview.md), men överväga att distribuera dina API: er som [API apps](../app-service/app-service-web-tutorial-rest-api.md), vilket gör jobbet enklare när du skapar, värd och använda API: er i molnet och lokalt. Du behöver ändra någon kod i dina API: er – distribuera bara din kod till API-app. Du kan vara värd för dina API: er på [Azure App Service](../app-service/app-service-web-overview.md), en plattform som-en-tjänst (PaaS) erbjudande som ger mycket skalbar och lätt API värd.

Även om du kan anropa API: er från en logikapp för bästa möjliga upplevelse, lägga till [OpenAPI (tidigare Swagger) metadata](http://swagger.io/specification/) som beskriver ditt API åtgärder och parametrar. Den här filen OpenAPI hjälper din API integrera enklare och fungerar bättre med logic apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Distribuera din API som en webbapp eller API-app

Innan du kan anropa ditt anpassade API från en logikapp, kan du distribuera din API som en webbapp eller API-app till Azure App Service. Även om du vill att din fil som kan läsas av Logic Apps Designer OpenAPI ange egenskaper för API-definition och aktivera [resursdelning för korsande ursprung (CORS)](../app-service/app-service-web-overview.md) för ditt webbprogram eller API-app.

1. I den [Azure-portalen](https://portal.azure.com), Välj webbprogram eller API-app.

2. I appmenyn som öppnas under **API**, Välj **API-definition**. Ange den **plats för API-definition** till URL: en för OpenAPI swagger.json-filen.

   Webbadressen visas vanligtvis i det här formatet: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Länka till OpenAPI fil för din anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Under **API**, Välj **CORS**. Skapa princip för CORS för **tillåtna ursprung** till **' *'** (Tillåt alla).

   Den här inställningen tillåter begäranden från logik App Designer.

   ![Tillåt begäranden från logik App Designer för ditt anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Mer information finns i [värd för en RESTful-API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Anropa ditt anpassade API från logik app arbetsflöden

När du ställer in egenskaperna för API-definition och CORS ska din anpassade API utlösare och åtgärder vara tillgänglig att inkludera i logik app arbetsflödet. 

*  Du kan bläddra din prenumeration webbplatser i Logic Apps Designer för att visa webbplatser som har OpenAPI URL: er.

*  Om du vill visa tillgängliga åtgärder och indata genom att peka på ett OpenAPI dokument genom att använda den [HTTP + Swagger åtgärden](../connectors/connectors-native-http-swagger.md).

*  För att anropa API: er, inklusive API: er som inte har eller exponera en OpenAPI dokumentet kan du alltid skapa en begäran med den [HTTP-åtgärden](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Nästa steg

* [Översikt över anpassad koppling](../logic-apps/custom-connector-overview.md)
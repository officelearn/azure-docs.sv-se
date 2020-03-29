---
title: Distribuera och anropa webb-API:er & REST-API:er från Azure Logic Apps
description: Distribuera och anropa webb-API:er & REST-API:er för arbetsflöden för systemintegrering i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790750"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Distribuera och anropa anpassade API:er från arbetsflöden i Azure Logic Apps

När du [har skapat anpassade API:er](./logic-apps-create-api-app.md) för användning i logikapparbetsflöden måste du distribuera API:erna innan du kan ringa dem. Du kan distribuera dina API:er som [webbappar,](../app-service/overview.md)men överväg att distribuera dina API:er som [API-appar](../app-service/app-service-web-tutorial-rest-api.md), vilket gör jobbet enklare när du skapar, är värd och använder API:er i molnet och lokalt. Du behöver inte ändra någon kod i dina API:er – bara distribuera koden till en API-app. Du kan vara värd för dina API:er på [Azure App Service](../app-service/overview.md), ett PaaS-erbjudande (Platform-as-a-Service) som ger mycket skalbar och enkel API-värd.

Även om du kan anropa alla API från en logikapp, lägger du till [OpenAPI-metadata (tidigare Swagger)](https://swagger.io/specification/) för bästa möjliga upplevelse som beskriver API:s åtgärder och parametrar. Den här OpenAPI-filen hjälper ditt API att integreras enklare och fungerar bättre med logikappar.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Distribuera ditt API som en webbapp eller API-app

Innan du kan anropa ditt anpassade API från en logikapp distribuerar du ditt API som en webbapp eller API-app till Azure App Service. Om du vill göra filen OpenAPI läsbar av Logic Apps Designer anger du också api-definitionsegenskaperna och aktiverar [CORS (Cross-Origin Resource Sharing)](../app-service/overview.md) för din webbapp eller API-app.

1. Välj din webbapp eller API-app i [Azure-portalen.](https://portal.azure.com)

2. Välj **API-definition**under API under **API.** Ange **API-definitionsplatsen** till URL:en för filen OpenAPI swagger.json.

   Vanligtvis visas WEBBADRESSEN i det här formatet:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Länka till OpenAPI-fil för ditt anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Under **API**väljer du **CORS**. Ange CORS-principen för tillåtet **ursprung** till **'*'** (tillåt alla).

   Den här inställningen tillåter begäranden från Logic App Designer.

   ![Tillåt begäranden från Logic App Designer till ditt anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Mer information finns i Vara värd för [ett RESTful API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Anropa ditt anpassade API från logikapparbetsflöden

När du har konfigurerat API-definitionsegenskaperna och CORS bör det anpassade API:ets utlösare och åtgärder vara tillgängliga för dig att inkludera i logikapparbetsflödet. 

*  Om du vill visa webbplatser med OpenAPI-url:er kan du bläddra bland dina prenumerationswebbplatser i Logic Apps Designer.

*  Om du vill visa tillgängliga åtgärder och indata genom att peka på ett OpenAPI-dokument använder du [åtgärden HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Om du vill anropa alla API:er, inklusive API:er som inte har eller exponerar ett OpenAPI-dokument, kan du alltid skapa en begäran med [HTTP-åtgärden](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Nästa steg

* [Översikt över anpassad anslutningsapp](../logic-apps/custom-connector-overview.md)
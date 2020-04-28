---
title: 'Distribuera och anropa webb-API: er & REST API: er från Azure Logic Apps'
description: 'Distribuera och anropa webb-API: er & REST-API: er för system integrerings arbets flöden i Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74790750"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Distribuera och anropa anpassade API: er från arbets flöden i Azure Logic Apps

När du har [skapat anpassade API:](./logic-apps-create-api-app.md) er som ska användas i Logic app-arbetsflöden måste du distribuera dina API: er innan du kan anropa dem. Du kan distribuera dina API: er som [webbappar](../app-service/overview.md), men du bör överväga att distribuera dina API: er som [API Apps](../app-service/app-service-web-tutorial-rest-api.md), vilket gör jobbet enklare när du skapar, är värd för och använder API: er i molnet och lokalt. Du behöver inte ändra någon kod i dina API: er – distribuera bara din kod till en API-app. Du kan vara värd för dina API: er på [Azure App Service](../app-service/overview.md), ett PaaS-erbjudande (Platform-as-a-Service) som ger mycket skalbara, enkla API-värdar.

Även om du kan anropa alla API: er från en Logic app, kan du lägga till [openapi-metadata (tidigare Swagger)](https://swagger.io/specification/) som beskriver API: ernas åtgärder och parametrar för bästa möjliga upplevelse. Den här OpenAPI-filen hjälper ditt API att integrera enklare och arbeta bättre med Logic Apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Distribuera ditt API som en webbapp eller API-app

Innan du kan anropa ditt anpassade API från en Logic app kan du distribuera ditt API som en webbapp eller en API-app för att Azure App Service. Om du vill göra din OpenAPI-fil läsbar av Logic Apps designer ställer du in egenskaper för API-definitioner och aktiverar [resurs delning mellan ursprung (CORS)](../app-service/overview.md) för webbappen eller API-appen.

1. I [Azure Portal](https://portal.azure.com)väljer du webbappen eller API-appen.

2. I menyn app som öppnas under **API**väljer du API- **definition**. Ange **platsen för API-definition** till URL: en för openapi-Swagger. JSON-filen.

   Normalt visas URL: en i följande format:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Länk till OpenAPI-fil för ditt anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Under **API**väljer du **CORS**. Ange CORS-principen för **tillåtna ursprung** till **"*"** (Tillåt alla).

   Den här inställningen tillåter förfrågningar från Logic App Designer.

   ![Tillåt förfrågningar från Logic App Designer till ditt anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Mer information finns i [hantera en RESTful-API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Anropa ditt anpassade API från Logic app-arbetsflöden

När du har ställt in egenskaper och CORS för API-definitioner, ska dina anpassade API: er och åtgärder vara tillgängliga så att du kan ta med i ditt Logic app-arbetsflöde. 

*  Om du vill visa webbplatser som har OpenAPI-URL: er kan du bläddra bland dina prenumerations webbplatser i Logic Apps designer.

*  Om du vill visa tillgängliga åtgärder och indata genom att peka på ett OpenAPI-dokument använder du [åtgärden http + Swagger](../connectors/connectors-native-http-swagger.md).

*  Om du vill anropa API: er, inklusive API: er som inte har eller visar ett OpenAPI-dokument, kan du alltid skapa en begäran med [http-åtgärden](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Nästa steg

* [Översikt över anpassad anslutningsapp](../logic-apps/custom-connector-overview.md)
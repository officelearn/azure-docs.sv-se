---
title: Konvertera JSON-data med en flytande transformeringar – Azure Logic Apps | Microsoft Docs
description: Skapa transformeringar eller maps för avancerade JSON-transformationer med Logic Apps och flytande mall
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 3441350a07047676ac43de23262be6c54912162c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104173"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Utföra avancerade JSON-transformationer med en flytande mallar i Azure Logic Apps

Du kan utföra grundläggande JSON-transformationer i dina logic apps med inbyggda åtgärden dataåtgärder som **Compose** eller **parsa JSON**. Om du vill utföra avancerade JSON-transformationer, du kan skapa mallar eller kartor med [flytande](https://shopify.github.io/liquid/), vilket är en öppen källkod mallspråk för flexibla web apps. Flytande mallar kan du definiera hur du omvandlar JSON-utdata och har stöd för mer komplexa JSON-transformationer, t.ex iterationer, kontrollera flöden, variabler och så vidare. 

Därför innan du kan utföra en flytande omvandling i din logikapp måste definiera du först JSON till JSON-mappning med en flytande mall och store som mappar i ditt integrationskonto. Den här artikeln visar hur du skapar och använder den här flytande mall eller en karta. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Eller, [registrera dig för en prenumeration med användningsbaserad betalning](https://azure.microsoft.com/pricing/purchase-options/).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* En grundläggande [Integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Grundläggande kunskaper om [flytande mallspråk.](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Skapa en flytande mall eller karta för ditt integrationskonto

1. I det här exemplet skapar du en flytande exempelmallen som beskrivs i det här steget. Du kan använda i din mall för flytande [flytande filtrerar](https://shopify.github.io/liquid/basics/introduction/#filters), som använder [DotLiquid](https://dotliquidmarkup.org/) och C# namngivningskonventioner. Dock se till att du *börja Filternamn med versaler*, inte gemener. 

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

2. Logga in på [Azure Portal](https://portal.azure.com). Välj på Azure-huvudmenyn **alla resurser**. I sökrutan, hitta och välj ditt integrationskonto.

   ![Välj integrationskontot](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Under **komponenter**väljer **Maps**.

    ![Välj maps](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Välj **Lägg till** och ange följande information på kartan:

   | Egenskap  | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | JsonToJsonTemplate | Namnet på kartan, vilket är ”JsonToJsonTemplate” i det här exemplet | 
   | **Mappningstyp** | **liquid** | Typ för kartan. Du måste välja för JSON till JSON-transformering **flytande**. | 
   | **Karta** | "SimpleJsonToJsonTemplate.liquid" | En befintlig flytande mall eller att mappa fil som ska användas för transformering, vilket är ”SimpleJsonToJsonTemplate.liquid” i det här exemplet. Du kan använda filväljaren för den här filen. |
   ||| 

   ![Lägg till en flytande mall](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Lägg till instruktionen flytande för JSON-transformering

1. I Azure-portalen följer du dessa steg för att [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. I Logic App Designer, lägger du till den [begäransutlösare](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) i logikappen.

3. Under utlösaren väljer **nytt steg**. 
   Ange ”flytande” som filter i sökrutan och välj den här åtgärden: **Transformera JSON till JSON - Liquid**

   ![Hitta och välja en flytande åtgärd](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Klicka i den **innehåll** så att den dynamiska innehållslistan visas och väljer den **brödtext** token.
  
   ![Välj brödtext](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Från den **kartan** väljer du den flytande mallen, som är ”JsonToJsonTemplate” i det här exemplet.

   ![Välj karta](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Om maps-listan är tom, är är förmodligen logikappen inte länkad till ditt integrationskonto. 
   Följ dessa steg för att länka din logikapp till integrationskontot med en flytande mall eller karta:

   1. På logikappmenyn, väljer **arbetsflödesinställningarna**.

   2. Från den **Välj ett integrationskonto** väljer du ditt integrationskonto, och välj **spara**.

      ![Länka logic app till integrationskontot](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testa din logikapp

Publicera JSON-indata till din logikapp från [Postman](https://www.getpostman.com/postman) eller ett liknande verktyg. Den omvandlade JSON-utdatan från din logikapp ser ut som i följande exempel:
  
![Exempel på utdata](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Fler exempel på en flytande åtgärd
Flytande är inte begränsad till JSON-transformationer. Här följer andra tillgängliga omvandling åtgärder som använder flytande.

* Transformera JSON till text
  
  Här är en flytande mallen som används för det här exemplet:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Här följer Exempelindata och utdata:
  
   ![Exempel på utdata JSON till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformera XML till JSON
  
  Här är en flytande mallen som används för det här exemplet:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Här följer Exempelindata och utdata:

   ![Exempel på utdata XML till JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformera XML till text
  
  Här är en flytande mallen som används för det här exemplet:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Här följer Exempelindata och utdata:

   ![Exempel på utdata XML till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Nästa steg

* [Mer information om Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  
* [Läs mer om maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Lär dig mer om maps för enterprise-integration")  


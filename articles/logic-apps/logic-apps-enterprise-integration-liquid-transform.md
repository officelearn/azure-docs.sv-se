---
title: Konvertera JSON-data med flytande transformeringar
description: Skapa transformeringar eller kartor för avancerade JSON-omvandlingar med hjälp av Logic Apps och flytande mall
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: fb9f9cfdba07ebe0bc5800def6d93950869e9727
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456641"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Utföra avancerade JSON-omvandlingar med flytande mallar i Azure Logic Apps

Du kan utföra grundläggande JSON-omvandlingar i Logi Kap par med åtgärder för inbyggd data åtgärd, till exempel **skapa** eller **parsa JSON**. Om du vill utföra avancerade JSON-omvandlingar kan du skapa mallar eller kartor med [flytande](https://shopify.github.io/liquid/), som är ett mall-språk med öppen källkod för flexibla webb program. En flytande mall definierar hur JSON-utdata ska transformeras och stöder mer komplexa JSON-omvandlingar, till exempel iterationer, kontroll flöden, variabler och så vidare. 

Innan du kan utföra en flytande omvandling i din Logic app måste du först definiera JSON-till-JSON-mappningen med en flytande mall och lagra mappningen i ditt integrations konto. Den här artikeln visar hur du skapar och använder den här vätske mal len eller kartan. 

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Eller [Registrera dig för en prenumeration där du betalar per](https://azure.microsoft.com/pricing/purchase-options/)användning.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ett grundläggande [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Grundläggande kunskaper om [flytande mallar språk](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Skapa en flytande mall eller karta för ditt integrations konto

1. I det här exemplet skapar du exempel mal len flytande som beskrivs i det här steget. I din flytande mall kan du använda [vätskor filter](https://shopify.github.io/liquid/basics/introduction/#filters), som använder [DotLiquid](https://dotliquidmarkup.org/) och C# namngivnings konventioner. 

   > [!NOTE]
   > Kontrol lera att filter namnen använder *menings hölje* i din mall. Annars fungerar inte filtren. Mappar har också [fil storleks begränsningar](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits).

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

2. Logga in på [Azure-portalen](https://portal.azure.com). På huvud menyn i Azure väljer du **alla resurser**. I sökrutan söker du efter och väljer ditt integrations konto.

   ![Välj integrations konto](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Under **komponenter**väljer du **Maps**.

    ![Välj kartor](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Välj **Lägg till** och ange den här informationen för kartan:

   | Egenskap | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | JsonToJsonTemplate | Namnet på kartan, som är "JsonToJsonTemplate" i det här exemplet | 
   | **Kart typ** | **liquid** | Kartans typ. För JSON till JSON-transformering måste du välja **flytande**. | 
   | **Karta** | "SimpleJsonToJsonTemplate.liquid" | En befintlig flytande mall eller kart fil som ska användas för omvandling, som är "SimpleJsonToJsonTemplate. flytande" i det här exemplet. Du kan använda fil väljaren för att hitta den här filen. För mappnings storleks gränser, se [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Lägg till flytande mall](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Lägg till vätske åtgärden för JSON-transformering

1. I Azure Portal följer du de här stegen för att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Lägg till [utlösaren för begäran](../connectors/connectors-native-reqres.md#add-request) till din Logic app i Logic App Designer.

3. Under utlösaren väljer du **nytt steg**. 
   I sökrutan anger du "flytande" som filter och väljer den här åtgärden: **TRANSFORMERA JSON till JSON-flytande**

   ![Sök och välj flytande åtgärd](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Klicka i **innehålls** rutan så att listan med dynamiskt innehåll visas och välj **Body** -token.
  
   ![Välj brödtext](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. I listan **karta** väljer du mallen flytande, som är "JsonToJsonTemplate" i det här exemplet.

   ![Välj karta](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Om Maps-listan är tom är den mest sannolika att din Logic app inte är länkad till ditt integrations konto. 
   Följ dessa steg om du vill länka din Logic app till det integrations konto som har den flytande mallen eller kartan:

   1. På din Logic app-meny väljer du **arbets flödes inställningar**.

   2. I listan **Välj ett integrations konto** väljer du ditt integrations konto och väljer **Spara**.

      ![Länka Logic app till integrations konto](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testa din Logic app

Publicera JSON-indata till din Logic app från [Postman](https://www.getpostman.com/postman) eller ett liknande verktyg. De transformerade JSON-utdata från din Logic app ser ut som i det här exemplet:
  
![Exempel på utdata](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Fler exempel på flytande åtgärder
Flytande är inte begränsad till JSON-transformationer. Här följer andra tillgängliga omvandlings åtgärder som använder Liquid.

* Transformera JSON till text
  
  Här är den flytande mall som används för det här exemplet:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Här är exempel på indata och utdata:
  
   ![Exempel på utdata-JSON till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformera XML till JSON
  
  Här är den flytande mall som används för det här exemplet:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Här är exempel på indata och utdata:

   ![Exempel på utdata-XML till JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformera XML till text
  
  Här är den flytande mall som används för det här exemplet:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Här är exempel på indata och utdata:

   ![Exempel på utdata-XML till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise-integrationspaket")  
* [Läs mer om Maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Lär dig mer om Enterprise integration Maps")  


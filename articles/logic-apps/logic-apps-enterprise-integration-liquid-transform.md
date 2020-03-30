---
title: Konvertera JSON-data med vätsketransformningar
description: Skapa transformeringar eller kartor för avancerade JSON-omvandlingar med hjälp av logic apps och flytande mall
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: fb9f9cfdba07ebe0bc5800def6d93950869e9727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456641"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Utföra avancerade JSON-omvandlingar med flytande mallar i Azure Logic Apps

Du kan utföra grundläggande JSON-omvandlingar i dina logikappar med åtgärder för inbyggt dataåtgärder som **Compose** eller **Parse JSON**. Om du vill utföra avancerade JSON-omvandlingar kan du skapa mallar eller kartor med [Liquid](https://shopify.github.io/liquid/), vilket är ett mallspråk med öppen källkod för flexibla webbappar. En flytande mall definierar hur du omvandlar JSON-utdata och stöder mer komplexa JSON-omvandlingar, till exempel iterationer, kontrollflöden, variabler och så vidare. 

Innan du kan utföra en flytande omvandling i logikappen måste du först definiera JSON-till JSON-mappningen med en flytande mall och lagra den kartan i ditt integrationskonto. Den här artikeln visar hur du skapar och använder denna flytande mall eller karta. 

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Eller [registrera dig för en prenumeration med användningsbaserad betalning](https://azure.microsoft.com/pricing/purchase-options/).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ett grundläggande [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Grundläggande kunskaper om [flytande mallspråk](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Skapa flytande mall eller karta för ditt integrationskonto

1. I det här exemplet skapar du exempelmallen Flytande som beskrivs i det här steget. I din liquid-mall kan du använda [Flytande filter](https://shopify.github.io/liquid/basics/introduction/#filters), som använder [DotLiquid](https://dotliquidmarkup.org/) och C# namngivningskonventioner. 

   > [!NOTE]
   > Kontrollera att filternamnen använder *meningshölje* i mallen. Annars fungerar inte filtren. Kartor har också [filstorleksgränser](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits).

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

2. Logga in på [Azure-portalen](https://portal.azure.com). På huvudmenyn i Azure väljer du **Alla resurser**. Leta reda på och välj ditt integrationskonto i sökrutan.

   ![Välj integrationskonto](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Under **Komponenter**väljer du **Kartor**.

    ![Välj kartor](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Välj **Lägg till** och ange dessa uppgifter för din karta:

   | Egenskap | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | JsonToJsonTemplate | Namnet på kartan, som är "JsonToJsonTemplate" i det här exemplet | 
   | **Typ av karta** | **Flytande** | Typ för din karta. För JSON till JSON omvandling, måste du välja **vätska**. | 
   | **Karta** | "SimplejsonTojsonTemplate.liquid" | En befintlig flytande mall eller kartfil som ska användas för omvandling, som är "SimpleJsonToJsonTemplate.liquid" i det här exemplet. Om du vill hitta den här filen kan du använda filväljaren. Begränsningar för kartstorlek finns i [Gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Lägg till flytande mall](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Lägg till flytande åtgärd för JSON omvandling

1. I Azure-portalen följer du dessa steg för att [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Lägg till [utlösaren för begäran i](../connectors/connectors-native-reqres.md#add-request) logikappen i Logikappdesignern.

3. Under utlösaren väljer du **Nytt steg**. 
   I sökrutan anger du "flytande" som filter och väljer den här åtgärden: **Förvandla JSON till JSON - Vätska**

   ![Sök efter och välj Flytande åtgärd](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Klicka i rutan **Innehåll** så att listan med dynamiskt innehåll visas och välj **body-token.**
  
   ![Välj brödtext](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Välj din Flytande mall i listan **Karta,** som är "JsonToJsonTemplate" i det här exemplet.

   ![Välj karta](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Om mapplistan är tom är din logikapp troligen inte länkad till ditt integrationskonto. 
   Så här länkar du logikappen till integrationskontot som har mallen flytande eller karta:

   1. Välj **Arbetsflödesinställningar**på logikapp-menyn .

   2. Välj ditt integrationskonto i listan **Välj ett integrationskonto** och välj **Spara**.

      ![Länka logikapp till integrationskonto](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testa logikappen

Publicera JSON-indata i logikappen från [Postman](https://www.getpostman.com/postman) eller ett liknande verktyg. Den transformerade JSON-utgången från logikappen ser ut så här:
  
![Exempel på utdata](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Fler exempel på flytande åtgärder
Vätska är inte begränsad till endast JSON transformationer. Här är andra tillgängliga omvandlingsåtgärder som använder Liquid.

* Omvandla JSON till text
  
  Här är flytande mall som används för detta exempel:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Här är exempelindata och utdata:
  
   ![Exempel på utdata JSON till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Omvandla XML till JSON
  
  Här är flytande mall som används för detta exempel:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Här är exempelindata och utdata:

   ![Exempel på XML för utdata till JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Omvandla XML till text
  
  Här är flytande mall som används för detta exempel:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Här är exempelindata och utdata:

   ![Exempel på XML för utdata till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise Integration Pack")  
* [Läs mer om kartor](../logic-apps/logic-apps-enterprise-integration-maps.md "Läs mer om företagsintegrationskartor")  


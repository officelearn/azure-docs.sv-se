---
title: Konvertera JSON-data med vätsketransformningar
description: Skapa transformeringar eller kartor för avancerade JSON-omvandlingar med hjälp av logic apps och flytande mall
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: 0ab9297e772a3b75a077da1c2ae74e5058b2731f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657190"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Utföra avancerade JSON-omvandlingar med flytande mallar i Azure Logic Apps

Du kan utföra grundläggande JSON-omvandlingar i dina logikappar med åtgärder för inbyggt dataåtgärder som **Compose** eller **Parse JSON**. Om du vill utföra avancerade JSON-omvandlingar kan du skapa mallar eller kartor med [Liquid](https://shopify.github.io/liquid/), vilket är ett mallspråk med öppen källkod för flexibla webbappar. En flytande mall definierar hur du omvandlar JSON-utdata och stöder mer komplexa JSON-omvandlingar, till exempel iterationer, kontrollflöden, variabler och så vidare.

Innan du kan utföra en flytande omvandling i logikappen måste du först definiera JSON-till JSON-mappningen med en flytande mall och lagra den kartan i ditt integrationskonto. Den här artikeln visar hur du skapar och använder denna flytande mall eller karta.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

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

1. Ange i [Azure-portalen](https://portal.azure.com)i sökrutan `integration accounts`i Azure och välj **Integrationskonton**.

   ![Hitta "Integrationskonton"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Hitta och välj ditt integrationskonto.

   ![Välj integrationskonto](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Välj **Kartor**under **Komponenter**i **fönstret Översikt** .

    ![Välj panel "Kartor"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. I fönstret **Kartor** väljer du **Lägg till** och anger följande information för kartan:

   | Egenskap | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | `JsonToJsonTemplate` | Namnet på kartan, som är "JsonToJsonTemplate" i det här exemplet | 
   | **Typ av karta** | **Flytande** | Typ för din karta. För JSON till JSON omvandling, måste du välja **vätska**. | 
   | **Karta** | `SimpleJsonToJsonTemplate.liquid` | En befintlig flytande mall eller kartfil som ska användas för omvandling, som är "SimpleJsonToJsonTemplate.liquid" i det här exemplet. Om du vill hitta den här filen kan du använda filväljaren. Begränsningar för kartstorlek finns i [Gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Lägg till flytande mall](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Lägg till flytande åtgärd för JSON omvandling

1. I Azure-portalen följer du dessa steg för att [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Lägg till [utlösaren för begäran i](../connectors/connectors-native-reqres.md#add-request) logikappen i Logikappdesignern.

1. Under utlösaren väljer du **Nytt steg**. I sökrutan anger `liquid` du som filter och väljer den här åtgärden: **Omvandla JSON till JSON - Vätska**

   ![Sök efter och välj Flytande åtgärd](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Öppna **kartlistan** och välj din Liquid-mall, som är "JsonToJsonTemplate" i det här exemplet.

   ![Välj karta](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Om mapplistan är tom är din logikapp troligen inte länkad till ditt integrationskonto. 
   Så här länkar du logikappen till integrationskontot som har mallen flytande eller karta:

   1. Välj **Arbetsflödesinställningar**på logikapp-menyn .

   1. Välj ditt integrationskonto i listan **Välj ett integrationskonto** och välj **Spara**.

      ![Länka logikapp till integrationskonto](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Lägg nu till egenskapen **Innehåll** i den här åtgärden. Öppna listan **Lägg till ny parameter** och välj **Innehåll**.

   ![Lägg till egenskapen "Innehåll" i åtgärd](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Om du vill ange **egenskapsvärdet Innehåll** klickar du i rutan **Innehåll** så att listan med dynamiskt innehåll visas. Välj **body** token, som representerar brödtextinnehållet som matas ut från utlösaren.

   ![Välj "Body"-token för egenskapsvärdet "Innehåll"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   När du är klar ser villkoret ut som i det här exemplet:

   ![Åtgärden "Transform JSON to JSON" har slutförts](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

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


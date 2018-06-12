---
title: Konvertera JSON-data med flytande transformeringar - Azure Logic Apps | Microsoft Docs
description: Skapa transformeringar eller för avancerade JSON-omformningar med hjälp av Logic Apps och flytande mallen.
services: logic-apps
documentationcenter: ''
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 42a102c9b2663380a93d56cc5f8fb82abaa83b74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299519"
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Utföra avancerade JSON-transformationer med en flytande mall

Med Azure Logikappar stöder grundläggande JSON omvandlingar via inbyggda-åtgärden åtgärder som **Compose** eller **parsa JSON**. Du kan använda flytande mallar med logic apps för avancerade JSON-transformationer. 
[Flytande](https://shopify.github.io/liquid/) är en öppen källkod mallspråk för flexibel web apps.
 
I den här artikeln lär du dig hur du använder en flytande kartan eller en mall som har stöd för mer komplexa JSON-omformningar, till exempel iterationer, kontroll flöden, variabler och så vidare. Innan du kan utföra en flytande omvandling i din logikapp, måste du definiera mappning från JSON till JSON för en flytande kartan och lagra mappar i ditt konto för integrering.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du [registrera dig för en prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* Grundläggande kunskaper om [skapa logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* En grundläggande [integrering konto](logic-apps-enterprise-integration-create-integration-account.md)


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>Skapa en flytande mall eller en mappning för ditt konto för integrering

1. Skapa exempel flytande mall för det här exemplet. Flytande mallen definierar hur du omvandlar JSON-indata som beskrivs här:

   ``` json
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
   > [!NOTE]
   > Om mallen flytande använder någon [filter](https://shopify.github.io/liquid/basics/introduction/#filters), dessa filter måste inledas med versal. 

2. Logga in på [Azure Portal](https://portal.azure.com).

3. Välj på Azure Huvudmeny, **alla resurser**. 

4. Hitta och välj ditt konto för integrering i sökrutan.

   ![Välj integration konto](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  Välj på panelen integration konto **Maps**.

   ![Välj maps](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Välj **Lägg till** och ange följande information för kartan:

   * **Namnet**: namnet på kartan som är ”JsontoJsonTemplate” i det här exemplet
   * **Mappa typen**: typen för kartan. Du måste välja för JSON till JSON-transformation **flytande**.
   * **Kartan**: en befintlig flytande mall eller att mappa fil som ska användas för omvandling, vilket är ”SimpleJsonToJsonTemplate.liquid” i det här exemplet. Du kan använda filväljaren för att hitta den här filen.

   ![Lägg till flytande mall](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Lägg till instruktionen flytande för JSON-omvandling

1. [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Lägg till den [begäran utlösaren](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) till din logikapp.

3. Välj **+ nytt steg > Lägg till en åtgärd**. I sökrutan anger *flytande*, och välj **flytande - transformeringen JSON till JSON**.

   ![Sök efter och välj flytande åtgärd](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. I den **innehåll** väljer **brödtext** från dynamiskt innehåll eller parametrar lista, beroende på vad som visas.
  
   ![Välj brödtext](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Från den **kartan** väljer du mallen flytande som är ”JsonToJsonTemplate” i det här exemplet.

   ![Välj karta](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Om listan är tom är logikappen förmodligen inte länkad till ditt konto för integrering. 
   Följ anvisningarna nedan om du vill länka din logikapp till kontot för integrering med flytande mall eller karta

   1. Välj på appmenyn dina logic **inställningar för arbetsflöde**.
   2. Från den **välja ett konto för integrering** väljer integration kontot, och välj **spara**.

   ![Länken logikapp integrering konto](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testa logikappen

Bokför JSON-indata till logikappen från [Postman](https://www.getpostman.com/postman) eller ett liknande verktyg. Den omvandlade JSON-utdatan från din logikapp ser ut så det här exemplet:
  
![Exempel på utdata](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Fler exempel på flytande åtgärd
Flytande är inte begränsat till JSON-omformningar. Här följer andra tillgängliga omvandling åtgärder som använder flytande.

* Transformera JSON till text
  
  Här är flytande mallen som används för det här exemplet:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Här är Exempelindata och utdata:
  
   ![Exempel på utdata JSON till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformera XML till JSON
  
  Här är flytande mallen som används för det här exemplet:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Här är Exempelindata och utdata:

   ![Exempel på utdata XML till JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformera XML till text
  
  Här är flytande mallen som används för det här exemplet:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Här är Exempelindata och utdata:

   ![Exempel på utdata XML till text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Nästa steg

* [Mer information om Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  
* [Mer information om maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Lär dig mer om enterprise integration maps")  


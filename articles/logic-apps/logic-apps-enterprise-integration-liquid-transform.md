---
title: Konvertera JSON-data med flytande transformeringar - Azure Logic Apps | Microsoft Docs
description: "Skapa transformeringar eller för avancerade JSON-omformningar med hjälp av Logic Apps och flytande mallen."
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: cd177b1ebcb5d236ce265dc153ee6a02125a69df
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="advanced-json-transformations-using-liquid-template"></a>Avancerade JSON-transformationer med flytande mall
Med Azure Logikappar stöder grundläggande JSON omvandlingar via native Dataåtgärden åtgärder som skriva eller parsa JSON. Logic Apps nu också stöd för avancerade JSON-transformationer med flytande mallar. [Flytande](https://shopify.github.io/liquid/) är en öppen källkod mallspråk för flexibel web apps.
 
I den här artikeln lär du dig hur du använder en flytande kartan eller en mall som har stöd för mer komplexa JSON-omformningar, till exempel iterationer, kontroll flöden, variabler och så vidare. Du måste definiera JSON för JSON-mappning med den här flytande kartan och lagrar som mappar i integration kontot innan du kan utföra en flytande omvandling i din logikapp.

## <a name="prerequisites"></a>Krav
Här är förutsättningar för att kunna använda instruktionen flytande:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du [registrera dig för en prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* Grundläggande kunskaper om [skapa logikappar](../logic-apps/logic-apps-create-a-logic-app.md).

* En grundläggande [integrering konto](logic-apps-enterprise-integration-create-integration-account.md).


## <a name="create-and-add-liquid-template-or-map-to-integration-account"></a>Skapa och Lägg till flytande mall eller mappas till integration konto

1. Skapa exempel flytande mall för det här exemplet. Flytande mallen definierar hur du omvandlar JSON-indata som beskrivs här:

   ```
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

3. Välj i Azure Huvudmeny, **alla resurser**. 

4. Ange kontot för integrering i sökrutan. Välj ditt konto.

   ![Välj integration konto](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  Välj på panelen integration konto **Maps**.

   ![Välj maps](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Välj **Lägg till** och ange följande information för kartan:
  * **Namnet**: namnet på kartan som är ”JsontoJsonTemplate” i det här exemplet.
  * **Mappa typen**: typen för kartan. Du måste välja för JSON till JSON-transformation **flytande**.
  * **Kartan**: en befintlig flytande mall eller att mappa fil som ska användas för omvandling, vilket är ”SimpleJsonToJsonTemplate.liquid” i det här exemplet. Du kan använda filväljaren för att hitta den här filen.

    ![Lägg till flytande mall](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

    
## <a name="add-the-liquid-action-to-transform-json-in-your-logic-app"></a>Lägg till instruktionen flytande för att omvandla JSON i din logikapp

1. [Skapa en logikapp](logic-apps-create-a-logic-app.md).

2. Lägg till den [begäran utlösaren](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) till din logikapp.

3. Välj **+ nytt steg > Lägg till en åtgärd**. Sök efter *flytande* i sökrutan. Välj **flytande - transformeringen JSON till JSON**.

  ![Sök-åtgärd-flytande](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. I den **innehåll** väljer **brödtext** från dynamiskt innehåll eller parametrar lista, beroende på vad som visas. 
  
  ![Välj brödtext](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Från den **kartan** listrutan väljer du den flytande mallen, som är JsonToJsonTemplate i det här exemplet.

  ![Välj-karta](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Om listan är tom inte är logikappen troligen länkad till ditt konto för integrering. Följ anvisningarna nedan om du vill länka din logikapp till kontot för integrering med flytande mall eller karta

   1. Välj på appmenyn dina logic **inställningar för arbetsflöde**. 
   2. Från den **välja ett konto för integrering** väljer integration kontot, och välj **spara**.

     ![Länken logikapp integrering konto](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)


## <a name="test-your-logic-app"></a>Testa logikappen

   Bokför JSON-indata till logikappen från [Postman](https://www.getpostman.com/postman) eller ett liknande verktyg. Den omvandlade JSON-utdatan från din logikapp ser ut så det här exemplet:
  
   ![Exempel på utdata](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)


## <a name="next-steps"></a>Nästa steg
* [Mer information om Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  
* [Mer information om maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Lär dig mer om enterprise integration maps")  


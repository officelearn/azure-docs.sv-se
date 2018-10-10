---
title: Lägga till villkorssatser i arbetsflöden – Azure Logic Apps | Microsoft Docs
description: Skapa villkor som styr åtgärder i arbetsflöden i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 462a21c760f7dec727148f2a41dec9f508e24dbc
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885246"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Skapa villkorssatser som styr arbetsflödesåtgärder i Azure Logic Apps

Om du vill köra specifika åtgärder i din logikapp efter att skicka ett angivet villkor, lägger du till en *villkorlig instruktionen*. Den här kontrollen strukturen jämför data i ditt arbetsflöde mot specifika värden eller fält. Du kan ange olika åtgärder som körs baserat på huruvida data uppfyller villkoret. Du kan kapsla villkor i varandra.

Anta exempelvis att du har en logikapp som skickar för många e-postmeddelanden när nya objekt visas på en webbplats RSS-feed. Du kan lägga till en villkorlig instruktion för att skicka e-post endast när det nya objektet innehåller en specifik sträng. 

> [!TIP]
> För att köra olika steg baserat på olika specifika värden, använder en [ *växla instruktionen* ](../logic-apps/logic-apps-control-flow-switch-statement.md) i stället.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Till exempel i den här artikeln [skapa den här exempellogikappen](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com eller Office 365 Outlook-konto.

## <a name="add-condition"></a>Lägg till villkor

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna logikappen i Logic App Designer.

1. Lägg till ett villkor på den plats som du vill. 

   För att lägga till ett villkor mellan steg flyttar du pekaren över pilen där du vill lägga till villkoret. Välj den **plustecknet** (**+**) som visas, välj sedan **Lägg till en åtgärd**. Exempel:

   ![Lägg till åtgärd mellan steg](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   När du vill lägga till ett villkor i slutet av arbetsflödet, längst ned på logikappen, Välj **nytt steg** > **Lägg till en åtgärd**.

1. I sökrutan anger du ”villkor” som filter. Välj den här åtgärden: **villkoret - kontroll**

   ![Lägg till villkor](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. I den **villkor** rutan, skapa dina villkor. 

   1. I den vänstra rutan, anger du de data eller fält som du vill jämföra.

      När du klickar i den vänstra rutan visas den dynamiska innehållslistan så du kan välja utdata från föregående steg i din logikapp. 
      Välj RSS-feed-sammanfattning i det här exemplet.

      ![Skapa dina villkor](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Välj åtgärden som ska utföras i den mellersta rutan. 
   Det här exemplet väljer du ”**innehåller**”. 

   1. I den högra rutan anger du ett värde eller ett fält som dina kriterier. 
   I det här exemplet anger du strängen: **Microsoft**

   Här är det fullständiga villkoren:

   ![Slutför villkor](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Om du vill lägga till en ny rad i villkoret, Välj **Lägg till** > **Lägg till rad**. 
   Om du vill lägga till en grupp med subconditions, Välj **Lägg till** > **Lägg till grupp**. 
   Om du vill gruppera befintliga rader, markerar du kryssrutorna för de raderna, väljer du ellipserna (...)-knappen för alla rader och välj sedan **Kontrollera grupp**.

1. Under **om värdet är true** och **om falskt**, lägga till stegen för att utföra baserat på om villkoret är uppfyllt. Exempel:

   ![Ange villkor med ”om det är SANT” och ”om det är FALSKT” sökvägar](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Du kan dra befintliga åtgärder i den **om värdet är true** och **om falskt** sökvägar.

1. Spara din logikapp.

Den här logikappen skickar e-post nu endast när nya objekt i RSS-flödet uppfyller villkoret.

## <a name="json-definition"></a>JSON-definition

Här är den övergripande koddefinitionen bakom en villkorlig instruktion:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på funktioner och förslag, Besök den [webbplatsen för Azure Logic Apps-Användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

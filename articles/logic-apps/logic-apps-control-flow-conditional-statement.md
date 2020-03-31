---
title: Lägga till villkorssatser i arbetsflöden
description: Så här skapar du villkor som styr åtgärder i arbetsflöden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: fe79cf5af86e1f303e4735214b993d8db4488a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793245"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Skapa villkorssatser som styr arbetsflödesåtgärder i Azure Logic Apps

Om du bara vill köra specifika åtgärder i logikappen efter att du har passerat ett angivet villkor lägger du till en *villkorssats*. Den här kontrollstrukturen jämför data i arbetsflödet med specifika värden eller fält. Du kan sedan ange olika åtgärder som körs baserat på om data uppfyller villkoret eller inte. Du kan kapsla förhållanden inuti varandra.

Anta till exempel att du har en logikapp som skickar för många e-postmeddelanden när nya objekt visas i en webbplats RSS-flöde. Du kan bara lägga till en villkorssats om du bara vill skicka e-post när det nya objektet innehåller en viss sträng. 

> [!TIP]
> Om du vill köra olika steg baserat på olika specifika värden använder du en [*switch-sats*](../logic-apps/logic-apps-control-flow-switch-statement.md) i stället.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Om du vill följa exemplet i den här artikeln [skapar du den här exempellogiken med](../logic-apps/quickstart-create-first-logic-app-workflow.md) ett Outlook.com eller Office 365 Outlook-konto.

## <a name="add-condition"></a>Lägg till villkor

1. Öppna logikappen i Logic App Designer i <a href="https://portal.azure.com" target="_blank">Azure-portalen.</a>

1. Lägg till ett villkor på den plats du vill använda. 

   Om du vill lägga till ett villkor mellan stegen flyttar du pekaren över pilen där du vill lägga till villkoret. Välj **plustecknet** **+**( ) som visas och välj sedan **Lägg till en åtgärd**. Ett exempel:

   ![Lägga till åtgärd mellan steg](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   När du vill lägga till ett villkor i slutet av arbetsflödet väljer du **Nytt steg** > **Lägg till en åtgärd**längst ned i logikappen .

1. Ange "villkor" som filter i sökrutan. Välj den här åtgärden: **Villkor - Kontroll**

   ![Lägg till villkor](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Bygg ditt tillstånd i rutan **Skick.** 

   1. I den vänstra rutan anger du de data eller det fält som du vill jämföra.

      När du klickar i den vänstra rutan visas listan med dynamiskt innehåll så att du kan välja utdata från föregående steg i logikappen. 
      I det här exemplet väljer du sammanfattningen av RSS-feeden.

      ![Bygg upp ditt tillstånd](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Markera den åtgärd som ska utföras i den mittruta som ska utföras. 
   I det här exemplet innehåller du "**innehåller**". 

   1. I den högra rutan anger du ett värde eller ett fält som dina villkor. 
   I det här exemplet anger du den här strängen: **Microsoft**

   Här är det fullständiga villkoret:

   ![Slutför villkor](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Om du vill lägga till ytterligare en rad i villkoret väljer du **Lägg till** > **lägg till rad**. 
   Om du vill lägga till en grupp med undervillkor väljer du **Lägg till** > **grupp**. 
   Om du vill gruppera befintliga rader markerar du kryssrutorna för dessa rader, väljer ellipser (...) för valfri rad och väljer sedan **Skapa grupp**.

1. Under **Om sant** och Om **falskt**lägger du till stegen för att utföra baserat på om villkoret är uppfyllt. Ett exempel:

   ![Villkor med sökvägarna "Om sant" och "Om falskt"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Du kan dra befintliga åtgärder till **om true** och **Om falska** banor.

1. Spara din logikapp.

Den här logikappen skickar nu bara e-post när de nya objekten i RSS-flödet uppfyller ditt tillstånd.

## <a name="json-definition"></a>JSON-definition

Här är högnivåkoddefinitionen bakom en villkorsbeskrivning:

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
* Om du vill skicka in eller rösta om funktioner och förslag besöker du [webbplatsen för användarfeedback för Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på olika värden (växelsatser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (loopar)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperad åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

---
title: Skapa eller koppla parallella grenar för åtgärder i arbetsflöden
description: Lär dig hur du skapar eller sammanfogar parallella grenar som körs för oberoende arbetsflödesåtgärder i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: c0b1519992ba930382a1987aed185ef3c92eded4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453439"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Skapa eller ansluta till parallella grenar för arbetsflödesåtgärder i Azure Logic Apps

Som standard körs dina åtgärder i logikapparbetsflöden sekventiellt. Om du vill utföra oberoende åtgärder samtidigt kan du skapa [parallella grenar](#parallel-branches)och sedan [ansluta till dessa grenar](#join-branches) senare i flödet. 

> [!TIP] 
> Om du har en utlösare som tar emot en matris och vill köra ett arbetsflöde för varje matrisobjekt kan *dubatchera* matrisen med [egenskapen **SplitOn-utlösaren** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Lägg till parallell gren

Om du vill köra oberoende steg samtidigt kan du lägga till parallella grenar bredvid ett befintligt steg. 

![Kör steg parallellt](media/logic-apps-control-flow-branches/parallel.png)

Logikappen väntar på att alla grenar ska slutföras innan arbetsflödet fortsätter. Parallella grenar körs `runAfter` bara när deras egenskapsvärden matchar det färdiga överordnade stegets status. Till exempel `branchAction1` båda `branchAction2` och är inställda `parentAction` på `Succeeded` att köras endast när det är klart med status.

> [!NOTE]
> Innan du börjar måste logikappen redan ha ett steg där du kan lägga till parallella grenar.

1. Öppna logikappen i Logic App Designer i <a href="https://portal.azure.com" target="_blank">Azure-portalen.</a>

1. Flytta pekaren över pilen ovanför det steg där du vill lägga till parallella grenar. Välj **plustecknet** **+**( ) som visas och välj sedan **Lägg till en parallell gren**. 

   ![Lägg till parallell gren](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Leta reda på och markera den åtgärd du vill använda i sökrutan.

   ![Sök efter och välj den åtgärd du vill ha](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Den valda åtgärden visas nu i den parallella grenen, till exempel:

   ![Sök efter och välj den åtgärd du vill ha](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Lägg nu till de steg du vill använda i varje parallell gren. Om du vill lägga till en annan åtgärd i en gren flyttar du pekaren under den åtgärd där du vill lägga till en sekventiell åtgärd. Välj **plustecknet** (**+**) som visas och välj sedan **Lägg till en åtgärd**.

   ![Lägga till sekventiell åtgärd i parallell gren](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Leta reda på och markera den åtgärd du vill använda i sökrutan.

   ![Söka efter och välja sekventiell åtgärd](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Den valda åtgärden visas nu inom den aktuella grenen, till exempel:

   ![Söka efter och välja sekventiell åtgärd](media/logic-apps-control-flow-branches/added-sequential-action.png)

Om du vill sammanfoga grenar ihop igen [ansluter du till dina parallella grenar](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Parallell grendefinition (JSON)

Om du arbetar i kodvyn kan du definiera den parallella strukturen i logikappens JSON-definition i stället, till exempel:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Gå med i parallella grenar

Om du vill sammanfoga parallella grenar tillsammans lägger du bara till ett steg längst ned under alla grenar. Det här steget körs när alla parallella grenar har körts klart.

![Gå med i parallella grenar](media/logic-apps-control-flow-branches/join.png)

1. Leta reda på och öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com) 

1. Välj **Nytt steg**under de parallella grenar som du vill gå med i . 

   ![Lägg till steg för att gå med](media/logic-apps-control-flow-branches/add-join-step.png)

1. Leta reda på och markera den åtgärd du vill ha som det steg som sammanfogar grenarna i sökrutan.

   ![Söka efter och markera den åtgärd som kopplar parallella grenar](media/logic-apps-control-flow-branches/join-steps.png)

   Dina parallella grenar slås nu samman.

   ![Sammanfogade grenar](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Kopplingsdefinition (JSON)

Om du arbetar i kodvyn kan du definiera kopplingsstrukturen i logikappens JSON-definition i stället, till exempel:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta om funktioner och förslag besöker du [webbplatsen för användarfeedback för Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (växelsatser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (loopar)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör steg baserat på grupperad åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

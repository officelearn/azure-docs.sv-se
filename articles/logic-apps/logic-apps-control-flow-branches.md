---
title: Skapa eller Anslut till parallella grenar – Azure Logic Apps | Microsoft Docs
description: Skapa eller ansluta till parallella grenar för arbetsflöden i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 68f7df2ab004477fae5df1d200fcd44929465e93
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233131"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Skapa eller Anslut till parallella grenar för arbetsflödesåtgärder i Azure Logic Apps

Som standard körs dina åtgärder i logikapparbetsflöden sekventiellt. För att utföra oberoende åtgärder på samma gång, kan du skapa [parallella grenar](#parallel-branches), och sedan [ansluta de grenarna](#join-branches) senare i ditt flöde. 

> [!TIP] 
> Om du har en utlösare som tar emot en matris och vill köra ett arbetsflöde för varje objekt i matrisen kan du *debatch* matrisen med den [ **SplitOn** utlösa egenskapen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Lägg till parallell gren

För att köra fristående steg på samma gång, kan du lägga till parallella grenar bredvid ett befintligt steg. 

![Kör du steg parallellt](media/logic-apps-control-flow-branches/parallel.png)

Logikappen väntar tills alla grenar slutförts innan du fortsätter arbetsflöde. Parallella grenar körs endast när deras `runAfter` egenskapsvärden matchar det färdiga överordnade steget status. Till exempel både `branchAction1` och `branchAction2` är inställd på att köras endast när den `parentAction` är klar med `Succeded` status.

> [!NOTE]
> Innan du börjar måste din logikapp redan ha ett steg där du kan lägga till parallella grenar.

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna logikappen i Logic App Designer.

1. Flyttar du pekaren över pilen ovanför steget där du vill lägga till parallella grenar. Välj den **plus** logga (**+**) som visas och välj sedan **lägga till en parallell gren**. 

   ![Lägg till parallell gren](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. I sökrutan, hitta och välj den åtgärd du vill.

   ![Sök efter och välj vilken åtgärd du önska](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Den valda åtgärden nu visas i parallell gren, till exempel:

   ![Sök efter och välj vilken åtgärd du önska](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Lägg nu till de steg som du vill i varje parallell gren. Om du vill lägga till en annan åtgärd till en gren, flyttar du pekaren under åtgärden där du vill lägga till en sekventiell åtgärd. Välj den **plus** (**+**) tecken som visas och välj sedan **Lägg till en åtgärd**.

   ![Lägga till sekventiella åtgärd parallell gren](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. I sökrutan, hitta och välj den åtgärd du vill.

   ![Hitta och välj sekventiella åtgärden](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Den valda åtgärden nu visas inom den aktuella grenen, till exempel:

   ![Hitta och välj sekventiella åtgärden](media/logic-apps-control-flow-branches/added-sequential-action.png)

Sammanfoga grenar tillbaka tillsammans [ansluta till din parallella grenar](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Parallell gren definition (JSON)

Om du arbetar i kodvy, kan du definiera parallella strukturen i din logikapp JSON-definition i stället till exempel:

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

## <a name="join-parallel-branches"></a>Ansluta till parallella grenar

Om du vill koppla parallella grenar tillsammans att bara lägga till ett steg längst ned under alla grenar. Det här steget körs när alla de parallella grenar Slutför körs.

![Ansluta till parallella grenar](media/logic-apps-control-flow-branches/join.png)

1. I den [Azure-portalen](https://portal.azure.com), hitta och öppna logikappen i Logic App Designer. 

1. Under de parallella grenar som du vill ansluta till, väljer **nytt steg**. 

   ![Lägg till steg för att ansluta till](media/logic-apps-control-flow-branches/add-join-step.png)

1. Hitta och välj vilken åtgärd du önska som ett steg som ansluter till grenarna i sökrutan.

   ![Hitta och välj den åtgärd som ansluter till parallella grenar](media/logic-apps-control-flow-branches/join-steps.png)

   Din parallella grenar slås nu samman.

   ![Domänansluten grenar](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Ansluta till definition (JSON)

Om du arbetar i kodvy, kan du definiera join-strukturen i din logikapp JSON-definition i stället till exempel:

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
* Om du vill skicka in eller rösta på funktioner och förslag, Besök den [webbplatsen för Azure Logic Apps-Användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

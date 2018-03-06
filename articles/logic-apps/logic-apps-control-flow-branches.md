---
title: "Parallella grenar – Azure Logic Apps | Microsoft Docs"
description: Skapa eller Anslut till parallella grenar i logikappar
services: logic-apps
keywords: filialer, parallell bearbetning
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 9a836b707a576b9a938f43397ef35c00aeb476bf
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="create-or-join-parallel-branches-in-your-logic-app"></a>Skapa eller Anslut till parallella grenar i din logikapp

Som standard körs åtgärder i en logikapp sekventiellt. Om du vill utföra oberoende åtgärder på samma gång, kan du skapa [parallella grenar](#parallel-branches), och sedan [ansluta dessa grenar](#join-branches) senare i din flödet. 

> [!TIP] 
> Om du har en utlösare som tar emot en matris och vill köra ett arbetsflöde för varje matris-objekt, kan du *debatch* att matris med de [ **SplitOn** utlösa egenskapen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande kunskaper om [skapa logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-a-parallel-branch"></a>Lägg till en parallell gren

Du kan lägga till parallella grenar bredvid ett befintligt steg om du vill köra oberoende steg på samma gång. 

![Parallell körning steg](media/logic-apps-control-flow-branches/parallel.png)

Din logikapp väntar grenar ska slutföras innan du fortsätter arbetsflödet.
Parallella grenar körs endast när deras `runAfter` egenskapsvärden matchar steget klar överordnade status. Till exempel både `branchAction1` och `branchAction2` är inställd på att köras endast när den `parentAction` är klar med `Succeded` status.

> [!NOTE]
> Innan du börjar måste din logikapp redan ha ett steg där du kan lägga till parallella grenar.

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna logikappen i logik App Designer.

2. Flytta musen över pilen ovanför steget där du vill lägga till parallella grenar.

3. Välj den **plus** logga (**+**), Välj **lägga till en parallell gren**, och välj ett objekt som du vill lägga till.

   ![Lägg till parallell gren](media/logic-apps-control-flow-branches/add-parallel-branch.png)

   Ditt valda objekt visas nu i en parallell gren.

4. Lägg till de steg som du vill använda för varje parallell gren. Om du vill lägga till en sekventiell åtgärd i en parallell gren musen under åtgärden där du vill lägga till åtgärden sekventiella. Välj den **plus** (**+**) tecken och det steg som du vill lägga till.

   ![Lägga till sekventiella steg för parallell gren](media/logic-apps-control-flow-branches/add-sequential-action-parallel-branch.png)

5. Du sammanfoga filialer tillbaka [ansluta din parallella grenar](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Parallell gren definition (JSON)

Om du arbetar i kodvy kan du definiera parallella strukturen i din logikapp JSON-definitionen i stället, till exempel:

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
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Anslut parallella grenar

Om du vill slå ihop parallella grenar, bara lägga till ett steg längst ned under alla förgreningar. Det här steget körs när alla parallella grenar slut körs.

![Anslut parallella grenar](media/logic-apps-control-flow-branches/join.png)

1. I den [Azure-portalen](https://portal.azure.com), hitta och öppna logikappen i logik App Designer. 

2. Lägg till det steg som du vill utföra under parallella grenar som du vill ansluta till.

   ![Lägga till ett steg som ansluter till parallella grenar](media/logic-apps-control-flow-branches/join-steps.png)

   Din parallella grenar har nu sammanfogats.

<a name="join-json"></a>

## <a name="join-definition-json"></a>Ansluta till definition (JSON)

Om du arbetar i kodvy kan du definiera strukturen join i din logikapp JSON-definitionen i stället till exempel:

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
* För att skicka eller rösta på funktioner och förslag, finns det [Azure Logikappar användare feedbackwebbplats](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Köra steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
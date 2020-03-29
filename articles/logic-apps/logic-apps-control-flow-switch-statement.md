---
title: Lägga till växlingssatser i arbetsflöden
description: Skapa växelsatser som styr arbetsflödesåtgärder baserat på specifika värden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 5c40feec2dca65e4bc9617a71a6d0a8e4c872a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793232"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Skapa växelsatser som kör arbetsflödesåtgärder baserat på specifika värden i Azure Logic Apps

Om du vill köra specifika åtgärder baserat på värdena för *switch* objekt, uttryck eller token lägger du till en switch-sats. Den här strukturen utvärderar objektet, uttrycket eller token, väljer det ärende som matchar resultatet och kör specifika åtgärder endast för det fallet. När switch-satsen körs ska endast ett ärende matcha resultatet.

Anta till exempel att du vill ha en logikapp som tar olika steg baserat på ett alternativ som valts i e-post. I det här exemplet kontrollerar logikappen en webbplats RSS-flöde efter nytt innehåll. När ett nytt objekt visas i RSS-flödet skickar logikappen e-post till en godkännare. Baserat på om godkännaren väljer "Godkänn" eller "Avvisa" följer logikappen olika steg.

> [!TIP]
> Precis som alla programmeringsspråk stöder bytesuttalanden endast jämlikhetsoperatörer. Om du behöver andra relationsoperatorer, till exempel "större än", använder du en [villkorssats](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> För att säkerställa deterministiskt körningsbeteende måste ärenden innehålla ett unikt och statiskt värde i stället för dynamiska token eller uttryck.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Om du vill följa exemplet i den här artikeln [skapar du den här exempellogiken med](../logic-apps/quickstart-create-first-logic-app-workflow.md) ett Outlook.com eller Office 365 Outlook-konto.

  1. När du lägger till åtgärden för att skicka e-post, hitta och välja den här åtgärden i stället: **Skicka ett godkännande e-postmeddelande**

     ![Välj "Skicka ett e-postmeddelande om godkännande"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Ange de fält som krävs, till exempel e-postadressen för den person som får e-postmeddelandet med godkännande. 
  Under **Användaralternativ**anger du "Godkänn, Avvisa".

     ![Ange e-postinformation](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Lägg till växelsats

1. I det här exemplet lägger du till en switch-sats i slutet av exempelarbetsflödet. Efter det sista steget väljer du **Nytt steg**.

   När du vill lägga till en växelsats mellan stegen flyttar du pekaren över pilen där du vill lägga till växelsatsen. Välj **plustecknet** **+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du "växla" som filter. Välj den här åtgärden: **Växla - Kontroll**

   ![Lägg till växel](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   En växelsats visas med ett ärende och ett standardärende. 
   Som standard kräver en växelsats minst ett ärende plus standardfallet. 

   ![Tomt standardväxelsats](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Klicka i rutan **På** så att listan med dynamiskt innehåll visas. I den listan väljer du det **SelectedOption-fält** vars utdata bestämmer vilken åtgärd som ska utföras. 

   ![Välj "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Om du vill hantera de `Approve` fall `Reject`där godkännaren väljer eller lägger du till ett annat ärende mellan **Ärende** och **Standard**. 

   ![Lägg till ett annat ärende](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Lägg till dessa åtgärder i motsvarande ärenden:

   | Fall # | **ValtOption** | Åtgärd |
   |--------|--------------------|--------|
   | Fall 1 | **Godkänn** | Lägg till outlook **Skicka en e-poståtgärd** för att skicka information om RSS-objektet endast när godkännaren valde **Godkänn**. |
   | Fall 2 | **Avvisa** | Lägg till Outlook **Skicka en e-poståtgärd** för att meddela andra godkännare att RSS-objektet avvisades. |
   | Default | Inget | Inga åtgärder behövs. I det här exemplet är **standardfallet** tomt eftersom **SelectedOption** bara har två alternativ. |
   |||

   ![Sats för färdigt växel](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Spara din logikapp. 

   Om du vill testa det här exemplet manuellt väljer du **Kör** tills logikappen hittar ett nytt RSS-objekt och skickar ett e-postmeddelande om godkännande. 
   Välj **Godkänn** om du vill följa resultaten.

## <a name="json-definition"></a>JSON-definition

Nu när du har skapat en logikapp med hjälp av en switch-sats ska vi titta på koddefinitionen på hög nivå bakom switch-satsen.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Label (Etikett) | Beskrivning |
|-------|-------------|
| `"Switch"`         | Namnet på switch-satsen, som du kan byta namn på för läsbarhet |
| `"type": "Switch"` | Anger att åtgärden är en switch-sats |
| `"expression"`     | I det här exemplet anger godkännarens valda alternativ som utvärderas mot varje ärende som deklareras senare i definitionen |
| `"cases"` | Definierar valfritt antal ärenden. För varje `"Case_*"` enskilt fall är standardnamnet för det fallet, som du kan byta namn på för läsbarhet |
| `"case"` | Anger ärendets värde, som måste vara ett konstant och unikt värde som switch-satsen använder för jämförelse. Om inga ärenden matchar resultatet för växeluttryck körs åtgärderna i avsnittet. `"default"` | 
| | | 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta om funktioner eller förslag besöker du [webbplatsen för användarfeedback för Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör och upprepa steg (loopar)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperad åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

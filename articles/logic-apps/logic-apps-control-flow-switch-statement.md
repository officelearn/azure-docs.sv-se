---
title: Lägga till switch-satser i arbetsflöden – Azure Logic Apps | Microsoft Docs
description: Så här skapar du switch-satser som styr arbetsflödesåtgärder baserat på specifika värden i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 2a3f8ee5cba3110d392555fad78c1cb2513b5d4e
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232451"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Skapa switch-satser som kör arbetsflödesåtgärder baserat på specifika värden i Azure Logic Apps

Om du vill köra specifika åtgärder baserat på värdena för objekt, uttryck eller token, lägger du till en *växla* instruktionen. Den här strukturen utvärderar objektet, uttryck eller token, väljer de ärenden som matchar resultatet och kör specifika åtgärder enbart för detta. När switch-instruktionen körs ska bara ett enda fall matcha resultatet.

Anta exempelvis att du vill att en logikapp som tar olika steg baserat på ett alternativ som valts i e-post. I det här exemplet kontrollerar logikappen en webbplats RSS-feed för nytt innehåll. När ett nytt objekt visas i RSS-flödet skickar logikappen ett e-post till en godkännare. Logic app följer utifrån om godkännaren väljer ”Godkänn” eller ”Godkänn” olika steg.

> [!TIP]
> Som alla programmeringsspråk stöder bara likheten operatörer i switch-satser. Om du behöver andra relationsoperatorer, till exempel ”större än” använda en [villkorlig instruktionen](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> För att säkerställa deterministisk körningsbeteende måste fall innehålla ett unikt och statiska värde i stället för dynamiska token eller uttryck.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Till exempel i den här artikeln [skapa den här exempellogikappen](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com eller Office 365 Outlook-konto.

  1. När du lägger till åtgärden Skicka e-post, söka efter och välj den här åtgärden i stället: **skicka ett e-postmeddelande för godkännande**

     ![Välj ”Skicka ett e-postmeddelande för godkännande”](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Ange fälten som krävs, t.ex. e-postadressen för den person som hämtar e-post för godkännande. 
  Under **användaralternativ**, ange ”Godkänn, avvisa”.

     ![Ange e-postinformation](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Lägga till switch-instruktionen

1. Det här exemplet lägger till ett switch-instruktionen i slutet din Exempelarbetsflöde. Efter det sista steget, välja **nytt steg**.

   När du vill lägga till ett switch-instruktionen mellan stegen muspekaren på pilen där du vill lägga till switch-instruktionen. Välj den **plustecknet** (**+**) som visas, välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du ”byta” som filter. Välj den här åtgärden: **växla - kontroll**

   ![Lägg till växeln](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   En switch-sats visas med ett enda fall och ett standard-fall. 
   Som standard måste en switch-sats minst ett fall plus standard fall. 

   ![Tom standard switch-instruktionen](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Klicka i den **på** så att den dynamiska innehållslistan visas. Från listan, Välj den **SelectedOption** fält vars utdata anger åtgärden som ska utföras. 

   ![Välj ”SelectedOption”](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Att hantera de fall där godkännaren väljer `Approve` eller `Reject`, lägga till ett annat fall mellan **fallet** och **standard**. 

   ![Lägg till en annan ärende](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Lägg till de här åtgärderna i motsvarande fall:

   | Case # | **SelectedOption** | Åtgärd |
   |--------|--------------------|--------|
   | Fall 1 | **Godkänn** | Lägg till i Outlook **skicka ett e-postmeddelande** åtgärd för att skicka information om RSS-objekt bara när godkännaren markerade **Godkänn**. |
   | Fall 2 | **Avvisa** | Lägg till i Outlook **skicka ett e-postmeddelande** åtgärd för att meddela andra granskare att RSS-objektet har avvisats. |
   | Standard | Ingen | Ingen åtgärd krävs. I det här exemplet på **standard** är tom eftersom **SelectedOption** har bara två alternativ. |
   |||

   ![Klar switch-instruktionen](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Spara din logikapp. 

   Om du vill testa det här exemplet manuellt, Välj **kör** tills logikappen söker efter ett nytt RSS-objekt och skickar ett e-postmeddelande för godkännande. 
   Välj **Godkänn** att Observera resultatet.

## <a name="json-definition"></a>JSON-definition

Nu när du har skapat en logikapp med en switch-sats kan vi titta på definitionen på hög nivå koden bakom switch-instruktionen.

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

| Etikett | Beskrivning |
|-------|-------------|
| `"Switch"`         | Namnet på den switch-instruktionen, som du kan byta namn för läsbarhet |
| `"type": "Switch"` | Anger att åtgärden är en switch-sats |
| `"expression"`     | I det här exemplet anger du godkännarens valda alternativ som ska utvärderas mot varje enskilt fall som deklarerats senare i definitionen |
| `"cases"` | Definierar valfritt antal fall. För varje scenario `"Case_*"` är standardnamnet för det ärende som du kan byta namn för läsbarhet |
| `"case"` | Anger det fallet värde, som måste vara ett konstant och unikt värde som switch-instruktionen använder för jämförelse. Om inga fall matcha växel uttryck resultatet, åtgärder i den `"default"` avsnittet körs. | 
| | | 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag eller funktioner, finns det [webbplatsen för Azure Logic Apps-Användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

---
title: Lägga till växeln instruktioner till arbetsflöden - Azure Logic Apps | Microsoft Docs
description: Så här skapar du switch-satser som styr arbetsflödesåtgärder baserat på specifika värden i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: cfowler
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2ffac49d14e05ff252d6cd0e90fc23d77ac0caff
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726082"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Skapa växeln instruktioner som kör arbetsflödesåtgärder baserat på specifika värden i Azure Logic Apps

Om du vill köra specifika åtgärder baserat på värdena för objekt, uttryck eller token, lägger du till en *växla* instruktionen. Den här strukturen utvärderar objektet, uttryck eller token, väljer fallet som matchar resultatet och kör specifika åtgärder endast för detta. När switch-instruktionen körs ska bara ett fall matcha resultatet.

Anta att du vill använda en logikapp som tar olika steg baserat på ett alternativ som valts i e-post. I det här exemplet kontrollerar logikappen webbplatsens RSS-feed för nytt innehåll. När ett nytt objekt visas i RSS-feeden skickar logikappen e-postmeddelande till en godkännare. Baserat på om godkännaren väljer ”Godkänn” eller ”ignorera”, följer logikappen olika steg.

> [!TIP]
> Precis som alla programmeringsspråk stöder switch-satser endast likheten operatörer. Om du behöver andra relationsoperatorer, till exempel ”större än” använda en [villkorlig instruktionen](#conditions).
> För att säkerställa deterministiska körningsbeteende måste fall innehålla ett unikt och statiska värde i stället för dynamiska token eller uttryck.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Till exempel i den här artikeln [skapa det här exemplet logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com eller Office 365 Outlook-konto.

  1. När du lägger till åtgärden för att skicka e-post **skickar ett e-godkännande** i stället.

     ![Välj ”Skicka ett e-postmeddelande för godkännande”](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. Ange de obligatoriska fälten som e-postadressen för den person som hämtar e-post för godkännande. 
  Under **användaralternativ**, ange ”godkänna, avvisa”.

     ![Ange information om e-post](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>Lägg till en switch-sats

1. I slutet av arbetsflödet exempel väljer **+ nytt steg** > **... Flera** > **lägga till ett switch-fall**. 

   ![Lägg till en switch-sats](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   En switch-instruktionen visas med ett fall och en standardfallet. 
   En switch-instruktionen kräver minst ett fall plus standardfallet. 

   När du vill lägga till en switch-instruktionen mellan stegen flytta pekaren över pilen där du vill lägga till switch-instruktionen. 
   Välj den **plustecknet** (**+**) som visas, väljer sedan **lägga till ett switch-fall**.

4. I den **på** markerar den **SelectedOption** fältet vars utdata anger åtgärden som ska utföras. 
   
   Du kan välja fält från den **lägga till dynamiskt innehåll** listan som visas.

5. Att hantera de fall där godkännaren väljer `Approve` eller `Reject`, lägga till ett annat fall mellan **fallet** och **standard**. 
   
6. Lägg till de här åtgärderna i motsvarande fall:

   | Case # | **SelectedOption** | Åtgärd |
   |:------ |:-------------------|:------ |
   | Fall 1 | **Godkänn** | Lägg till Outlook **skickar ett e-** åtgärd för att skicka information om RSS-objektet som endast när godkännaren valts **Godkänn**. |
   | Fall 2 | **Avvisa** | Lägg till Outlook **skickar ett e-** åtgärd för att meddela andra godkännare att RSS-objektet avvisades. |
   | Standard | \<Ingen\> | Ingen åtgärd krävs. I det här exemplet i **standard** fallet är tom eftersom **SelectedOption** har bara två alternativ. |
   |         |          |

   ![Switch-instruktionen](./media/logic-apps-control-flow-switch-statement/switch.png)

7. Spara din logikapp. 

   Om du vill testa det här exemplet manuellt, Välj **kör** tills logikappen hittar ett nytt RSS-objekt och skickar ett e-postmeddelande för godkännande. 
   Välj **Godkänn** att se resultaten.

## <a name="json-definition"></a>JSON-definition

Nu när du har skapat en logikapp med hjälp av en switch-instruktionen känna till hur definitionen för övergripande koden bakom switch-instruktionen.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
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

| Etikett              | Beskrivning |
| :----------------- | :---------- |
| `"Switch"`         | Namnet på den switch-instruktionen, vilket du kan byta namn på för att läsa |
| `"type": "Switch"` | Anger att åtgärden är en switch-sats |
| `"expression"`     | I det här exemplet anger du godkännarens valda alternativ som ska utvärderas mot varje fall som deklarerats senare i definitionen |
| `"cases"` | Definierar valfritt antal fall. För varje fall `"Case_*"` är standardnamnet för det fall där du kan byta namn på för att läsa |
| `"case"` | Anger värdet för det fall, som måste vara ett konstant och unikt värde som switch-uttrycket som används för jämförelse. Om inga fall matchar uttryckresultatet växel, åtgärder i den `"default"` avsnittet körs.
|           |         |

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* För att skicka eller rösta på förslag eller funktioner, finns det [Azure Logikappar användare feedbackwebbplats](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Köra steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

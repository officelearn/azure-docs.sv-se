---
title: "Växla uttryck för olika åtgärder i Azure Logic Apps | Microsoft Docs"
description: "Välj olika åtgärder att utföra i logikappar utifrån uttrycken med hjälp av en switch-sats"
services: logic-apps
keywords: Switch-instruktionen
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: 338b6a5b549d7bf81186550295608438ac4aee32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>Utför olika åtgärder i logikappar med en switch-sats

När du skapar ett arbetsflöde kan måste du ofta vidta olika åtgärder baserat på värdet för ett objekt eller ett uttryck. Du kanske exempelvis vill logikappen fungerar på olika sätt beroende på statuskod för en HTTP-begäran eller ett alternativ som valts i ett e-postmeddelande.

Du kan använda en switch-sats för att implementera dessa scenarier. Din logikapp kan utvärdera en token eller ett uttryck och välj fallet med samma värde för att utföra angivna åtgärder. Endast ett fall ska matcha switch-instruktionen.

> [!TIP]
> Precis som alla programmeringsspråk stöder switch-satser endast likheten operatörer. Om du behöver andra relationsoperatorer använder du en villkorssatsen, till exempel ”större än”.
> För att säkerställa deterministiska körningsbeteende måste fall innehålla ett unikt och statiska värde i stället för dynamiska token eller uttryck.

## <a name="prerequisites"></a>Krav

- En aktiv Azure-prenumeration. Om du inte har en aktiv Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/), eller försök [Logic Apps för kostnadsfri](https://tryappservice.azure.com/).
- [Grundläggande kunskaper om logikappar](logic-apps-what-are-logic-apps.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>Lägga till en switch-instruktionen i arbetsflödet

Om du vill visa hur en switch-instruktionen fungerar skapar det här exemplet en logikapp som övervakar filer som överförts till Dropbox. När nya filer överförs skickar logikappen e-post till en godkännare som väljer om du vill överföra dessa filer till SharePoint. Appen använder en switch-sats som utför olika åtgärder baserat på värdet som godkännaren väljer.

1. Skapa en logikapp och välj den här utlösaren: **Dropbox - när en fil skapas**.

   ![Använda Dropbox - när en fil skapas utlösare](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. Lägg till den här åtgärden under utlösaren: **Outlook.com - Skicka godkännande e-post**

   > [!TIP]
   > Logikappar stöder även skicka e-scenarier för godkännande från ett Office 365 Outlook-konto.

   - Om du inte har en befintlig anslutning, uppmanas du att skapa en.
   - Fyll i de obligatoriska fälten. Till exempel under **till**, ange den e-postadressen för att skicka e-postmeddelandet godkännare.
   - Under **användaralternativ**, ange `Approve, Reject`.

   ![Konfigurera anslutning](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Lägg till en switch-sats.

   - Välj **+ nytt steg** > **... Flera** > **lägga till ett switch-fall**. 
   - Nu vill vi väljer åtgärden som ska utföras baserat på de `SelectedOptions` utdata från den *skicka e-post för godkännande* åtgärd. 
   Du hittar det här fältet i den **lägga till dynamiskt innehåll** väljare.
   - Använd *fall 1* ska hanteras när godkännaren väljer `Approve`.
     - Om det godkänns, kopiera den ursprungliga filen till SharePoint Online med den [ **SharePoint Online - skapa filen** åtgärd](../connectors/connectors-create-api-sharepointonline.md).
     - Lägg till en annan åtgärd i fallet att meddela användare att en ny fil är tillgänglig i SharePoint.
   - Lägg till ett annat fall ska hanteras när användaren väljer `Reject`.
     - Skicka ett e-postmeddelande som informerar andra godkännare att filen avvisas och ingen ytterligare åtgärd krävs om avvisas.
   - `SelectedOptions`ger endast två alternativ, så vi kan lämna den **standard** fallet är tom.

   ![Switch-instruktionen](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > En switch-instruktionen måste minst ett fall utöver standardfallet.

4. Ta bort den ursprungliga filen har överförts till Dropbox genom att lägga till den här åtgärden efter switch-sats: **Dropbox - ta bort filen**

5. Spara din logikapp. Testa din app genom att överföra en fil till Dropbox. Du bör få ett godkännande e-postmeddelande inom kort. Välj ett alternativ och se hur detta fungerar.

   > [!TIP]
   > Kolla hur man [övervaka dina logikappar](logic-apps-monitor-your-logic-apps.md).

## <a name="understand-the-code-behind-switch-statements"></a>Förstå koden bakom switch-satser

Nu när du har skapat en logikapp med hjälp av en switch-sats, vi titta på koden definition bakom switch-instruktionen.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
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

* `"Switch"`är namnet på switch-instruktionen, som du kan byta namn för läsbarhet. 
* `"type": "Switch"`Anger att åtgärden är en switch-sats. 
* `"expression"`Godkännarens valda alternativ i det här exemplet och utvärderas mot varje fall som deklarerats senare i definitionen. 
* `"cases"`kan innehålla valfritt antal fall. För varje fall `"Case *"` är standardnamnet i fall där du kan byta namn för läsbarhet. 
`"case"`Anger etiketten fall där switch-uttryck för jämförelse, och måste vara en konstant och unikt värde. Om ingen av fall matcha switch-uttrycket åtgärder under `"default"` utförs.

## <a name="get-help"></a>Få hjälp

I [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) kan du ställa frågor, besvara frågor och sedan vad andra Azure Logic Apps-användare håller på med.

På [webbplatsen för Azure Logic Apps-användarfeedback](http://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Azure Logic Apps och anslutningsapparna genom att rösta på förslag eller komma med egna förslag på förbättringar.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [Lägg till villkor](logic-apps-use-logic-app-features.md)
- Lär dig mer om [fel och undantagshantering](logic-apps-exception-handling.md)
- Utforska mer [arbetsflöde språk funktioner](logic-apps-author-definitions.md)
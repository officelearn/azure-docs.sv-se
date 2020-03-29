---
title: Felsöka och diagnostisera fel i arbetsflödet
description: Lär dig hur du felsöker och diagnostiserar problem, fel och fel i dina arbetsflöden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905096"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Felsöka och diagnostisera fel i Azure Logic Apps

Logikappen genererar information som kan hjälpa dig att diagnostisera och felsöka problem i din app. Du kan diagnostisera en logikapp genom att granska varje steg i arbetsflödet via Azure-portalen. Du kan också lägga till några steg i ett arbetsflöde för felsökning under körning.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Kontrollera utlösarhistorik

Varje logikappkörning börjar med ett utlösarförsök, så om utlösaren inte utlöses gör du så här:

1. Kontrollera utlösarens status genom [att kontrollera utlösarhistoriken](../logic-apps/monitor-logic-apps.md#review-trigger-history). Om du vill visa mer information om utlösarförsöket väljer du den utlösarhändelsen, till exempel:

   ![Visa utlösarstatus och historik](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Kontrollera utlösarens indata för att bekräfta att de visas som förväntat. Under **Länken Ingångar**väljer du länken som visar **fönstret Ingångar.**

   Utlösarindata innehåller de data som utlösaren förväntar sig och kräver för att starta arbetsflödet. Genom att granska dessa indata kan du avgöra om utlösarindata är korrekta och om villkoret uppfylldes så att arbetsflödet kan fortsätta.

   Egenskapen `feedUrl` här har till exempel ett felaktigt RSS-feedvärde:

   ![Granska utlösarindata för fel](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Kontrollera eventuella utlösande faktorer för att bekräfta att de visas som förväntat. Under **Länken Utdata**väljer du länken som visar **fönstret Utdata.**

   Utlösarutdata innehåller de data som utlösaren skickar till nästa steg i arbetsflödet. Genom att granska dessa utdata kan du avgöra om de korrekta eller förväntade värdena som överförs till nästa steg i arbetsflödet, till exempel:

   ![Granska utlösande utgångar för fel](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Om du hittar något innehåll som du inte känner igen kan du läsa mer om [olika innehållstyper](../logic-apps/logic-apps-content-type.md) i Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Kontrollera körningar historia

Varje gång utlösaren utlöses för ett objekt eller en händelse skapar och kör Logic Apps-motorn en separat arbetsflödesinstans för varje objekt eller händelse. Om en körning misslyckas följer du dessa steg för att granska vad som hände under körningen, inklusive status för varje steg i arbetsflödet plus indata och utdata för varje steg.

1. Kontrollera arbetsflödets körstatus genom [att kontrollera körningarhistoriken](../logic-apps/monitor-logic-apps.md#review-runs-history). Om du vill visa mer information om en misslyckad körning, inklusive alla steg i körningen i deras status, väljer du den misslyckade körningen.

   ![Visa körningshistorik och välj misslyckad körning](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. När alla steg i körningen visas expanderar du det första misslyckade steget.

   ![Expandera det första misslyckade steget](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Kontrollera det misslyckade stegets indata för att bekräfta om de visas som förväntat.

1. Granska information för varje steg i en viss körning. Under **Körhistorik**väljer du den körning som du vill granska.

   ![Granskning kör historia](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Visa information om en logikappkörning](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Om du vill undersöka indata, utdata och eventuella felmeddelanden för ett visst steg väljer du det steget så att formen expanderar och visar informationen. Ett exempel:

   ![Visa information om steg](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Utför felsökning av körningen

Om du vill hjälpa till med felsökning kan du lägga till diagnostiska steg i ett logikapparbetsflöde, tillsammans med att granska utlösaren och köra historik. Du kan till exempel lägga till steg som använder tjänsten [Webhook Tester](https://webhook.site/) så att du kan granska HTTP-begäranden och bestämma deras exakta storlek, form och format.

1. Gå till [webhooktestares](https://webhook.site/) webbplats och kopiera den genererade unika url:en.

1. I logikappen lägger du till en HTTP POST-åtgärd plus brödtextinnehållet som du vill testa, till exempel ett uttryck eller en annan stegutdata.

1. Klistra in webbadressen från Webhook-testaren i HTTP POST-åtgärden.

1. Om du vill granska hur en begäran skapas när den genereras från Logic Apps-motorn kör du logikappen och besöker webbplatsen Webhook Tester för mer information.

## <a name="next-steps"></a>Nästa steg

* [Övervaka logikappen](../logic-apps/monitor-logic-apps.md)

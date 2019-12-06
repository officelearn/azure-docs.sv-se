---
title: Övervaka Azure Automation runbooks med mått varningar
description: Den här artikeln vägleder dig genom övervakning Azure Automation runbooks baserade på mått
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: bea99820bee107b8329dd3c36ac3ceb84a042b86
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850983"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Övervaka Runbooks med mått varningar

I den här artikeln får du lära dig hur du skapar aviseringar baserat på slut för ande status för Runbooks.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in i Azure på https://portal.azure.com

## <a name="create-alert"></a>Skapa avisering

Med aviseringar kan du definiera ett villkor för övervakning av och en åtgärd som ska vidtas när villkoret är uppfyllt.

I Azure Portal navigerar du till ditt Automation-konto. Under **övervakning**väljer du **aviseringar** och klickar på **+ ny varnings regel**. Målets omfattning har redan definierats för ditt Automation-konto.

### <a name="configure-alert-criteria"></a>Konfigurera aviserings villkor

1. Klicka på **+ Lägg till kriterier**. Välj **mått** för **signal typen**och välj **Totalt antal jobb** från tabellen.

2. Sidan **Konfigurera signal logik** är den plats där du definierar den logik som utlöser aviseringen. Under det historiska diagrammet visas två dimensioner, **Runbook-namn** och **status**. Dimensioner är olika egenskaper för ett mått som kan användas för att filtrera resultat. För **Runbook-namn**väljer du den Runbook som du vill Avisera om eller lämnar tomt för aviseringar på alla Runbooks. För **status**väljer du en status från List rutan som du vill övervaka för. Runbook-namn och status värden som visas i list rutan är bara för jobb som har körts den senaste veckan.

   Om du vill Avisera om en status eller Runbook som inte visas i list rutan klickar du på **\+** bredvid dimensionen. Den här åtgärden öppnar en dialog ruta där du kan ange ett anpassat värde, som inte har genererats för den dimensionen nyligen. Om du anger ett värde som inte finns för en egenskap kommer aviseringen inte att utlösas.

   > [!NOTE]
   > Om du inte använder ett namn för **RunbookName** -dimensionen, kommer du att få en avisering om det finns Runbooks som uppfyller status villkoren, vilket inkluderar dolda system-Runbooks.

3. Under **aviserings logik**definierar du villkoret och tröskelvärdet för aviseringen. En för hands version av det definierade villkoret visas under.

4. Under **utvärdera baserat på**väljer du TimeSpan för frågan och hur ofta du vill att frågan kördes. Om du till exempel väljer **över de senaste 5 minuterna** för **perioden** och **var 1 minut** för **frekvensen**söker aviseringen efter antalet Runbooks som uppfyller dina kriterier under de senaste 5 minuterna. Den här frågan körs varje minut och när aviserings kriterierna du definierade inte längre hittas i ett 5-minuters fönster, löses aviseringen. Klicka på **Klar** när du är klar.

   ![Välj en resurs för aviseringen](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definiera aviseringsinformationen

1. Under **2. Definiera aviserings information**, ge aviseringen ett eget namn och en beskrivning. Ange **allvarlighets grad** som matchar varnings villkoret. Det finns fem allvarlighets grader mellan 0 och 5. Aviseringarna behandlas oberoende av allvarlighets graden. du kan matcha allvarlighets graden så att den matchar din affärs logik.

1. Längst ned i avsnittet finns en knapp som gör det möjligt att aktivera regeln när den har slutförts. Som standard är regler aktiverade när de skapas. Om du väljer Nej kan du skapa aviseringen och den skapas i ett **inaktiverat** tillstånd. På sidan **regler** i Azure Monitor kan du markera den och klicka på **Aktivera** för att aktivera aviseringen när du är klar.

### <a name="define-the-action-to-take"></a>Definiera åtgärden som ska vidtas

1. Under **3. Definiera åtgärds grupp**, klicka på **+ ny åtgärds grupp**. En åtgärds grupp är en grupp med åtgärder som du kan använda i mer än en avisering. Dessa kan inkludera, men är inte begränsade till, e-postmeddelanden, Runbooks, Webhooks och många fler. Läs mer om åtgärdsgrupper i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md)

1. I rutan **Åtgärdsgruppnamn** ge ett eget namn och ett kort namn. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

1. I avsnittet **åtgärder** under **Åtgärds typ**väljer du **e-post/SMS/push/röst**.

1. På sidan **e-post/SMS/Push/röst**, ge den ett namn. Markera kryssrutan **e-post** och ange en giltig e-postadress som ska användas.

   ![Konfigurera e-poståtgärdsgrupp](./media/automation-alert-activity-log/add-action-group.png)

1. Klicka på **OK** på sidan **e-post/SMS/Push/röst** för att stänga den och klicka på **OK** att stänga sidan **Lägg till åtgärdsgrupp**. Namnet som anges på den här sidan sparas som **Åtgärds namn**.

1. Klicka på **Spara** när du är klar. Den här åtgärden skapar en regel som varnar dig när en Runbook har slutförts med en viss status.

> [!NOTE]
> När du lägger till en e-postadress i en åtgärds grupp skickas ett e-postmeddelande med information om att adressen har lagts till i en åtgärds grupp.

## <a name="notification"></a>Avisering

När aviserings villkoret är uppfyllt kör åtgärds gruppen den definierade åtgärden. I den här artikeln är ett e-postmeddelande som skickas. Följande bild är ett exempel på ett e-postmeddelande som du får när aviseringen har utlösts:

![E-postavisering](./media/automation-alert-activity-log/alert-email.png)

När måttet inte längre är utanför det angivna tröskelvärdet inaktive ras aviseringen och åtgärds gruppen kör den definierade åtgärden. Om en e-poståtgärds typ väljs skickas en lösnings-e-postadress som talar om att den har lösts.

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande artikel om du vill lära dig mer om andra sätt att integrera aviseringar i ditt Automation-konto.

> [!div class="nextstepaction"]
> [Använda en avisering för att utlösa en Azure Automation Runbook](automation-create-alert-triggered-runbook.md)

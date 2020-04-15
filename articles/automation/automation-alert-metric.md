---
title: Övervaka Azure Automation-runbooks med måttaviseringar
description: Den här artikeln går igenom övervakning av Azure Automation-runbooks baserat på mått
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: f288029bb35fe4e3c71db37a1de265edbcd913b1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310546"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Övervaka runbooks med måttaviseringar

I den här artikeln får du lära dig hur du skapar aviseringar baserat på körningsstatusen för runbooks.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in i Azure på https://portal.azure.com

## <a name="create-alert"></a>Skapa avisering

Aviseringar kan du definiera ett villkor att övervaka för och en åtgärd att vidta när villkoret uppfylls.

Navigera till ditt Automation-konto i Azure-portalen. Under **Övervakning**väljer du Aviseringar och klickar på **+ Ny varningsregel**. **Alerts** Omfattningen för målet har redan definierats för ditt Automation-konto.

### <a name="configure-alert-criteria"></a>Konfigurera varningsvillkor

1. Klicka på **+ Lägg till villkor**. Välj **Mått** för **typen Signal**och välj Summa **jobb** i tabellen.

2. Sidan **Konfigurera signallogik** är där du definierar logiken som utlöser aviseringen. Under det historiska diagrammet visas två dimensioner, **Runbook Name** och **Status**. Dimensioner är olika egenskaper för ett mått som kan användas för att filtrera resultat. För **Runbook Name**väljer du den runbook som du vill avisera på eller lämnar tom för att avisera på alla runbooks. För **Status**väljer du en status i den listruta som du vill övervaka för. Runbook-namn- och statusvärden som visas i listrutan är bara för jobb som har körts under den senaste veckan.

   Om du vill avisera en status eller runbook som inte visas **\+** i listrutan klickar du på bredvid dimensionen. Den här åtgärden öppnar en dialogruta som gör att du kan ange i ett anpassat värde, som inte har avgetts för den dimensionen nyligen. Om du anger ett värde som inte finns för en egenskap kommer aviseringen inte att utlösas.

   > [!NOTE]
   > Om du inte använder ett namn för dimensionen **RunbookName** får du en avisering om det finns några runbooks som uppfyller statuskriterierna, som innehåller dolda systemkörningar.

3. Under **Varningslogik**definierar du villkoret och tröskelvärdet för aviseringen. En förhandsgranskning av ditt definierade villkor visas under.

4. Under **Utvärderad baserat på**väljer du tidsintervallet för frågan och hur ofta du vill att frågan ska kördes. Om du till exempel väljer **Under de senaste 5 minuterna** för **Period** och **Var 1 minut** för **Frekvens**söker aviseringen efter antalet runbooks som har uppfyllt dina kriterier under de senaste 5 minuterna. Den här frågan körs varje minut och när de varningsvillkor som du har definierat inte längre finns i ett 5-minutersfönster löser varningen sig själv. Klicka på **Klar** när du är klar.

   ![Välj en resurs för aviseringen](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definiera aviseringsinformationen

1. Under **2. Definiera aviseringsinformation**, ge aviseringen ett eget namn och en beskrivning. Ställ in **allvarlighetsgraden** så att den matchar ditt aviseringsvillkor. Det finns fem allvarlighetsgrader som sträcker sig från 0 till 5. Aviseringarna behandlas på samma sätt oberoende av allvarlighetsgraden, du kan matcha allvarlighetsgraden så att den matchar din affärslogik.

1. Längst ned i avsnittet finns en knapp som gör att du kan aktivera regeln när den är klar. Som standard är regler aktiverade när de skapas. Om du väljer Nej kan du skapa aviseringen och den skapas i **inaktiverat** tillstånd. På sidan **Regler** i Azure Monitor kan du välja det och klicka på **Aktivera** för att aktivera aviseringen när du är redo.

### <a name="define-the-action-to-take"></a>Definiera vilken åtgärd som ska vidtas

1. Under **3. Definiera åtgärdsgrupp**, klicka på **+ Ny åtgärdsgrupp**. En åtgärdsgrupp är en grupp åtgärder som du kan använda i mer än en avisering. Dessa kan inkludera men är inte begränsade till, e-postmeddelanden, runbooks, webhooks och många fler. Läs mer om åtgärdsgrupper i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md)

1. I rutan **Åtgärdsgruppnamn** ge ett eget namn och ett kort namn. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

1. I avsnittet **Åtgärder** under **ÅTGÄRDSTYP**väljer du **E-post/SMS/Push/Voice**.

1. På sidan **e-post/SMS/Push/röst**, ge den ett namn. Markera kryssrutan **e-post** och ange en giltig e-postadress som ska användas.

   ![Konfigurera e-poståtgärdsgrupp](./media/automation-alert-activity-log/add-action-group.png)

1. Klicka på **OK** på sidan **e-post/SMS/Push/röst** för att stänga den och klicka på **OK** att stänga sidan **Lägg till åtgärdsgrupp**. Namnet som anges på den här sidan sparas som **ÅTGÄRDSNAMN**.

1. Klicka på **Spara** när du är klar. Den här åtgärden skapar regeln som varnar dig när en runbook har slutförts med en viss status.

> [!NOTE]
> När du lägger till en e-postadress i en åtgärdsgrupp skickas ett e-postmeddelande där det står att adressen har lagts till i en åtgärdsgrupp.

## <a name="notification"></a>Avisering

När aviseringsvillkoren är uppfyllda kör åtgärdsgruppen den definierade åtgärden. I den här artikelns exempel skickas ett e-postmeddelande. Följande bild är ett exempel på ett e-postmeddelande som du får när aviseringen har utlösts:

![E-postavisering](./media/automation-alert-activity-log/alert-email.png)

När måttet inte längre ligger utanför det definierade tröskelvärdet inaktiveras aviseringen och åtgärdsgruppen kör åtgärden definierad. Om en e-poståtgärdstyp har valts skickas ett e-postmeddelande med en lösning som anger att det har lösts.

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande artikel om du vill veta mer om andra sätt som du kan integrera aviseringar i ditt Automation-konto.

> [!div class="nextstepaction"]
> [Använda en avisering för att utlösa en Azure Automation-runbook](automation-create-alert-triggered-runbook.md)

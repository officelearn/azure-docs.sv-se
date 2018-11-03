---
title: Övervaka Azure Automation-runbooks med måttaviseringar
description: Den här artikeln vägleder dig genom övervakning av Azure Automation-runbookflöden baserade på mått
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 65de18445f114f468dd42c5a7e7128dd2f63d44c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959842"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Övervakning av runbooks med måttaviseringar

I den här artikeln lär du dig att skapa aviseringar baserat på status för slutförande av runbooks.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in i Azure på https://portal.azure.com

## <a name="create-alert"></a>Skapa avisering

Aviseringar kan du definiera ett villkor för att övervaka och en åtgärd som ska vidtas när villkoret uppfylls.

Gå till ditt Automation-konto i Azure-portalen. Under **övervakning**väljer **aviseringar** och klicka på **+ ny Aviseringsregel**. Omfång för målet har redan definierats i Automation-konto.

### <a name="configure-alert-criteria"></a>Konfigurera aviseringsvillkoren

1. Klicka på **+ Lägg till villkor**. Välj **mått** för den **signalera typ**, och välj **totala antal jobb** från tabellen.

2. Den **konfigurera signallogiken** sidan är här du definierar logiken som utlöser aviseringen. Under den historiska diagram visas två dimensioner **Runbooknamn** och **Status**. Dimensioner är olika egenskaper för ett mått som kan användas för att filtrera resultat. För **Runbooknamn**, Välj den runbook som du vill varning i eller lämna tomt för aviseringen för alla runbooks. För **Status**, Välj en status i listrutan som du vill övervaka. Runbook-namnet och statusen för värdena som visas i listrutan är endast för jobb som har körts under den senaste veckan.

   Om du vill att Avisera om en status eller en runbook som inte visas i listrutan, klickar du på den **\+** bredvid dimensionen. Den här åtgärden öppnar en dialogruta där du kan ange i ett anpassat värde inte anges för den aktuella dimensionen nyligen. Om du anger ett värde som inte finns för en egenskap som aviseringen inte kommer utlösas.

3. Under **Avisera logic**, definiera villkor och tröskelvärdet för aviseringen. En förhandsgranskning av villkoret definierats visas under.

4. Under **Evaluated baserat på**, Välj tidsintervall för frågan och hur ofta du vill att frågan kördes. Exempel: Om du väljer **under de senaste 5 minuterna** för **Period** och **var 1 minut** för **frekvens**, aviseringen söker efter hur många runbooks som uppfyller dina kriterier under de senaste 5 minuterna. Den här frågan körs varje minut, och när aviseringsvillkoren du definierade är inte längre finns i ett fönster i 5 minuter, aviseringen löser sig själv. Klicka på **Klar** när du är klar.

   ![Välj en resurs för aviseringen](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definiera aviseringsinformationen

1. Under **2. Definiera aviseringsinformation**, ge aviseringen ett eget namn och en beskrivning. Ange den **allvarlighetsgrad** så att de matchar dina varningsvillkor. Det finns fem allvarlighetsgrader mellan 0 och 5. Aviseringarna behandlas samma oberoende av allvarlighetsgrad, kan du stämma med allvarlighetsgraden för att matcha din affärslogik.

1. Är en knapp som gör att du kan aktivera regeln när åtgärden har slutförts längst ned i avsnittet. Regler aktiveras som standard när du skapar. Om du väljer Nej, du kan skapa aviseringen och den har skapats i en **inaktiverad** tillstånd. Från den **regler** sidan i Azure Monitor kan du markera den och klicka på **aktivera** att aktivera aviseringen när du är klar.

### <a name="define-the-action-to-take"></a>Definiera åtgärd att vidta

1. Under **3. Definiera åtgärdsgrupp**, klicka på **+ Ny åtgärdsgrupp**. En åtgärdsgrupp är en uppsättning åtgärder som du kan använda i mer än en avisering. Dessa kan inkludera, men inte begränsat till, e-postaviseringar, runbooks, webhooks och många fler. Läs mer om åtgärdsgrupper i [Skapa och hantera åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md)

1. I rutan **Åtgärdsgruppnamn** ge ett eget namn och ett kort namn. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

1. I den **åtgärder** avsnittet **ÅTGÄRDSTYP**väljer **e-post/SMS/Push/röst**.

1. På sidan **e-post/SMS/Push/röst**, ge den ett namn. Markera kryssrutan **e-post** och ange en giltig e-postadress som ska användas.

   ![Konfigurera e-poståtgärdsgrupp](./media/automation-alert-activity-log/add-action-group.png)

1. Klicka på **OK** på sidan **e-post/SMS/Push/röst** för att stänga den och klicka på **OK** att stänga sidan **Lägg till åtgärdsgrupp**. Namnet i den här sidan sparas som den **ÅTGÄRDSNAMN**.

1. Klicka på **Spara** när du är klar. Den här åtgärden skapar regeln som varnar dig när en runbook har slutförts med en viss status.

> [!NOTE]
> När du lägger till en e-postadress till en åtgärdsgrupp, skickas ett e-postmeddelande om adressen har lagts till en åtgärdsgrupp.

## <a name="notification"></a>Avisering

När aviseringsvillkoren uppfylls, körs den åtgärd som definierats i åtgärdsgruppen. I den här artikeln exemplet skickas ett e-postmeddelande. Följande bild är ett exempel på ett e-postmeddelande när aviseringen utlöses:

![E-postavisering](./media/automation-alert-activity-log/alert-email.png)

När måttet inte längre är utanför det definierade tröskelvärdet aviseringen har inaktiverats och åtgärdsgruppen körs den åtgärd som definierats. Om en e-åtgärdstyp väljs skickas ett upplösning om den är löst.

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande artikel om du vill veta mer om andra sätt att du kan integrera alertings i ditt Automation-konto.

> [!div class="nextstepaction"]
> [Använd en avisering för att utlösa en Azure Automation-runbook](automation-create-alert-triggered-runbook.md)

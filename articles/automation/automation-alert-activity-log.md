---
title: Övervaka Azure Automation-runbook med aktiviteten loggar aviseringar
description: Den här artikeln vägleder dig genom övervakning Azure Automation-runbooks med aktivitetsloggen
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a0bd291af98477308cda898580fd52e33b1e6bbd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="monitoring-runbooks-with-azure-activity-log-alerts"></a>Övervakning av runbooks med aktivitet för Azure log aviseringar

Lär dig hur du skapar varningar baserat på status för slutförande av runbooks i den här artikeln.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com

## <a name="create-alert"></a>Skapa avisering

Aviseringar kan du definiera ett villkor för att övervaka och en åtgärd som ska vidtas när villkoret uppfylls.

I Azure-portalen går du till **alla tjänster** och välj **övervakaren**. Välj på sidan övervakaren **aviseringar** och på **+ ny Varningsregeln**.

### <a name="define-the-alert-condition"></a>Definiera villkoret för aviseringen

1. Under **1. Definiera aviseringstillståndet**, klickar du på **+ väljer mål**. Välj din prenumeration, och under **filtrera efter resurstyp**väljer **Automation-konton**. Välj ditt Automation-konto och klicka på **klar**.

   ![Välj en resurs för aviseringen](./media/automation-alert-activity-log/select-resource.png)

### <a name="configure-alert-criteria"></a>Konfigurera aviseringsvillkoren

1. Klicka på **+ Lägg till kriterier**. Välj **mått** för den **Signal type**, och välj **Totalt antal jobb** från tabellen.

1. Den **konfigurera signal logik** är där du definierar logik som utlöser varningen. Under den historiska diagram visas två dimensioner, **Runbooknamnet** och **Status**. Dimensioner är olika egenskaper för ett mått som kan användas för att filtrera resultat. För **Runbooknamnet**, Välj den runbook du vill varning i eller lämna tomt för varning för alla runbooks. För **Status**, Välj en status i listrutan som du vill övervaka. Runbook-namn och status värdena som visas i listrutan är endast för jobb som har körts under den senaste veckan.

   Om du vill Varna på en status eller en runbook som inte visas i listrutan, klickar du på den **\+** bredvid dimensionen. Då öppnas en dialogruta där du kan ange i ett anpassat värde som inte har orsakat för dimensionen nyligen. Om du anger ett värde som inte finns för en egenskap aktiveras inte aviseringen.

1. Under **Varna logik**, definiera villkor och tröskel för aviseringen. En förhandsgranskning av dina villkor definierade visas under.

1. Under **Evaluated baserat på** Välj timespan för frågan och hur ofta du vill att frågan kördes. Om du exempelvis **under de senaste 5 minuterna** för **Period** och **var 1 minut** för **frekvens**, aviseringen söker efter numret runbooks som uppfyller dina kriterier under de senaste 5 minuterna. Den här frågan är körde varje minut och när aviseringsvillkoren du angett är inte längre finns i ett fönster i 5 minuter, löser aviseringen sig själv. När du är klar klickar du på **klar**.

   ![Välj en resurs för aviseringen](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definiera aviseringsinformation

1. Under **2. Definiera aviseringsinformation**, ge aviseringen ett eget namn och beskrivning. Ange den **allvarlighetsgrad** som matchar din aviseringstillståndet. Det finns fem allvarlighetsgraderna mellan 0 och 5. Aviseringarna behandlas samma oberoende av allvarlighetsgrad, kan du stämma med allvarlighetsgraden för att matcha affärslogik.

1. Längst ned i avsnittet är en knapp som gör att du kan aktivera regel när åtgärden har slutförts. Som standard aktiverade regler när skapas. Om du väljer Nej, du kan skapa aviseringen och den skapas i en **inaktiverad** tillstånd. Från den **regler** sida i Azure-Monitor kan du markera den och klicka på **aktivera** att aktivera en avisering när du är klar.

### <a name="define-the-action-to-take"></a>Definiera vad som ska göras

1. Under **3. Definiera åtgärdsgrupp**, klickar du på **+ ny grupp**. En grupp är en grupp av åtgärder som kan användas i flera aviseringar. Dessa kan inkludera, men är inte begränsade till e-postmeddelanden, runbooks, webhooks och mycket mer. Läs mer om åtgärdsgrupper i [skapa och hantera åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md)

1. I den **åtgärd gruppnamn** och ge det ett eget namn och ett kort namn. Det korta namnet används i stället för en fullständig åtgärd gruppnamn när meddelanden som skickas med den här gruppen.

1. I den **åtgärder** avsnittet **ÅTGÄRDSTYP**väljer **e-post/SMS/Push/röst**.

1. På den **e-post/SMS/Push/röst** och ge det ett namn. Kontrollera den **e-post** kryssrutan och ange en giltig e-postadress som ska användas.

   ![Konfigurera e-grupp](./media/automation-alert-activity-log/add-action-group.png)

1. Klicka på **OK** på den **e-post/SMS/Push/röst** att stänga den och klicka på **OK** att stänga den **Lägg till grupp** sidan. Namnet som angetts i den här sidan sparas som den **ÅTGÄRDSNAMN**.

1. Klicka på **Spara** när du är klar. Detta skapar en regel som aviserar när en runbook som slutfördes med en viss status.

> [!NOTE]
> När du lägger till en e-postadress i en grupp, skickas ett e-postmeddelande om adressen har lagts till i en grupp.

## <a name="notification"></a>Avisering

Åtgärdsgruppen körs åtgärden som definieras när aviseringsvillkoren uppfylls. I den här artikeln exempelvis skickas ett e-postmeddelande. Följande bild är ett exempel på ett e-postmeddelande när en avisering utlöses:

![E-postavisering](./media/automation-alert-activity-log/alert-email.png)

När måttet är inte längre utanför definierade tröskelvärdet, aviseringen har inaktiverats och åtgärdsgruppen körs den åtgärd som definierats. Om en e-åtgärdstyp väljs postmeddelande en upplösning e-om den har lösts.

## <a name="next-steps"></a>Nästa steg

Fortsätta att följande artikel om du vill veta mer om andra sätt att du kan integrera alertings i ditt Automation-konto.

> [!div class="nextstepaction"]
> [Använd en avisering för att utlösa en Azure Automation-runbook](automation-create-alert-triggered-runbook.md)
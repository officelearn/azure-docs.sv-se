---
title: "Skapa aviseringar för Azure-tjänster - Azure-portalen | Microsoft Docs"
description: "Utlösaren e-postmeddelanden meddelanden, anropa webbplatser URL: er (webhooks) eller automation när angivna villkor uppfylls."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb
ms.openlocfilehash: 3e09c145d35665ec1c2467b60f06191ac51a5c16
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>Skapa mått aviseringar i Azure-Monitor för Azure-tjänster - Azure-portalen
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in Azure mått aviseringar med Azure-portalen. 

Du kan ta emot en avisering baserat på övervakning mätvärden för eller händelser på Azure-tjänster.

* **Måttvärden** -aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar i båda riktningarna. Det vill säga den utlöser både när villkoret uppfylls först och sedan efteråt när villkor som inte längre är uppfyllt.    
* **Aktiviteten logghändelser** -utlösa en avisering på *varje* händelse eller bara när vissa händelser inträffar. Lär dig mer om [aktivitet loggen aviseringar](monitoring-activity-log-alerts.md).

Du kan konfigurera en mått avisering när den utlöser gör du följande:

* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* anropa en webhook
* Starta körning av en Azure-runbook (endast från Azure portal)

> [!NOTE]
> Azure-Monitor stöder nu nära realtid mått aviseringar som förhandsversion. Dessa använda grupper för åtgärden. Lär dig mer om [nära realtid mått aviseringar](monitoring-near-real-time-metric-alerts.md).
>
>

Du kan konfigurera och få information om mått Varningsregler med

* [Azure-portalen](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [kommandoradsgränssnittet (CLI)](insights-alerts-command-line-interface.md)
* [Azure-Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Skapa en aviseringsregel på ett mått med Azure-portalen
1. I den [portal](https://portal.azure.com/), leta upp den resurs som du är intresserad av övervakning och markera den.

2. Välj **aviseringar** eller **Varna regler** under avsnittet övervakning. Text och ikon kan variera något mellan olika resurser.  

    ![Övervakning](./media/insights-alerts-portal/AlertRulesButton.png)

3. Välj den **Lägg till avisering** kommando och Fyll i fälten.

    ![Lägg till avisering](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Namnet** aviseringen regel och väljer en **beskrivning**, som visar även i e-postmeddelanden.

5. Välj den **mått** du vill övervaka och väljer sedan en **villkoret** och **tröskelvärdet** värdet för måttet. Också välja den **Period** som mått regeln måste uppfyllas innan aviseringen utlösare. Om du använder perioden ”under de senaste 5 minuterna” och aviseringen söker efter CPU över 80 procent, startar exempelvis aviseringen när Processorn har konsekvent ovan 80% i 5 minuter. När den första utlösaren infaller utlöses igen när Processorn är mindre än 80% i 5 minuter. CPU mått mätningen inträffar var en minut.

6. Kontrollera **e-ägare...**  om du vill att administratörer och medadministratörer kan skickas när aviseringen utlöses.

7. Om du vill att ytterligare e-postmeddelanden ett meddelande när aviseringen utlöses, lägga till dem i den **ytterligare administratören email(s)** fältet. Avgränsa flera e-postmeddelanden med semikolon -  *email@contoso.com;email2@contoso.com*

8. Placera i en giltig URI i den **Webhook** om du vill att den anropas när aviseringen utlöses.

9. Om du använder Azure Automation kan välja du en Runbook som ska köras när aviseringen utlöses.

10. Välj **OK** när du är klar för att skapa aviseringen.   

Inom några minuter aviseringen är aktiv och utlöser som beskrivits tidigare.

## <a name="managing-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering, kan du välja den och:

* Visa ett diagram som visar mått tröskelvärdet och faktiska värden från föregående dag.
* Redigera eller ta bort den.
* **Inaktivera** eller **aktivera** den om du vill att tillfälligt stoppa eller återuppta tar emot meddelanden om den här aviseringen.

## <a name="next-steps"></a>Nästa steg
* [Få en översikt över Azure övervakning](monitoring-overview.md) inklusive typerna av information som du kan samla in och övervaka.
* Mer information om den nya [nära realtid mått aviseringar (förhandsgranskning)](monitoring-near-real-time-metric-alerts.md)
* Lär dig mer om [hur du konfigurerar webhooks i aviseringar](insights-webhooks-alerts.md).
* Lär dig mer om [konfigurera aviseringar på aktiviteten logghändelser](monitoring-activity-log-alerts.md).
* Lär dig mer om [Azure Automation-Runbooks](../automation/automation-starting-a-runbook.md).
* Hämta en [översikt över diagnostikloggar](monitoring-overview-of-diagnostic-logs.md) och samla in detaljerade hög frekvens mått på din tjänst.
* Hämta en [översikt över mått samling](insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarstid.

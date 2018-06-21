---
title: Använda Azure portal för att skapa klassisk aviseringar för Azure-tjänster | Microsoft Docs
description: 'Aktivera e-post eller meddelanden eller anropa webbplats-URL: er (webhooks) eller automation när du anger villkor är uppfyllda.'
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287437"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Använda Azure portal för att skapa klassiska mått aviseringar i Azure-Monitor för Azure-tjänster 

> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> Den här artikeln beskriver hur du skapar äldre klassiska mått aviseringar. Stöd för Azure övervakaren [nyare mått aviseringar](monitoring-near-real-time-metric-alerts.md). 


Den här artikeln visar hur du ställer in klassiska Azure-mått aviseringar med hjälp av Azure portal. 

Du kan ta emot en avisering baserat på mått för din Azure-tjänster eller du kan ta emot aviseringar om händelser som inträffar i Azure.

* **Måttvärden**: aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar i båda riktningarna. Det vill säga den utlöser både när villkoret uppfylls först och sedan när den är inte längre är uppfyllt.    

* **Aktiviteten logghändelser**: en avisering kan utlösa på *varje* händelse eller när vissa händelser inträffar. Lär dig mer om [aktivitet loggen aviseringar](monitoring-activity-log-alerts.md).

Du kan konfigurera en klassiska mått avisering när den utlöser gör du följande:

* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer.
* Skicka e-post till ytterligare e-postadresser som du anger.
* Anropa en webhook.
* Starta körning av en Azure-runbook (endast från Azure portal).

Du kan konfigurera och få information om klassiska mått Varningsregler från följande platser: 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Azure-Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Skapa en aviseringsregel på ett mått med Azure-portalen
1. I den [portal](https://portal.azure.com/), leta upp den resurs som du vill övervaka och markerar den.

2. I den **övervakning** väljer **aviseringar (klassisk)**. Text och ikon kan variera något för olika resurser. Om du inte hittar **aviseringar (klassisk)** här, kan det vara i **aviseringar** eller **Varningsregler**.

    ![Övervakning](./media/insights-alerts-portal/AlertRulesButton.png)

3. Välj den **Lägg till mått avisering (klassisk)** kommandot och fyll sedan i fälten.

    ![Lägg till avisering](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Namnet** regel för varning. Välj en **beskrivning**, som också visas i e-postmeddelanden.

5. Välj den **mått** som du vill övervaka. Välj en **villkoret** och **tröskelvärdet** värdet för måttet. Också välja den **Period** som mått regeln måste uppfyllas innan aviseringen utlösare. Om du använder perioden ”under de senaste 5 minuterna” och aviseringen söker efter en processor över 80 procent, startar exempelvis aviseringen när Processorn har konsekvent ovan 80% i 5 minuter. Efter först inträffar utlösaren utlöser igen när Processorn är mindre än 80% i 5 minuter. CPU mått mätningen sker varje minut.

6. Välj **e-ägare...**  om du vill att administratörer och medadministratörer för e-postavisering när aviseringen utlöses.

7. Om du vill skicka meddelanden till ytterligare e-postadresser när aviseringen utlöses, lägga till dem i den **ytterligare administratören email(s)** fältet. Avgränsa flera e-postmeddelanden med semikolon, i följande format:  *email@contoso.com; email2@contoso.com*

8. Placera i en giltig URI i den **Webhook** om du vill att det ska anropas när aviseringen utlöses.

9. Om du använder Azure Automation kan välja du en runbook som ska köras när aviseringen utlöses.

10. Välj **OK** att skapa aviseringen.   

Inom några minuter aviseringen är aktiv och utlöser som beskrivits tidigare.

## <a name="manage-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering du markera den och gör något av följande uppgifter:

* Visa ett diagram som visar mått tröskelvärdet och faktiska värden från föregående dag.
* Redigera eller ta bort den.
* **Inaktivera** eller **aktivera** den om du vill att tillfälligt stoppa eller återuppta tar emot meddelanden om den här aviseringen.

## <a name="next-steps"></a>Nästa steg
* [Få en översikt över Azure övervakning](monitoring-overview.md), inklusive typerna av information som du kan samla in och övervaka.
* Lär dig mer om den [nyare mått aviseringar](monitoring-near-real-time-metric-alerts.md).
* Lär dig mer om [hur du konfigurerar webhooks i aviseringar](insights-webhooks-alerts.md).
* Lär dig mer om [konfigurera aviseringar på aktiviteten logghändelser](monitoring-activity-log-alerts.md).
* Lär dig mer om [Azure Automation-runbooks](../automation/automation-starting-a-runbook.md).
* Hämta en [översikt över diagnostikloggar](monitoring-overview-of-diagnostic-logs.md), och samla in detaljerade hög frekvens mått på din tjänst.
* Hämta en [översikt över mått samling](insights-how-to-customize-monitoring.md) se till att tjänsten är tillgänglig och svarstid.

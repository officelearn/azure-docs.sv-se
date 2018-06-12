---
title: Skapa klassiska aviseringar för Azure-tjänster - plattformar CLI
description: 'Utlösaren e-postmeddelanden meddelanden, anropa webbplatser URL: er (webhooks) eller automation när angivna villkor uppfylls.'
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 35f87f140772be1777ddfb184e78b61446bb3bd6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267755"
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Skapa klassiska mått aviseringar i Azure-Monitor för Azure-tjänster - plattformar CLI
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Översikt
> [!NOTE]
> Den här artikeln beskriver hur du skapar äldre klassiska mått aviseringar. Stöd för Azure övervakaren [nyare, bättre mått aviseringar](monitoring-near-real-time-metric-alerts.md). Dessa aviseringar kan du övervaka flera och tillåter aviseringar om dimensionell mått. CLI-stöd för nyare mått aviseringar kommer snart.
>
>

Den här artikeln visar hur du ställer in Azure klassiska mått aviseringar via plattformsoberoende kommandoradsgränssnittet (CLI).

> [!NOTE]
> Azure övervakaren är det nya namnet för vad anropades ”Azure Insights” förrän den 25 september 2016. Namnområden och därmed nedanstående kommandon fortfarande innehåller dock ”insikter”.
>
>

Du kan ta emot en avisering baserat på övervakning mätvärden för eller händelser på Azure-tjänster.

* **Måttvärden** -aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar i båda riktningarna. Det vill säga den utlöser både när villkoret uppfylls först och sedan efteråt när villkor som inte längre är uppfyllt.    
* **Aktiviteten logghändelser** -utlösa en avisering på *varje* händelse eller bara när en viss händelse inträffar. Mer information om aktiviteten loggen aviseringar [Klicka här](monitoring-activity-log-alerts.md)

Du kan konfigurera en klassiska mått avisering när den utlöser gör du följande:

* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* anropa en webhook
* Starta körning av en Azure-runbook (endast från Azure-portalen just nu)

Du kan konfigurera och få information om klassiska mått Varningsregler med

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Kommandoradsgränssnittet (CLI)](insights-alerts-command-line-interface.md)
* [Azure-Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Du kan alltid få hjälp för kommandon genom att skriva ett kommando och tas - hjälp i slutet. Exempel:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Skapa Varningsregler med hjälp av CLI
1. Utföra förutsättningarna och logga in på Azure. Se [Azure övervakaren CLI exempel](insights-cli-samples.md). Kort sagt: Installera CLI och köra dessa kommandon. De får du loggade in, visa vilken prenumeration som du använder och förbereda dig för att köra Azure-Monitor-kommandon.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Använd följande syntax om du vill visa en lista med befintliga regler på en resursgrupp, **azure insikter aviseringar regel listan** *[alternativ] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Om du vill skapa en regel som du behöver ha flera viktiga uppgifter för först.
  * Den **resurs-ID** för den resurs som du vill aktivera en avisering för
  * Den **måttdefinitioner** tillgänglig för den här resursen

     Ett sätt att hämta resurs-ID är att använda Azure-portalen. Under förutsättning att resursen har redan skapats, väljer du den i portalen. Välj sedan i bladet nästa *egenskaper* under den *inställningar* avsnitt. Den *resurs-ID* är ett fält i bladet nästa. Ett annat sätt är att använda den [resursutforskaren Azure](https://resources.azure.com/).

     Är ett exempel resurs-id för en webbapp

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Om du vill hämta en lista över tillgängliga mått och enheter för dessa mätvärden för exemplet ovan resurs, kan du använda kommandot CLI:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* är Granulariteten för tillgängliga mått (1 minut). Olika granulariteter får du använder olika alternativ för mått.
4. Om du vill skapa ett mått baserat varningsregeln använder du kommandot i följande format:

    **Azure insikter aviseringar regeluppsättning mått** *[alternativ] &lt;ruleName&gt; &lt;plats&gt; &lt;resourceGroup&gt; &lt;fönsterstorlek&gt; &lt;operatorn&gt; &lt;tröskelvärdet&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    I följande exempel ställer in en avisering för en resurs för webbplatsen. Aviseringen utlösare när den får konsekvent all trafik för 5 minuter och igen när den tar emot någon trafik i 5 minuter.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. För att skapa webhooken eller skicka e-postmeddelande när en klassiska mått avisering utlöses, först skapa e-post och/eller webhooks. Skapa regel omedelbart efteråt. Du kan inte associera webhook eller e-post med redan skapat regler med hjälp av CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Du kan kontrollera att aviseringar har skapats korrekt genom att titta på en enskild regel.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Kommandot formuläret för att ta bort regler:

    **insikter aviseringar regel delete** [alternativ] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Dessa kommandon ta bort regler som skapats tidigare i den här artikeln.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Nästa steg
* [Få en översikt över Azure övervakning](monitoring-overview.md) inklusive typerna av information som du kan samla in och övervaka.
* Lär dig mer om [hur du konfigurerar webhooks i aviseringar](insights-webhooks-alerts.md).
* Lär dig mer om [konfigurera aviseringar på aktiviteten logghändelser](monitoring-activity-log-alerts.md).
* Lär dig mer om [Azure Automation-Runbooks](../automation/automation-starting-a-runbook.md).
* Hämta en [översikt över att samla in diagnostikloggar](monitoring-overview-of-diagnostic-logs.md) att samla in detaljerade hög frekvens mått på din tjänst.
* Hämta en [översikt över mått samling](insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarstid.

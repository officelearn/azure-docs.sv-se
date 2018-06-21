---
title: Använd plattformsoberoende Azure CLI för att skapa klassisk aviseringar för Azure-tjänster | Microsoft Docs
description: 'Aktivera e-post eller meddelanden eller anropa webbplatser URL: er (webhooks) eller automation när du anger villkor är uppfyllda.'
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287120"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Använd plattformsoberoende Azure CLI för att skapa klassisk mått aviseringar i Azure-Monitor för Azure-tjänster 

> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> Den här artikeln beskriver hur du skapar äldre klassiska mått aviseringar. Stöd för Azure övervakaren [nyare, bättre mått aviseringar](monitoring-near-real-time-metric-alerts.md). Dessa aviseringar kan du övervaka flera och tillåter aviseringar om dimensionell mått. Azure CLI-stöd för nyare mått aviseringar kommer snart.
>
>

Den här artikeln visar hur du ställer in Azure klassiska mått aviseringar med hjälp av plattformsoberoende kommandoradsgränssnittet (Azure CLI).

> [!NOTE]
> Azure övervakaren är det nya namnet för vad anropades ”Azure Insights” förrän den 25 September 2016. Dock innehåller namnområden och därmed de kommandon som beskrivs här fortfarande ordet ”insikter”.

Du kan ta emot en avisering baserat på mått för din Azure-tjänster eller baserat på händelser som inträffar i Azure.

* **Måttvärden**: aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar i båda riktningarna. Det vill säga den utlöser både när villkoret uppfylls först och sedan när den är inte längre är uppfyllt.    

* **Aktiviteten logghändelser**: en avisering kan utlösa på *varje* händelse eller när vissa händelser inträffar. Läs mer om aktivitetsloggar i [och skapa aktiviteten Logga varningar (klassisk)](monitoring-activity-log-alerts.md). 

Du kan konfigurera en klassiska mått avisering när den utlöser gör du följande:

* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer. 
* Skicka e-post till e-postadresser som du anger.
* Anropa en webhook.
* Starta körning av en Azure-runbook (endast från Azure-portalen just nu).

Du kan konfigurera och få information om klassiska mått Varningsregler med hjälp av följande: 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Azure-Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Du kan också få hjälp för kommandon genom att skriva ett kommando med **-hjälp** i slutet. Följande är ett exempel: 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Skapa Varningsregler med hjälp av Azure CLI
1. När du har installerat kraven kan du logga in på Azure. Se [Azure övervakaren CLI prover](insights-cli-samples.md) för kommandon som du behöver för att komma igång. Dessa kommandon hjälpa dig få loggat in, visa vilken prenumeration som du använder och förbereda dig för att köra Azure-Monitor-kommandon.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Om du vill visa en lista med befintliga regler på en resursgrupp, använder du följande format: 

   **Azure insikter aviseringar regel listan** *[alternativ] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Om du vill skapa en regel som du behöver ha flera viktiga uppgifter för först.
    * Den **resurs-ID** för den resurs som du vill aktivera en avisering för.
    * Den **måttdefinitioner** som är tillgängliga för den här resursen.

     Ett sätt att hämta resurs-ID är att använda Azure-portalen. Förutsatt att resursen har redan skapats, markerar du den i portalen. I bladet nästa i den **inställningar** väljer **egenskaper**. **RESURS-ID** är ett fält i bladet nästa. 
     
     Du kan också få resurs-ID med hjälp av [resursutforskaren Azure](https://resources.azure.com/).

     Följande är ett exempel resurs-ID för ett webbprogram:

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Om du vill hämta en lista över tillgängliga mått och enheter för mätvärdena som i exemplet ovan resurs, använder du följande Azure CLI-kommando:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* är Granulariteten för tillgängliga mått (i 1 minut). När du använder olika granulariteter har du olika alternativ för mått.
     
4. Om du vill skapa ett mått baserat varningsregeln använder du kommandot i följande format:

    **Azure insikter aviseringar regeluppsättning mått** *[alternativ] &lt;ruleName&gt; &lt;plats&gt; &lt;resourceGroup&gt; &lt;fönsterstorlek&gt; &lt;operatorn&gt; &lt;tröskelvärdet&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    I följande exempel ställer in en avisering för en resurs för webbplatsen. Aviseringen utlösare när den får konsekvent all trafik för 5 minuter och igen när den tar emot någon trafik i 5 minuter.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Om du vill skapa en webhook eller skicka ett e-postmeddelande när en klassiska mått avisering utlöses du först skapa e-post eller webhooken. Skapa regel omedelbart efteråt. Du kan inte associera webhooks eller e-postmeddelanden med regler som redan har skapats.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Du kan kontrollera att aviseringar har skapats korrekt genom att titta på en enskild regel.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Om du vill ta bort regler, använder du kommandot i följande format:

    **insikter aviseringar regel delete** [alternativ] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Dessa kommandon ta bort regler som tidigare har skapats i den här artikeln.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Nästa steg
* [Få en översikt över Azure övervakning](monitoring-overview.md), inklusive typerna av information som du kan samla in och övervaka.
* Lär dig mer om [hur du konfigurerar webhooks i aviseringar](insights-webhooks-alerts.md).
* Lär dig mer om [konfigurera aviseringar på aktiviteten logghändelser](monitoring-activity-log-alerts.md).
* Lär dig mer om [Azure Automation-runbooks](../automation/automation-starting-a-runbook.md).
* Hämta en [översikt över att samla in diagnostikloggar](monitoring-overview-of-diagnostic-logs.md) att samla in detaljerade hög frekvens mått för din tjänst.
* Hämta en [översikt över mått samling](insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarstid.

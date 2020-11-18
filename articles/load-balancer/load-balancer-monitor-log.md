---
title: Övervaka åtgärder, händelser och räknare för offentliga Basic-Load Balancer
titleSuffix: Azure Load Balancer
description: Lär dig hur du aktiverar aviserings händelser och loggning av avsöknings hälso status för offentliga Basic-Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: f24ab2c646757f0241748336243b0d5f977d081c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698333"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor-loggar för offentlig Basic Load Balancer

Du kan använda olika typer av loggar i Azure för att hantera och felsöka grundläggande belastnings utjämning. Vissa av dessa loggar kan nås via portalen. Loggar kan strömmas till en händelsehubben eller en arbets yta för Log Analytics. Alla loggar kan extraheras från Azure Blob Storage och visas i olika verktyg, till exempel Excel och Power BI.  Du kan lära dig mer om de olika typerna av loggar i listan nedan.

* **Aktivitets loggar:** Du kan använda [Visa aktivitets loggar för att övervaka åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md) för att visa all aktivitet som skickas till din Azure-prenumeration (er) och deras status. Aktivitets loggar är aktiverade som standard och kan visas i Azure Portal.
* **Aviserings händelse loggar:** Du kan använda den här loggen för att visa aviseringar som aktive ras av belastningsutjämnaren. Status för belastningsutjämnaren samlas in var femte minut. Den här loggen skrivs bara om en belastnings Utjämnings aviserings händelse höjs.
* **Hälso avsöknings loggar:** Du kan använda den här loggen för att visa problem som upptäckts av din hälso avsökning, till exempel antalet instanser i din backend-pool som inte tar emot begär Anden från belastningsutjämnaren på grund av hälso avsöknings fel. Loggen skrivs till när statusen för hälso avsökningen ändras.

> [!IMPORTANT]
> **Händelse loggar för hälso avsökning fungerar inte för närvarande och anges i de [kända problemen för Azure Load Balancer](whats-new.md#known-issues).** Loggar är bara tillgängliga för resurser som distribueras i distributions modellen för Resource Manager. Du kan inte använda loggar för resurser i den klassiska distributions modellen. Mer information om distributions modellerna finns i [förstå Resource Manager-distribution och klassisk distribution](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Aktivera loggning

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Aktivera loggning av händelse-och hälso avsökning för att börja samla in data som är tillgängliga via dessa loggar. Använd följande steg för att aktivera loggning.

Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har en belastningsutjämnare måste du [skapa en belastningsutjämnare](./quickstart-load-balancer-standard-public-portal.md) innan du fortsätter.

1. I portalen klickar du på **resurs grupper**.
2. Välj **\<resource-group-name>** var belastningsutjämnaren är.
3. Välj din lastbalanserare.
4. Välj **Activity log**  >  **diagnostiska inställningar** för aktivitets loggen.
5. I fönstret **diagnostikinställningar** , under **diagnostikinställningar**, väljer du **+ Lägg till diagnostisk inställning**.
6. I fönstret Skapa **diagnostikinställningar** anger du **MyLBDiagnostics** i fältet **namn** .
7. Det finns tre alternativ för **diagnostikinställningar**.  Du kan välja en, två eller alla tre och konfigurera var och en för dina behov:
   * **Arkivera till ett lagrings konto**
   * **Strömma till en händelsehubb**
   * **Skicka till Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Arkivera till ett lagringskonto
    Du behöver ett lagrings konto som redan har skapats för den här processen.  Information om hur du skapar ett lagrings konto finns i [skapa ett lagrings konto](../storage/common/storage-account-create.md?tabs=azure-portal)

    1. Markera kryss rutan bredvid **arkivera till ett lagrings konto**.
    2. Välj **Konfigurera** för att öppna fönstret **Välj ett lagrings konto** .
    3. Välj den **prenumeration** där ditt lagrings konto skapades i den nedrullningsbara rutan.
    4. Välj namnet på ditt lagrings konto under **lagrings konto** i den nedrullningsbara List rutan.
    5. Välj OK.

    ### <a name="stream-to-an-event-hub"></a>Strömma till en händelsehubb
    Du behöver en Event Hub som redan har skapats för den här processen.  Information om hur du skapar en Event Hub finns i [snabb start: skapa en Event Hub med Azure Portal](../event-hubs/event-hubs-create.md)

    1. Markera kryss rutan bredvid **strömma till en Event Hub**
    2. Välj **Konfigurera** för att öppna fönstret **Välj händelsehubben** .
    3. Välj den **prenumeration** där händelsehubben skapades i den nedrullningsbara rutan.
    4. **Välj namn område för Event Hub** i den nedrullningsbara rutan.
    5. **Välj princip namn för Event Hub** i den nedrullningsbara rutan.
    6. Välj OK.

    ### <a name="send-to-log-analytics"></a>Skicka till Log Analytics
    Du behöver redan ha en Log Analytics-arbetsyta skapad och konfigurerad för den här processen.  Om du vill skapa en Log Analytics arbets yta, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/learn/quick-create-workspace.md)

    1. Markera kryss rutan bredvid **Skicka till Log Analytics**.
    2. Välj den **prenumeration** där Log Analytics arbets ytan finns i den nedrullningsbara rutan.
    3. Välj **arbets ytan Log Analytics** i list rutan.


8. Under avsnittet **logg** i fönstret **diagnostikinställningar** väljer du kryss rutan bredvid båda:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Under avsnittet **mått** i fönstret **diagnostikinställningar** väljer du kryss rutan bredvid:
   * **AllMetrics**

11. Kontrol lera att allting ser korrekt ut och klicka sedan på **Spara** högst upp i fönstret Skapa **diagnostiska inställningar** .

## <a name="activity-log"></a>Aktivitetslogg

Aktivitets loggen skapas som standard. Loggarna bevaras för 90 dagar i Azures händelse logg arkiv. Läs mer om dessa loggar genom att läsa artikeln [Visa aktivitets loggar för att övervaka åtgärder i resurser](../azure-resource-manager/management/view-activity-logs.md) .

## <a name="archive-to-storage-account-logs"></a>Arkivera till lagringskontologgar

### <a name="alert-event-log"></a>Aviserings händelse logg

Den här loggen skapas endast om du har aktiverat den per belastningsutjämnare. Händelserna är inloggade i JSON-format och lagras i det lagrings konto du angav när du aktiverade loggningen. Följande exempel är en händelse.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

JSON-utdata visar egenskapen *EventName* , som beskriver orsaken till att belastningsutjämnaren skapade en avisering. I det här fallet orsakade den genererade aviseringen på grund av att TCP-portens belastning orsakade av NAT-gränser (SNAT).

### <a name="health-probe-log"></a>Hälso avsöknings logg

Den här loggen skapas endast om du har aktiverat den per belastningsutjämnare enligt beskrivningen ovan. Data lagras i det lagrings konto du angav när du aktiverade loggningen. En behållare med namnet Insights-logs-loadbalancerprobehealthstatus skapas och följande data loggas:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

JSON-utdata visas i fältet egenskaper grundläggande information om avsökningens hälso status. Egenskapen *dipDownCount* visar det totala antalet instanser på Server delen, som inte tar emot nätverks trafik på grund av misslyckade avsöknings svar.

### <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Du kan visa och analysera aktivitets logg data med någon av följande metoder:

* **Azure-verktyg:** Hämta information från aktivitets loggen via Azure PowerShell, kommando rads gränssnittet för Azure (CLI), Azure-REST API eller Azure Portal. Stegvisa instruktioner för varje metod beskrivs i artikeln [Granska åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** Om du inte redan har ett [Power BI](https:// .microsoft.com/pricing) konto kan du prova det kostnads fritt. Med [innehålls paketet för Azure audit-loggar för Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)kan du analysera dina data med förkonfigurerade instrument paneler, eller så kan du anpassa vyer så att de passar dina behov.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visa och analysera hälso avsökningen och händelse loggen

Anslut till ditt lagrings konto och hämta JSON-logg poster för händelse-och hälso avsöknings loggar. När du har hämtat JSON-filerna kan du konvertera dem till CSV och Visa i Excel, Power BI eller något annat verktyg för data visualisering.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.

## <a name="stream-to-an-event-hub"></a>Strömma till en händelsehubb
När diagnostikinformation strömmas till en Event Hub, kan den användas för centraliserad logg analys i ett SIEM-verktyg från tredje part med Azure Monitor-integrering. Mer information finns i [Stream Azure Monitoring data to Event Hub](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Skicka till Log Analytics
Resurser i Azure kan få sin diagnostikinformation att skickas direkt till en Log Analytics arbets yta där komplexa frågor kan köras mot informationen för fel sökning och analys.  Mer information finns i [samla in Azure-resursposter i Log Analytics arbets yta i Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="next-steps"></a>Nästa steg

[Förstå avsökningar av lastbalanserare](load-balancer-custom-probe-overview.md)
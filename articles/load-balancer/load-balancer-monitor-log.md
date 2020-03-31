---
title: Övervaka åtgärder, händelser och räknare för offentliga grundläggande belastningsutjämnare
titleSuffix: Azure Load Balancer
description: Lär dig hur du aktiverar varningshändelser och avsöker hälsostatusloggning för offentliga Grundläggande belastningsutjämnare
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935319"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor-loggar för offentlig Basic Load Balancer

>[!IMPORTANT]
>Azures Load Balancer stöder två typer: grundläggande och standard. Den här artikeln beskriver den grundläggande lastbalanseraren. Mer information om standardbelastningsutjämning finns i [standardöverbelastningsutjämnaröversikt](load-balancer-standard-overview.md) som exponerar telemetri via flerdimensionella mått i Azure Monitor.

Du kan använda olika typer av loggar i Azure för att hantera och felsöka grundläggande belastningsutjämnare. Vissa av dessa loggar kan nås via portalen. Loggar kan strömmas till en händelsehubb eller en Log Analytics-arbetsyta. Alla loggar kan extraheras från Azure blob storage och visas i olika verktyg, till exempel Excel och Power BI.  Du kan läsa mer om de olika typerna av loggar från listan nedan.

* **Aktivitetsloggar:** Du kan använda [Visa aktivitetsloggar för att övervaka åtgärder på resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) för att visa all aktivitet som skickas till dina Azure-prenumerationer och deras status. Aktivitetsloggar är aktiverade som standard och kan visas i Azure-portalen.
* **Varningshändelseloggar:** Du kan använda den här loggen för att visa aviseringar som utlöses av belastningsutjämnaren. Statusen för belastningsutjämnaren samlas in var femte minut. Den här loggen skrivs endast om en belastningsutjämnad varningshändelse utlöses.
* **Hälsoavsökningsloggar:** Du kan använda den här loggen för att visa problem som identifierats av hälsoavsökningen, till exempel antalet instanser i serverdapoolen som inte tar emot begäranden från belastningsutjämnaren på grund av hälsoavsökningsfel. Den här loggen skrivs till när hälsoavsökningsstatusen ändras.

> [!IMPORTANT]
> Azure Monitor-loggar fungerar för närvarande endast för offentliga grundläggande belastningsutjämnare. Loggar är endast tillgängliga för resurser som distribueras i Resurshanterarens distributionsmodell. Du kan inte använda loggar för resurser i den klassiska distributionsmodellen. Mer information om distributionsmodellerna finns i [Förstå Resurshanterarens distribution och klassisk distribution](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Aktivera loggning

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Aktivera loggning av händelse- och hälsoavsökningar för att börja samla in tillgängliga data via dessa loggar. Gör så här för att aktivera loggning.

Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har en belastningsutjämnare [skapar du en belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) innan du fortsätter.

1. Klicka på **Resursgrupper**i portalen .
2. Välj ** \<resursgruppnamn>** där belastningsutjämnaren finns.
3. Välj din belastningsutjämnare.
4. Välj Inställningar för**övervakningsdiagnostik** **Monitoring** > .
5. Välj **+ Lägg till diagnostikinställning**under **Diagnostikinställningar**i fönstret **Diagnostikinställningar** .
6. Ange **myLBDiagnostics** i fältet **Namn** i fönstret **Diagnostikinställningar.**
7. Du har tre alternativ för **diagnostikinställningarna**.  Du kan välja en, två eller alla tre och konfigurera var och en för dina behov:
   * **Arkivera till ett lagringskonto**
   * **Strömma till ett händelsenav**
   * **Skicka till Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Arkivera till ett lagringskonto
    Du behöver ett lagringskonto som redan har skapats för den här processen.  Information om hur du skapar ett lagringskonto finns i [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Markera kryssrutan **bredvid Arkiv till ett lagringskonto**.
    2. Välj **Konfigurera** om du vill öppna fönstret **Välj ett lagringskonto.**
    3. Välj den **prenumeration** där ditt lagringskonto skapades i rulllådan.
    4. Välj namnet på ditt lagringskonto under **Lagringskontot** i rulllådan.
    5. Välj OK.

    ### <a name="stream-to-an-event-hub"></a>Strömma till en händelsehubb
    Du behöver en händelsehubb som redan har skapats för den här processen.  Information om hur du skapar en händelsehubb finns i [Snabbstart: Skapa en händelsehubb med Azure-portalen](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Markera kryssrutan bredvid **Strömma till en händelsehubb**
    2. Välj **Konfigurera** om du vill öppna fönstret **Välj händelsehubb.**
    3. Välj den **prenumeration** där händelsehubben skapades i rulllådan.
    4. **Välj namnområde för händelsehubben** i rullrutan.
    5. **Välj händelsenavprincipnamn** i rulllådan.
    6. Välj OK.

    ### <a name="send-to-log-analytics"></a>Skicka till Log Analytics
    Du måste redan ha en logganalysarbetsyta skapad och konfigurerad för den här processen.  Information om hur du skapar en log Analytics-arbetsyta finns [i Skapa en log Analytics-arbetsyta i Azure-portalen](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Markera kryssrutan bredvid **Skicka till Logganalys**.
    2. Välj den **prenumeration** där arbetsytan Log Analytics finns i rulllådan.
    3. Välj **arbetsytan Logganalys** i rulllådan.


8. Markera kryssrutan bredvid båda under **avsnittet LOGG** i fönstret Inställningar **för diagnostik:**
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Markera kryssrutan **METRIC** bredvid: **Diagnostics settings**
   * **AllMetrics**

11. Kontrollera att allt ser korrekt ut och klicka på **Spara** högst upp i fönstret **Skapa diagnostikinställningar.**

## <a name="activity-log"></a>Aktivitetslogg

Aktivitetsloggen genereras som standard. Loggarna bevaras i 90 dagar i Azures eventloggar. Läs mer om dessa loggar genom att läsa [visa aktivitetsloggar för att övervaka åtgärder på resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) artikeln.

## <a name="archive-to-storage-account-logs"></a>Arkivera till lagringskontologgar

### <a name="alert-event-log"></a>Varningshändelselogg

Den här loggen genereras bara om du har aktiverat den per belastningsutjämnare. Händelserna loggas i JSON-format och lagras i det lagringskonto som du angav när du aktiverade loggningen. Följande exempel är av en händelse.

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

JSON-utdata visar egenskapen *eventname,* som beskriver orsaken till att belastningsutjämnaren skapade en avisering. I det här fallet var aviseringen som genererades på grund av TCP-portutmattning orsakad av källans IP NAT-gränser (SNAT).

### <a name="health-probe-log"></a>Hälsoavsökningslogg

Den här loggen genereras bara om du har aktiverat den per belastningsutjämnare enligt beskrivningen ovan. Data lagras i det lagringskonto som du angav när du aktiverade loggningen. En behållare med namnet "insights-logs-loadbalancerprobehealthstatus" skapas och följande data loggas:

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

JSON-utdata visar i egenskapsfältet den grundläggande informationen för avsökningens hälsostatus. Egenskapen *dipDownCount* visar det totala antalet instanser på serverdelen, som inte tar emot nätverkstrafik på grund av misslyckade avsökningssvar.

### <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Du kan visa och analysera aktivitetsloggdata med någon av följande metoder:

* **Azure-verktyg:** Hämta information från aktivitetsloggen via Azure PowerShell, AZURE Command Line Interface (CLI), Azure REST API eller Azure-portalen. Steg-för-steg-instruktioner för varje metod beskrivs i artikeln [Granskning med Resurshanteraren.](../azure-resource-manager/management/view-activity-logs.md)
* **Power BI:** Om du inte redan har ett [Power BI-konto](https:// .microsoft.com/pricing) kan du prova det gratis. Med hjälp av [innehållspaketet för Azure Audit Logs för Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)kan du analysera dina data med förkonfigurerade instrumentpaneler eller anpassa vyer efter dina behov.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visa och analysera hälsoavsökningen och händelseloggen

Anslut till ditt lagringskonto och hämta JSON-loggposterna för händelse- och hälsoavsökningsloggar. När du har hämtat JSON-filerna kan du konvertera dem till CSV och visa i Excel, Power BI eller något annat datavisualiseringsverktyg.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.

## <a name="stream-to-an-event-hub"></a>Strömma till en händelsehubb
När diagnostikinformation strömmas till en händelsehubb kan den användas för centraliserad logganalys i ett SIEM-verktyg från tredje part med Azure Monitor-integrering. Mer information finns i [Strömma Azure-övervakningsdata till en händelsehubb](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Skicka till Log Analytics
Resurser i Azure kan få sin diagnostikinformation skickad direkt till en Log Analytics-arbetsyta där komplexa frågor kan köras mot informationen för felsökning och analys.  Mer information finns [i Samla in Azure-resursloggar i Log Analytics-arbetsytan i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Nästa steg

[Förstå avsökningar av lastbalanserare](load-balancer-custom-probe-overview.md)

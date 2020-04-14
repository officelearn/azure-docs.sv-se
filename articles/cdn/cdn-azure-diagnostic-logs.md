---
title: Diagnostikloggar i Azure | Microsoft-dokument
description: Kunden kan aktivera logganalys för Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: 35d028a38e6ac19f270abcc8708a532b3749eb39
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254809"
---
# <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

Med Diagnostikloggar i Azure kan du visa kärnanalyser och spara dem på en eller flera destinationer, inklusive:

 - Azure Storage-konto
 - Azure Event Hubs
 - [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Den här funktionen är tillgänglig på CDN-slutpunkter för alla prisnivåer. 

Med Azure-diagnostikloggar kan du exportera grundläggande användningsmått från CDN-slutpunkten till en mängd olika källor så att du kan använda dem på ett anpassat sätt. Du kan till exempel göra följande typer av dataexport:

- Exportera data till blob-lagring, exportera till CSV och generera diagram i Excel.
- Exportera data till eventhubbar och korrelera med data från andra Azure-tjänster.
- Exportera data till Azure Monitor-loggar och visa data på din egen Log Analytics-arbetsyta

Följande diagram visar en typisk CDN-kärnanalysvy över data.

![portal - Diagnostikloggar](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Bild 1 - CDN-kärnanalysvy*

Mer information om diagnostikloggar finns i [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Aktivera loggning i Azure-portalen

Gör så här kan du logga med CDN-kärnanalys:

Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har aktiverat CDN för arbetsflödet [skapar du en Azure CDN-profil och slutpunkt](cdn-create-new-endpoint.md) innan du fortsätter.

1. Navigera till **CDN-profilen i Azure-portalen**.

2. Sök efter en CDN-profil i Azure-portalen eller välj en från instrumentpanelen. Välj sedan den CDN-slutpunkt som du vill aktivera diagnostikloggar för.

    ![portal - Diagnostikloggar](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Välj **Diagnostikloggar** i avsnittet ÖVERVAKNING.

   Sidan **Diagnostikloggar** visas.

    ![portal - Diagnostikloggar](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Aktivera loggning med Azure Storage

Så här använder du ett lagringskonto för att lagra loggarna:
    
1. För **Namn**anger du ett namn för diagnostiklogginställningarna.
 
2. Välj **Arkiv till ett lagringskonto**och välj sedan **CoreAnalytics**. 

2. För **kvarhållning (dagar)** väljer du antalet kvarhållningsdagar. En kvarhållning av noll dagar lagrar loggarna på obestämd tid. 

    ![portal - Diagnostikloggar](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Välj **lagringskonto**.

    Sidan **Välj ett lagringskonto** visas.

4. Välj ett lagringskonto i listrutan och välj sedan **OK**.

    ![portal - Diagnostikloggar](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. När du har gjort dina diagnostiklogginställningar väljer du **Spara**.

### <a name="logging-with-azure-monitor"></a>Logga med Azure Monitor

Så här använder du Azure Monitor för att lagra loggarna:

1. På sidan **Diagnostikloggar** väljer du **Skicka till Log Analytics**. 

    ![portal - Diagnostikloggar](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Välj **Konfigurera** för att konfigurera Azure Monitor-loggning. 

   Sidan **Logganalysarbetsytor** visas.

    >[!NOTE] 
    >OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.

    ![portal - Diagnostikloggar](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Välj **Skapa ny arbetsyta**.

    Arbetsytan **Log Analytics** visas.

    >[!NOTE] 
    >OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.

    ![portal - Diagnostikloggar](./media/cdn-diagnostics-log/07_Create-new.png)

4. För **Log Analytics-arbetsyta**anger du ett logganalysarbetsytenamn. Arbetsytenamnet Log Analytics måste vara unikt och innehåller endast bokstäver, siffror och bindestreck. utrymmen och understreck är inte tillåtna. 

5. För **Prenumeration**väljer du en befintlig prenumeration i listrutan. 

6. Skapa en ny resursgrupp för **resursgruppen**eller välj en befintlig resursgrupp.

7. För **Plats**väljer du en plats i listan.

8. Välj **Fäst på instrumentpanelen** om du vill spara loggkonfigurationen på instrumentpanelen. 

9. Välj **OK** för att slutföra konfigurationen.

10. När arbetsytan har skapats returneras du till sidan **Diagnostikloggar.** Bekräfta namnet på den nya Log Analytics-arbetsytan.

    ![portal - Diagnostikloggar](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Välj **CoreAnalytics**och välj sedan **Spara**.

12. Om du vill visa den nya log analytics-arbetsytan väljer du **Kärnanalys** från cdn-slutpunktssidan.

    ![portal - Diagnostikloggar](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Din Log Analytics-arbetsyta är nu klar att logga data. För att kunna använda dessa data måste du använda en [Azure Monitor-logglösning](#consuming-diagnostics-logs-from-a-log-analytics-workspace)som beskrivs senare i den här artikeln.

Mer information om fördröjningar av loggdata finns i [Logga dataförseningar](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>aktivera loggning med PowerShell

I följande exempel visas hur du aktiverar diagnostikloggar via Azure PowerShell-cmdlets.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Aktivera diagnostikloggar i ett lagringskonto

1. Logga in och välj en prenumeration:

    Anslut-AzAccount 

    Välj AzureSubscription -SubscriptionId 

2. Om du vill aktivera diagnostikloggar i ett lagringskonto anger du det här kommandot:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Om du vill aktivera diagnostikloggar på en Log Analytics-arbetsyta anger du det här kommandot:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Använda diagnostikloggar från Azure Storage
I det här avsnittet beskrivs schemat för CDN-kärnanalys, hur det är organiserat i ett Azure-lagringskonto och exempelkod för att hämta loggarna i en CSV-fil.

### <a name="using-microsoft-azure-storage-explorer"></a>Använda Microsoft Azure Storage Explorer
Innan du kan komma åt kärnanalysdata från ett Azure-lagringskonto behöver du först ett verktyg för att komma åt innehållet i ett lagringskonto. Det finns flera verktyg på marknaden, men det som vi rekommenderar är Microsoft Azure Storage Explorer. Information om hur du hämtar verktyget finns i [Azure Storage Explorer](https://storageexplorer.com/). När du har hämtat och installerat programvaran konfigurerar du den så att den använder samma Azure-lagringskonto som konfigurerades som en destination till CDN Diagnostics Logs.

1.  Öppna **Microsoft Azure Storage Explorer**
2.  Leta reda på lagringskontot
3.  Expandera noden **Blob Containers** under det här lagringskontot.
4.  Välj behållaren med namnet *insights-logs-coreanalytics*.
5.  Resultaten visas i den högra rutan, som börjar med den första nivån, som *resourceId=*. Fortsätt att välja varje nivå tills du hittar filen *PT1H.json*. En förklaring av sökvägen finns i [Blob-sökvägsformat](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Varje blob *PT1H.json-fil* representerar analysloggarna i en timme för en viss CDN-slutpunkt eller dess anpassade domän.
7.  Schemat för innehållet i den här JSON-filen beskrivs i avsnittsschemat för kärnanalysloggarna.


#### <a name="blob-path-format"></a>Blob-sökvägsformat

Kärnanalysloggar genereras varje timme och data samlas in och lagras inuti en enda Azure-blob som en JSON-nyttolast. Eftersom verktyget Lagringsutforskare tolkar /' som en katalogavgränsare och visar hierarkin, visas sökvägen till Azure-bloben som om det finns en hierarkisk struktur och representerar blobnamnet. Namnet på blobben följer följande namngivningskonvention:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Beskrivning av fält:**

|Värde|Beskrivning|
|-------|---------|
|Prenumerations-ID:t    |ID för Azure-prenumerationen i Guid-format.|
|Namn på resursgrupp |Namn på den resursgrupp som CDN-resurserna tillhör.|
|Profilnamn |Namn på CDN-profilen|
|Namn på slutpunkt |Namn på CDN-slutpunkten|
|Year|  Årets fyrsiffriga representation, till exempel 2017|
|Month| Tvåsiffrig representation av månadsnumret. 01=Januari ... 12=December|
|Day|   Tvåsiffrig representation av dagen i månaden|
|PT1H.json| Faktisk JSON-fil där analysdata lagras|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportera kärnanalysdata till en CSV-fil

För att göra det enkelt att komma åt kärnanalys, finns exempelkod för ett verktyg. Det här verktyget gör det möjligt att hämta JSON-filerna till ett platt kommaavgränsat filformat, som kan användas för att skapa diagram eller andra aggregeringar.

Så här kan du använda verktyget:

1.  Besök GitHub-länken:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Ladda ner koden.
3.  Följ instruktionerna för att kompilera och konfigurera.
4.  Kör verktyget.
5.  Den resulterande CSV-filen visar analysdata i en enkel platt hierarki.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Använda diagnostikloggar från en Log Analytics-arbetsyta
Azure Monitor är en Azure-tjänst som övervakar dina molnmiljöer och lokala miljöer för att behålla deras tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor. 

Om du vill använda Azure Monitor måste du [aktivera loggning](#enable-logging-with-azure-storage) till Azure Log Analytics-arbetsytan, som beskrivs tidigare i den här artikeln.

### <a name="using-the-log-analytics-workspace"></a>Använda arbetsytan Logganalys

 I följande diagram visas arkitekturen för in- och utdata i databasen:

![Log Analytics-arbetsyta](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Bild 3 - Databas för logganalys*

Du kan visa data på flera olika sätt med hjälp av Hanteringslösningar. Du kan hämta hanteringslösningar från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Du kan installera övervakningslösningar från Azure Marketplace genom att välja länken **Hämta den nu** längst ned i varje lösning.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Lägga till en Azure Monitor CDN-övervakningslösning

Så här lägger du till en övervakningslösning för Azure Monitor:

1.   Logga in på Azure-portalen med din Azure-prenumeration och gå till instrumentpanelen.
    ![Azure-instrumentpanel](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Välj **Övervakning + hantering**under **Marketplace**på den **nya** sidan.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. På sidan **Övervakning + hantering** väljer du Visa **alla**.

    ![Visa alla](./media/cdn-diagnostics-log/15_See-all.png)

4. Sök efter CDN i sökrutan.

    ![Visa alla](./media/cdn-diagnostics-log/16_Search-for.png)

5. Välj **Azure CDN Core Analytics**. 

    ![Visa alla](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. När du har valt **Skapa**uppmanas du att skapa en ny Log Analytics-arbetsyta eller använda en befintlig. 

    ![Visa alla](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Markera arbetsytan som du skapade tidigare. Du måste sedan lägga till ett automatiseringskonto.

    ![Visa alla](./media/cdn-diagnostics-log/19_Add-automation.png)

8. På följande skärm visas det formulär för automatiseringskonto som du måste fylla i. 

    ![Visa alla](./media/cdn-diagnostics-log/20_Automation.png)

9. När du har skapat automatiseringskontot är du redo att lägga till din lösning. Välj knappen **Skapa**.

    ![Visa alla](./media/cdn-diagnostics-log/21_Ready.png)

10. Din lösning har nu lagts till på din arbetsyta. Gå tillbaka till instrumentpanelen för Azure-portalen.

    ![Visa alla](./media/cdn-diagnostics-log/22_Dashboard.png)

    Välj arbetsytan Log Analytics som du skapade för att gå till arbetsytan. 

11. Välj **panelen OMS Portal** för att se din nya lösning.

    ![Visa alla](./media/cdn-diagnostics-log/23_workspace.png)

12. Portalen ska nu se ut så här:

    ![Visa alla](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Välj en av panelerna om du vill visa flera vyer i dina data.

    ![Visa alla](./media/cdn-diagnostics-log/25_Interior-view.png)

    Du kan rulla åt vänster eller höger om du vill visa ytterligare paneler som representerar enskilda vyer i data. 

    Välj en av panelerna om du vill se mer information om dina data.

     ![Visa alla](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Erbjudanden och prisnivåer

Du kan se erbjudanden och prisnivåer för hanteringslösningar [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Anpassa vyer

Du kan anpassa vyn till dina data med hjälp av **Visa designer**. Om du vill börja designa går du till arbetsytan Log Analytics och väljer panelen **Visa designer.**

![Vydesigner](./media/cdn-diagnostics-log/27_Designer.png)

Dra och släpp typerna av diagram och fyll i den datainformation som du vill analysera.

![Vydesigner](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Logga dataförseningar

I följande tabell visas loggdatafördröjningar för **Azure CDN Standard från Microsoft,** **Azure CDN Standard från Akamai**och **Azure CDN Standard/Premium från Verizon**.

Microsoft-loggdata försenas | Verizon loggdata förseningar | Akamai loggar data förseningar
--- | --- | ---
Försenad med 1 timme. | Fördröjd med 1 timme och kan ta upp till 2 timmar att börja visas efter slutpunktsspridningen. | Försenad med 24 timmar; Om det skapades för mer än 24 timmar sedan tar det upp till 2 timmar att börja visas. Om det nyligen har skapats kan det ta upp till 25 timmar innan loggarna börjar visas.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnostikloggtyper för CDN-kärnanalys

Microsoft erbjuder för närvarande endast grundläggande analysloggar, som innehåller mått som visar HTTP-svarsstatistik och utgående statistik som kan ses från CDN-pop/kanter.

### <a name="core-analytics-metrics-details"></a>Information om kärnanalysmått
I följande tabell visas en lista över mått som är tillgängliga i kärnanalysloggarna för **Azure CDN Standard från Microsoft,** **Azure CDN Standard från Akamai**och **Azure CDN Standard/Premium från Verizon**. Alla mått är inte tillgängliga från alla leverantörer, även om sådana skillnader är minimala. Tabellen visar också om ett givet mått är tillgängligt från en provider. Måtten är endast tillgängliga för cdn-slutpunkter som har trafik på dem.


|Mått                     | Beskrivning | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Totalt antal träffar för begäran under den här perioden. | Ja | Ja |Ja |
| RequestCounthttpStatus2xx | Antal alla begäranden som resulterade i en 2xx HTTP-kod (till exempel 200, 202). | Ja | Ja |Ja |
| RequestCounthttpStatus3xx | Antal alla begäranden som resulterade i en 3xx HTTP-kod (till exempel 300, 302). | Ja | Ja |Ja |
| RequestCounthttpStatus4xx | Antal alla begäranden som resulterade i en 4xx HTTP-kod (till exempel 400, 404). | Ja | Ja |Ja |
| RequestCounthttpStatus5xx | Antal alla begäranden som resulterade i en 5xx HTTP-kod (till exempel 500, 504). | Ja | Ja |Ja |
| RequestCountHttpStatusOthers | Antal av alla andra HTTP-koder (utanför 2xx-5xx). | Ja | Ja |Ja |
| RequestCounthttpStatus200 | Antal alla begäranden som resulterade i ett 200 HTTP-kodsvar. | Ja | Inga  |Ja |
| RequestCounthttpStatus206 | Antal alla begäranden som resulterade i ett 206 HTTP-kodsvar. | Ja | Inga  |Ja |
| RequestCounthttpStatus302 | Antal alla begäranden som resulterade i ett 302 HTTP-kodsvar. | Ja | Inga  |Ja |
| RequestCounthttpStatus304 | Antal alla begäranden som resulterade i ett 304 HTTP-kodsvar. | Ja | Inga  |Ja |
| RequestCounthttpStatus404 | Antal alla begäranden som resulterade i ett 404 HTTP-kodsvar. | Ja | Inga  |Ja |
| RequestCountCacheHit | Räkna alla begäranden som resulterade i en cacheträff. Tillgången delgavs direkt från POP till kunden. | Ja | Ja | Inga  |
| BegäranCountCacheMiss | Räkna alla begäranden som resulterade i en cachemiss. En cachemiss innebär att tillgången inte hittades på POP närmast klienten och hämtades därför från Origin. | Ja | Ja | Inga |
| BegäranCountCacheNoCache | Antal alla begäranden till en tillgång som förhindras från att cachelagras på grund av en användarkonfiguration på kanten. | Ja | Ja | Inga |
| RequestCountCacheUncacheable | Antal alla begäranden till tillgångar som förhindras från att cachelagras av tillgångens cachekontroll- och utgångshuvuden, vilket indikerar att den inte bör cachelagras på en POP eller av HTTP-klienten. | Ja | Ja | Inga |
| RequestCountCacheOthers | Antal alla begäranden med cachestatus som inte omfattas av ovan. | Inga | Ja | Inga  |
| EgressTotal (på annat sätt) | Överföring av utgående data i GB | Ja |Ja |Ja |
| EgressHttpStatus2xx | Överföring av utgående data* för svar med 2xx HTTP-statuskoder i GB. | Ja | Ja | Inga  |
| EgressHttpStatus3xx | Utgående dataöverföring för svar med 3xx HTTP-statuskoder i GB. | Ja | Ja | Inga  |
| EgressHttpStatus4xx | Utgående dataöverföring för svar med 4xx HTTP-statuskoder i GB. | Ja | Ja | Inga  |
| EgressHttpStatus5xx | Utgående dataöverföring för svar med 5xx HTTP-statuskoder i GB. | Ja | Ja | Inga |
| EgressHttpStatusOthers | Utgående dataöverföring för svar med andra HTTP-statuskoder i GB. | Ja | Ja | Inga  |
| EgressCacheHit (EgressCache) | Utgående dataöverföring för svar som levererades direkt från CDN-cachen på CDN-POP/Kanter. | Ja | Ja | Inga |
| EgressCacheMiss. | Utgående dataöverföring för svar som inte hittades på närmaste POP-server och hämtas från ursprungsservern. | Ja | Ja | Inga |
| EgressCacheNoCache | Utgående dataöverföring för tillgångar som förhindras från att cachelagras på grund av en användarkonfiguration på kanten. | Ja | Ja | Inga |
| EgressCacheOacheable | Utgående dataöverföring för tillgångar som förhindras från att cachelagras av tillgångens cachekontroll- och/eller utgångshuvuden. Anger att den inte ska cachelagras på en POP eller av HTTP-klienten. | Ja | Ja | Inga |
| EgressCacheOthers | Utgående dataöverföringar för andra cachescenarier. | Inga | Ja | Inga |

*Utgående dataöverföring avser trafik som levereras från CDN POP-servrar till klienten.


### <a name="schema-of-the-core-analytics-logs"></a>Schema för kärnanalysloggarna 

Alla loggar lagras i JSON-format och varje post har strängfält enligt följande schema:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Där *tiden* representerar starttiden för den timgräns för vilken statistiken rapporteras. När ett mått inte stöds av en CDN-provider, i stället för ett dubbel- eller heltalsvärde, finns det ett null-värde. Det här null-värdet anger frånvaron av ett mått och skiljer sig från värdet 0. Det finns en uppsättning av dessa mått per domän som konfigurerats på slutpunkten.

Exempelegenskaper:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure-diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Kärnanalys via Azure CDN-tilläggsportal](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [REST-API för Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)








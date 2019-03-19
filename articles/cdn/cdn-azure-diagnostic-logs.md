---
title: Azure diagnostikloggar | Microsoft Docs
description: Kunden kan aktivera logganalys för Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: a5fab3e2bf9908fa35cf5f5485df3116b7718d8c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881137"
---
# <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

Du kan använda Azure diagnostikloggar för att visa grundläggande analys och spara dem i en eller flera mål, inklusive:

 - Azure-lagringskonto
 - Azure Event Hubs
 - [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Den här funktionen är tillgänglig på CDN-slutpunkter för alla prisnivåer. 

Azure-diagnostikloggar kan du exportera grundläggande användningsstatistik från CDN-slutpunkten till en mängd olika källor, så att du kan använda dem på ett anpassat sätt. Du kan exempelvis göra följande typer av export av data:

- Exportera data till blob-lagring, exportera till CSV och skapa diagram i Excel.
- Exportera data till Event Hubs och jämföra med data från andra Azure-tjänster.
- Exportera data till Azure Monitor-loggar och visa data i din egen Log Analytics-arbetsyta

I följande diagram visas en typisk CDN core analytics-vy över data.

![Portal - diagnostikloggar](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Bild 1 – CDN core analytics-vy*

Läs mer om diagnostikloggar [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Aktivera loggning med Azure portal

Följ dessa steg aktivera loggning med CDN core analytics:

Logga in på [Azure Portal](https://portal.azure.com). Om du inte redan har aktiverat CDN för ditt arbetsflöde [skapa en Azure CDN-profil och slutpunkt](cdn-create-new-endpoint.md) innan du fortsätter.

1. I Azure-portalen går du till **CDN-profil**.

2. Sök efter en CDN-profil i Azure-portalen, eller välj en från instrumentpanelen. Välj CDN-slutpunkten som du vill aktivera diagnostikloggar.

    ![Portal - diagnostikloggar](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Välj **diagnostikloggar** i avsnittet övervakning.

   Den **diagnostikloggar** visas.

    ![Portal - diagnostikloggar](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Aktivera loggning med Azure Storage

Följ dessa steg om du vill använda ett lagringskonto för att spara loggarna:
    
1. För **namn**, ange ett namn för din diagnostiklogginställningar.
 
2. Välj **arkivet till ett lagringskonto**och välj sedan **CoreAnalytics**. 

2. För **Kvarhållning (dagar)**, Välj antalet kvarhållningsdagar. En kvarhållning av noll dagar lagras loggarna på obestämd tid. 

    ![Portal - diagnostikloggar](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Välj **lagringskonto**.

    Den **Välj ett lagringskonto** visas.

4. Välj ett lagringskonto från den nedrullningsbara listan och välj sedan **OK**.

    ![Portal - diagnostikloggar](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. När du är nöjd med din diagnostiklogginställningar väljer **spara**.

### <a name="logging-with-azure-monitor"></a>Loggning med Azure Monitor

Följ dessa steg för att använda Azure Monitor för att lagra loggarna måste:

1. Från den **diagnostikloggar** väljer **skicka till Log Analytics**. 

    ![Portal - diagnostikloggar](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Välj **konfigurera** konfigurera Azure Monitor-loggning. 

   Den **Log Analytics-arbetsytor** visas.

    >[!NOTE] 
    >OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.

    ![Portal - diagnostikloggar](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Välj **Skapa ny arbetsyta**.

    Den **Log Analytics-arbetsyta** visas.

    >[!NOTE] 
    >OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.

    ![Portal - diagnostikloggar](./media/cdn-diagnostics-log/07_Create-new.png)

4. För **Log Analytics-arbetsyta**, ange ett namn för Log Analytics-arbetsytan. Namnet på Log Analytics-arbetsytan måste vara unika och innehålla endast bokstäver, siffror och bindestreck; blanksteg och understreck tillåts inte. 

5. För **prenumeration**, Välj en befintlig prenumeration från den nedrullningsbara listan. 

6. För **resursgrupp**, skapa en ny resursgrupp eller välj en befintlig.

7. För **plats**, Välj en plats i listan.

8. Välj **fäst på instrumentpanelen** om du vill spara loggningskonfigurationen på instrumentpanelen. 

9. Välj **OK** att slutföra konfigurationen.

10. När arbetsytan har skapats kan du kommer tillbaka till den **diagnostikloggar** sidan. Bekräfta namnet på den nya Log Analytics-arbetsytan.

    ![Portal - diagnostikloggar](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Välj **CoreAnalytics**och välj sedan **spara**.

12. Om du vill visa den nya Log Analytics-arbetsytan, Välj **grundläggande analysfunktioner** från din sida för CDN-slutpunkten.

    ![Portal - diagnostikloggar](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Log Analytics-arbetsytan är nu redo att logga data. För att kunna använda data, måste du använda en [Azure Monitor loggar lösningen](#consuming-diagnostics-logs-from-a-log-analytics-workspace), tas upp senare i den här artikeln.

Läs mer om log data fördröjningar [logga data fördröjningar](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>aktivera loggning med PowerShell

I följande exempel visas hur du aktiverar diagnostikloggar via Azure PowerShell-Cmdlets.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Aktivera diagnostiska loggar i ett lagringskonto

1. Logga in och välj en prenumeration:

    Connect-AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. Om du vill aktivera diagnostikloggar i ett lagringskonto, anger du det här kommandot:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Om du vill aktivera diagnostikloggar i en Log Analytics-arbetsyta, anger du det här kommandot:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Använda diagnostikloggar från Azure Storage
Det här avsnittet beskriver schemat för CDN core analytics, hur de organiseras i ett Azure storage-konto och visar exempelkod för att hämta loggarna i en CSV-fil.

### <a name="using-microsoft-azure-storage-explorer"></a>Med Microsoft Azure Storage Explorer
Innan du kan komma åt core analytics-data från ett Azure storage-konto, måste du först ett verktyg för att få åtkomst till innehållet i ett lagringskonto. Det finns flera verktyg på marknaden, är det som vi rekommenderar Microsoft Azure Storage Explorer. Om du vill hämta verktyget, se [Azure Storage Explorer](https://storageexplorer.com/). När du laddar ned och installera programvaran kan konfigureras för att använda samma Azure storage-kontot som har konfigurerats som ett mål för CDN-diagnostikloggar.

1.  Öppna **Microsoft Azure Lagringsutforskaren**
2.  Leta upp lagringskontot
3.  Expandera den **Blobbehållare** nod under det här lagringskontot.
4.  Välj behållaren med namnet *insights-logs-coreanalytics*.
5.  Resultat visar upp i den högra rutan, från och med den första nivån som *resourceId =*. Fortsätta att välja varje nivå tills du hittar filen *PT1H.json*. En förklaring av sökvägen finns i [Blob Sökvägsformatet](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Varje blob *PT1H.json* filen representerar analysloggarna under en timme för en specifik CDN-slutpunkt eller den anpassa domänen.
7.  Schemat för innehållet i det här JSON-fil beskrivs i avsnittet schemat för core analytics-loggarna.


#### <a name="blob-path-format"></a>Formatet för BLOB-sökväg

Core analytics loggarna genereras varje timme och data som samlas in och lagras inom en enda Azure-blob som en JSON-nyttolast. Eftersom verktyget Storage explorer tolkar '/' som en katalogavgränsare och visar hierarkin, sökvägen till Azure-blobben visas som om det är en hierarkisk struktur och representerar blobnamnet. Namnet på blobben följer följande namngivningskonvention:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Beskrivning av fält:**

|Värde|Beskrivning|
|-------|---------|
|Prenumerations-ID:t    |ID för Azure-prenumerationen i Guid-format.|
|Resursgruppsnamn |Namnet på resursgruppen som tillhör CDN-resurser.|
|Profilnamn |Namnet på den CDN-profilen|
|Namnet på slutpunkten |Namnet på CDN-slutpunkten|
|År|  Fyrsiffrigt representation av året, till exempel 2017|
|Månad| Tvåsiffrig representation av månadsnummer. 01 = januari... 12=December|
|Dag|   Tvåsiffrig representation av dagen i månaden|
|PT1H.json| Faktiska JSON-fil som analytics-data ska lagras|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportera core analytics-data till en CSV-fil

Om du vill göra det enkelt att komma åt kärnanalys, tillhandahålls exempelkod för ett verktyg som. Det här verktyget kan ladda ned JSON-filerna till en fast CSV-format, vilket kan användas för att skapa diagram eller andra aggregeringar.

Här är hur du kan använda verktyget:

1.  Gå till GitHub-länken: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Ladda ned koden.
3.  Följ anvisningarna för att kompilera och konfigurera.
4.  Kör verktyget.
5.  Den resulterande CSV-filen visar analytics-data i en enkel fast hierarki.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Använda diagnostikloggar från en Log Analytics-arbetsyta
Azure Monitor är en Azure-tjänst som övervakar dina molnbaserade och lokala miljöer för att bibehålla tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor. 

Om du vill använda Azure Monitor, måste du [aktivera loggning](#enable-logging-with-azure-storage) till Azure Log Analytics-arbetsyta som beskrivs tidigare i den här artikeln.

### <a name="using-the-log-analytics-workspace"></a>Använda Log Analytics-arbetsyta

 Följande diagram visar arkitekturen för indata och utdata för databasen:

![Log Analytics-arbetsyta](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Bild 3 – Log Analytics-lagringsplatsen*

Du kan visa data i en mängd olika sätt med hjälp av lösningar för hantering. Du kan hämta hanteringslösningar från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Du kan installera övervakningslösningar från Azure marketplace genom att välja den **Hämta nu** länken längst ned i varje lösning.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Lägg till en Azure CDN för övervakaren övervakningslösning

Följ dessa steg för att lägga till en Azure Monitor övervakningslösning:

1.   Logga in på Azure portal med din Azure-prenumeration och gå till instrumentpanelen.
    ![Azure-instrumentpanelen](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. I den **New** sidan under **Marketplace**väljer **övervakning + hantering**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. I den **övervakning + hantering** väljer **se alla**.

    ![Se alla](./media/cdn-diagnostics-log/15_See-all.png)

4. Sök efter CDN i sökrutan.

    ![Se alla](./media/cdn-diagnostics-log/16_Search-for.png)

5. Välj **Azure CDN Core Analytics**. 

    ![Se alla](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. När du har valt **skapa**, uppmanas du att skapa en ny Log Analytics-arbetsyta eller välj en befintlig. 

    ![Se alla](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Välj den arbetsyta som du skapade innan. Du måste sedan lägga till ett automation-konto.

    ![Se alla](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Följande skärmbild visar formuläret automation-konto måste du fylla ut. 

    ![Se alla](./media/cdn-diagnostics-log/20_Automation.png)

9. När du har skapat ett automation-kontot, är du redo att lägga till din lösning. Välj knappen **Skapa**.

    ![Se alla](./media/cdn-diagnostics-log/21_Ready.png)

10. Lösningen har nu lagts till din arbetsyta. Gå tillbaka till instrumentpanelen i Azure portal.

    ![Se alla](./media/cdn-diagnostics-log/22_Dashboard.png)

    Välj Log Analytics-arbetsytan som du skapade för att gå till din arbetsyta. 

11. Välj den **OMS-portalen** ikonen för att visa den nya lösningen.

    ![Se alla](./media/cdn-diagnostics-log/23_workspace.png)

12. Din portal bör nu se ut som följande skärmbild:

    ![Se alla](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Välj en av panelerna för att se flera vyer i dina data.

    ![Se alla](./media/cdn-diagnostics-log/25_Interior-view.png)

    Du kan rulla åt vänster eller höger för att se ytterligare paneler som representerar enskilda vyer i data. 

    Välj någon av panelerna för att se mer information om dina data.

     ![Se alla](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Erbjudanden och prisnivåer

Du kan se erbjudanden och prisnivåer för hanteringslösningar [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Anpassa vyer

Du kan anpassa vyn till dina data med hjälp av den **Vydesigner**. Om du vill börja designa, gå till Log Analytics-arbetsytan och välj den **Vydesigner** panelen.

![Vydesigner](./media/cdn-diagnostics-log/27_Designer.png)

Dra och släpp typerna av diagram och Fyll i data information om du vill analysera.

![Vydesigner](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Logga data fördröjningar

I följande tabell visas log data förseningar för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**, och **Azure CDN Standard/Premium från Verizon**.

Microsoft log data fördröjningar | Verizon log data fördröjningar | Akamai log data fördröjningar
--- | --- | ---
Försenade på grund av 1 timme. | Försenade på grund av 1 timme och kan ta upp till 2 timmar att börja visas efter endpoint spridning slutförande. | Minska frekvensen genom 24 timmar. Om den har skapats sedan mer än 24 timmar, tar det upp till 2 timmar att börja visas. Om den har skapats nyligen kan ta det upp till 25 timmar innan loggarna för att starta visas.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnostiklogg typer för CDN core analytics

Microsoft erbjuder för närvarande core analytics loggar, som innehåller mått som visar statistik för HTTP-svar och utgående trafik som visas från CDN POP/kanter.

### <a name="core-analytics-metrics-details"></a>Information om Core analytics-mått
I följande tabell visas en lista över mått som är tillgängliga i grundläggande analysloggar för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**, och **Azure CDN Standard/Premium från Verizon**. Inte alla mått är tillgängliga från alla providers, även om skillnaderna är minimal. Tabellen visar även om ett visst mått är tillgängliga från en leverantör. Mått är tillgängliga för endast de CDN-slutpunkter som har trafik på dem.


|Mått                     | Beskrivning | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Totalt antal träffar för begäran under denna period. | Ja | Ja |Ja |
| RequestCountHttpStatus2xx | Uppräkning av alla begäranden som resulterade i ett 2xx HTTP-kod (till exempel 200, 202). | Ja | Ja |Ja |
| RequestCountHttpStatus3xx | Uppräkning av alla begäranden som resulterade i en 3xx HTTP-kod (till exempel 300, 302). | Ja | Ja |Ja |
| RequestCountHttpStatus4xx | Uppräkning av alla begäranden som resulterade i en 4xx HTTP-kod (till exempel 400, 404). | Ja | Ja |Ja |
| RequestCountHttpStatus5xx | Uppräkning av alla begäranden som resulterade i 5xx HTTP-koden (exempelvis 500, 504). | Ja | Ja |Ja |
| RequestCountHttpStatusOthers | Uppräkning av alla andra HTTP-koder (utanför 2xx 5xx). | Ja | Ja |Ja |
| RequestCountHttpStatus200 | Uppräkning av alla begäranden som resulterade i en 200 HTTP-kod-svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus206 | Uppräkning av alla begäranden som resulterade i ett 206 HTTP-kod-svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus302 | Uppräkning av alla begäranden som resulterade i en 302 kod HTTP-svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus304 | Uppräkning av alla begäranden som resulterade i en 304 kod HTTP-svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus404 | Uppräkning av alla begäranden som resulterade i ett 404 kod HTTP-svar. | Ja | Nej  |Ja |
| RequestCountCacheHit | Uppräkning av alla begäranden som resulterade i en Cache träffar. Tillgången har behandlats direkt från POP till klienten. | Ja | Ja | Nej  |
| RequestCountCacheMiss | Uppräkning av alla begäranden som resulterade i en cachemiss. En cachemiss innebär tillgången hittades inte på den POP-plats som är närmast klienten, och därför har hämtats från ursprunget. | Ja | Ja | Nej |
| RequestCountCacheNoCache | Uppräkning av alla begäranden till en tillgång som hindras från att cachelagras på grund av en Användarkonfiguration på gränsen. | Ja | Ja | Nej |
| RequestCountCacheUncacheable | Antal för alla förfrågningar till tillgångar som hindras från att cachelagras av tillgångens Cache-Control och Expires-huvuden som indikerar att det inte ska cachelagras på en POP eller av HTTP-klienten. | Ja | Ja | Nej |
| RequestCountCacheOthers | Uppräkning av alla begäranden med Cachestatus som inte omfattas av ovan. | Nej | Ja | Nej  |
| EgressTotal | Utgående dataöverföring i GB | Ja |Ja |Ja |
| EgressHttpStatus2xx | Utgående dataöverföring * för responser 2xx HTTP-statuskoder i GB. | Ja | Ja | Nej  |
| EgressHttpStatus3xx | Utgående dataöverföring för responser 3xx HTTP-statuskoder i GB. | Ja | Ja | Nej  |
| EgressHttpStatus4xx | Utgående dataöverföring för responser 4xx HTTP-statuskoder i GB. | Ja | Ja | Nej  |
| EgressHttpStatus5xx | Utgående dataöverföring för responser 5xx HTTP-statuskoder i GB. | Ja | Ja | Nej |
| EgressHttpStatusOthers | Utgående dataöverföring för svar med andra HTTP-statuskoder i GB. | Ja | Ja | Nej  |
| EgressCacheHit | Utgående dataöverföring för svar som har levererats direkt från CDN-cachen på CDN POP/kanter. | Ja | Ja | Nej |
| EgressCacheMiss. | Utgående dataöverföring för svar som inte hittades på den närmaste POP-servern, och hämtas från den ursprungliga servern. | Ja | Ja | Nej |
| EgressCacheNoCache | Utgående dataöverföring för tillgångar som hindras från att cachelagras på grund av en Användarkonfiguration på gränsen. | Ja | Ja | Nej |
| EgressCacheUncacheable | Utgående dataöverföring för tillgångar som hindras från att cachelagras av tillgångens Cache-Control eller Expires-huvuden. Anger att det inte ska cachelagras på en POP eller av HTTP-klienten. | Ja | Ja | Nej |
| EgressCacheOthers | Utgående dataöverföringar som för andra cache-scenarier. | Nej | Ja | Nej |

* Utgående dataöverföring avser trafik som levereras från CDN POP-servrar till klienten.


### <a name="schema-of-the-core-analytics-logs"></a>Schemat för core analytics-loggar 

Alla loggar lagras i JSON-format och varje post innehåller strängfält enligt följande schema:

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

Där *tid* representerar starttiden för timme gränsen som statistik rapporteras. När ett mått inte stöds av en CDN-leverantör i stället för ett double-värde eller integer-värde är det ett null-värde. Den här nullvärde anger om ett mått och skiljer sig från värdet 0. Det finns en uppsättning med de här måtten per domän konfigurerad på slutpunkten.

Exempel-egenskaper:

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

* [Azure diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Grundläggande analys via Azure CDN kompletterande portalen](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)








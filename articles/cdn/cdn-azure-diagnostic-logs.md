---
title: Azure diagnostikloggar | Microsoft Docs
description: Kunden kan aktivera logganalys för Azure CDN.
services: cdn
documentationcenter: ''
author: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: v-deasim
ms.openlocfilehash: c367cffa8f0453a0f7e230571d861d039122c291
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

Du kan använda Azure diagnostikloggar för att visa core analytics och spara dem i en eller flera mål, inklusive:

 - Azure-lagringskonto
 - Azure Event Hubs
 - [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Den här funktionen är tillgänglig för alla CDN-slutpunkter som hör till Verizon (Standard och Premium) och Akamai (Standard) CDN profiler. 

Azure diagnostics loggar kan du exportera grundläggande användningsstatistik från CDN-slutpunkten till en mängd olika datakällor så att du kan använda dem i ett anpassat sätt. Du kan till exempel göra följande typer av export av data:

- Exportera data till blob storage, exportera till CSV och skapa diagram i Excel.
- Exportera data till Händelsehubbar och samordna med data från andra Azure-tjänster.
- Exportera data för att logga analytics och visa data i din egen logganalys-arbetsyta

Följande bild visar en typisk CDN core analytics data.

![Portal - diagnostik loggar](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Bild 1 - CDN core analytics visa*

Läs mer om diagnostikloggar [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-azure-portal"></a>Aktivera loggning med Azure-portalen

Följ dessa steg aktivera loggning med CDN core analytics:

Logga in på [Azure-portalen](http://portal.azure.com). Om du inte redan har CDN som aktiverats för ditt arbetsflöde [aktivera Azure CDN](cdn-create-new-endpoint.md) innan du fortsätter.

1. I portalen, går du till **CDN-profilen**.
2. Välj en CDN-profil och sedan CDN-slutpunkt som du vill aktivera **diagnostik loggar**.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Välj **diagnostik loggar** i den **övervakning** avsnitt.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Aktivera loggning med Azure Storage
    
1. Om du vill använda Azure storage för att lagra loggfilerna, Välj **arkivet till ett lagringskonto**väljer **CoreAnalytics**, och välj sedan antalet dagar kvarhållning under **bevarande (dagar)**. En kvarhållning av noll dagar lagrar loggarna på obestämd tid. 
2. Ange ett namn för inställningen och klicka sedan på **lagringskonto**. När du har valt ett lagringskonto, klickar du på **spara**.

![Portal - diagnostik loggar](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*Bild 2 - loggning med Azure Storage*

### <a name="logging-with-log-analytics"></a>Loggning med logganalys

Följ dessa steg om du vill använda logganalys för att lagra loggfilerna:

1. Från den **diagnostik loggar** bladet väljer **skicka till logganalys**. 

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Klicka på **konfigurera** konfigurera log analytics loggning. Du kan välja en tidigare arbetsyta eller skapa en ny i dialogrutan OMS arbetsytor.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Klicka på **Skapa ny arbetsyta**.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/07_Create-new.png)

4. Ange ett nytt namn för logganalys-arbetsytan. Ett namn för logganalys-arbetsytan måste vara unika och innehålla bara bokstäver, siffror och bindestreck; blanksteg och understreck tillåts inte. 
5. Välj sedan en befintlig prenumeration, resursgrupp (ny eller befintlig), plats och prisnivå. Du har också möjlighet att fästa den här konfigurationen på instrumentpanelen. Klicka på **OK** för att slutföra konfigurationen.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  När ditt arbetsområde har skapats kan tillbaka du till ditt windows-diagnostikloggar. Kontrollera namnet på din nya log analytics-arbetsyta.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    När du har konfigurerat log analytics-konfiguration, kontrollera att du har valt **CoreAnalytics**.

6. Klicka på **Spara**.

7. Om du vill visa nya logganalys-arbetsytan, gå till instrumentpanelen i Azure portal och klicka på namnet på log analytics-arbetsyta. Klicka på panelen OMS-portalen om du vill visa logganalys-arbetsytan. 

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Logganalys-arbetsytan är nu redo att logga data. För att kunna använda data måste du använda en [Log Analytics lösningen](#consuming-diagnostics-logs-from-a-log-analytics-workspace), tas upp senare i den här artikeln.

Läs mer om logga data fördröjningar [logga data fördröjningar](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Aktivera loggning med PowerShell

I följande exempel visas hur du aktiverar diagnostikloggar via Azure PowerShell-Cmdlets.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Aktivera diagnostik loggar i ett Lagringskonto

Först logga in och välj en prenumeration:

    Connect-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Att aktivera diagnostikloggar i ett Lagringskonto, Använd följande kommando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Använd följande kommando för att aktivera diagnostik-loggar i logganalys-arbetsytan:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Förbrukar diagnostik loggar från Azure Storage
Det här avsnittet beskrivs schemat för CDN core analytics, hur den ordnas i ett Azure storage-konto och exempelkod för att hämta loggarna i en CSV-fil.

### <a name="using-microsoft-azure-storage-explorer"></a>Med Microsoft Azure Lagringsutforskaren
Innan du kan komma åt core analysdata från Azure Storage-konto, måste du först ett verktyg för att få åtkomst till innehållet i ett lagringskonto. Det finns flera verktyg på marknaden, är det som vi rekommenderar Microsoft Azure Lagringsutforskaren. Om du vill hämta verktyget, se [Azure Lagringsutforskaren](http://storageexplorer.com/). Konfigurera den att använda samma Azure storage-konto som har konfigurerats som ett mål i CDN diagnostik loggar efter hämta och installera programvaran.

1.  Öppna **Lagringsutforskaren för Microsoft Azure**
2.  Hitta storage-konto
3.  Gå till den **”Blob-behållare”** nod under lagringen konto och expandera noden
4.  Markera den behållare som heter **”insikter-loggar-coreanalytics”** och dubbelklicka på det.
5.  Resultatet visar upp i den högra rutan som börjar med den första nivån, som ser ut så **”resourceId =”**. Klickar du på ända tills du ser filen **PT1H.json**. Se följande anmärkning förklaring av sökvägen.
6.  Varje blobb **PT1H.json** representerar analytics-loggarna för en timme för en specifik CDN-slutpunkten eller den anpassa domänen.
7.  Schemat för innehållet i JSON-fil är beskrivs i avsnittet schemat core analytics loggar


> [!NOTE]
> **Formatet för BLOB-sökväg**
> 
> Core analytics loggar skapas varje timme och data som samlas in och lagras inuti en enda Azure blob som en JSON-nyttolast. Eftersom verktyget lagring explorer tolkar '/' som en katalogavgränsare och visar hierarkin, sökvägen till Azure-blobben visas som om det är en hierarkisk struktur och representerar blobbnamnet på. Namnet på blobben följer följande namngivningskonvention: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Beskrivning av fält:**

|värde|description|
|-------|---------|
|Prenumerations-ID:t    |ID för Azure-prenumeration i Guid-format.|
|Resurs |Gruppnamn namnet på resursgruppen som CDN resurser tillhör.|
|Profilnamn |Namnet på CDN-profilen|
|Namnet på slutpunkten |Namnet på CDN-slutpunkten|
|År|  4-siffrig representation år, till exempel 2017|
|Månad| 2-siffrig representation av månadsnummer. 01 = januari... 12 = December|
|Dag|   2-siffrig representation av dagen i månaden|
|PT1H.json| Faktiska JSON-fil där analytics-data lagras|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportera core analytics data till en CSV-fil

Om du vill göra det enklare att komma åt core analytics, tillhandahålls exempelkod för ett verktyg som. Det här verktyget kan hämta JSON-filer till en platt CSV-format som kan användas för att skapa diagram eller andra aggregeringar.

Här är hur du kan använda verktyget:

1.  Gå till github-länk: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  Hämta koden.
3.  Följ instruktionerna för att kompilera och konfigurera.
4.  Kör verktyget.
5.  Den resulterande CSV-filen innehåller analytics-data i en enkel platt hierarki.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Förbrukar diagnostik loggar från logganalys-arbetsytan
Log Analytics är en tjänst i Azure som hjälper dig att övervaka molnet och lokala miljöer för att bibehålla tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor. 

Om du vill använda logganalys måste du [aktivera loggning](#enable-logging-with-azure-storage) till Azure logganalys-arbetsyta som beskrivs tidigare i den här artikeln.

### <a name="using-the-log-analytics-workspace"></a>Med hjälp av logganalys-arbetsytan

 Följande diagram visar arkitekturen för indata och utdata för databasen:

![Log Analytics-arbetsyta](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Bild 3 - Log Analytics-databasen*

Du kan visa data i en mängd olika sätt med hjälp av lösningar för hantering. Du kan hämta hanteringslösningar från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Du kan installera hanteringslösningar från Azure marketplace genom att klicka på den **blir det nu** länken längst ned i varje lösning.

### <a name="adding-a-log-analytics-cdn-management-solution"></a>Lägga till en Log Analytics CDN lösning

Följ dessa steg för att lägga till en lösning:

1.   Om du inte redan har gjort logga in på Azure-portalen med din Azure-prenumeration och gå till instrumentpanelen.
    ![Instrumentpanelen för Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. I den **ny** bladet under **Marketplace**väljer **övervakning + management**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. I den **övervakning + management** bladet, klickar du på **se alla**.

    ![Se alla](./media/cdn-diagnostics-log/15_See-all.png)

4.  Sök efter CDN i sökrutan.

    ![Se alla](./media/cdn-diagnostics-log/16_Search-for.png)

5.  Välj **Azure CDN Core Analytics**. 

    ![Se alla](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  När du klickar på **skapa**, uppmanas du att skapa en ny logganalys-arbetsyta eller använda en befintlig. 

    ![Se alla](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  Välj arbetsytans innan. Du måste sedan lägga till ett automation-konto.

    ![Se alla](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Följande skärmbild visar formuläret automation-konto måste du fylla ut. 

    ![Se alla](./media/cdn-diagnostics-log/20_Automation.png)

9. När du har skapat automation-kontot, är du redo att lägga till din lösning. Klicka på knappen **Skapa**.

    ![Se alla](./media/cdn-diagnostics-log/21_Ready.png)

10. Din lösning har nu lagts till arbetsytan. Gå tillbaka till instrumentpanelen i Azure portal.

    ![Se alla](./media/cdn-diagnostics-log/22_Dashboard.png)

    Klicka på logganalys-arbetsytan som du skapade för att gå till arbetsytan. 

11. Klicka på den **OMS-portalen** ruta visas den nya lösningen.

    ![Se alla](./media/cdn-diagnostics-log/23_workspace.png)

12. Din portal bör nu se ut som följande skärmbild:

    ![Se alla](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Klicka på ett av rutor finns flera vyer i dina data.

    ![Se alla](./media/cdn-diagnostics-log/25_Interior-view.png)

    Du kan rulla åt vänster eller höger för att se ytterligare paneler som representerar enskilda vyer i data. 

    Klicka på någon av panelerna får du mer information om dina data.

     ![Se alla](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Erbjudanden och prisnivåer

Du kan se erbjudanden och prisnivåer för hanteringslösningar [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Anpassa vyer

Du kan anpassa vyn i dina data med hjälp av den **Vydesigner**. Om du vill börja designa, gå till logganalys-arbetsytan och klicka på **Vydesigner** panelen.

![Vydesigner](./media/cdn-diagnostics-log/27_Designer.png)

Du kan dra typer av diagram och Fyll i de information som du vill analysera.

![Vydesigner](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Logga data fördröjningar

Verizon logga data fördröjningar | Akamai logga data fördröjningar
--- | ---
Loggdata för Verizon försenas 1 timme och ta upp till 2 timmar att starta visas efter att endpoint-spridningen har slutförts. | Loggdata för Akamai är skjutas upp med 24 timmar. Om det skapades mer än 24 timmar sedan tar det upp till två timmar att starta visas. Det kan ta upp till 25 timmar för loggfilerna för att starta visas om den nyligen har skapats.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typer av diagnostiska loggen för CDN core analytics

Vi har för närvarande endast core analytics loggarna, vilket innehåller mått som visar statistik för HTTP-svar och utgående trafik som visas för CDN POP/kanter.

### <a name="core-analytics-metrics-details"></a>Core analytics mätvärden information
Följande tabell visar en lista över tillgängliga i core analytics loggarna mått. Inte alla mått är tillgängliga från alla leverantörer, även om dessa skillnader är minimal. I följande tabell visas även om ett visst mått är tillgänglig från en leverantör. Observera att mätvärdena som är tillgängliga för bara de CDN-slutpunkter som har trafik på dem.


|Mått                     | Beskrivning   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Totalt antal träffar för begäran under denna period| Ja  |Ja   |
| RequestCountHttpStatus2xx |Antal alla begäranden som resulterade i en 2xx http-kod (till exempel 200, 202)              | Ja  |Ja   |
| RequestCountHttpStatus3xx | Antal alla begäranden som resulterade i en 3xx http-kod (till exempel 300, 302)              | Ja  |Ja   |
| RequestCountHttpStatus4xx |Antal alla begäranden som resulterade i en 4xx http-kod (till exempel 400, 404)               | Ja   |Ja   |
| RequestCountHttpStatus5xx | Antal alla begäranden som resulterade i en 5xx http-kod (till exempel 500, 504)              | Ja  |Ja   |
| RequestCountHttpStatusOthers |  Uppräkning av alla andra HTTP-koder (utanför 2xx 5xx) | Ja  |Ja   |
| RequestCountHttpStatus200 | Antal alla begäranden som resulterade i en 200 HTTP-svar för kod              |Nej   |Ja   |
| RequestCountHttpStatus206 | Antal alla begäranden som resulterade i ett 206 HTTP-svar för kod              |Nej   |Ja   |
| RequestCountHttpStatus302 | Antal alla begäranden som resulterade i en 302 HTTP-svar för kod              |Nej   |Ja   |
| RequestCountHttpStatus304 |  Antal alla begäranden som resulterade i ett 304 HTTP-svar för kod             |Nej   |Ja   |
| RequestCountHttpStatus404 | Antal alla begäranden som resulterade i ett 404 HTTP-svar för kod              |Nej   |Ja   |
| RequestCountCacheHit |Antal alla begäranden som resulterade i ett antal träffar. Tillgången behandlades direkt från POP till klienten.               | Ja  |Nej   |
| RequestCountCacheMiss | Antal alla begäranden som resulterade i en Cache-Miss. Detta innebär tillgången hittades inte på POP närmast klienten och därför har hämtats från ursprunget.              |Ja   | Nej  |
| RequestCountCacheNoCache | Antal alla begäranden till en tillgång som hindras från att cachelagras på grund av en Användarkonfiguration kant.              |Ja   | Nej  |
| RequestCountCacheUncacheable | Antal alla förfrågningar till tillgångar som hindras från att cachelagras av tillgångens Cache-Control och Expires-huvuden som indikerar att det inte ska cachelagras på en POP eller genom att HTTP-klienten                |Ja   |Nej   |
| RequestCountCacheOthers | Antal begäranden med cachen inte omfattas av ovan.              |Ja   | Nej  |
| EgressTotal | Utgående dataöverföring i GB              |Ja   |Ja   |
| EgressHttpStatus2xx | Utgående data transfer * för svar med 2xx HTTP-statuskoder i GB            |Ja   |Nej   |
| EgressHttpStatus3xx | Utgående dataöverföring för svar med 3xx HTTP-statuskoder i GB              |Ja   |Nej   |
| EgressHttpStatus4xx | Utgående dataöverföring för svar med 4xx HTTP-statuskoder i GB               |Ja   | Nej  |
| EgressHttpStatus5xx | Utgående dataöverföring för svar med 5xx HTTP-statuskoder i GB               |Ja   |  Nej |
| EgressHttpStatusOthers | Utgående dataöverföring för svar med andra HTTP-statuskoder i GB                |Ja   |Nej   |
| EgressCacheHit |  Utgående dataöverföring för svar som har levererats direkt från CDN-cachen på CDN POP/kanter  |Ja   |  Nej |
| EgressCacheMiss | Utgående dataöverföring för svar som inte hittades på den närmaste POP-servern, och hämtas från den ursprungliga servern              |Ja   |  Nej |
| EgressCacheNoCache | Utgående dataöverföring för tillgångar som hindras från att cachelagras på grund av en Användarkonfiguration kant.                |Ja   |Nej   |
| EgressCacheUncacheable | Utgående dataöverföring för tillgångar som hindras från att cachelagras av tillgångens Cache-Control eller Expires-huvuden. Anger att det inte ska cachelagras på en POP eller genom att HTTP-klienten.                   |Ja   | Nej  |
| EgressCacheOthers |  Utgående dataöverföringar för andra cache-scenarier.             |Ja   | Nej  |

* Utgående dataöverföring refererar till trafik från CDN POP-servrar som levereras till klienten.


### <a name="schema-of-the-core-analytics-logs"></a>Schemat för kärnor analytics loggar 

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

Där är tid starttiden gränsens timme som statistik rapporteras. Ett mått inte stöds av en CDN-providern, i stället för ett värde för double eller heltal har ett null-värde. Null-värde anger avsaknaden av ett mått och skiljer sig från värdet 0. Det finns en uppsättning av de här måtten per domän konfigurerad på slutpunkten.

Exempel egenskaper:

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
* [Core analytics via Azure CDN kompletterande portalen](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure logganalys REST-API](https://docs.microsoft.com/rest/api/loganalytics)








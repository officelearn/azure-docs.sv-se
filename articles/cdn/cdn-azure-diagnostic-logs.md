---
title: Azure diagnostikloggar | Microsoft Docs
description: Kunden kan aktivera logganalys för Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.openlocfilehash: 98a7fc5c4607115811e17a7cf6acd4e867663833
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261312"
---
# <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

Du kan använda Azure diagnostikloggar för att visa core analytics och spara dem i en eller flera mål, inklusive:

 - Azure-lagringskonto
 - Azure Event Hubs
 - [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Den här funktionen är tillgänglig på CDN-slutpunkter för alla prisnivåer. 

Azure diagnostics loggar kan du exportera grundläggande användningsstatistik från CDN-slutpunkten till en mängd olika datakällor så att du kan använda dem i ett anpassat sätt. Du kan till exempel göra följande typer av export av data:

- Exportera data till blob storage, exportera till CSV och skapa diagram i Excel.
- Exportera data till Händelsehubbar och samordna med data från andra Azure-tjänster.
- Exportera data till logganalys och visa data i din egen logganalys-arbetsyta

Följande diagram visar en typisk CDN core analytics data.

![Portal - diagnostik loggar](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Bild 1 - CDN core analytics visa*

Läs mer om diagnostikloggar [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-the-azure-portal"></a>Aktivera loggning med Azure-portalen

Följ dessa steg aktivera loggning med CDN core analytics:

Logga in på [Azure-portalen](http://portal.azure.com). Om du inte redan har aktiverat CDN för arbetsflödet, [skapa en Azure CDN-profilen och slutpunkten](cdn-create-new-endpoint.md) innan du fortsätter.

1. I Azure-portalen går du till **CDN-profilen**.

2. Sök efter en CDN-profil eller välj en från instrumentpanelen i Azure-portalen. Markera CDN-slutpunkt som du vill aktivera diagnostik loggar.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Välj **diagnostik loggar** i avsnittet övervakning.

   Den **diagnostik loggar** visas.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Aktivera loggning med Azure Storage

Följ dessa steg om du vill använda ett lagringskonto för lagring av loggarna:
    
1. För **namn**, ange ett namn för din diagnostiska Logginställningar.
 
2. Välj **arkivet till ett lagringskonto**och välj **CoreAnalytics**. 

2. För **bevarande (dagar)**, Välj antalet dagar för kvarhållning. En kvarhållning av noll dagar lagrar loggarna på obestämd tid. 

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Välj **lagringskonto**.

    Den **Välj ett lagringskonto** visas.

4. Välj ett lagringskonto från den nedrullningsbara listrutan och väljer sedan **OK**.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. När du är klar med din diagnostiska Logginställningar, Välj **spara**.

### <a name="logging-with-log-analytics"></a>Loggning med logganalys

Följ dessa steg om du vill använda logganalys för att lagra loggfilerna:

1. Från den **diagnostik loggar** väljer **skicka till logganalys**. 

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Välj **konfigurera** konfigurera logganalys loggning. 

   Den **OMS arbetsytor** visas.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Välj **Skapa ny arbetsyta**.

    Den **OMS-arbetsytan** visas.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/07_Create-new.png)

4. För **OMS-arbetsytan**, ange ett namn på OMS-arbetsyta. Namnet på OMS-arbetsytan måste vara unika och innehålla bara bokstäver, siffror och bindestreck; blanksteg och understreck tillåts inte. 

5. För **prenumeration**, Välj en befintlig prenumeration från den nedrullningsbara listan. 

6. För **resursgruppen**, skapa en ny resursgrupp eller välj en befintlig.

7. För **plats**, Välj en plats i listan.

8. Välj **fäst på instrumentpanelen** om du vill spara loggningskonfigurationen av på instrumentpanelen. 

9. Välj **OK** för att slutföra konfigurationen.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/08_Workspace-resource.png)

10. När ditt arbetsområde har skapats kan du tillbaka till den **diagnostikloggar** sidan. Kontrollera namnet på den nya logganalys-arbetsytan.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Välj **CoreAnalytics**och välj **spara**.

12. Om du vill visa nya logganalys-arbetsytan, Välj **Core analytics** från sidan CDN-slutpunkten.

    ![Portal - diagnostik loggar](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Logganalys-arbetsytan är nu redo att logga data. För att kunna använda data måste du använda en [Log Analytics lösningen](#consuming-diagnostics-logs-from-a-log-analytics-workspace), tas upp senare i den här artikeln.

Läs mer om logga data fördröjningar [logga data fördröjningar](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Aktivera loggning med PowerShell

I följande exempel visas hur du aktiverar diagnostikloggar via Azure PowerShell-Cmdlets.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Aktivera diagnostiska loggar i ett lagringskonto

1. Logga in och välj en prenumeration:

    Ansluta AzureRmAccount 

    Välj AzureSubscription - prenumerations-ID 

2. Om du vill aktivera diagnostikloggar i ett lagringskonto, skriver du följande kommando:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Om du vill aktivera diagnostik loggar i logganalys-arbetsytan, skriver du följande kommando:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Förbrukar diagnostik loggar från Azure Storage
Det här avsnittet beskrivs schemat för CDN core analytics, hur den ordnas i ett Azure storage-konto och exempelkod för att hämta loggarna i en CSV-fil.

### <a name="using-microsoft-azure-storage-explorer"></a>Med Microsoft Azure Lagringsutforskaren
Innan du kan komma åt core analysdata från ett Azure storage-konto, måste du först ett verktyg för att få åtkomst till innehållet i ett lagringskonto. Det finns flera verktyg på marknaden, är det som vi rekommenderar Microsoft Azure Lagringsutforskaren. Om du vill hämta verktyget, se [Azure Lagringsutforskaren](http://storageexplorer.com/). Konfigurera den att använda samma Azure storage-konto som har konfigurerats som ett mål i CDN diagnostik loggar efter hämta och installera programvaran.

1.  Öppna **Lagringsutforskaren för Microsoft Azure**
2.  Hitta storage-konto
3.  Expandera den **Blobbbehållare** nod under det här lagringskontot.
4.  Markera den behållare som heter *insights-loggar-coreanalytics*.
5.  Resultatet visar upp i den högra rutan, från och med den första nivån som *resourceId =*. Fortsätt att välja varje nivå tills du hittar filen *PT1H.json*. Se följande *Blob sökvägsformat* Obs en förklaring av sökvägen.
6.  Varje blobb *PT1H.json* filen representerar analytics-loggarna för en timme för en specifik CDN-slutpunkten eller den anpassa domänen.
7.  Schemat för innehållet i JSON-fil beskrivs i avsnittet schemat core analytics loggar.


> [!NOTE]
> **Formatet för BLOB-sökväg**
> 
> Core analytics loggar skapas varje timme och data som samlas in och lagras inuti en enda Azure blob som en JSON-nyttolast. Eftersom verktyget lagring explorer tolkar '/' som en katalogavgränsare och visar hierarkin, sökvägen till Azure-blobben visas som om det är en hierarkisk struktur och representerar blobbnamnet på. Namnet på blobben följer följande namngivningskonvention: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Beskrivning av fält:**

|Värde|Beskrivning|
|-------|---------|
|Prenumerations-ID:t    |ID för Azure-prenumeration i Guid-format.|
|Resursgruppsnamn |Namnet på resursgruppen som CDN resurser tillhör.|
|Profilnamn |Namnet på CDN-profilen|
|Namnet på slutpunkten |Namnet på CDN-slutpunkten|
|År|  Fyrsiffriga representation år, till exempel 2017|
|Månad| Två siffror representation av månadsnummer. 01 = januari... 12 = December|
|Dag|   Tvåsiffrig representation av dagen i månaden|
|PT1H.json| Faktiska JSON-fil där analytics-data lagras|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportera core analytics data till en CSV-fil

Om du vill göra det enklare att komma åt core analytics, tillhandahålls exempelkod för ett verktyg som. Det här verktyget kan hämta JSON-filer till en platt CSV-format som kan användas för att skapa diagram eller andra aggregeringar.

Här är hur du kan använda verktyget:

1.  Gå till github-länk: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Hämta koden.
3.  Följ instruktionerna för att kompilera och konfigurera.
4.  Kör verktyget.
5.  Den resulterande CSV-filen innehåller analytics-data i en enkel platt hierarki.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Förbrukar diagnostik loggar från logganalys-arbetsytan
Log Analytics är en Azure-tjänst som övervakar molnet och lokala miljöer för att upprätthålla sin tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor. 

Om du vill använda logganalys måste du [aktivera loggning](#enable-logging-with-azure-storage) till Azure logganalys-arbetsyta som beskrivs tidigare i den här artikeln.

### <a name="using-the-log-analytics-workspace"></a>Med hjälp av logganalys-arbetsytan

 Följande diagram visar arkitekturen för indata och utdata för databasen:

![Log Analytics-arbetsyta](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Bild 3 - Log Analytics-databasen*

Du kan visa data i en mängd olika sätt med hjälp av lösningar för hantering. Du kan hämta hanteringslösningar från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Du kan installera hanteringslösningar från Azure marketplace genom att välja den **blir det nu** länken längst ned i varje lösning.

### <a name="add-a-log-analytics-cdn-management-solution"></a>Lägg till en Log Analytics CDN lösning

Följ dessa steg för att lägga till en Log Analytics lösning:

1.   Logga in på Azure-portalen med din Azure-prenumeration och gå till instrumentpanelen.
    ![Instrumentpanelen för Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. I den **ny** sidan under **Marketplace**väljer **övervakning + management**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. I den **övervakning + management** väljer **se alla**.

    ![Se alla](./media/cdn-diagnostics-log/15_See-all.png)

4. Sök efter CDN i sökrutan.

    ![Se alla](./media/cdn-diagnostics-log/16_Search-for.png)

5. Välj **Azure CDN Core Analytics**. 

    ![Se alla](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. När du har valt **skapa**, uppmanas du att skapa en ny logganalys-arbetsyta eller använda en befintlig. 

    ![Se alla](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Välj arbetsytans innan. Du måste sedan lägga till ett automation-konto.

    ![Se alla](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Följande skärmbild visar formuläret automation-konto måste du fylla ut. 

    ![Se alla](./media/cdn-diagnostics-log/20_Automation.png)

9. När du har skapat automation-kontot, är du redo att lägga till din lösning. Välj knappen **Skapa**.

    ![Se alla](./media/cdn-diagnostics-log/21_Ready.png)

10. Din lösning har nu lagts till arbetsytan. Gå tillbaka till instrumentpanelen i Azure portal.

    ![Se alla](./media/cdn-diagnostics-log/22_Dashboard.png)

    Välj logganalys-arbetsytan som du skapade för att gå till arbetsytan. 

11. Välj den **OMS-portalen** ruta visas den nya lösningen.

    ![Se alla](./media/cdn-diagnostics-log/23_workspace.png)

12. Din portal bör nu se ut som följande skärmbild:

    ![Se alla](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Välj ett av rutor finns flera vyer i dina data.

    ![Se alla](./media/cdn-diagnostics-log/25_Interior-view.png)

    Du kan rulla åt vänster eller höger för att se ytterligare paneler som representerar enskilda vyer i data. 

    Välj ett av rutor finns mer information om dina data.

     ![Se alla](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Erbjudanden och prisnivåer

Du kan se erbjudanden och prisnivåer för hanteringslösningar [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Anpassa vyer

Du kan anpassa vyn i dina data med hjälp av den **Vydesigner**. Om du vill börja designa, gå till logganalys-arbetsytan och välj den **Vydesigner** panelen.

![Vydesigner](./media/cdn-diagnostics-log/27_Designer.png)

Dra och släpp typer av diagram och Fyll i data information du vill analysera.

![Vydesigner](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Logga data fördröjningar

I följande tabell visas logga data fördröjningar för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**, och **Azure CDN Standard/Premium från Verizon**.

Microsoft logga data fördröjningar | Verizon logga data fördröjningar | Akamai logga data fördröjningar
--- | --- | ---
Fördröjd 1 timme. | Skjutas upp med 1 timme och kan ta upp till två timmar att starta visas efter att endpoint-spridningen har slutförts. | Skjutas upp med 24 timmar. Om det skapades mer än 24 timmar sedan tar det upp till två timmar att starta visas. Det kan ta upp till 25 timmar för loggfilerna för att starta visas om den nyligen har skapats.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typer av diagnostiska loggen för CDN core analytics

Microsoft erbjuder för närvarande core analytics loggar endast, som innehåller mått som visar statistik för HTTP-svar och utgående trafik som visas för CDN POP/kanter.

### <a name="core-analytics-metrics-details"></a>Core analytics mätvärden information
I följande tabell visas en lista över tillgängliga i grundläggande mått analytics loggar för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**, och **Azure CDN Standard/Premium från Verizon**. Inte alla mått är tillgängliga från alla leverantörer, även om dessa skillnader är minimal. Tabellen visar även om ett visst mått är tillgängliga från en leverantör. Mätvärdena som är tillgängliga för bara de CDN-slutpunkter som har trafik på dem.


|Mått                     | Beskrivning | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Totalt antal träffar i begäran under denna tid. | Ja | Ja |Ja |
| RequestCountHttpStatus2xx | Antal alla begäranden som resulterade i en 2xx http-kod (till exempel 200, 202). | Ja | Ja |Ja |
| RequestCountHttpStatus3xx | Antal alla begäranden som resulterade i en 3xx http-kod (till exempel 300, 302). | Ja | Ja |Ja |
| RequestCountHttpStatus4xx | Antal alla begäranden som resulterade i en 4xx http-kod (till exempel 400, 404). | Ja | Ja |Ja |
| RequestCountHttpStatus5xx | Antal alla begäranden som resulterade i en 5xx http-kod (till exempel 500, 504). | Ja | Ja |Ja |
| RequestCountHttpStatusOthers | Antal alla andra HTTP-koder (utanför 2xx 5xx). | Ja | Ja |Ja |
| RequestCountHttpStatus200 | Antal alla begäranden som resulterade i en 200 HTTP-svar som kod. | Ja | Nej  |Ja |
| RequestCountHttpStatus206 | Antal alla begäranden som resulterade i ett 206 kod HTTP-svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus302 | Antal alla begäranden som resulterade i en 302 kod HTTP-svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus304 | Antal alla begäranden som resulterade i ett 304 kod HTTP-svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus404 | Antal alla begäranden som resulterade i ett 404 HTTP-svar för koden. | Ja | Nej  |Ja |
| RequestCountCacheHit | Antal alla begäranden som resulterade i ett cacheminne påträffades. Tillgången behandlades direkt från POP till klienten. | Ja | Ja | Nej  |
| RequestCountCacheMiss | Antal alla begäranden som resulterade i en Cache-miss. En Cache-miss innebär tillgången hittades inte på POP närmast klienten och därför har hämtats från ursprunget. | Ja | Ja | Nej |
| RequestCountCacheNoCache | Antal alla begäranden till en tillgång som hindras från att cachelagras på grund av en Användarkonfiguration kant. | Ja | Ja | Nej |
| RequestCountCacheUncacheable | Antal alla förfrågningar till tillgångar som hindras från att cachelagras av tillgångens Cache-Control och Expires-huvuden som indikerar att det inte ska cachelagras på en POP eller genom att HTTP-klienten. | Ja | Ja | Nej |
| RequestCountCacheOthers | Antal begäranden med cachen inte omfattas av ovan. | Nej | Ja | Nej  |
| EgressTotal | Utgående dataöverföring i GB | Ja |Ja |Ja |
| EgressHttpStatus2xx | Utgående data transfer * för svar med 2xx HTTP-statuskoder i GB. | Ja | Ja | Nej  |
| EgressHttpStatus3xx | Utgående dataöverföring för svar med 3xx HTTP-statuskoder i GB. | Ja | Ja | Nej  |
| EgressHttpStatus4xx | Utgående dataöverföring för svar med 4xx HTTP-statuskoder i GB. | Ja | Ja | Nej  |
| EgressHttpStatus5xx | Utgående dataöverföring för svar med 5xx HTTP-statuskoder i GB. | Ja | Ja | Nej |
| EgressHttpStatusOthers | Utgående dataöverföring för svar med andra HTTP-statuskoder i GB. | Ja | Ja | Nej  |
| EgressCacheHit | Utgående dataöverföring för svar som har levererats direkt från CDN-cachen på CDN POP/kanter. | Ja | Ja | Nej |
| EgressCacheMiss. | Utgående dataöverföring för svar som inte hittades på den närmaste POP-servern, och hämtas från den ursprungliga servern. | Ja | Ja | Nej |
| EgressCacheNoCache | Utgående dataöverföring för tillgångar som hindras från att cachelagras på grund av en Användarkonfiguration kant. | Ja | Ja | Nej |
| EgressCacheUncacheable | Utgående dataöverföring för tillgångar som hindras från att cachelagras av tillgångens Cache-Control eller Expires-huvuden. Anger att det inte ska cachelagras på en POP eller genom att HTTP-klienten. | Ja | Ja | Nej |
| EgressCacheOthers | Utgående dataöverföringar för andra cache-scenarier. | Nej | Ja | Nej |

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

Där *tid* representerar starttiden gränsens timme som statistik rapporteras. Ett mått inte stöds av en CDN-providern, i stället för ett värde för double eller heltal har ett null-värde. Null-värde anger avsaknaden av ett mått och skiljer sig från värdet 0. Det finns en uppsättning av de här måtten per domän konfigurerad på slutpunkten.

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








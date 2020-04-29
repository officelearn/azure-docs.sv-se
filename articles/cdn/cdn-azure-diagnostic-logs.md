---
title: Azure-diagnostikloggar | Microsoft Docs
description: Kunden kan aktivera logg analys för Azure CDN.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81254809"
---
# <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

Med Azure Diagnostic-loggar kan du Visa kärn analyser och spara dem i en eller flera destinationer, inklusive:

 - Azure Storage-konto
 - Azure Event Hubs
 - [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Den här funktionen är tillgänglig på CDN-slutpunkter för alla pris nivåer. 

Med Azure Diagnostics-loggar kan du exportera grundläggande användnings statistik från CDN-slutpunkten till flera olika källor så att du kan använda dem på ett anpassat sätt. Du kan till exempel göra följande typer av data export:

- Exportera data till Blob Storage, exportera till CSV och generera grafer i Excel.
- Exportera data till Event Hubs och korrelera med data från andra Azure-tjänster.
- Exportera data till Azure Monitor loggar och visa data i din egen Log Analytics arbets yta

Följande diagram visar en typisk grundläggande analys visning av data i CDN.

![Portal – diagnostikloggar](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Bild 1 – vyn CDN Core Analytics*

Mer information om diagnostiska loggar finns i [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Aktivera loggning i Azure-portalen

Följ dessa steg för att aktivera loggning med CDN Core Analytics:

Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har aktiverat CDN för arbets flödet [skapar du en Azure CDN profil och en slut punkt](cdn-create-new-endpoint.md) innan du fortsätter.

1. I Azure Portal navigerar du till **CDN-profil**.

2. I Azure Portal söker du efter en CDN-profil eller väljer en från din instrument panel. Välj sedan den CDN-slutpunkt som du vill aktivera diagnostikloggar för.

    ![Portal – diagnostikloggar](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Välj **diagnostikloggar** i avsnittet övervakning.

   Sidan **diagnostikloggar loggar** visas.

    ![Portal – diagnostikloggar](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Aktivera loggning med Azure Storage

Följ dessa steg om du vill använda ett lagrings konto för att lagra loggarna:
    
1. I **namn**anger du ett namn för inställningarna för diagnostikloggar.
 
2. Välj **Arkiv till ett lagrings konto**och välj sedan **CoreAnalytics**. 

2. För **kvarhållning (dagar)** väljer du antalet dagar för kvarhållning. En kvarhållning på noll dagar lagrar loggarna på obestämd tid. 

    ![Portal – diagnostikloggar](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Välj **lagrings konto**.

    Sidan **Välj ett lagrings konto** visas.

4. Välj ett lagrings konto i list rutan och välj sedan **OK**.

    ![Portal – diagnostikloggar](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. När du har utfört inställningarna för diagnostikloggar väljer du **Spara**.

### <a name="logging-with-azure-monitor"></a>Logga med Azure Monitor

Följ dessa steg om du vill använda Azure Monitor för att lagra loggarna:

1. På sidan **diagnostikloggar loggar** väljer **du skicka till Log Analytics**. 

    ![Portal – diagnostikloggar](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Välj **Konfigurera** för att konfigurera Azure Monitor loggning. 

   Sidan **Log Analytics arbets ytor** visas.

    >[!NOTE] 
    >OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.

    ![Portal – diagnostikloggar](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Välj **Skapa ny arbets yta**.

    Sidan **Log Analytics arbets yta** visas.

    >[!NOTE] 
    >OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.

    ![Portal – diagnostikloggar](./media/cdn-diagnostics-log/07_Create-new.png)

4. Ange ett namn på en Log Analytics arbets yta för **Log Analytics arbets yta**. Namnet på den Log Analytics arbets ytan måste vara unikt och får bara innehålla bokstäver, siffror och bindestreck. blank steg och under streck är inte tillåtna. 

5. För **prenumeration**väljer du en befintlig prenumeration i list rutan. 

6. För **resurs grupp**skapar du en ny resurs grupp eller väljer en befintlig.

7. För **plats**väljer du en plats i listan.

8. Välj **Fäst på instrument panelen** om du vill spara logg konfigurationen på instrument panelen. 

9. Klicka på **OK** för att slutföra konfigurationen.

10. När din arbets yta har skapats kommer du tillbaka till sidan **diagnostikloggar** . Bekräfta namnet på din nya Log Analytics-arbetsyta.

    ![Portal – diagnostikloggar](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Välj **CoreAnalytics**och välj sedan **Spara**.

12. Om du vill visa den nya Log Analytics arbets ytan väljer du **Core Analytics** på sidan CDN-slutpunkt.

    ![Portal – diagnostikloggar](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Din Log Analytics arbets yta är nu klar att logga data. För att kunna använda dessa data måste du använda en [lösning för Azure Monitor loggar](#consuming-diagnostics-logs-from-a-log-analytics-workspace), som beskrivs längre fram i den här artikeln.

Mer information om fördröjningar i logg data finns i [försenade logg data](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>aktivera loggning med PowerShell

I följande exempel visas hur du aktiverar diagnostikloggar via Azure PowerShell-cmdletar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Aktivera diagnostikloggar i ett lagrings konto

1. Logga in och välj en prenumeration:

    Anslut – AzAccount 

    Select-AzureSubscription-SubscriptionId 

2. Om du vill aktivera diagnostikloggar i ett lagrings konto anger du följande kommando:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Ange följande kommando för att aktivera diagnostikloggar i en Log Analytics arbets yta:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Använda diagnostikloggar från Azure Storage
I det här avsnittet beskrivs schemat för CDN Core Analytics, hur det organiseras i ett Azure Storage-konto och innehåller exempel kod för att ladda ned loggarna i en CSV-fil.

### <a name="using-microsoft-azure-storage-explorer"></a>Använda Microsoft Azure Storage Explorer
Innan du kan komma åt kärn analys data från ett Azure Storage-konto behöver du först ett verktyg för att komma åt innehållet i ett lagrings konto. Även om det finns flera tillgängliga verktyg på marknaden, är det som vi rekommenderar Microsoft Azure Storage Explorer. Information om hur du hämtar verktyget finns [Azure Storage Explorer](https://storageexplorer.com/). När du har laddat ned och installerat program varan konfigurerar du det så att det använder samma Azure Storage-konto som har kon figurer ATS som ett mål för CDN-diagnostikloggar.

1.  Öppna **Microsoft Azure Storage Explorer**
2.  Hitta lagrings kontot
3.  Expandera noden **BLOB containers** under det här lagrings kontot.
4.  Välj behållaren med namnet *Insights-logs-coreanalytics*.
5.  Resultaten visas i den högra rutan, med början på den första nivån som *resourceId =*. Fortsätt att markera varje nivå tills du hittar filen *PT1H. JSON*. En förklaring av sökvägen finns i [BLOB Path-format](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Varje Blob- *PT1H. JSON* -fil representerar analys loggar för en timme för en angiven CDN-slutpunkt eller dess anpassade domän.
7.  Schemat för innehållet i denna JSON-fil beskrivs i avsnittet-schemat i Core Analytics-loggarna.


#### <a name="blob-path-format"></a>Blobb Sök vägs format

Kärn analys loggar genereras varje timme och data samlas in och lagras i en enda Azure-blob som en JSON-nyttolast. Eftersom Storage Explorer-verktyget tolkar "/" som en katalog avgränsare och visar hierarkin visas sökvägen till Azure-blobben som om det finns en hierarkisk struktur och representerar BLOB-namnet. Namnet på blobben följer följande namngivnings konvention:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Fält Beskrivning:**

|Värde|Beskrivning|
|-------|---------|
|Prenumerations-ID:t    |ID för Azure-prenumerationen i GUID-format.|
|Resurs grupps namn |Namnet på resurs gruppen som CDN-resurserna tillhör.|
|Profilnamn |Namn på CDN-profilen|
|Slut punkts namn |Namn på CDN-slutpunkten|
|Year|  Fyrsiffrig representation av året, till exempel 2017|
|Month| Tvåsiffrig representation av månads nummer. 01 = januari... 12 = december|
|Day|   Tvåsiffrig representation av dagen i månaden|
|PT1H. JSON| Faktisk JSON-fil där analys data lagras|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportera huvud analys data till en CSV-fil

För att göra det enkelt att komma åt kärn analys tillhandahålls exempel kod för ett verktyg. Med det här verktyget kan du ladda ned JSON-filerna till ett geografiskt semikolonavgränsat fil format som kan användas för att skapa diagram eller andra agg regeringar.

Så här kan du använda verktyget:

1.  Besök GitHub-länken:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Ladda ned koden.
3.  Följ anvisningarna för att kompilera och konfigurera.
4.  Kör verktyget.
5.  Den resulterande CSV-filen visar analys data i en enkel, platt hierarki.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Använda diagnostikloggar från en Log Analytics-arbetsyta
Azure Monitor är en Azure-tjänst som övervakar molnet och lokala miljöer för att bevara deras tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor. 

Om du vill använda Azure Monitor måste du [Aktivera loggning](#enable-logging-with-azure-storage) till Azure Log Analytics-arbetsytan, som beskrivs tidigare i den här artikeln.

### <a name="using-the-log-analytics-workspace"></a>Använda arbets ytan Log Analytics

 Följande diagram visar arkitekturen för indata och utdata för lagrings platsen:

![Log Analytics-arbetsyta](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Bild 3 – Log Analytics lagrings plats*

Du kan visa data på flera olika sätt med hjälp av hanterings lösningar. Du kan hämta hanterings lösningar från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Du kan installera övervaknings lösningar från Azure Marketplace genom att välja länken **Hämta nu** längst ned i varje lösning.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Lägg till en Azure Monitor CDN-övervaknings lösning

Följ dessa steg om du vill lägga till en Azure Monitor övervaknings lösning:

1.   Logga in på Azure Portal med din Azure-prenumeration och gå till instrument panelen.
    ![Azure-instrumentpanel](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. På sidan **ny** under **Marketplace**väljer du **övervakning + hantering**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. På sidan **övervakning + hantering** väljer du **Visa alla**.

    ![Visa alla](./media/cdn-diagnostics-log/15_See-all.png)

4. Sök efter CDN i sökrutan.

    ![Visa alla](./media/cdn-diagnostics-log/16_Search-for.png)

5. Välj **Azure CDN Core Analytics**. 

    ![Visa alla](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. När du har valt **skapa**uppmanas du att skapa en ny Log Analytics arbets yta eller att använda en befintlig. 

    ![Visa alla](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Välj arbets ytan som du skapade tidigare. Därefter måste du lägga till ett Automation-konto.

    ![Visa alla](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Följande skärm bild visar formuläret Automation-konto som du måste fylla i. 

    ![Visa alla](./media/cdn-diagnostics-log/20_Automation.png)

9. När du har skapat Automation-kontot är du redo att lägga till din lösning. Välj knappen **Skapa**.

    ![Visa alla](./media/cdn-diagnostics-log/21_Ready.png)

10. Din lösning har nu lagts till i din arbets yta. Gå tillbaka till Azure Portal instrument panelen.

    ![Visa alla](./media/cdn-diagnostics-log/22_Dashboard.png)

    Välj Log Analytics arbets ytan som du skapade för att gå till din arbets yta. 

11. Välj panelen **OMS-Portal** för att se din nya lösning.

    ![Visa alla](./media/cdn-diagnostics-log/23_workspace.png)

12. Portalen bör nu se ut som på följande skärm:

    ![Visa alla](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Välj en av panelerna om du vill se flera vyer i dina data.

    ![Visa alla](./media/cdn-diagnostics-log/25_Interior-view.png)

    Du kan rulla åt vänster eller höger om du vill se fler paneler som representerar enskilda vyer i data. 

    Välj en av panelerna om du vill visa mer information om dina data.

     ![Visa alla](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Erbjudanden och pris nivåer

Du kan se erbjudanden och pris nivåer för hanterings lösningar [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Anpassa vyer

Du kan anpassa vyn till dina data med hjälp av **View Designer**. Börja utforma genom att gå till din Log Analytics arbets yta och välj panelen **Visa designer** .

![Vydesigner](./media/cdn-diagnostics-log/27_Designer.png)

Dra och släpp de olika typerna av diagram och fyll i data informationen som du vill analysera.

![Vydesigner](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Logga data fördröjningar

I följande tabell visas fördröjningar i logg data för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**och **Azure CDN Standard/Premium från Verizon**.

Microsoft logg data fördröjningar | Data fördröjningar för Verizon-logg | Akamai logg data fördröjningar
--- | --- | ---
Försenat med 1 timme. | Försenat med 1 timme och kan ta upp till 2 timmar innan slut för ande av slut punkts spridningen påbörjas. | Fördröjt med 24 timmar; om det skapades mer än 24 timmar sedan tar det upp till 2 timmar innan det börjar visas. Om den nyligen har skapats kan det ta upp till 25 timmar innan loggarna börjar visas.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnostiska logg typer för CDN Core Analytics

Microsoft erbjuder för närvarande endast grundläggande analys loggar, som innehåller mått som visar statistik över HTTP-svar och utgångs statistik som visas från CDN pop/Kants.

### <a name="core-analytics-metrics-details"></a>Statistik information om kärn analys
I följande tabell visas en lista över mått som är tillgängliga i Core Analytics-loggarna för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**och **Azure CDN Standard/Premium från Verizon**. Alla mått är inte tillgängliga från alla leverantörer, även om sådana skillnader är minimala. Tabellen visar även om ett specifikt mått är tillgängligt från en leverantör. Måtten är endast tillgängliga för de CDN-slutpunkter som har trafik på dem.


|Mått                     | Beskrivning | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Totalt antal begär ande träffar under denna period. | Ja | Ja |Ja |
| RequestCountHttpStatus2xx | Antal begär Anden som resulterade i en 2xx HTTP-kod (till exempel 200, 202). | Ja | Ja |Ja |
| RequestCountHttpStatus3xx | Antal begär Anden som resulterade i en 3xx HTTP-kod (till exempel 300, 302). | Ja | Ja |Ja |
| RequestCountHttpStatus4xx | Antal begär Anden som resulterade i en 4xx HTTP-kod (till exempel 400, 404). | Ja | Ja |Ja |
| RequestCountHttpStatus5xx | Antal begär Anden som resulterade i en 5xx HTTP-kod (till exempel 500, 504). | Ja | Ja |Ja |
| RequestCountHttpStatusOthers | Antal andra HTTP-koder (utanför 2xx-5xx). | Ja | Ja |Ja |
| RequestCountHttpStatus200 | Antal begär Anden som resulterade i ett 200-HTTP-kods svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus206 | Antal begär Anden som resulterade i ett 206-HTTP-kods svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus302 | Antal begär Anden som resulterade i ett 302-HTTP-kods svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus304 | Antal begär Anden som resulterade i ett 304-HTTP-kods svar. | Ja | Nej  |Ja |
| RequestCountHttpStatus404 | Antal begär Anden som resulterade i ett 404-HTTP-kods svar. | Ja | Nej  |Ja |
| RequestCountCacheHit | Antal begär Anden som resulterade i en cacheträff. Till gången betjänades direkt från POP till klienten. | Ja | Ja | Nej  |
| RequestCountCacheMiss | Antal begär Anden som resulterade i ett cache-missar. Ett cache-missar innebär att till gången inte hittades på den POP som är närmast klienten, och därför hämtades från ursprunget. | Ja | Ja | Nej |
| RequestCountCacheNoCache | Antalet förfrågningar till en till gång som förhindras från att cachelagras på grund av en användar konfiguration på gränsen. | Ja | Ja | Nej |
| RequestCountCacheUncacheable | Antalet förfrågningar till till gångar som förhindras från att cachelagras av till gångens Cache-Control-och Expires-huvuden, vilket indikerar att den inte ska cachelagras på en POP eller HTTP-klienten. | Ja | Ja | Nej |
| RequestCountCacheOthers | Antal begär Anden med cache-status som inte omfattas av ovan. | Nej | Ja | Nej  |
| EgressTotal | Utgående data överföring i GB | Ja |Ja |Ja |
| EgressHttpStatus2xx | Utgående data överföring * för svar med 2xx HTTP-status koder i GB. | Ja | Ja | Nej  |
| EgressHttpStatus3xx | Utgående data överföring för svar med 3xx HTTP-status koder i GB. | Ja | Ja | Nej  |
| EgressHttpStatus4xx | Utgående data överföring för svar med 4xx HTTP-status koder i GB. | Ja | Ja | Nej  |
| EgressHttpStatus5xx | Utgående data överföring för svar med 5xx HTTP-status koder i GB. | Ja | Ja | Nej |
| EgressHttpStatusOthers | Utgående data överföring för svar med andra HTTP-statuskod i GB. | Ja | Ja | Nej  |
| EgressCacheHit | Utgående data överföring för svar som levererades direkt från CDN-cachen på CDN-pop/-kanter. | Ja | Ja | Nej |
| EgressCacheMiss. | Utgående data överföring för svar som inte hittades på den närmaste POP-servern och som hämtats från ursprungs servern. | Ja | Ja | Nej |
| EgressCacheNoCache | Utgående data överföring för till gångar som hindras från att cachelagras på grund av en användar konfiguration på gränsen. | Ja | Ja | Nej |
| EgressCacheUncacheable | Utgående data överföring för till gångar som hindras från att cachelagras av till gångens Cache-Control och/eller Expires-huvuden. Anger att den inte ska cachelagras på en POP-eller HTTP-klient. | Ja | Ja | Nej |
| EgressCacheOthers | Utgående data överföringar för andra cache-scenarier. | Nej | Ja | Nej |

* Utgående data överföring avser trafik som levereras från CDN POP-servrar till klienten.


### <a name="schema-of-the-core-analytics-logs"></a>Schema för kärn analys loggar 

Alla loggar lagras i JSON-format och varje post har sträng fält enligt följande schema:

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

Där *tid* representerar start tiden för den timmes gräns för vilken statistiken rapporteras. Om ett mått inte stöds av en CDN-Provider, i stället för ett dubbel-eller heltals värde, så finns det ett null-värde. Det här null-värdet anger att ett mått saknas och skiljer sig från värdet 0. Det finns en uppsättning av dessa mått per domän som kon figurer ATS på slut punkten.

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

* [Azure Diagnostic-loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Core Analytics via Azure CDN kompletterande Portal](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)








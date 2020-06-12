---
title: Hantera Azure-kostnader med automatisering
description: Den här artikeln beskriver hur du kan hantera Azure-kostnader med automatisering.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449839"
---
# <a name="manage-costs-with-automation"></a>Hantera kostnader med automatisering

Med Cost Management-automatisering kan du skapa en anpassad uppsättning lösningar för att hämta och hantera kostnadsdata. Den här artikeln beskriver vanliga scenarier för Cost Management-automatisering och de alternativ som är tillgängliga utifrån din situation. Om du tänker använda API:er ges exempel på vanliga API-begäranden som hjälper dig att påskynda utvecklingsprocessen.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatisera hämtning av kostnadsdata för analys offline

Du kan behöva ladda ned dina Azure-kostnadsdata för att slå ihop dem med andra datauppsättningar. Eller så kan du behöva integrera kostnadsdata i dina egna system. Det finns olika alternativ tillgängliga beroende på hur mycket data det rör sig om. Du måste ha Cost Management-behörighet i lämpligt omfång för att kunna använda API:er och verktyg. Mer information finns i [Tilldela åtkomst till data](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data).

## <a name="suggestions-for-handling-large-datasets"></a>Förslag för hantering av stora datauppsättningar

Om din organisation använder Azure i stor omfattning med många resurser eller prenumerationer har du en stor mängd användningsinformation. Excel kanske inte kan läsa in så stora filer. I så fall rekommenderar vi följande alternativ:

**Power BI**

Power BI används för att mata in och hantera stora mängder data. Om du är Enterprise-avtalskund kan du använda Power BI-mallappen för att analysera kostnaderna för ditt faktureringskonto. Rapporten innehåller viktiga vyer som används av kunder. Mer information finns i [Analysera Azure-kostnader med Power BI-mallappen](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

**Power BI-dataanslutning**

Om du vill analysera dina data dagligen rekommenderar vi att du använder [Power BI-dataanslutningen](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management) för att hämta data för detaljerad analys. Alla rapporter du skapar uppdateras av anslutningsprogrammet när det uppstår kostnader.

**Cost Management-exporter**

Du kanske inte behöver analysera data dagligen. I så fall kan du använda [exportfunktionen](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) i Cost Management för att schemalägga dataexporter till ett Azure Storage-konto. Sedan kan du läsa in data i Power BI om det behövs, eller analysera dem i Excel om inte filen är för stor. Exportfunktionen finns i Azure-portalen, men du kan också konfigurera exporter med [export-API:et](https://docs.microsoft.com/rest/api/cost-management/exports).

**API för användningsinformation**

Överväg att använda [API:et för användningsinformation](https://docs.microsoft.com/rest/api/consumption/usageDetails) om du har en liten mängd kostnadsdata. Om du har en stor mängd kostnadsdata bör du begära den minsta möjliga mängden användningsdata för en period. Det gör du genom att antingen ange ett litet tidsintervall eller genom att använda ett filter i begäran. Om du till exempel behöver tre års kostnadsdata får du bättre prestanda med API:et om du gör flera anrop för olika tidsintervall i stället för ett enda anrop. Sedan kan du läsa in data i Excel för ytterligare analys.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatisera hämtning med API:et för användningsinformation

Med [API:et för användningsinformation](https://docs.microsoft.com/rest/api/consumption/usageDetails) kan du enkelt hämta råa, icke-aggregerade kostnadsdata som motsvarar din Azure-faktura. API:et är användbart när din organisation behöver en lösning för att hämta data programmatiskt. Överväg att använda API:et om du vill analysera mindre mängder kostnadsdata. Du bör dock använda andra lösningar som nämnts tidigare om du har större datamängder. Användningsinformationen tillhandahålls per mätare per dag. Den används för beräkning av din månadsfaktura. Versionen för allmän tillgänglighet för API:erna är `2019-10-01`. Använd `2019-04-01-preview` för att komma åt förhandsversionen för reservation och Azure Marketplace-köp med API:erna.

### <a name="usage-details-api-suggestions"></a>Förslag för API:et för användningsinformation

**Schema för begäranden**

Vi rekommenderar att du inte gör _fler än ett begärande_ till API:et för användningsinformation per dag. Mer information om hur ofta kostnadsdata uppdateras och hur avrundning hanteras finns i [Förstå Cost Management-data](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule).

**Mål på den översta nivån utan filtrering**

Använd API:et för att hämta alla data som du behöver på den högsta tillgängliga nivån. Vänta tills alla data som behövs har matats in innan du utför någon filtrering, gruppering eller aggregerad analys. API:et är optimerat för att tillhandahålla stora mängder råa, icke-aggregerade kostnadsdata. Du kan läsa mer om vilka omfång som är tillgängliga i Cost Management i [Förstå och arbeta med omfång](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes). När du har laddat ned de data som behövs för ett omfång använder du Excel för att analysera data ytterligare med filter och pivottabeller.

## <a name="example-usage-details-api-requests"></a>Exempelbegäranden till API:et för användningsinformation

Följande exempel är vanliga typer av begäranden som används av Microsoft-kunder.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Hämta användningsinformation för ett omfång inom ett specifikt datumintervall

De data som returneras av begäran motsvarar det datum då användningen togs emot av faktureringssystemet. Informationen kan omfatta kostnader från flera fakturor.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>Hämta information om amorterad kostnad

Om du behöver de faktiska kostnaderna för att visa inköp när de påförs kan du ändra *måttet* till `ActualCost` i följande begäran. För att kunna använda amorterade och faktiska kostnader måste du använda `2019-04-01-preview`-versionen. Den aktuella API-versionen fungerar på samma sätt som `2019-10-01`-versionen, med undantag för det nya typ/mått-attributet och de ändrade egenskapsnamnen. Om du har ett Microsoft-kundavtal är dina filter `startDate` och `endDate` i följande exempel.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Hämta stora mängder data regelbundet med exportfunktionen

Med exportfunktionen kan du schemalägga regelbundna kostnadsdatadumpar. Det här är det rekommenderade sättet att hämta icke-aggregerade kostnadsdata för organisationer vars användningsfiler är för stora för att anropa och ladda ned data på ett tillförlitligt sätt med API:et för användningsinformation. Data placeras i ett Azure Storage-konto som du väljer. Därifrån kan du läsa in data i dina egna system och dina team kan analysera dem efter behov. Information om hur du konfigurerar exporter i Azure-portalen finns i [Exportera data](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data).

## <a name="data-latency-and-rate-limits"></a>Svarstid och hastighetsbegränsningar för data

Vi rekommenderar att du anropar API:erna högst en gång per dag. Cost Management-data uppdateras var fjärde timme i och med att nya användningsdata tas emot från Azure-resursleverantörer. Mer frekventa anrop resulterar inte i ytterligare data. Det medför bara en ökad belastning. Om du vill veta mer om hur ofta data ändras och hur svarstiden hanteras kan du läsa [Förstå Cost Management-data](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Felkod 429 – Antalet anrop har överskridit hastighetsbegränsningen

För att alla Cost Management-prenumeranter ska få en smidig upplevelse har API:erna för Cost Management hastighetsbegränsningar. Om du når gränsen får du HTTP-statuskoden `429: Too many requests`. Aktuella dataflödesgränser för våra API:er:

- 30 anrop per minut – gränsen gäller per omfång, per användare eller program.
- 200 anrop per minut – gränsen gäller per klientorganisation, per användare eller program.

## <a name="next-steps"></a>Nästa steg

- [Analysera Azure-kostnader med Power BI-mallappen](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).
- [Skapa och hantera exporterade data](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) med exporter.
- Läs mer om [API:et för användningsinformation](https://docs.microsoft.com/rest/api/consumption/usageDetails).
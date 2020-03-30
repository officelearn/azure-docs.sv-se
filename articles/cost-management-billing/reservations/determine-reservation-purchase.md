---
title: Välja rätt Azure-reservation för dig
description: Den här artikeln hjälper dig att välja rätt reservation för dina behov.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1b639da3494c0527141347ca61e77980d29a59ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80135563"
---
# <a name="determine-what-reservation-to-purchase"></a>Välja rätt reservation

Alla reservationer, förutom Azure Databricks, tillämpas per timme. Reservationsköp bör göras utifrån en konsekvent basanvändning. Det finns flera sätt att avgöra vad du bör köpa, och i den här artikeln får du hjälp med att välja rätt reservation för dina behov.

Om du köper mer kapacitet än vad du historiskt sett förbrukat, leder det till en underutnyttjad reservation. Underutnyttjad kapacitet bör undvikas i möjligaste mån. Outnyttjad reserverad kapacitet överförs inte från en timme till nästa.  Användning som överskrider den reserverade kvantiteten debiteras med dyrare ”betala per användning”-priser.

## <a name="analyze-usage-data"></a>Analysera användningsdata

I följande avsnitt får du hjälp med att analysera dina dagliga användningsdata för att fastställa din normalanvändning och vilken reservation du bör köpa.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>Analysera användningen för köp av en reserverad VM-instans

Identifiera rätt VM-storlek för ditt köp. En reservation som köpts för virtuella datorer i ES-serien gäller till exempel inte för virtuella datorer i E-serien och tvärtom.

De virtuella datorerna i Promo-serien får ingen reservationsrabatt, och kan därför uteslutas från analysen.

Tillämpa följande filter på dina användningsdata för att filtrera fram kvalificerad VM-användning:

- Filtrera **MeterCategory** till **Virtual Machines**.
- Hämta **ServiceType**-information från **AdditionalInfo**. Informationen ger förslag på lämplig VM-storlek. Till exempel Standard E32.
- Använd fältet **ResourceLocation** för att fastställa användningsdatacentret.

Ignorera resurser som har mindre än 24 timmars användning per dag.

Om du vill analysera på nivån för instansstorleksfamilj kan du hämta de olika värdena för instansstorlekarna från [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv). Kombinera värdena med dina data för att utföra analysen. Mer information om flexibel instansstorlek finns i artikeln om [flexibel storlek för virtuella datorer med reserverade VM-instanser](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-usage-for-a-sql-database-reserved-instance-purchase"></a>Analysera användningen för köp av en reserverad SQL Database-instans

Reserverad kapacitet gäller vCore-beräkningspriser för SQL-databaser. Den gäller inte för DTU-baserade priser, SQL-licenskostnader eller andra kostnader än beräkning.

Tillämpa följande filter på dina användningsdata för att filtrera fram kvalificerad SQL-användning:


- Filtrera **MeterCategory** till **SQL Database**.
- Filtrera **MeterName** till **vCore**.
- Filtrera **MeterSubCategory** till alla användningsposter vars namn innehåller _Compute_.

Hämta **vCores**-värdet från **AdditionalInfo**. Det visar hur många virtuella kärnor som användes. Antalet är **vCores** multiplicerat med antalet timmar som databasen användes.

Informationen visar den konsekventa användningen för:

- Kombination av databastyp. Till exempel hanterad instans eller elastisk pool per enskild databas.
- Tjänstnivå. Till exempel generell användning eller affärskritisk.
- Generation. Till exempel Generation 5.
- Resursplats

### <a name="analysis-for-sql-data-warehouse"></a>Analys för SQL Data Warehouse

Reserverad kapacitet gäller SQL Data Warehouse DWU-användning och köps i steg om 100 DWU. Tillämpa följande filter på dina användningsdata för att filtrera fram kvalificerad SQL-användning:

- Filtrera **MeterName** till **100 DWU:er**.
- Filtrera **Meter Sub-Category** till **Compute Optimized Gen2**.

Använd fältet **Resursplats** för att avgöra SQL DW-användningen i en region.

SQL Data Warehouse-användningen kan skalas upp och ned under dagen. Prata med teamet som hanterade SQL Data Warehouse-instansen och lär dig mer om basanvändningen.

Gå till Reservationer på Azure-portalen och köp reserverad SQL Data Warehouse-kapacitet i multiplar om 100 DWU:er.

## <a name="reservation-purchase-recommendations"></a>Rekommendationer för reservationsköp

Rekommendationer för reservationsköp beräknas genom att dina användningsdata per timme under de senaste 7, 30 och 60 dagarna analyseras. Azure beräknar vad dina kostnader skulle ha varit om du hade en reservation och jämför dem med dina kostnader med användningsbaserad betalning under tidsperioden. Beräkningen utförs för all kvantitet som du använde under tidsperioden. Den kvantitet som maximerar dina besparingar rekommenderas. 

Du kanske till exempel använder 500 virtuella datorer merparten av tiden, men ibland går användningen upp till 700 virtuella datorer. I det här exemplet beräknar Azure dina besparingar för både 500 och 700 virtuella datorer. Eftersom användningen av 700 virtuella datorer är sporadisk avgör beräkningen att besparingarna maximeras med ett reservationsköp för 500 virtuella datorer och rekommendationen ges för en kvantitet på 500.

Observera följande punkter:

- Reservationsrekommendationer beräknas baserat på de priser för användning på begäran som gäller för dig.
- Rekommendationer beräknas för enskilda storlekar, inte för instansstorleksfamiljen.
- Den rekommenderade kvantiteten för ett omfång minskas samma dag som du köper reservationer för omfånget.
    - En uppdatering av rekommendationen om reservationskvantitet över alla omfång kan dock ta upp till 25 dagar. Om du till exempel köper baserat på rekommendationer om delat omfång kan det ta upp till 25 dagar att justera omfångsrekommendationerna för enskilda prenumerationer.

## <a name="recommendations-in-the-azure-portal"></a>Rekommendationer på Azure-portalen

Reservationsköp som beräknas av rekommendationsmotorn visas på fliken **Rekommenderas** på [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs). Här är en exempelbild.

![Bild som visar rekommendationer](./media/determine-reservation-purchase/select-product-ri.png)

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Rekommendationer i Cost Management Power BI-appen

Kunder som har Enterprise-avtal och Microsoft-kundavtal kan använda VM RI Coverage-rapporter (VM RI-täckning) för virtuella datorer och köprekommendationer. Täckningsrapporterna visar total användning och användning som omfattas av reserverade instanser.

1. Skaffa [Cost Management-appen](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
2. Gå till VM RI Coverage-rapporten – delat eller enskilt omfång, beroende på vilket omfång du vill köpa.
3. Välj regionen och instansstorleksfamiljen för att se användningen, RI-täckningen och köprekommendationen för det valda filtret.

## <a name="recommendations-in-azure-advisor"></a>Rekommendationer i Azure Advisor

Rekommendationer för reservationsköp finns i [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview).

- Advisor tillhandahåller endast rekommendationer som omfattas av en prenumeration.
- Advisor-rekommendationerna bygger på dina data från de senaste 30 dagarna. De planerade besparingarna gäller en reservationsperiod på tre år.
- Om du köper en reservation med delad omfattning kan det ta upp till 30 dagar innan Advisors rekommendationer slutar att visas.

## <a name="recommendations-using-apis"></a>Rekommendationer med API:er

Använd REST-API:et för [reservationsrekommendationer](/rest/api/consumption/reservationrecommendations/list) för att visa rekommendationer programmatisk.

## <a name="next-steps"></a>Nästa steg

- [Hantera Azure Reservations](manage-reserved-vm-instance.md)
- [Förstå reservationsanvändning för din prenumeration med Betala per användning-priser](understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](reserved-instance-windows-software-costs.md)
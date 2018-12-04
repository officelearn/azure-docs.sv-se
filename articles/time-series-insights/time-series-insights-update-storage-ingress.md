---
title: Lagring av data- och ingångsanspråk The Azure Time Series Insights (förhandsversion) | Microsoft Docs
description: Förstå datalagring och ingående The Azure Time Series Insights (förhandsversion)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 6635558d1b7cf664084c103e3b3b37b44c022fa6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856994"
---
# <a name="data-storage-and-ingress-in-the-azure-time-series-insights-preview"></a>Lagring av data- och ingångsanspråk i Azure Time Series Insights (förhandsversion)

Den här artikeln redogörs för ändringar för lagring av data- och ingångsanspråk från Azure Time Series Insights (förhandsversion). Den behandlar den underliggande lagringsstrukturen, filformat, och **Time Series-ID** egenskapen. Det diskuterar även den underliggande ingress-processen, dataflöde och begränsningar.

## <a name="data-storage"></a>Datalagring

När du skapar en Time Series Insights-uppdatering (**PAYG Sku**) miljö kan du skapa två resurser:

1. En Azure TSI-miljö.
1. Ett Azure storage allmänna V1-konto där data kommer att lagras.

TSI (förhandsversion) använder Azure Blob Storage med Parquet-filtypen. Azure TSI hanterar alla dataåtgärder inklusive att skapa blobbar, indexering och partitionera data i Azure Storage-kontot. Dessa blobar skapas med ett Azure Storage-konto. Att säkerställa att alla händelser kan efterfrågas på ett effektivt sätt. TSI-uppdateringen stöder Azure Storage allmänna V1 och V2 ”heta” konfigurationsalternativ.  

Som alla andra Azure Storage BLOB-objekt, kan du läsa och skriva till din Azure TSI-skapade att stödja olika integrationsscenarier.

> [!TIP]
> Det är viktigt att komma ihåg TSI prestanda kan påverkas negativt av läsning eller skrivning till dina blobar för ofta.

En översikt över Azure Blob Storage fungerar, läsa den [Storage-blobbar introduktion](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Mer information om filtypen Parquet granska [filtyper som stöds i Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Parquet-filformat

Parquet är kolumnorienterade datafilformat som utformats för:

* Samverkan
* Utrymmeseffektivitet
* Frågans effektiviteten

Azure TSI valde Parquet eftersom det ger effektiv datakomprimering och kodning scheman med förbättrad prestanda för att hantera komplexa data gruppvis.

För bättre förstå filens vilka Parquet format handlar om, gå till den [officiella Parquet-sidan](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Händelse-strukturen i Parquet

Två kopior av BLOB-objekt som skapats av Azure TSI kommer att lagras i följande format:

1. Först, en inledande kopia kommer partitioneras efter ankomsttid:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI internal suffix>.parquet`
    * BLOB Skapandetid för blobbar som partitioneras efter ankomsttid.

1. Andra, en repartitioned kopia kommer partitioneras av dynamiska grupperingen av time series-ID:

    • `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_< TSI internal suffix >.parquet`
    * Min händelsetidsstämpel i blob för blobar som har partitionerats med tidsserie-ID.

> [!NOTE]
> * `<YYYY>` Maps år.
> * `<MM>` kartor för månad.
> * `<YYYYMMDDHHMMSSfff>` mappar till fullständig tidsstämpel i millisekunder.

Azure TSI-händelser är mappade till Parquet-filens innehåll på följande sätt:

* Varje händelse som mappar till en enda rad.
* Inbyggda **tidsstämpel** kolumn med en tidsstämpel för händelsen. Den **tidsstämpel** egenskapen aldrig är null.  Standard **händelse källa Kötid** om den **tidsstämpel** egenskapen har inte angetts i händelsekällan.  **Tidsstämpel** är i UTC.  
* Alla andra egenskaper för kartan till kolumner avslutas med `_string` (sträng), `_bool` (boolesk), `_datetime` (datetime), och `_double` (dubbla) beroende på typ vlastnosti.
* Det här är den första versionen av filformatet och vi refererar till den som ***V = 1**.  Om den här funktionen utvecklas namnet på ökas därefter till **V = 2**, **V = 3**och så vidare.

## <a name="how-to-partition"></a>Så här partition

Varje Azure TSI (förhandsgranskning)-miljö måste ha en **Time Series-ID** egenskap och ett **tidsstämpel** -egenskap som unikt identifierar den. Din **Time Series-ID** fungerar som en logisk partition för dina data och innehåller Azure TSI (förhandsgranskning)-miljö med en naturlig gräns för att distribuera data över fysiska partitioner. Fysisk partition management hanteras av Azure TSI (förhandsversion) i ett Azure Storage-konto.

Azure TSI använder dynamisk partitionering för att optimera lagringsprestanda för användning och fråga genom att släppa och återskapa partitioner. TSI (förhandsversion) dynamisk partitionering algoritm strävar efter att undvika en enskild fysisk partition med data för flera olika logiska partitioner. Eller med andra ord partitionering algoritmen målet är att behålla alla data som är relaterade till en enda **Time Series-ID** måste endast finnas i Parquet-filer utan att överlagrad med andra **Time Series-ID: N**. Algoritmen för dynamisk partitionering strävar också efter att bevara den ursprungliga ordningen för händelser i en enda **Time Series-ID**.

Först när inkommande helst även data partitioneras efter den **tidsstämpel** så att en enda, logisk partition inom ett visst tidsintervall kan spridas till flera fysiska partitioner. En enda fysisk partition kan också innehålla många eller alla logiska partitioner.  På grund av storleksbegränsningar för blob, även med optimala partitionering en enda logisk partition kan uppta flera fysiska partitioner.

> [!NOTE]
> Den **tidsstämpel** värdet är meddelandet **Kötid** i din konfigurerade händelsekälla som standard.  

Om du laddar upp historiska data eller batchbearbetning av meddelanden, bör du ange den **tidsstämpel** -egenskapen i dina data som mappar till lämplig **tidsstämpel** värde som du vill lagra med dina data.  Den **tidsstämpel** egenskapen är skiftlägeskänsliga. Mer information finns att läsa den [Tidsseriemodell artikeln](./time-series-insights-update-tsm.md).

## <a name="physical-partition"></a>Fysisk partition

En fysisk partition är en blockblob som lagras i Azure Storage. Den verkliga storleken på blobarna som varierar eftersom den är beroende av push-pris, men vi förväntar oss BLOB-och är cirka 20-50 MB i storlek. På grund av det förväntar sig, TSI-teamet har valt 20 MB vara storleken att optimera frågeprestanda. Detta kan ändras med tiden baserat på filstorleken och hastighet för inkommande data.

> [!NOTE]
> * Blobbar storlek på 20MB.
> * Azure-blobar är ibland partitionera om fragmentet för bättre prestanda genom att släppas och återskapas.
> * Observera också att samma TSI data kan finnas i flera blobar.

## <a name="logical-partition"></a>Logisk partition

En logisk partition är en partition inom en fysisk partition som lagrar alla data som är associerade med ett enskilt partitionsnyckelvärde. TSI (förhandsversion) partitionerar logiskt varje blob baserat på två egenskaper:

1. **Time Series-ID** -är en partitionsnyckel för alla TSI-data i händelseströmmen och modellen.
1. **Tidsstämpel** – baserat på den första inkommande.

Azure TSI (förhandsversion) ger högpresterande frågor som baseras på dessa två egenskaper. Dessa två egenskaper ger även den effektivaste metoden för att leverera TSI data snabbt.

Det är viktigt att välja en lämplig **Time Series-ID: N**, eftersom det är en egenskap som inte kan ändras.  Se [välja Time Series-ID: N](./time-series-insights-update-how-to-id.md) för mer information.

## <a name="your-azure-storage-account"></a>Azure Storage-kontot

### <a name="storage"></a>Storage

När du skapar en TSI PAYG-miljö kan skapa du två resurser – TSI-miljö och en Azure-lagring generell användning V1-konto där data kommer att lagras. I framtiden kommer Azure Storage-konton för generell användning V1 begränsas till befintliga Azure-kunder, därför alla nya kunder kan Azure etablerar en TSI (förhandsgranskning)-miljö som standard skapar ett Azure Storage generell användning V2 ”Hot”-konto.  Vi valde att göra Azure Storage generell användning V1 standardvärdet på grund av dess samverkan, pris och prestanda.  

Azure TSI publiceras upp till två kopior av varje händelse i Azure Storage-kontot. Den ursprungliga kopian bevaras alltid för att säkerställa att du kan fråga den performantly med andra tjänster. Därför Spark, Hadoop och andra välbekanta verktyg kan enkelt användas i **Time Series-ID: N** över råa Parquet filer eftersom dessa motorer stöder grundläggande filen Namnfiltrering. Gruppera blobar per år och månad är användbar för att samla in blob lista i specifika tidsintervallet för ett anpassat jobb.  

Dessutom TSI att partitionera om Parquet-filer för att optimera för API: er för Azure TSI och nyligen repartitioned filen ska sparas.

Allmänt tillgängliga förhandsversionen lagras data på obestämd tid i Azure Storage-kontot. Möjligheten att ta bort data kommer att läggas i framtiden, så att en större grad av kontroll över hantering av gamla data.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Skriva och redigera Time Series Insights-blobar

För att säkerställa prestanda och tillgänglighet för data, inte redigera eller ta bort alla blobbar som skapats av TSD.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Få åtkomst till och export av data från Time Series Insights (förhandsversion)

Du kanske vill komma åt data som lagras i Azure TSI (förhandsversion) Explorer för att använda tillsammans med andra tjänster. Du kan till exempel vill använda dina data för rapportering i Power BI för att utföra maskininlärning med hjälp av Azure Machine Learning Studio eller i en anteckningsbok programmet Jupyter Notebooks osv.

Det finns tre allmänna vägar för att komma åt dina data:

* (Förhandsversion) för Azure TSI-Utforskaren.
* Azure TSI (förhandsversion) API: er.
* Direkt från ett Azure Storage-konto.

### <a name="from-the-time-series-insights-preview-explorer"></a>Från Utforskaren Time Series Insights (förhandsversion)

Du kan exportera data som en CSV-fil från TSI (förhandsversion) Explorer. Läs mer om den [TSI (förhandsversion) Explorer](./time-series-insights-update-explorer.md).

### <a name="from-the-time-series-insights-preview-apis"></a>Från API: er för Time Series Insights (förhandsversion)

API-slutpunkten kan nås på `/getRecorded`. Om du vill veta mer om detta API kan du läsa om [Time Series fråga](./time-series-insights-update-tsq.md).

### <a name="from-an-azure-storage-account"></a>Från ett Azure Storage-konto

1. Du måste ha läsbehörighet beviljas till kontot du kommer att använda komma åt dina TSI-data. Mer information om hur du ger läsåtkomst till Azure Blob Storage [hantera åtkomst till lagringsresurser](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

1. Mer information om direkt sätt att läsa data från Azure Blob Storage [flytta data till och från Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

1. Exportera data från ett Azure Storage-konto:

    * Kontrollera först att ditt konto har de nödvändiga kraven uppfylls om du vill exportera data. Läs [Storage importera och exportera krav](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements) för mer information.
    * Lär dig mer om andra sätt att exportera data från Azure Storage-kontot genom att besöka [Storage importera och exportera data från BLOB-objekt](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs)

### <a name="blob-storage-considerations"></a>Överväganden för lagring av BLOB

* Azure storage har läsa och skriva begränsningar baserat på hur stor användningen TSI (förhandsversion) gäller.  
* Azure TSI Private Preview ger ännu inte alla slags Parquet blob meta-store för bearbetning av externa system. Men vi undersöker det här och kan lägga till stöd i framtiden.  
* Kunder måste att läsa Azure-blobar partitioneras efter tid för att kunna bearbeta data.
* Förhandsversionen av Azure TSI privat utför dynamisk ompartitionering av blob-data för bättre prestanda. Detta åstadkoms genom att släppa och återskapa blobar. De flesta tjänster hanteras bäst med hjälp av de ursprungliga filerna.  
* Skapas kan dubbletter TSI (förhandsgranskning)-data i blobar.

### <a name="data-deletion"></a>Data raderas

Azure TSI Private Preview stöder för närvarande inte Databorttagning men ska i framtiden. Vi räknar med att ge GA, men potentiellt snabbare. Vi meddelar användare när vi har stöd för borttagning.

Ta inte bort blobbar eftersom Time Series Insights (förhandsversion) innehåller metadata om blobarna i TSI-uppdatering.

## <a name="ingress"></a>Ingress

### <a name="azure-time-series-insights-ingress-policies"></a>Azure Time Series Insights ingress-principer

Azure TSI Private Preview stöder samma händelsekällor och filtyper som det gör i dag.

Händelsekällor som stöds är:

* Azure IoT Hub
* Azure Event Hubs
  * Obs: Azure Event Hub-instanser stöder Kafka.

Filtyper som stöds är:

* JSON
  * Mer på stöds JSON-former vi kan hantera, finns i den [Time Series fråga](./time-series-insights-update-tsq.md) dokumentation.

### <a name="data-availability"></a>Tillgänglighet för data

Förhandsversionen av Azure TSI privat indexerar data med hjälp av en strategi för optimering av blob-storlek. Det innebär att data är tillgängliga att fråga när den har indexerats (som baseras på hur mycket data kommer i och på vilken hastighet). När blir allmänt tillgänglig förhandsversion, läggs logik för att söka efter nya händelser några sekunders mellanrum (som gör data tillgängliga för frågor snabbare och mer tillförlitligt).

> [!IMPORTANT]
> * Offentlig förhandsversion TSI gör data tillgängliga inom 60 sekunder det stöter på en händelsekälla.  
> * Under den privata förhandsgranskningen vi förväntar oss att se en längre period innan data görs tillgänglig.  
>   * Om det uppstår några betydande fördröjning kan du kontakta oss.

### <a name="scale"></a>Skala

Vi förväntar oss Azure TSI privat förhandsgranskning som stöder upp till 6 Mbps per miljö. Förbättrat stöd för skalning är planerade inför kommande versioner.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>Nästa steg

Läs den [Azure TSI (förhandsversion) lagring och Ingångsanspråk](./time-series-insights-update-storage-ingress.md).

Läs mer om den nya [Tidsseriemodell](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
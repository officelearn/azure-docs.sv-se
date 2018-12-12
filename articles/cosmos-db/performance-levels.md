---
title: Indragna prestandanivåer för Azure Cosmos DB
description: Läs mer om S1, S2 och S3 prestandanivåer tidigare var tillgängliga i Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27e9e8c2cb3201ea4d4522662cd49609a2163997
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865564"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Dra tillbaka S1, S2 och S3-prestandanivåer

> [!IMPORTANT] 
> Prestandanivåerna S1, S2 och S3 beskrivs i den här artikeln är dras tillbaka och är inte längre tillgängliga för nya Azure Cosmos DB-konton.
>

Den här artikeln innehåller en översikt över prestandanivåerna S1, S2 och S3 och beskriver hur de samlingar som använder dessa prestandanivåer kan vara migrerade till en enda partitionerade samlingar. När du har läst den här artikeln kommer du att kunna besvara följande frågor:

- [Varför är prestandanivåerna S1, S2 och S3 är ur drift?](#why-retired)
- [Hur enskilda partitionssamlingar och partitionerade samlingar jämfört med S1, S2 och S3-prestandanivåer?](#compare)
- [Vad behöver jag göra för att säkerställa oavbruten åtkomst med mina data?](#uninterrupted-access)
- [Hur kommer min samling ändra efter migreringen?](#collection-change)
- [Hur kommer jag ändra när jag har migrerat till enskilda partitionssamlingar?](#billing-change)
- [Vad händer om jag behöver fler än 10 GB lagringsutrymme?](#more-storage-needed)
- [Kan jag byta mellan S1, S2 och S3 prestandanivåer innan den planerade migreringen?](#change-before)
- [Hur migrerar jag från S1, S2 och S3-prestandanivåer för enskilda partitionssamlingar på egen hand?](#migrate-diy)
- [Hur kan jag påverkas om jag är en EA-kund?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Varför är S1, S2 och S3 prestanda nivåer dras tillbaka?

Prestandanivåerna S1, S2 och S3 erbjuder inte den flexibilitet som innehåller Azure Cosmos DB-Standarderbjudandet. Med S1, S2 och S3-prestandanivåer kapacitet för både dataflöde och lagring har redan ställts in och erbjöd inte elasticitet. Azure Cosmos DB erbjuder nu möjligheten att anpassa ditt dataflöde och lagring, erbjuder dig mycket större flexibilitet i din möjlighet att skala allteftersom dina behov förändras.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hur enskilda partitionssamlingar och partitionerade samlingar jämfört med S1, S2 och S3-prestandanivåer?

I följande tabell jämförs dataflöde och lagring alternativen som finns i enskilda partitionssamlingar, partitionerade samlingar och S1, S2 och S3-prestandanivåer. Här är ett exempel för regionen USA, östra 2:

|   |Partitionerad samling|Enskilda partitionssamlingar|S1|S2|S3|
|---|---|---|---|---|---|
|Maximalt dataflöde|Obegränsat|10 K RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Minsta dataflöde|2.5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Maximalt lagringsutrymme|Obegränsat|10 GB|10 GB|10 GB|10 GB|
|Pris (per månad)|Dataflöde: $6 / 100 RU/s<br><br>Lagring: $0.25/ GB|Dataflöde: $6 / 100 RU/s<br><br>Lagring: $0.25/ GB|25 USD|$50 USD|100 USD|

Är du en EA-kund? I så, fall Se [hur kan jag påverkas om jag är en EA-kund?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Vad behöver jag göra för att säkerställa oavbruten åtkomst med mina data?

Om du har en S1, S2 eller S3-samling kan du bör migrera samlingen till enskilda partitionssamlingar programmässigt [med hjälp av .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hur kommer min samling ändra efter migreringen?

Om du har en S1-samling kan du migrera dem till enskilda partitionssamlingar med 400 RU/s genomströmning. 400 RU/s är den lägsta genomströmningen som är tillgängliga med enskilda partitionssamlingar. Men kostnaden för 400 RU/s i enskilda partitionssamlingar är ungefär densamma som du betalade med S1-samling och 250 RU/s – så att du inte betalar för den extra 150 RU/s tillgängliga för dig.

Om du har en S2-samling kan du migrera dem till enskilda partitionssamlingar med 1 K RU/s. Ingen ändring ser du att din dataflödesnivåer.

Om du har en S3-samling kan du migrera dem till enskilda partitionssamlingar med 2,5 K RU/s. Ingen ändring ser du att din dataflödesnivåer.

I dessa fall, när du har migrerat samlingen kommer du att kunna anpassa dina dataflödesnivå eller skala upp och ned efter behov för att ge låg latens åtkomst till dina användare. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Hur kommer jag ändra efter att jag har migrerats till de enskilda partitionssamlingar?

Förutsatt att du har 10 S1 samlingar, 1 GB lagringsutrymme för varje i regionen östra USA och du migrera dessa 10 S1-samlingar till 10 enskilda partitionssamlingar med 400 RU/sek (lägsta nivå). Fakturan ska se ut så här om du behåller 10 enskilda partitionssamlingar för en hel månad:

![Hur S1 priser för 10 samlingar jämför till 10 samlingar med hjälp av priser för enskilda partitionssamlingar](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Vad händer om jag behöver fler än 10 GB lagringsutrymme?

Om du har en samling på S1, S2 eller S3-prestandanivå eller har en enda partition-samling som har 10 GB lagringsutrymme som är tillgängliga, du kan använda datamigreringsverktyget för Azure Cosmos DB för att migrera data till en partitionerad samling med i stort sett obegränsad lagring. Information om fördelarna med en partitionerad samling finns i [partitionering och skalning i Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Kan jag byta mellan S1, S2 och S3 prestandanivåer innan den planerade migreringen?

Endast befintliga konton med S1, S2 och S3 prestanda kan ändras och ändra nivån prestandanivåer programmässigt [med hjälp av .NET SDK](#migrate-diy). Om du ändrar från S1, S3 och S3 till enskilda partitionssamlingar kan du inte återgå till prestandanivåerna S1, S2 eller S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hur migrerar jag från S1, S2 och S3-prestandanivåer för enskilda partitionssamlingar på egen hand?

Du kan migrera från S1, S2 och S3-prestandanivåer för enskilda partitionssamlingar programmässigt [med hjälp av .NET SDK](#migrate-diy). Du kan göra detta på egen hand innan den planerade migreringen kan dra nytta av flexibla dataflöde alternativ som är tillgängliga med enskilda partitionssamlingar.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrera till enskilda partitionssamlingar med hjälp av .NET SDK

Det här avsnittet beskriver bara ändra en samling prestanda nivå med hjälp av den [SQL .NET API](sql-api-sdk-dotnet.md), men processen är liknande för våra andra SDK: er.

Här är ett kodfragment för att ändra på samlingsdataflödet till 5 000 enheter för programbegäran per sekund:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Besök [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) att visa ytterligare exempel och lär dig mer om vårt erbjudande-metoder:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Hur kan jag påverkas om jag är en EA-kund?

EA-kunder kommer att skyddas fram till slutet av avtalets aktuella priset.

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om priser och hantering av data med Azure Cosmos DB kan du utforska dessa resurser:

1.  [Partitionera data i Cosmos DB](sql-api-partition-data.md). Förstå skillnaden mellan enskild partition behållare och partitionerad behållare samt tips om hur du implementerar en partitioneringsstrategi att skala smidigt.
2.  [Priser för cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Läs mer om kostnaden för och etablera dataflöde och förbruka lagring.
3.  [Enheter för programbegäran](request-units.md). Förstå användningen av genomströmning för olika åtgärdstyper, till exempel läsa, skriva, fråga.

---
title: Dragits tillbaka Azure Cosmos DB prestandanivåer | Microsoft Docs
description: Läs mer om S1, S2 och S3 prestandanivåer som tidigare fanns i Azure Cosmos DB.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 62ef3c8988b0f11d6d8bcb69080f4e1b98a44641
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Ta bort prestandanivåer S1, S2 och S3

> [!IMPORTANT] 
> Prestandanivåer S1, S2 och S3 i den här artikeln är som har återkallats och är inte längre tillgängliga för den nya Azure Cosmos DB konton.
>

Den här artikeln innehåller en översikt över S1, S2 och S3 prestandanivåer och beskriver hur de samlingar som använder dessa prestandanivåer kommer att migreras till enskilda partitionssamlingar sent i 2017. När du har läst den här artikeln kommer du att kunna svara på följande frågor:

- [Varför är S1, S2 och S3 prestanda nivåer som har återkallats?](#why-retired)
- [Hur enskilda partitionssamlingar och partitionerade samlingar Jämför med S1, S2, prestandanivåer S3?](#compare)
- [Vad behöver jag för att säkerställa kontinuerlig tillgång till Mina data?](#uninterrupted-access)
- [Hur ändrar jag samlingen efter migreringen?](#collection-change)
- [Hur ändras efter att jag har migrerats till enskilda partitionssamlingar av min fakturering?](#billing-change)
- [Vad händer om jag behöver fler än 10 GB lagringsutrymme?](#more-storage-needed)
- [Kan jag ändra mellan S1, S2 och S3 prestandanivåer innan den planerade migreringen?](#change-before)
- [Hur vet jag om min samling har migrerats?](#when-migrated)
- [Hur jag migrera från S1, S2, S3 prestandanivåer för enskilda partitionssamlingar själv?](#migrate-diy)
- [Hur kan jag påverkas om jag en EA-kund?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Varför är S1, S2 och S3 prestanda nivåer som har återkallats?

S1, S2 och S3 prestandanivåer erbjuder inte som standard Azure Cosmos DB erbjudandet ger flexibilitet. S1, S2 S3 prestandanivåer genomströmning-och lagringskapacitet har redan angetts och erbjöd inte elasticitet. Azure Cosmos-DB erbjuder nu möjlighet att anpassa din dataflöden och lagringsutrymmen, ger mycket större flexibilitet i möjligheten att skala efter dina behov förändras.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hur enskilda partitionssamlingar och partitionerade samlingar Jämför med S1, S2, prestandanivåer S3?

I följande tabell jämförs dataflöden och lagringsutrymmen alternativen i enskilda partitionssamlingar partitionerade samlingar och S1, S2 S3 prestandanivåer. Här är ett exempel på oss östra 2 region:

|   |Partitionerad samling|Enskild partition samling|S1|S2|S3|
|---|---|---|---|---|---|
|Maximalt dataflöde|Obegränsat|10 K RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Minsta dataflöde|2.5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Maximalt lagringsutrymme|Obegränsat|10 GB|10 GB|10 GB|10 GB|
|Pris (månad)|Genomströmning: $6 / 100 RU/s<br><br>Lagring: $ 0,25/GB|Genomströmning: $6 / 100 RU/s<br><br>Lagring: $ 0,25/GB|$25 USD|$50 USD|$100 USD|

Är du en kund med EA? I så fall, finns [hur får jag påverkas om jag en EA-kund?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Vad behöver jag för att säkerställa kontinuerlig tillgång till Mina data?

Något, Cosmos DB hanterar du migreringen. Om du har en S1, S2 och S3 samling kommer aktuella samlingen att migreras till en enda partition samling sent i 2017. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hur ändrar jag samlingen efter migreringen?

Om du har en S1 samling migreras till en enda partition samling med 400 RU/s genomströmning. 400 RU/s är den lägsta genomflödet som är tillgängliga med enskilda partitionssamlingar. Dock kostnaden för 400 RU/s i en enda partition samling är ungefär densamma som du betalar med S1 samlingen och 250 RU/s – så att du inte betalar för extra 150 RU/s tillgängliga.

Om du har en S2 samling migreras till en enda partition samling med 1 K RU/s. Ingen ändring ser du till genomflödet-nivå.

Om du har en S3 samling migreras till en enda partition samling med 2,5 K RU/s. Ingen ändring ser du till genomflödet-nivå.

I dessa fall migrerat samlingen kommer du att kunna anpassa genomströmning-nivå eller skala den upp eller ned efter behov för att ge låg latens åtkomst till dina användare. Om du vill ändra dataflödesnivån när din samling har migrerats, helt enkelt öppna Cosmos-DB-konto i Azure-portalen, klickar du på skala, Välj din samling och sedan justera dataflöde, som visas i följande skärmbild:

![Så här skalar genomflöde i Azure-portalen](./media/performance-levels/portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>Hur ändras efter att jag har migrerats till enskilda partitionssamlingar av min fakturering?

Om du har 10 S1 samlingar, 1 GB lagringsutrymme i region oss Öst och du migrerar dessa 10 S1 samlingar till 10 enskilda partitionssamlingar på 400 RU/s (lägsta nivå). Fakturan ska se ut så här om du behåller 10 enskilda partitionssamlingar för en hel månad:

![Hur S1 priser för 10 samlingar jämför till 10 samlingar med priser för en enskild partition samling](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Vad händer om jag behöver fler än 10 GB lagringsutrymme?

Om du har en samling med en S1, S2 och S3 prestandanivå eller ha en enda partition samling, 10 GB tillgängligt lagringsutrymme som alla har kan du använda Cosmos-Migreringsverktyget DB Data för att migrera dina data till en partitionerad samling med praktiskt taget obegränsade lagring. Information om fördelarna med en partitionerad samling finns [partitionering och skalning i Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Kan jag ändra mellan S1, S2 och S3 prestandanivåer innan den planerade migreringen?

Endast befintliga konton med S1, S2 och S3 prestanda kommer att kunna ändra och ändra nivån prestandanivåer via portalen eller programmässigt. Om du ändrar från S1, S3 eller S3 till en enda partition samling, kan du återgå till prestandanivåer S1, S2 och S3.

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>Hur vet jag om min samling har migrerats?

Migreringen sker på sent i 2017. Om du har en samling som använder S1 S2 eller S3 prestandanivåer, Cosmos-DB-teamet kommer att kontakta dig via e-post innan migreringen sker. När migreringen är klar visas den Azure-portalen att samlingen använder standardprisnivån.

![Hur du kontrollerar din samling har migrerats till Standard prisnivån](./media/performance-levels/portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hur jag migrera från S1, S2, S3 prestandanivåer för enskilda partitionssamlingar själv?

Du kan migrera från prestandanivåer S1, S2 och S3 till enskilda partitionssamlingar med Azure-portalen eller programmässigt. Du kan göra detta på egen hand innan den planerade migreringen att dra nytta av flexibla genomströmning-alternativen med enskilda partitionssamlingar eller kommer vi att migrera samlingar som du sen i 2017.

**Att migrera till enskilda partitionssamlingar med Azure-portalen**

1. I den [ **Azure-portalen**](https://portal.azure.com), klickar du på **Azure Cosmos DB**, Välj Cosmos-DB-konto för att ändra. 
 
    Om **Azure Cosmos DB** är inte i Jumpbar klickar du på >, bläddra till **databaser**väljer **Azure Cosmos DB**, och välj sedan kontot.  

2. På menyn resurs under **behållare**, klickar du på **skala**, markera samlingen för att ändra listrutan och klicka sedan på **prisnivån**. Konton med hjälp av fördefinierade genomströmning har en prisnivå för S1, S2 och S3.  I den **Välj din prisnivå** klickar du på **Standard** ändra till användardefinierade genomflöde och klicka sedan på **Välj** att spara ändringen.

    ![Skärmbild av sidan visar var du kan ändra värdet för dataflöde](./media/performance-levels/change-performance-set-thoughput.png)

3. I den **skala** sidan den **prisnivån** ändras till **Standard** och **genomströmning (RU/s)** visas med en standard värdet för 400. Ange dataflöde mellan 400 och 10 000 [programbegäran](request-units.md)/second (RU/s). Den **uppskattad Månadsfaktura** längst ned på sidan uppdateringar automatiskt för att ge en uppskattning av månadskostnaden. 

    >[!IMPORTANT] 
    > När du sparar ändringarna och gå till den Standardprisnivån kan inte du återställa prestandanivåer S1, S2 och S3.

4. Klicka på **spara** att spara ändringarna.

    Om du anser att du behöver mer genomströmning (större än 10 000 RU/s) eller mer lagringsutrymme (större än 10 GB) kan du skapa en partitionerad samling. Om du vill migrera en enskild partition samling till en partitionerad samling finns [migrera från en enskild partition till partitionerade samlingar](sql-api-partition-data.md#migrating-from-single-partition).

    > [!NOTE]
    > Ändra från S1, S2 och S3 till Standard kan det ta upp till två minuter.
    > 
    > 

**Att migrera till enskilda partitionssamlingar med .NET SDK**

Ett annat alternativ för att ändra din samlingar prestandanivåer är via Azure Cosmos DB SDK. Det här avsnittet beskriver bara ändra prestanda för en samling nivå med hjälp av den [SQL .NET API](sql-api-sdk-dotnet.md), men processen är liknande för våra andra SDK: er.

Här är ett kodfragment för att ändra samlingen genomströmning till 5 000 frågeenheter per sekund:
    
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

Besök [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) att visa ytterligare exempel och lär dig mer om våra erbjudanden metoder:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Hur kan jag påverkas om jag en EA-kund?

EA-kunder kommer att pris skyddade till slutet av avtalets aktuella.

## <a name="next-steps"></a>Nästa steg
Mer information om priser och hantera data med Azure Cosmos DB utforska dessa resurser:

1.  [Partitionering data i Cosmos DB](sql-api-partition-data.md). Förstå skillnaden mellan enskild partition behållare och partitionerade behållare samt tips om hur du implementerar en partitioneringsstrategi att sömlöst skala.
2.  [Priser för cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Läs mer om kostnaden för etablering genomflöde och använda lagring.
3.  [Enheter för programbegäran](request-units.md). Förstå förbrukningen av genomströmning för olika åtgärdstyper, till exempel läsa, skriva frågan.

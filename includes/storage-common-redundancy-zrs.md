---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 86c301748b58e7642df9a738c70b4fe70be3310b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400016"
---
Zonredundant lagring (ZRS) replikerar dina data synkront i tre lagringskluster i en enda region. Varje lagringskluster är fysiskt avgränsade från de andra och ligger i sin egen tillgänglighetszon (AZ). Varje tillgänglighetszon och ZRS-klustret i denna är autonoma med olika verktyg och nätverksfunktioner.

Lagra data i ett ZRS-konto säkerställer att du kan få åtkomst till och hantera dina data i händelse av att en zon blir otillgänglig. ZRS ger utmärkt prestanda och låg fördröjning. ZRS ger samma [skalbarhetsmål](../articles/storage/common/storage-scalability-targets.md) som [lokalt redundant lagring (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Överväg ZRS för scenarier som kräver stark konsekvens, stark hållbarhet och hög tillgänglighet, även om ett avbrott eller en naturkatastrof återger ett zonindelad Datacenter inte tillgänglig. ZRS ger hållbarhet för lagringsobjekt på minst 99,9999999999% (12 9) under ett givet år.

Läs mer om tillgänglighetszoner [Tillgänglighetszoner översikt](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Supporttäckning och regional tillgänglighet
ZRS stöder för närvarande standard [generell användning v2 (GPv2)](../articles/storage/common/storage-account-options.md#general-purpose-v2-accounts) kontotyper. ZRS är tillgänglig för blockblob-objekt, sidblob-disk, filer, tabeller och köer. Dessutom alla dina [Lagringsanalys](../articles/storage/common/storage-analytics.md) loggar och [Lagringsmått](../articles/storage/common/storage-enable-and-view-metrics.md)

ZRS är allmänt tillgängligt i följande regioner:

- USA, östra 2
- USA, västra 2
- USA, centrala
- Norra Europa
- Västeuropa
- Frankrike, centrala
- Sydostasien

Microsoft fortsätter att aktivera ZRS i ytterligare Azure-regioner. Kontrollera den [tjänstuppdateringar för Azure](https://azure.microsoft.com/updates/) regelbundet för information om nya regioner.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Vad händer när en zon blir otillgänglig?

Dina data förblir återhämtas om en zon blir otillgänglig. Microsoft rekommenderar att du fortsätter att följa metoder för hantering av, till exempel implementera principer för återförsök med exponentiell backoff tillfälliga fel. När en zon är otillgänglig, utför Azure uppdateringar, till exempel DNS-repointing. De här uppdateringarna kan påverka ditt program om du får åtkomst till dina data innan de har slutförts.

ZRS kan inte skydda dina data mot ett regionalt haveri där flera zoner permanent som påverkas. ZRS ger i stället återhämtning för dina data om det blir tillfälligt otillgänglig. Microsoft rekommenderar för skydd mot regionala problem med hjälp av geo-redundant lagring (GRS). Läs mer om GRS [Geo-redundant lagring (GRS): tvärregional replikering för Azure Storage](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Konvertera till ZRS-replikering
Du kan idag använda Azure portal eller API: T för Storage Resource Provider för att ändra typ av lagringsredundans för ditt konto, så länge som du migrerar till eller från LRS, GRS och RA-GRS. Med ZRS, men är migreringen inte som intuitivt eftersom den omfattar fysiska dataförflyttning från en enda lagringsstämpeln till flera stämplar inom en region. 

Du har två huvudsakliga alternativ för migrering till eller från ZRS. Du kan manuellt kopiera eller flytta data till ett nytt ZRS-konto från ditt befintliga konto. Du kan också begära en Direktmigrering. Microsoft rekommenderar att du utför en manuell migrering eftersom det finns ingen garanti om när en Direktmigrering slutförs. En manuell migrering väg ger bättre flexibilitet än en Direktmigrering gör och du har kontroll över tidpunkten för migreringen.

Om du vill utföra en manuell migrering, har du en rad alternativ:
- Använd befintliga verktyg som AzCopy, lagrings-SDK, verktyg för tillförlitlig från tredje part, osv.
- Om du är bekant med Hadoop- eller HDInsight, kan du bifoga både källa och mål (ZRS)-konto till ditt kluster och använda något som DistCp till mycket parallellisera kopieringsprocessen data
- Skapa dina egna verktyg för att använda en version av SDK: er för storage

Om en manuell migrering leder till vissa driftstopp i programmet men det inte går att absorbera som från din sida, tillhandahåller Microsoft ett alternativ för Direktmigrering. En Direktmigrering är en plats-migrering där du kan fortsätta att använda ett befintligt lagringskonto medan dina data migreras mellan käll- och storage-stämplar. Under migreringen, kommer du fortfarande har samma nivå av hållbarhet och tillgänglighet som du brukar göra.

Direktmigrering kommer dock vissa begränsningar. De listas nedan.

- Microsoft tar upp din begäran om Direktmigrering utan dröjsmål, finns men det ingen garanti avseende när migreringen slutförs. Om du behöver dina data finnas i ZRS inom en viss tid, bör du göra en manuell migrering. Allmänt, Ju mer data du har i ditt konto, desto längre tid tar det för att migrera data. 
- Du kan bara utföra en Direktmigrering från ett konto med LRS eller GRS-replikering. Om ditt konto använder RA-GRS, måste du först migrerar till en av de här typerna av replikering innan du fortsätter. Det här mellanliggande steget säkerställer att den sekundära skrivskyddade slutpunkten som RA-GRS ger tas bort före migreringen.
- Ditt konto måste innehålla data.
- Endast intra-region migreringar stöds. Om du vill migrera dina data till ett ZRS-konto finns i en region som är annorlunda än källkontot, måste du utföra en manuell migrering.
- Endast standard storage-kontotyper stöds. Du kan inte migrera från ett premium storage-konto.

Direktmigrering begäranden går via supporten för Azure-portalen. Från portalen kan välja du storage-konto som du vill konvertera till ZRS.
1. Klicka på **ny supportbegäran**
2. Kontrollera grunderna. Klicka på **Nästa**. 
3. På den **problemet** avsnittet 
    - Lämna allvarlighetsgrad som – är.
    - Problemtyp = **datamigrering**
    - Kategori = **migrera till ZRS inom en region**
    - Rubrik = **ZRS konto migrering** (eller något beskrivande)
    - Information om = jag skulle vilja migrera till ZRS från [LRS, GRS] i regionen ___. 
4. Klicka på **Nästa**.
5. Kontrollera att den kontaktinformation är korrekt på bladet kontaktinformation.
6. Klicka på **skicka**.

Supporttekniker kommer sedan att vi kontaktar dig. Den personen kommer att kunna tillhandahålla den hjälp du behöver. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS Classic: Ett äldre alternativ för block BLOB-redundans
> [!NOTE]
> ZRS Classic-konton är tänkta att utfasning och nödvändiga migrering på den 31 mars 2021. Microsoft skickar information till ZRS Classic kunder innan utfasning. Microsoft planerar att tillhandahålla en process för automatisk migrering från ZRS Classic till ZRS i framtiden.

>[!NOTE]
> När ZRS är [allmänt tillgänglig](#support-coverage-and-regional-availability) i en region, kommer du inte längre att kunna skapa ett ZRS Classic-konto från portalen i den samma regionen. Men kan du fortfarande skapa en på annat sätt som Microsoft PowerShell och Azure CLI, det vill säga tills ZRS Classic är inaktuell.

ZRS Classic replikerar data asynkront mellan datacenter i en eller två regioner. En replik kanske inte är tillgänglig om inte Microsoft initierar sekundär redundans. ZRS Classic är endast tillgänglig för **blockblobbar** i [general-purpose V1 (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1-accounts) storage-konton. Ett ZRS Classic-konto går inte att konvertera till eller från LRS eller GRS, och har inte kapacitet för mått eller loggning.

ZRS Classic-konton kan inte konverteras till eller från LRS, GRS eller RA-GRS. ZRS Classic-konton stöder inte heller mått eller loggning.

Om du vill migrera manuellt ZRS-kontodata till eller från ett LRS, ZRS Classic, GRS eller RA-GRS-konto, använder du AzCopy, Azure Storage Explorer, Azure PowerShell eller Azure CLI. Du kan också skapa egna migreringslösning med någon av Azure Storage-klientbibliotek.

---
title: Bygga väldigt tillgängliga program för Azure Storage på zonredundant lagring (ZRS) | Microsoft Docs
description: Zonredundant lagring (ZRS) erbjuder ett enkelt sätt att bygga väldigt tillgängliga program. ZRS skyddar mot maskinvarufel i datacentret och mot vissa regionala problem.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: 0a4ebefd89414fa62ca69f2f9cc726f471694bee
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733626"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Zonredundant lagring (ZRS): Azure Storage-program med hög tillgänglighet
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Supporttäckning och regional tillgänglighet
ZRS stöder för närvarande standard generell användning v2-kontotyper. Mer information om lagringskontotyper finns i [översikt över Azure storage-konton](storage-account-overview.md).

ZRS är tillgänglig för blockblob-objekt, sidblob-disk, filer, tabeller och köer.

ZRS är allmänt tillgängligt i följande regioner:

- USA, Östra
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

ZRS kan inte skydda dina data mot ett regionalt haveri där flera zoner permanent som påverkas. ZRS ger i stället återhämtning för dina data om det blir tillfälligt otillgänglig. Microsoft rekommenderar för skydd mot regionala problem med hjälp av geo-redundant lagring (GRS). Läs mer om GRS [Geo-redundant lagring (GRS): tvärregional replikering för Azure Storage](storage-redundancy-grs.md).

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
> Microsoft avverka och migrera ZRS Classic-konton på den 31 mars 2021. Mer information ges till ZRS Classic kunder innan utfasningen. 
>
> När ZRS blir [allmänt tillgänglig](#support-coverage-and-regional-availability) i en region kunder kommer inte längre att kunna skapa ZRS Classic-konton från portalen i den regionen. Använda Microsoft PowerShell och Azure CLI för att skapa ZRS Classic-konton stöds förrän ZRS Classic är inaktuell.

ZRS Classic replikerar data asynkront mellan datacenter i en eller två regioner. En replik kanske inte är tillgänglig om inte Microsoft initierar sekundär redundans. Ett ZRS Classic-konto går inte att konvertera till eller från LRS eller GRS, och har inte kapacitet för mått eller loggning.

ZRS Classic är endast tillgänglig för **blockblobbar** i allmänna V1 (GPv1)-konton. Mer information om lagringskonton finns i [översikt över Azure storage-konton](storage-account-overview.md).

ZRS Classic-konton kan inte konverteras till eller från LRS, GRS eller RA-GRS. ZRS Classic-konton stöder inte heller mått eller loggning.

Om du vill migrera manuellt ZRS-kontodata till eller från ett LRS, ZRS Classic, GRS eller RA-GRS-konto, använder du AzCopy, Azure Storage Explorer, Azure PowerShell eller Azure CLI. Du kan också skapa egna migreringslösning med någon av Azure Storage-klientbibliotek.

## <a name="see-also"></a>Se också
- [Azure Storage-replikering](storage-redundancy.md)
- [Lokalt redundant lagring (LRS): låg kostnad dataredundans för Azure Storage](storage-redundancy-lrs.md)
- [GEO-redundant lagring (GRS): tvärregional replikering för Azure Storage](storage-redundancy-grs.md)
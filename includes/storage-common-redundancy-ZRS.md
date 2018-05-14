---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: 0347d6ca951b75c385b138487f58b85a809c6805
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
Zonen Redundant lagring (ZRS) replikerar synkront data mellan tre (3) lagringskluster i en region. Varje lagringsklustret separeras fysiskt från de andra och finns i en egen tillgänglighet zon (AZ). Varje zon för tillgänglighet och ZRS-kluster i denna är självständiga, med separata verktyg och nätverksfunktioner.

Lagra data i ett konto för ZRS garanterar att du kommer att använda och hantera dina data i händelse av att en zon blir otillgänglig. ZRS ger utmärkt prestanda och mycket låg latens. I själva verket ZRS har samma [skalbarhetsmål](../articles/storage/common/storage-scalability-targets.md) som LRS.

Överväg att ZRS för scenarier som kräver stark konsekvens, starkt hållbarhet och hög tillgänglighet, även om ett avbrott eller en naturkatastrof återgivningar zonal Datacenter inte tillgänglig. ZRS ger hållbarhet för lagringsobjekt minst 99.9999999999% (12 9's) över ett visst år.

Mer information om tillgänglighet zoner finns [tillgänglighet zoner översikt](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Stöd för täckning och regional tillgänglighet
ZRS stöder för närvarande [ **standard, generell v2 (GPv2)** ](../articles/storage/common/storage-account-options.md#general-purpose-v2) kontotyp. ZRS är tillgängligt för blockblobbar, sidblobbar-disk, filer, tabeller och köer. Dessutom alla dina [Storage Analytics](../articles/storage/common/storage-analytics.md) loggar och [Storage-mätvärden](../articles/storage/common/storage-enable-and-view-metrics.md)

ZRS är **allmänt tillgänglig** inom följande områden:

- Östra USA 2
- Centrala USA
- Norra Europa
- Västeuropa
- Frankrike, centrala
- Sydostasien

Microsoft fortsätter att aktivera ZRS i andra Azure-regioner och kommer att uppdatera den här listan när detta sker. Vi kommer också att göra sådana meddelanden via standard kanaler som den [Azure Service uppdaterar](https://azure.microsoft.com/updates/) sidan eller e-postmeddelanden Azure-prenumeration ägare och administratörer.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Vad händer när en zon blir otillgänglig?

Dina data förblir flexibel om en zon blir otillgänglig. Microsoft rekommenderar att du fortsätter att följa metoder för att tillfälligt fel hantering, till exempel implementera principer för återförsök med exponentiell inte. När en zon inte är tillgänglig åtar uppdateringar, till exempel DNS-repointing i Azure. Dessa uppdateringar kan påverka ditt program om du ansluter till dina data innan de har slutförts.

ZRS kan inte skydda dina data mot en regionala katastrof där flera zoner permanent påverkas. ZRS ger i stället återhämtning för dina data när det gäller temporala inte finns. För skydd mot regionala katastrofer Microsoft rekommenderar att du använder [Geo-redundant lagring (GRS): mellan regionala replikering för Azure Storage](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Konvertera till ZRS-replikering
Idag är det är enkelt att ändra mellan LRS-, GRS- och RA-GRS - du använda portalen eller API. Med ZRS, men är det inte som intuitivt eftersom den omfattar fysiska data flyttas från en enda lagringsstämpel till flera stämplar i en region. Därmed kan du har två primära alternativ - manuellt kopiera/flytta data till ett nytt konto ZRS från ditt befintliga konto eller be en Direktmigrering. Vi rekommenderar starkt att du utför en manuell migrering eftersom vi kan inte garantera att när en Direktmigrering ska slutföras. Det finns många faktorer som påverkar slutförandet av ett migreringsjobb direkt eller indirekt. 

Om du vill utföra en manuell migrering, har du en mängd alternativ:
- Använd befintliga verktygsuppsättning som AzCopy, lagrings-SDK, tillförlitlig tredjepartsverktyg osv.
- Om du är bekant med Hadoop eller HDInsight, kan du bifoga både källa och mål (ZRS) kontot till klustret och använda något som liknar DistCp att massivt parallelize kopia av data
- Skapa din egen verktygsuppsättning som utnyttjar en lagrings-SDK-varianten

Som nämnts tidigare, rekommenderar vi att du går vägen manuell migrering eftersom den ger dig större flexibilitet än en Direktmigrering gör. Du finns också i fullständig kontroll över när migreringen sker.

Om dock en manuell migrering medför några driftavbrott och det inte går att upptar som på din slutpunkt, ger vi ett alternativ för Direktmigrering. En Direktmigrering är en Direktmigrering som gör att du kan fortsätta använda ditt befintliga lagringskonto när data flyttas mellan käll- och storage-stämplar. Under migreringen, kommer du fortfarande ha samma nivå av hållbarhet och tillgänglighet SLA som du brukar göra.

Direktmigrering kommer dock med vissa begränsningar. De anges nedan.

- Medan vi att behandla din begäran om Direktmigrering omedelbart, vi inte garantera när migreringen kommer att slutföra. Om du behöver dina data i ZRS genom att en viss tid, bör du göra en manuell migrering. Vanligtvis har mer data i ditt konto, desto längre tid det tar för att migrera data. 
- Du kan bara direktmigrera från ett konto med LRS- och GRS replikering. Om du har RA-GRS måste först ändra till en av dessa typer av replikering innan du fortsätter. Det här mellanliggande steget säkerställer att sekundär skrivskyddad slutpunkt som RA-GRS tillhandahåller är tas bort när du är klar.
- Ditt konto måste vara tomt.
- Endast intra-region migreringar stöds. Om du vill migrera dina data till ett ZRS-konto finns i en region som skiljer sig källkonto måste du utföra en manuell migrering.
- Standardlagring endast kontotyper. Du kan inte migrera från ett premiumlagringskonto.

Direktmigrering begäranden gå igenom stöd för Azure-portalen. Storage-konto som du vill konvertera till ZRS välja portal kan du.
1. Klicka på **ny supportbegäran**
2. Kontrollera grunderna. Klicka på **Nästa**. 
3. På den **problemet** avsnittet 
    - Lämna allvarlighetsgrad som-är.
    - Problemtyp = **datamigrering**
    - Kategori = **migrera till ZRS inom en region**
    - Rubrik = **ZRS konto migrering** (eller något beskrivande)
    - Information = jag vill migrera till ZRS från [LRS GRS] i ___ region. 
4. Klicka på **Nästa**.
5. Kontrollera att den kontaktinformation är korrekt i bladet kontaktinformation.
6. Klicka på **skicka**.

Supportpersonal kommer sedan att har kontakt med du. Den personen som kommer att kunna ge någon hjälp du kan behöva. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS klassisk: Ett äldre alternativ för redundans för block-BLOB
> [!NOTE]
> ZRS klassiska konton planeras för nödvändiga migrering 31 mars 2021 och utfasningen. Microsoft skickar mer information till den klassiska ZRS kunder innan utfasningen. Microsoft planerar att tillhandahålla en process för automatisk migrering från den klassiska ZRS till ZRS i framtiden.

>[!NOTE]
> När ZRS är [allmänt tillgänglig](#support-coverage-and-regional-availability) i en region kommer du inte längre att kunna skapa ett ZRS klassiska konto från portalen i samma regionen. Men kan du fortfarande skapa en på annat sätt som Microsoft PowerShell och Azure CLI, som är tills ZRS klassiska är föråldrad.

ZRS klassiska replikerar data asynkront mellan datacenter inom en till två regioner. En replik kanske inte är tillgänglig om inte Microsoft initierar sekundär redundans. ZRS klassiska är bara tillgängligt för **blockblobbar** i [allmänna V1 (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1) storage-konton. Ett ZRS Classic-konto går inte att konvertera till eller från LRS eller GRS, och har inte kapacitet för mått eller loggning.

ZRS klassiska konton kan inte konverteras till eller från LRS-, GRS- eller RA-GRS. ZRS klassiska konton stöder inte heller mått eller loggning.

Om du vill migrera ZRS kontodata manuellt till eller från ett LRS-, ZRS klassiska, GRS eller RA-GRS-konto, Använd AzCopy, Azure Lagringsutforskaren, Azure PowerShell eller Azure CLI. Du kan också skapa din egen migreringslösning med en Azure Storage-klientbibliotek för.

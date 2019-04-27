---
title: Bygga väldigt tillgängliga program för Azure Storage på zonredundant lagring (ZRS) | Microsoft Docs
description: Zonredundant lagring (ZRS) erbjuder ett enkelt sätt att bygga väldigt tillgängliga program. ZRS skyddar mot maskinvarufel i datacentret och mot vissa regionala problem.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.subservice: common
ms.openlocfilehash: ab3984b29b3bdfac7599c68c14bd6cc5b671cdf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731420"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Zonredundant lagring (ZRS): Azure Storage-program med hög tillgänglighet
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Supporttäckning och regional tillgänglighet
ZRS stöder för närvarande standard generell användning v2-kontotyper. Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md).

ZRS är tillgänglig för blockblob-objekt, sidblob-disk, filer, tabeller och köer.

ZRS är allmänt tillgängligt i följande regioner:

- Sydostasien
- Västeuropa
- Nordeuropa
- Frankrike, centrala
- Östra Japan
- Storbritannien, södra
- USA, Östra
- USA, östra 2
- USA, västra 2
- USA, centrala

Microsoft fortsätter att aktivera ZRS i ytterligare Azure-regioner. Kontrollera den [tjänstuppdateringar för Azure](https://azure.microsoft.com/updates/) regelbundet för information om nya regioner.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Vad händer när en zon blir otillgänglig?
Dina data är fortfarande tillgängliga för både läsning och skrivning operations även om en zon blir otillgänglig. Microsoft rekommenderar att du fortsätter att följa metoder för hantering av tillfälliga fel. Dessa omfattar implementera principer för återförsök med exponentiell backoff.

När en zon är otillgänglig, utför Azure uppdateringar, till exempel DNS-repointing. De här uppdateringarna kan påverka ditt program om du får åtkomst till dina data innan uppdateringarna har slutförts.

ZRS kan inte skydda dina data mot ett regionalt haveri där flera zoner permanent som påverkas. ZRS ger i stället återhämtning för dina data om det blir tillfälligt otillgänglig. Microsoft rekommenderar för skydd mot regionala problem med hjälp av geo-redundant lagring (GRS). Läs mer om GRS [Geo-redundant lagring (GRS): Tvärregional replikering för Azure Storage](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Konvertera till ZRS-replikering
Det är enkelt att migrera till eller från LRS, GRS och RA-GRS. Använd Azure portal eller API: T för Storage Resource Provider för att ändra typ av lagringsredundans för ditt konto. Azure replikeras sedan dina data i enlighet med detta. 

Migrera data till eller från ZRS kräver en egen strategi. ZRS-migrering innebär att fysiskt flytta data från en enda lagringsstämpeln till flera stämplar inom en region.

Det finns två huvudsakliga alternativ för migrering till ZRS: 

- Manuellt kopiera eller flytta data till ett nytt ZRS-konto från ett befintligt konto.
- Begär en Direktmigrering.

Microsoft rekommenderar att du utför en manuell migrering. En manuell migrering ger bättre flexibilitet än en Direktmigrering. Med en manuell migrering är kontroll över tiden.

Om du vill utföra en manuell migrering, har du alternativ:
- Använd befintliga verktyg som AzCopy, Azure Storage-klientbibliotek eller tillförlitlig tredjepartsverktyg.
- Om du är bekant med Hadoop- eller HDInsight, bifoga både källa och mål (ZRS)-konto till ditt kluster. Sedan kan parallellisera kopieringsprocessen data med ett verktyg som DistCp.
- Skapa dina egna verktyg med någon av Azure Storage-klientbibliotek.

En manuell migrering kan resultera i driftstopp i programmet. Om programmet kräver hög tillgänglighet, tillhandahåller Microsoft även ett alternativ för Direktmigrering. En Direktmigrering är en migrering på plats. 

Du kan använda ditt lagringskonto medan dina data migreras mellan käll- och storage-stämplar under en Direktmigrering. Under migreringsprocessen har samma nivå av hållbarhet och tillgänglighet serviceavtal som du vanligtvis utför.

Tänk på följande begränsningar för Direktmigrering:

- Microsoft hanterar din förfrågan om Direktmigrering utan dröjsmål, finns men det ingen garanti om när en Direktmigrering slutförs. Om du behöver dina data migreras till ZRS före ett visst datum, så Microsoft rekommenderar att du utför en manuell migrering i stället. Allmänt, Ju mer data du har i ditt konto, desto längre tid det tar för att migrera data. 
- Direktmigrering stöds endast för lagringskonton som använder LRS eller GRS för replikering. Om ditt konto använder RA-GRS, måste du först ändra replikering kontotyp till LRS eller GRS innan du fortsätter. Den här mellanliggande steg tar du bort den sekundära skrivskyddade slutpunkten som tillhandahålls av RA-GRS före migreringen.
- Ditt konto måste innehålla data.
- Du kan bara migrera data inom samma region. Om du vill migrera dina data till ett ZRS-konto finns i en region som är annorlunda än källkontot, måste du utföra en manuell migrering.
- Endast standard storage-kontotyper stöd för Direktmigrering. Premium storage-konton måste migreras manuellt.
- Direktmigrering från ZRS LRS, GRS eller RA-GRS stöds inte. Du måste manuellt flytta data till ett nytt eller ett befintligt lagringskonto.

Du kan begära Direktmigrering via den [Support för Azure-portalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Välj lagringskontot som du vill konvertera till ZRS i portalen.
1. Välj **ny supportbegäran**
2. Slutför den **grunderna** baserat på din kontoinformation. I den **Service** väljer **Lagringskontohantering** och den resurs du vill konvertera till ZRS. 
3. Välj **Nästa**. 
4. Ange följande värden i **problemet** avsnittet: 
    - **Allvarlighetsgrad**: Lämna standardvärdet-är.
    - **Problemtyp**: Välj **datamigrering**.
    - **Kategori**: Välj **migrera till ZRS inom en region**.
    - **Rubrik**: Ange en beskrivande rubrik, till exempel **ZRS konto migrering**.
    - **Information om**: Ange ytterligare information finns i den **information** box, till exempel jag skulle vilja migrera till ZRS från [LRS, GRS] i den \_ \_ region. 
5. Välj **Nästa**.
6. Kontrollera att kontaktinformationen är korrekt på den **kontaktinformation** bladet.
7. Välj **Skapa**.

Supporttekniker kommer att kontakta dig och ge någon hjälp du behöver.

## <a name="live-migration-to-zrs-faq"></a>Direktmigrering ZRS vanliga frågor och svar

**Ska jag planera för någon stilleståndstid under migreringen?**

Det finns inga driftstopp som orsakas av migreringen. Du kan fortsätta ditt lagringskonto medan dina data migreras mellan käll- och storage-stämplar under en Direktmigrering. Under migreringsprocessen har samma nivå av hållbarhet och tillgänglighet serviceavtal som du vanligtvis utför.

**Finns det någon förlust av data som är associerade med migreringen?**

Det finns inga data går förlorade som är associerade med migreringen. Under migreringsprocessen har samma nivå av hållbarhet och tillgänglighet serviceavtal som du vanligtvis utför.

**Är alla uppdateringar som krävs för att programmen när migreringen är klar?**

När migreringen är klar ändras replikeringstyp av konto till ”zonredundant lagring (ZRS)”. Tjänstslutpunkter kan komma åt SAS-nycklar och andra konto konfigurationsalternativ som förblir oförändrade och intakta.

**Kan jag begära en Direktmigrering av min general-purpose v1-konton till ZRS?**

ZRS stöder endast gpv2-konton så innan du skickar en begäran för en Direktmigrering till ZRS ser du till att uppgradera dina konton till gpv2. Se [översikt över Azure storage-konton](https://docs.microsoft.com/azure/storage/common/storage-account-overview) och [uppgradera till ett gpv2-lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) för mer information.

**Kan jag begära en Direktmigrering av min läsåtkomst till geografiskt redundant lagring (RA-GRS) eller till ZRS-konton?**

Innan du skickar en begäran för en Direktmigrering till ZRS ser du till ditt program eller workload(s) inte längre behöver åtkomst till den sekundära skrivskyddade slutpunkten och ändra replikering av dina lagringskonton till geo-redundant lagring (GRS). Se [ändra replikeringsstrategi](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) för mer information.

**Kan jag begära en Direktmigrering av min lagringskonton till ZRS till en annan region?**

Om du vill migrera dina data till ett ZRS-konto finns i en region som skiljer sig från regionen som källkontot, måste du utföra en manuell migrering.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS Classic: Ett äldre alternativ för block BLOB-redundans
> [!NOTE]
> Microsoft avverka och migrera ZRS Classic-konton på den 31 mars 2021. Mer information ges till ZRS Classic kunder innan utfasningen. 
>
> När ZRS blir [allmänt tillgänglig](#support-coverage-and-regional-availability) i en region kunder kommer inte att kunna skapa ZRS Classic-konton från portalen i den regionen. Använda Microsoft PowerShell och Azure CLI för att skapa ZRS Classic-konton är ett alternativ tills ZRS Classic är inaktuell.

ZRS Classic replikerar data asynkront mellan datacenter i en eller två regioner. Replikerade data kanske inte tillgänglig om inte Microsoft initierar sekundär redundans. Ett ZRS Classic-konto kan inte konverteras till eller från LRS, GRS eller RA-GRS. ZRS Classic-konton också stöder inte mått eller loggning.

ZRS Classic är endast tillgänglig för **blockblobbar** i allmänna V1 (GPv1)-konton. Mer information om lagringskonton finns i [kontoöversikten för Azure Storage](storage-account-overview.md).

Om du vill migrera manuellt ZRS-kontodata till eller från ett LRS, ZRS Classic, GRS eller RA-GRS-konto, använder du något av följande verktyg: AzCopy, Azure Storage Explorer, Azure PowerShell eller Azure CLI. Du kan också skapa egna migreringslösning med någon av Azure Storage-klientbibliotek.

Du kan också uppgradera dina ZRS Classic-konton till ZRS i portalen eller med hjälp av Azure PowerShell eller Azure CLI i regioner där ZRS är tillgänglig.

Uppgradera till ZRS i portalen går du till konfigurationsavsnittet för kontot och välj uppgradera:![Uppgradera ZRS Classic till ZRS i portalen](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.jpg)

För att uppgradera till ZRS med hjälp av PowerShell för att anropa följande kommando:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

För att uppgradera till ZRS med hjälp av CLI för att anropa följande kommando:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Se också
- [Azure Storage-replikering](storage-redundancy.md)
- [Lokalt redundant lagring (LRS): Dataredundans med låg kostnad för Azure Storage](storage-redundancy-lrs.md)
- [GEO-redundant lagring (GRS): Tvärregional replikering för Azure Storage](storage-redundancy-grs.md)

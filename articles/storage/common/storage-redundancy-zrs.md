---
title: Bygga Azure Storage program med hög tillgänglighet i zoner – redundant lagring (ZRS) | Microsoft Docs
description: Zone-redundant lagring (ZRS) erbjuder ett enkelt sätt att bygga program med hög tillgänglighet. ZRS skyddar mot maskin varu problem i data centret och mot vissa regionala katastrofer.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5fefe469bfac4816a67c6ceb344f12c1e52de60c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68550455"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Zon-redundant lagring (ZRS): Azure Storage program med hög tillgänglighet
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Support täckning och regional tillgänglighet
ZRS stöder för närvarande standard konto typer för allmän användning v2. Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md).

ZRS är tillgängligt för block-blobar, icke-diskens sid blobbar, filer, tabeller och köer.

ZRS är allmänt tillgänglig i följande regioner:

- Sydostasien
- Västeuropa
- Nordeuropa
- Frankrike, centrala
- Östra Japan
- Storbritannien, södra
- USA, centrala
- USA, östra
- USA, östra 2
- USA, västra 2

Microsoft fortsätter att aktivera ZRS i ytterligare Azure-regioner. På sidan [uppdateringar för Azure-tjänsten](https://azure.microsoft.com/updates/) regelbundet hittar du information om nya regioner.

**Kända begränsningar**

- Arkiv lag rings nivån stöds inte för närvarande på ZRS-konton. Mer information finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) lag rings nivåer.
- Hanterade diskar har inte stöd för ZRS. Du kan lagra ögonblicks bilder och avbildningar för Standard SSD Managed Disks på Standard HDD lagring och [välja mellan alternativen LRS och ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Vad händer när en zon blir otillgänglig?
Dina data är fortfarande tillgängliga för både Läs-och skriv åtgärder även om en zon blir otillgänglig. Microsoft rekommenderar att du fortsätter att följa praxis för hantering av tillfälliga fel. Dessa metoder omfattar implementering av principer för återförsök med exponentiell säkerhets kopiering.

När en zon inte är tillgänglig gör Azure nätverks uppdateringar, t. ex. DNS-ompekare. De här uppdateringarna kan påverka ditt program om du har åtkomst till dina data innan uppdateringarna har slutförts.

ZRS kanske inte skyddar dina data mot en regional katastrof där flera zoner påverkas permanent. ZRS erbjuder i stället återhämtnings kapacitet för dina data om det blir tillfälligt otillgängligt. För skydd mot regionala katastrofer rekommenderar Microsoft att använda Geo-redundant lagring (GRS). Mer information om GRS finns i [Geo-redundant lagring (GRS): Replikering mellan regioner för Azure Storage](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Konverterar till ZRS-replikering
Det är enkelt att migrera till eller från LRS, GRS och RA-GRS. Använd Azure Portal eller Provider-API: et för lagrings resurser för att ändra kontots redundans typ. Azure kommer sedan att replikera dina data i enlighet med detta. 

Att migrera data till ZRS kräver en annan strategi. ZRS-migreringen innebär fysisk förflyttning av data från en enda lagrings stämpel till flera stämplar inom en region.

Det finns två primära alternativ för migrering till ZRS: 

- Kopiera eller flytta data manuellt till ett nytt ZRS-konto från ett befintligt konto.
- Begär en direktmigrering.

Microsoft rekommenderar starkt att du utför en manuell migrering. En manuell migrering ger större flexibilitet än en direktmigrering. Med en manuell migrering har du kontroll över tids inställningen.

Om du vill utföra en manuell migrering har du följande alternativ:
- Använd befintligt verktyg som AzCopy, en av Azure Storage klient bibliotek eller pålitliga verktyg från tredje part.
- Om du är bekant med Hadoop eller HDInsight ansluter du både käll-och mål kontot (ZRS) till klustret. Parallellisera sedan data kopierings processen med ett verktyg som DistCp.
- Bygg ditt eget verktyg med ett av de Azure Storage klient biblioteken.

En manuell migrering kan resultera i avbrott i programmet. Om ditt program kräver hög tillgänglighet tillhandahåller Microsoft också ett alternativ för direktmigrering. En direktmigrering är en migrering på plats. 

Under en direktmigrering kan du använda ditt lagrings konto medan dina data migreras mellan käll-och mål lagrings märken. Under migreringsprocessen har du samma nivå av SLA och tillgänglighets-SLA som vanligt.

Tänk på följande begränsningar för direktmigrering:

- Microsoft hanterar din begäran om direktmigrering utan dröjsmål, men det finns inte någon garanti om när den slutförs. Om du behöver migrera dina data till ZRS med ett visst datum rekommenderar Microsoft att du utför en manuell migrering i stället. Ju mer data du har på ditt konto, desto längre tid tar det vanligtvis att migrera dina data. 
- Direktmigrering stöds endast för lagrings konton som använder LRS eller GRS-replikering. Om ditt konto använder RA-GRS måste du först ändra ditt kontos replikeringstyp till antingen LRS eller GRS innan du fortsätter. Detta mellanliggande steg tar bort den sekundära skrivskyddade slut punkten som tillhandahålls av RA-GRS innan migreringen.
- Ditt konto måste innehålla data.
- Du kan bara migrera data inom samma region. Om du vill migrera dina data till ett ZRS-konto som finns i en annan region än käll kontot, måste du utföra en manuell migrering.
- Endast standard lagrings konto typer stöder Direktmigrering. Premium Storage-konton måste migreras manuellt.
- Direktmigreringen från ZRS till LRS, GRS eller RA-GRS stöds inte. Du måste flytta data manuellt till ett nytt eller ett befintligt lagrings konto.
- Managed disks är bara tillgängliga för LRS och kan inte migreras till ZRS. Du kan lagra ögonblicks bilder och avbildningar för Standard SSD Managed Disks på Standard HDD lagring och [välja mellan alternativen LRS och ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). För integrering med tillgänglighets uppsättningar, se [Introduktion till Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).
- LRS-eller GRS-konton med Arkiv data kan inte migreras till ZRS.

Du kan begära en Direktmigrering via [Azure-support portalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Från portalen väljer du det lagrings konto som du vill konvertera till ZRS.
1. Välj **ny supportbegäran**
2. Slutför **grunderna** baserat på din konto information. I avsnittet **tjänst** väljer du **lagrings konto hantering** och den resurs som du vill konvertera till ZRS. 
3. Välj **Nästa**. 
4. Ange följande värden i avsnittet **problem** : 
    - **Allvarlighets grad**: Låt standardvärdet vara kvar.
    - **Problem typ**: Välj **datamigrering**.
    - **Kategori**: Välj **migrera till ZRS inom en region**.
    - **Rubrik**: Ange en beskrivande rubrik, till exempel **ZRS-kontots migrering**.
    - **Information**: Skriv ytterligare information i rutan **information** , till exempel om jag vill migrera till ZRS från [LRS, GRS] i \_ \_ regionen. 
5. Välj **Nästa**.
6. Kontrol lera att kontakt informationen är korrekt på bladet med **kontakt information** .
7. Välj **Skapa**.

En support person kommer att kontakta dig och tillhandahålla den hjälp du behöver.

## <a name="live-migration-to-zrs-faq"></a>Vanliga frågor om Direktmigrering till ZRS

**Bör jag planera för eventuell stillestånds tid under migreringen?**

Det finns ingen nedtid som orsakas av migreringen. Under en direktmigrering kan du fortsätta att använda ditt lagrings konto medan dina data migreras mellan käll-och mål lagrings märken. Under migreringsprocessen har du samma nivå av SLA och tillgänglighets-SLA som vanligt.

**Finns det någon data förlust som är associerad med migreringen?**

Ingen data förlust är associerad med migreringen. Under migreringsprocessen har du samma nivå av SLA och tillgänglighets-SLA som vanligt.

**Krävs det några uppdateringar för programmen när migreringen är klar?**

När migreringen har slutförts ändras replikeringstillståndet för kontona till "Zone-redundant lagring (ZRS)". Tjänst slut punkter, åtkomst nycklar, SAS och andra konto konfigurations alternativ förblir oförändrade och intakta.

**Kan jag begära en direktmigrering av mitt generella v1-konton till ZRS?**

ZRS har endast stöd för General-Purpose v2-konton, så innan du skickar en begäran om en Direktmigrering till ZRS måste du uppgradera dina konton till General-Purpose v2. Mer information finns i [Översikt över Azure Storage-kontot](https://docs.microsoft.com/azure/storage/common/storage-account-overview) och [Uppgradera till ett allmänt lagrings konto för allmän användning v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) .

**Kan jag begära en direktmigrering av mitt Läs åtkomst till Geo-redundant lagrings konto (RA-GRS) till ZRS?**

Innan du skickar en begäran om Direktmigrering till ZRS bör du se till att dina program eller arbets belastningar inte längre behöver åtkomst till den sekundära skrivskyddade slut punkten och ändra replikeringstillståndet för dina lagrings konton till Geo-redundant lagring (GRS). Mer information finns i [ändra replikeringsprincip](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) .

**Kan jag begära direktmigrering av mitt lagrings konto till ZRS till en annan region?**

Om du vill migrera dina data till ett ZRS-konto som finns i en annan region än käll kontots region måste du utföra en manuell migrering.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Klassisk ZRS: Ett bakåtkompatibelt alternativ för block blobs-redundans
> [!NOTE]
> Microsoft kommer att föråldra och migrera ZRS klassiska konton den 31 mars 2021. Mer information kommer att ges till ZRS klassiska kunder före utfasning. 
>
> När ZRS blir [allmänt tillgängliga](#support-coverage-and-regional-availability) i en region kan kunder inte skapa ZRS klassiska konton från portalen i den regionen. Att använda Microsoft PowerShell och Azure CLI för att skapa ZRS klassiska konton är ett alternativ tills ZRS Classic är föråldrad.

ZRS klassisk replikerar data mellan data Center i en och två regioner. Replikerade data kanske inte är tillgängliga om inte Microsoft initierar redundans till den sekundära. Ett klassiskt ZRS-konto kan inte konverteras till eller från LRS, GRS eller RA-GRS. ZRS klassiska konton stöder inte heller mått eller loggning.

ZRS Classic är endast tillgängligt för **block** -blobar i GPv1-lagrings konton (General Purpose v1). Mer information om lagringskonton finns i [kontoöversikten för Azure Storage](storage-account-overview.md).

Om du vill migrera ZRS-Datadata manuellt till eller från ett LRS-, ZRS-klassiskt-, GRS-eller RA-GRS-konto använder du något av följande verktyg: AzCopy, Azure Storage Explorer, Azure PowerShell eller Azure CLI. Du kan också bygga en egen lösning för migrering med ett av klient biblioteken för Azure Storage.

Du kan också uppgradera dina ZRS klassiska konton till ZRS i portalen eller använda Azure PowerShell eller Azure CLI i regionerna där ZRS är tillgängligt.

Om du vill uppgradera till ZRS i portalen går du till konfigurations avsnittet för kontot och väljer uppgradering:![Uppgradera ZRS Classic till ZRS i portalen](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.jpg)

Om du vill uppgradera till ZRS med PowerShell anropar du följande kommando:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Om du vill uppgradera till ZRS med CLI anropar du följande kommando:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Se också
- [Azure Storage-replikering](storage-redundancy.md)
- [Lokalt redundant lagring (LRS): Data redundans för låg kostnad för Azure Storage](storage-redundancy-lrs.md)
- [Geo-redundant lagring (GRS): Replikering mellan regioner för Azure Storage](storage-redundancy-grs.md)

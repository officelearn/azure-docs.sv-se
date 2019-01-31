---
title: Vad du gör i händelse av ett avbrott i Azure Storage | Microsoft Docs
description: Vad du gör i händelse av ett avbrott i Azure Storage
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c9d949b32fb298c22142a35b939860bae240c803
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454818"
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Vad du gör om ett avbrott i Azure Storage inträffar?
På Microsoft arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga. Ibland tvingar utöver vårt styr hur oss på ett sätt som kan leda till oplanerade driftstopp i en eller flera regioner. För att hjälpa dig att hantera dessa sällsynta förekomster, tillhandahåller vi följande övergripande riktlinjer för Azure Storage-tjänster.

## <a name="how-to-prepare"></a>Så här förbereder du
Det är viktigt för varje kund att förbereda en plan för haveriberedskap. Att återställa från driftstörningar vanligtvis omfattar både anställda och automatiserade procedurer för att återaktivera dina program i ett fungerande tillstånd. Finns i Azure-dokumentationen nedan om du vill skapa en egen plan för katastrofåterställning:

* [Tillgänglighetschecklista](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Utforma återhämtningsprogram för Azure](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Azure Site Recovery-tjänsten](https://azure.microsoft.com/services/site-recovery/)
* [Azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Azure Backup-tjänsten](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Hur identifieras
Det rekommenderade sättet att bestämma status för Azure-tjänsten är att prenumerera på den [Hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Vad du gör om ett lagringsutrymmesavbrott inträffar
Om en eller flera Storage-tjänster är inte tillgänglig för tillfället på en eller flera regioner, finns det två alternativ för dig att tänka på. Om du vill ha omedelbar åtkomst till dina data Överväg att alternativ 2.

### <a name="option-1-wait-for-recovery"></a>Alternativ 1: Vänta tills återställningen
I så fall krävs ingen åtgärd från din sida. Vi arbetar hårt för att återställa Azure-tjänst-anslutningen. Du kan övervaka status för tjänsten på den [Hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Alternativ 2: Kopiera data från sekundär
Om du har valt [Read-access geo-redundant lagring (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (rekommenderas) för dina lagringskonton, du har läsbehörighet till dina data från den sekundära regionen. Du kan använda verktyg som [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), och [Azure Data Movement library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) kopiera data från den sekundära regionen till ett annat lagringskonto i en unimpacted region, och peka sedan dina program till den storage-konto för både läsa och skriva tillgänglighet.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Vad som händer om det uppstår redundans lagring
Om du har valt [Geo-redundant lagring (GRS)](storage-redundancy-grs.md) eller [Read-access geo-redundant lagring (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (rekommenderas), Azure Storage håller dina data varaktiga i två regioner (primär eller sekundär). I båda regionerna underhåller Azure Storage ständigt flera kopior av dina data.

När ett regionalt haveri påverkar den primära regionen, försöker vi först återställa tjänsten i den regionen för att ge den bästa kombinationen av RTO och RPO. Beroende på typen av katastrofen och dess påverkan, i vissa sällsynta fall visar vi kanske inte kan återställa den primära regionen. Då ska vi köra en geo-redundansväxling. Replikering av data över flera regioner är en asynkron åtgärd som inbegriper en fördröjning, så det är möjligt att ändringar som ännu inte har replikerats till den sekundära regionen kan gå förlorade.

Ett antal punkter angående lagring geo-redundansväxling upplevelse:

* Storage geo-redundansväxling aktiveras endast av Azure Storage-teamet – det finns ingen kundåtgärd krävs. Redundansen utlöses när Azure Storage-teamet har uttömt alla alternativ för återställning av data i samma region, vilket ger den bästa kombinationen av RTO och RPO.
* Din befintliga lagring Tjänsteslutpunkter för blobbar, tabeller, köer och filer förblir detsamma efter redundansen; från Microsoft DNS-posten måste uppdateras om du vill växla från den primära regionen till den sekundära regionen. Microsoft utför den här uppdateringen automatiskt som en del av geo-redundansväxling.
* Före och under geo-redundansväxling du har inte skrivbehörighet till storage-kontot på grund av effekten av katastrofen, men du kan ändå läsa från sekundärt om ditt lagringskonto har konfigurerats som RA-GRS.
* När geo-redundansväxling har slutförts och DNS-ändringarna spridits, återställs Läs- och skrivåtkomst till ditt storage-konto om du har GRS eller RA-GRS. Den slutpunkt som tidigare var den sekundära slutpunkten blir den primära slutpunkten. 
* Du kan kontrollera status för den primära platsen och frågan senast geo-redundans för ditt lagringskonto. Mer information finns i [Lagringskonton – hämta egenskaper](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/getproperties).
* Efter redundansen kan ditt storage-konto kommer att fungera helt och hållet, men i ett ”försämrat” tillstånd eftersom den är värd för en fristående region med ingen geo-replikering möjligt. För att minska denna risk avslöjar vi återställer den ursprungliga primära regionen och gör sedan en geo-återställning efter fel för att återställa det ursprungliga tillståndet. Om den ursprungliga primära regionen är ett oåterkalleligt, kommer vi tilldela en annan sekundär region.

## <a name="best-practices-for-protecting-your-data"></a>Metodtips för att skydda dina data
Det finns några rekommenderade metoder för att säkerhetskopiera dina storage-data med jämna mellanrum.

* VM-diskar – Använd den [Azure Backup-tjänsten](https://azure.microsoft.com/services/backup/) för säkerhetskopiering av VM-diskar som används av virtuella datorer i Azure.
* Blockblob-objekt – aktivera [mjuk borttagning](../blobs/storage-blob-soft-delete.md) att skydda mot på objektnivå borttagningar och skriver över eller kopierar blobar till ett annat lagringskonto i en annan region med [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), eller [Azure Data Movement library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Använd tabeller – [AzCopy](storage-use-azcopy.md) att exportera data till ett annat lagringskonto i en annan region.
* Filer – använda [AzCopy](storage-use-azcopy.md) eller [Azure PowerShell](storage-powershell-guide-full.md) att kopiera filer till ett annat lagringskonto i en annan region.

Information om hur du skapar program som dra full nytta av funktionen för RA-GRS, Kolla in [utforma högtillgängliga program med hjälp av RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)

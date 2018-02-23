---
title: "Vad du ska göra om ett avbrott i Azure Storage | Microsoft Docs"
description: "Vad du ska göra om ett avbrott i Azure Storage"
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/19/2017
ms.author: tamram
ms.openlocfilehash: 66406ed327f496dce7e77bb9ff650e0eec44bbdd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Vad du gör om ett avbrott i Azure Storage inträffar?
På Microsoft är arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga. Ibland tvingar utöver våra styr hur oss på ett sätt som kan leda till oplanerade driftstopp i en eller flera regioner. För att hantera dessa sällsynta förekomster ska ger vi följande övergripande riktlinjer för Azure Storage-tjänster.

## <a name="how-to-prepare"></a>Så här förbereder du
Det är viktigt för varje kund att förbereda en plan för haveriberedskap. För att återställa från ett avbrott för lagring vanligtvis omfattar både operations personal och automatiserade procedurer för att återaktivera ditt program med fungerande tillstånd. Hittar du i Azure-dokumentationen nedan för att skapa en egen plan för katastrofåterställning:

* [Tillgänglighetschecklista](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Utforma återhämtningsprogram för Azure](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Azure Site Recovery-tjänsten](https://azure.microsoft.com/services/site-recovery/)
* [Azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Azure Backup-tjänsten](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Hur du identifierar
Det rekommenderade sättet att avgöra status för Azure-tjänsten är att prenumerera på den [Azure Hälsoinstrumentpanelen](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Vad du ska göra om ett lagringsutrymme strömavbrott uppstår
Om en eller flera lagringstjänster är otillgängliga just nu på en eller flera regioner, finns det två alternativ för att du bör tänka på. Överväg alternativ 2 om du vill ha direkt tillgång till dina data.

### <a name="option-1-wait-for-recovery"></a>Alternativ 1: Vänta tills återställningen
I så fall krävs ingen åtgärd. Vi arbetar ordentligt om du vill återställa Azure tjänsternas tillgänglighet. Du kan övervaka status för tjänsten på den [Azure Hälsoinstrumentpanelen](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Alternativ 2: Kopiera data från sekundär
Om du väljer [geo-redundant lagring med läsbehörighet (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (rekommenderas) för dina lagringskonton, du har läsbehörighet till dina data från den sekundära regionen. Du kan använda verktyg som [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), och [Azure Data Movement library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) att kopiera data från den sekundära regionen till ett annat lagringskonto i en unimpacted region och peka på dina program till att lagringskontot för både läsa och skriva tillgänglighet.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Vad som händer om det uppstår redundans lagring
Om du väljer [Geo-redundant lagring (GRS)](storage-redundancy.md#geo-redundant-storage) eller [geo-redundant lagring med läsbehörighet (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (rekommenderas), Azure Storage kommer att hålla dina data beständig i två regioner (primär eller sekundär). I båda regioner underhåller Azure Storage ständigt flera kopior av dina data.

När en regional katastrof påverkar din primära region, kommer vi först försöker återställa tjänsten i den regionen. Beroende av haveriet och dess påverkan, i vissa sällsynta fall vi kanske inte kan återställa den primära regionen. Vi kommer då att utföra en geo-redundans. Cross-region datareplikering är en asynkron åtgärd som kan medföra en fördröjning, så det är möjligt att ändringar som ännu inte har replikerats till den sekundära regionen kan gå förlorade. Du kan fråga efter den [”tidpunkt för senaste synkronisering” för ditt lagringskonto](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) att hämta information om replikeringsstatus.

Ett antal punkter för geo-redundans-upplevelse lagring:

* Lagring geo-redundans kommer endast att utlösas av Azure Storage-teamet – kund åtgärd ingen krävs.
* Din befintliga lagring slutpunkter för blobbar, tabeller, köer och filer förblir detsamma efter växling vid fel; från Microsoft DNS-posten måste uppdateras om du vill växla från den primära regionen till den sekundära regionen.  Microsoft kommer att utföra den här uppdateringen automatiskt som en del av processen geo-redundans.
* Före och under geo-redundans du har inte skrivbehörighet till ditt lagringskonto på grund av effekten av haveriet men du fortfarande kan läsa från sekundärt om ditt lagringskonto har konfigurerats som RA-GRS.
* När geo-redundans har slutförts och sprids DNS-ändringarna, återupptas Läs- och skrivåtkomst till ditt lagringskonto. Detta datapunkter som användes för att vara din sekundära slutpunkt. 
* Observera att du har skrivbehörighet om du har GRS eller RA-GRS som konfigurerats för lagringskontot. 
* Du kan fråga [”Geo redundans senast” för ditt lagringskonto](https://msdn.microsoft.com/library/azure/ee460802.aspx) att få mer information.
* Efter växling vid fel, storage-konto kommer att fungera helt och hållet, men en status som ”degraderad” som faktiskt finns i en fristående region med ingen geo-replikering möjligt. För att minska denna risk, kommer återställa den ursprungliga primära regionen och gör sedan ett geo-återställning efter fel om du vill återställa det ursprungliga tillståndet. Om den ursprungliga primära regionen är ett oåterkalleligt allokerar vi en annan sekundär region.
  Mer information om infrastrukturen för Azure Storage geo-replikering finns i artikeln på Storage-teamets blogg om [alternativ för redundans och RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Metodtips för att skydda dina data
Det finns några rekommenderade metoder för säkerhetskopiering av storage-data regelbundet.

* Virtuella diskar – Använd den [Azure Backup-tjänsten](https://azure.microsoft.com/services/backup/) för säkerhetskopiering av VM-diskar som används av Azure virtuella datorer.
* Block-blobbar – skapa en [ögonblicksbild](https://msdn.microsoft.com/library/azure/hh488361.aspx) för var och en blockblobb eller kopierar blobar till ett annat lagringskonto i en annan region med [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), eller [Azure Data Movement library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* – Använder [AzCopy](storage-use-azcopy.md) att exportera informationen till ett annat lagringskonto i en annan region.
* Filer – använda [AzCopy](storage-use-azcopy.md) eller [Azure PowerShell](storage-powershell-guide-full.md) att kopiera filer till ett annat lagringskonto i en annan region.

Information om hur du skapar program som dra full nytta av funktionen RA-GRS checka ut [skapar hög tillgängliga program använder RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)


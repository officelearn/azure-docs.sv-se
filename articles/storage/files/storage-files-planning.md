---
title: Planera för distribution av Azure Files | Microsoft Docs
description: Lär dig att tänka på när du planerar för distribution av Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 69ca9474c613752b98efa6bb236919508a2fe430
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753698"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planera för distribution av Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet som är branschstandard. Eftersom Azure Files är fullständigt hanterad, är distribuerar den i produktionsscenarier mycket enklare än att distribuera och hantera en filserver eller NAS-enhet. Den här artikeln tar upp ämnen att tänka på när du distribuerar en Azure-filresurs för användning i produktion i din organisation.

## <a name="management-concepts"></a>Management-relaterade begrepp
 Följande diagram illustrerar konstruktionerna för hantering av Azure Files:

![Filstruktur](./media/storage-files-introduction/files-concepts.png)

* **Storage-konto**: All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för information om kapacitet för lagringskonton.

* **Dela**: En fillagring-resurs är en SMB-filresurs i Azure. Alla kataloger och filer måste skapas i en överordnad resurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till 5 TiB totala kapacitet filresursen.

* **Directory**: En valfri hierarki med kataloger.

* **Filen**: En fil i resursen. En fil kan vara upp till 1 TiB i storlek.

* **URL-format**: Filer är adresserbara via följande URL-format för begäranden till en Azure-filresurs som görs med fil-REST-protokollet:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Dataåtkomstmetod
Azure Files erbjuder två inbyggda och smidigt dataåtkomst med metoder som du kan använda separat eller i kombination med varandra, komma åt dina data:

1. **Direkt molnåtkomst**: Alla Azure-filresurs kan monteras av [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), och/eller [Linux](storage-how-to-use-files-linux.md) med Server Message Block (SMB) protokollet som är branschstandard eller via File REST API. Med SMB görs läsningar och skrivningar till filer på resursen direkt på en filresurs i Azure. Om du vill montera av en virtuell dator i Azure, SMB-klienten i Operativsystemet måste stödja minst SMB 2.1. Att montera lokalt, t.ex. på en användares arbetsstation SMB-klienten som stöds av arbetsstationen måste minst stödja SMB 3.0 (med kryptering). Förutom SMB, nya program eller tjänster kan direkt komma åt filresursen via File REST, vilket ger ett enkelt och skalbart programmeringsgränssnitt för programutveckling.
2. **Azure File Sync**: Med Azure File Sync kan filresurser replikeras till Windows-servrar på lokala eller i Azure. Användarna kan komma åt filresursen via Windows-Server som via en SMB- eller NFS-resurs. Detta är användbart för scenarier där data nås och ändras långt från ett Azure-datacenter, till exempel i ett scenario med ett avdelningskontor. Data kan replikeras mellan flera Windows Server-slutpunkter, till exempel mellan flera olika avdelningskontor. Slutligen kan nivåindelade data till Azure Files, så att alla data är fortfarande tillgängliga via servern, men servern har inte en fullständig kopia av data. Data återställs i stället sömlöst när öppnas av användaren.

I följande tabell visar hur dina användare och program kan komma åt din Azure-filresurs:

| | Direkt molnåtkomst | Azure File Sync |
|------------------------|------------|-----------------|
| Vilka protokoll behöver du använda? | Azure Files stöder SMB 2.1 och SMB 3.0 File REST API. | Få åtkomst till din Azure-filresurs via alla protokoll som stöds på Windows Server (SMB, NFS, FTPS osv.) |  
| Var körs din arbetsbelastning? | **I Azure**: Azure Files erbjuder direkt åtkomst till dina data. | **On-premises med långsamt nätverk**: Windows, Linux och macOS klienter kan montera en lokala Windows-filresurs som ett snabbt cacheminne för din Azure-filresurs. |
| Vilken nivå av ACL: er behöver du? | Resurs- och nivå. | Resurs-, fil- och användaren nivå. |

## <a name="data-security"></a>Datasäkerhet
Azure Files finns flera inbyggda alternativ för att säkerställa datasäkerhet:

* Stöd för kryptering i båda protokollen over-under överföring: SMB 3.0-kryptering och fil-REST via HTTPS. Som standard: 
    * Klienter som stöder SMB 3.0-kryptering skicka och ta emot data via en krypterad kanal.
    * Klienter som inte stöder SMB 3.0 med kryptering kan kommunicera intra-datacenter över SMB 2.1 eller SMB 3.0 utan kryptering. SMB-klienter är inte tillåtna att kommunicera mellan datacenter över SMB 2.1 eller SMB 3.0 utan kryptering.
    * Klienter kan kommunicera via File REST med HTTP eller HTTPS.
* Kryptering i vila ([Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Storage Service Encryption (SSE) har aktiverats för alla lagringskonton. Data i vila är krypterad med fullständigt hanterade nycklar. Kryptering i vila inte öka kostnader för lagring och minska prestanda. 
* Valfritt behovet av krypterade data under överföring: när du väljer Azure Files nekar åtkomst till data i okrypterad kanaler. Mer specifikt tillåts endast HTTPS och SMB 3.0 med kryptering anslutningar. 

    > [!Important]  
    > Äldre SMB-klienter inte kan kommunicera med SMB 3.0 med kryptering inte leder till att kräva säker överföring av data. Mer information finns i [montera på Windows](storage-how-to-use-files-windows.md), [montera i Linux](storage-how-to-use-files-linux.md), och [montera på macOS](storage-how-to-use-files-mac.md).

För maximal säkerhet rekommenderar vi alltid att aktivera båda kryptering i vila och aktivera kryptering av data under överföring när du använder modern klienter för att komma åt dina data. Om du behöver att montera en filresurs på en virtuell Windows Server 2008 R2 dator som bara stöder SMB 2.1, måste du tillåta okrypterade trafik till ditt lagringskonto eftersom SMB 2.1 inte stöder kryptering.

Om du använder Azure File Sync för att få åtkomst till din Azure-filresurs, använder vi alltid HTTPS och SMB 3.0 med kryptering för att synkronisera dina data till dina Windows-servrar, oavsett om du kräver kryptering av data i vila.

## <a name="file-share-performance-tiers"></a>Filen prestandanivåer för resursen
Azure Files stöder två prestandanivåer: standard och premium.

* **Standard-filresurser** backas upp av rotational hårddiskar (HDD) som ger en pålitlig prestanda för i/o-arbetsbelastningar som är mindre känsliga för variationer i prestandan, till exempel allmänna filresurser och miljöer för utveckling/testning. Standard-filresurser är endast tillgängliga i en användningsbaserad fakturering modell.
* **Premium-filresurser (förhandsversion)** backas upp av SSD-diskar (SSD) som ger konsekvent hög prestanda och låg latens på ensiffriga millisekunder för de flesta i/o-åtgärder för de i/o-intensiva arbetsbelastningar. Detta gör dem lämpliga för en mängd olika arbetsbelastningar som databaser, webbplatsvärd, utvecklingsmiljöer osv. Premium-filresurser är endast tillgängliga i den etablerade faktureringsmodellen.

### <a name="provisioned-shares"></a>Etablerade resurser
Premium-filresurser har etablerats utifrån ett fast GiB/IOPS/dataflödet-förhållande. För varje GiB som etablerats utfärdas resursen en IOPS och 0,1 MiB/s genomströmning för upp till de maximala gränserna per resurs. Minsta tillåtna etablering är 100 GiB med min IOPS/dataflödet. Filresursens storlek kan ökas när som helst och minskas när som helst, men kan minskas en gång per dygn sedan den senaste ökningen.

Efter bästa förmåga Utöka alla resurser upp till tre IOPS per GiB etablerad lagring i 60 minuter eller längre beroende på storleken på resursen. Nya resurser börjar med den fullständiga burst-krediten baserat på etablerad kapacitet.

| Etablerad kapacitet | 100 giB | 500 giB | 1 TiB | 5 TiB | 
|----------------------|---------|---------|-------|-------|
| Baslinjen IOPS | 100 | 500 | 1,024 | 5,120 | 
| Burst-gräns | 300 | 1,500 | 3,072 | 15,360 | 
| Dataflöde | 110 MiB/sec | 150 MiB/sec | 202 MiB/sec | 612 MiB/sec |

## <a name="file-share-redundancy"></a>Filen resurs redundans
Azure Files stöder tre alternativ för dataredundans: lokalt redundant lagring (LRS), zonredundant lagring (ZRS) och geo-redundant lagring (GRS). I följande avsnitt beskrivs skillnaderna mellan de olika redundansalternativ:

### <a name="locally-redundant-storage"></a>Lokalt redundant lagring
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Zonredundant lagring
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Geografiskt redundant lagring
> [!Warning]  
> Du bör inte initiera redundans för storage-konto om du använder din Azure-filresurs som en slutpunkt i molnet på ett GRS-lagringskonto. Synkronisering av orsaken till slutar fungera och kan också gör oväntade data går förlorade vid nyligen nivåindelade filer. När det gäller förlust av en Azure-region utlöser Microsoft redundans för storage-konto på ett sätt som är kompatibel med Azure File Sync.

[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Tillväxt datamönster
Idag är den maximala storleken för en Azure-filresurs 5 TiB. På grund av den här tillfälliga begränsningen måste du överväga att den förväntade datatillväxten när du distribuerar en Azure-filresurs. 

Det är möjligt att synkronisera flera Azure-filresurser på en enda Windows Server med Azure File Sync. På så sätt kan du se till att äldre, stora filresurser som du kan ha en lokal kan anslutas till Azure File Sync. Mer information finns i [planera för distribution av Azure File Sync](storage-files-planning.md).

## <a name="data-transfer-method"></a>Metod för överföring av data
Det finns många enkelt alternativ för att massregistrera överföra data från en befintlig fil delar, till exempel en lokal filresurs, för i Azure Files. Några populära alternativ inkluderar (ofullständig lista):

* **Azure File Sync**: Som en del av en första synkronisering mellan en Azure-filresurs (en ”Molnslutpunkt”) och ett Windows directory namnområde (en ”Serverslutpunkt”) replikerar Azure File Sync alla data från den befintliga filresursen till Azure Files.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Azure Import/Export-tjänsten kan du på ett säkert sätt överföra stora mängder data till en Azure-filresurs via hårddiskar till ett Azure-datacenter. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: ROBOCOPY är ett välkänt kopiera verktyg som levereras med Windows och Windows Server. ROBOCOPY kan användas för att överföra data till Azure Files genom att montera filresursen lokalt och sedan använda den monterade platsen som mål i Robocopy-kommandot.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Azure Files, samt Azure Blob storage med hjälp av enkla kommandon med optimala prestanda. AzCopy är tillgängligt för Windows och Linux.

## <a name="next-steps"></a>Nästa steg
* [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
* [Distribuera Azure Files](storage-files-deployment-guide.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)

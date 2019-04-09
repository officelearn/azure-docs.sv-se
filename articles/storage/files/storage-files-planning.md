---
title: Planera för distribution av Azure Files | Microsoft Docs
description: Lär dig att tänka på när du planerar för distribution av Azure Files.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 609e774c36ab685d017f311a74c8680dbb9750c9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283026"
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

Azure Files erbjuder två prestandanivåer: standard och premium.

* **Standard-filresurser** backas upp av rotational hårddiskar (HDD) som ger en pålitlig prestanda för i/o-arbetsbelastningar som är mindre känsliga för variationer i prestandan, till exempel allmänna filresurser och miljöer för utveckling/testning. Standard-filresurser är endast tillgängliga i en användningsbaserad fakturering modell.
* **Premium-filresurser (förhandsversion)** backas upp av SSD-diskar (SSD) som ger konsekvent hög prestanda och låg latens på ensiffriga millisekunder för de flesta i/o-åtgärder för de i/o-intensiva arbetsbelastningar. Detta gör dem lämpliga för en mängd olika arbetsbelastningar som databaser, webbplatsvärd, utvecklingsmiljöer osv. Premium-filresurser är endast tillgängliga i den etablerade faktureringsmodellen. Premium-filresurser använder en distributionsmodell som är separat från standard-filresurser. Om du vill veta hur du skapar en premium-filresurs finns i vår artikel om ämnet: [Så här skapar du ett Azure premium storage-konto filen](storage-how-to-create-premium-fileshare.md).

> [!IMPORTANT]
> Premium-filen resurser är fortfarande i förhandsversion, endast tillgängligt med LRS, och är endast tillgängliga i flera regioner med med Azure Backup stöd kan finnas i Välj regioner:

|Tillgängliga regionen  |Azure Backup-support  |
|---------|---------|
|USA, östra 2      | Ja|
|Östra USA       | Ja|
|Västra USA       | Nej |
|USA, västra 2      | Nej |
|Centrala USA    | Nej |
|Norra Europa  | Nej |
|Västra Europa   | Ja|
|Sydostasien       | Ja|
|Östra Japan    | Nej |
|Sydkorea, centrala | Nej |
|Östra Australien| Nej |

### <a name="provisioned-shares"></a>Etablerade resurser

Premium-filresurser (förhandsversion) har etablerats utifrån ett fast GiB/IOPS/dataflödet-förhållande. För varje GiB som etablerats utfärdas resursen en IOPS och 0,1 MiB/s genomströmning för upp till de maximala gränserna per resurs. Minsta tillåtna etablering är 100 GiB med min IOPS/dataflödet. Filresursens storlek kan ökas vid all tid och minskad när som helst, men kan minskas en gång per dygn sedan den senaste ökningen.

Efter bästa förmåga Utöka alla resurser upp till tre IOPS per GiB etablerad lagring i 60 minuter eller längre beroende på storleken på resursen. Nya resurser börjar med den fullständiga burst-krediten baserat på etablerad kapacitet.

Alla resurser kan tillhandahålla upp till minst 100 IOPS och mål dataflöde på 100 MiB/s. Filresurser måste etableras i steg om 1 GiB. Minsta storleken är 100 GiB, nästa storlek är 101 GIB och så vidare.

> [!TIP]
> Baslinjen IOPS = 100 + 1 * etablerade GiB. (Upp till högst 100 000 IOPS).
>
> Utöka gränsen = 3 * baslinje IOPS. (Upp till högst 100 000 IOPS).
>
> priset för utgående = 60 MiB/s + 0.06 etablerade GiB (upp till 6 GiB/s)
>
> ingångshändelser = 40 MiB/s + 0,04 etablerade GiB (upp till 4 GiB)

Filresursens storlek kan ökas vid all tid och minskad när som helst, men kan minskas en gång per dygn sedan den senaste ökningen. IOPS/dataflödet ändringar börjar gälla inom 24 timmar efter ändringen storlek.

I följande tabell visas några exempel på dessa produkter för de etablerade resursen storlekarna:

(Storlekar enligt en * är begränsad offentlig förhandsversion)

|Kapacitet (GiB) | Baslinjen IOPS | Burst-gräns | Utgående (MiB/s) | Inkommande (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Upp till 300     | 66   | 44   |
|500         | 500     | Upp till 1 500   | 90   | 60   |
|1,024       | 1,024   | Upp till 3 072   | 122   | 81   |
|5,120       | 5,120   | Upp till 15 360  | 368   | 245   |
|10,240 *     | 10,240  | Upp till 30 720  | 675 | 450   |
|33,792 *     | 33,792  | Upp till 100 000 | 2,088 | 1,392   |
|51,200 *     | 51,200  | Upp till 100 000 | 3,132 | 2,088   |
|100,000 *    | 100 000 | Upp till 100 000 | 6,204 | 4,136   |

För närvarande filstorlekar dela upp till 5 TiB är i offentlig förhandsversion, upp till 102 TiB är begränsad offentlig förhandsversion, att begära åtkomst till den begränsade offentliga förhandsversionen som fullständig [undersökningen.](https://aka.ms/azurefilesatscalesurvey)

### <a name="bursting"></a>Bursting

Premium-filresurser kan utöka sina IOPS upp till en faktor på tre. Bursting sker automatiskt och fungerar baserat på ett kredit-system. Bursting fungerar på bästa förmåga och burst-gränsen är ingen garanti, filresurser kan öka användningsgraden *upp till* gränsen.

Krediter samlas i en burst-bucket när trafik för den filresurser är understiger baslinje IOPS. Till exempel har en 100 GiB resurs 100 baslinje IOPS. Om faktiska trafik på resursen var 40 IOPS för ett specifikt intervall 1 sekund, krediteras 60 oanvända IOPS till en burst-bucket. Dessa krediter ska sedan användas senare när åtgärder skulle överskrida baslinjen IOPs.

> [!TIP]
> Storleken på burst gränsen bucket = Baseline_IOPS * 2 * 3600.

Varje gång en resurs överskrider baslinjen IOPS och har krediter i en burst-bucket, bursting. Resurser kan fortsätta att utöka så länge kredit kvar, även om resurser som är mindre än 50 tiB kommer endast att hålla sig i burst-gränsen för upp till en timme. Resurser som är större än 50 TiB tekniskt sett kan överskrida begränsningen en timme, upp till två timmar men detta baseras på antalet burst-krediter ackumuleras. Varje i/o utöver baslinje IOPS förbrukar en kredit och när alla krediter förbrukas resursen skulle gå tillbaka till baslinje IOPS.

Dela krediter har tre lägen:

- Betalar om filresursen använder mindre än baslinjen IOPS.
- Avböja när filresursen bursting.
- Återstående på noll, när det finns inga krediter eller baslinje används IOPS.

Nya start på grund av fil-resurser med fullständig antal krediter i dess burst-bucket.

## <a name="file-share-redundancy"></a>Filen resurs redundans

Azure standard filresurser stöder tre alternativ för dataredundans: lokalt redundant lagring (LRS), zonredundant lagring (ZRS) och geo-redundant lagring (GRS).

Azure filer premium delar endast har stöd för lokalt redundant lagring (LRS).

I följande avsnitt beskrivs skillnaderna mellan de olika redundansalternativ:

### <a name="locally-redundant-storage"></a>Lokalt redundant lagring

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Zonredundant lagring

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Geografiskt redundant lagring

> [!Warning]  
> Du bör inte initiera redundans för storage-konto om du använder din Azure-filresurs som en slutpunkt i molnet på ett GRS-lagringskonto. Synkronisering av orsaken till slutar fungera och kan också gör oväntade data går förlorade vid nyligen nivåindelade filer. När det gäller förlust av en Azure-region utlöser Microsoft redundans för storage-konto på ett sätt som är kompatibel med Azure File Sync.

GEO-redundant lagring (GRS) är utformad för att tillhandahålla minst 99,99999999999999% (16 9) objektshållbarhet under ett givet år genom att replikera dina data till en sekundär region som ligger hundratals mil bort från den primära regionen. Om ditt lagringskonto har GRS aktiverat, sedan dina data är beständiga även vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte återställas.

Om du väljer för läsåtkomst till geografiskt redundant lagring (RA-GRS) bör du veta att Azure-filen inte stöd för läsåtkomst till geografiskt redundant lagring (RA-GRS) i valfri region just nu. Filresurser i RA-GRS-lagringskonto fungerar som de skulle ha gjort i GRS-konton och är debiteras enligt priserna för GRS.

GRS replikerar dina data till ett annat datacenter i en sekundär region utan att data kan vara skrivskyddad om Microsoft initierar en växling från primär till sekundär region.

Alla data för ett lagringskonto med aktiverad GRS replikeras först med lokalt redundant lagring (LRS). En uppdatering först är allokerad till den primära platsen som replikeras med LRS. Uppdateringen replikeras sedan asynkront till den sekundära regionen som med GRS. När data skrivs till den sekundära platsen, replikeras det också på den platsen med hjälp av LRS.

Båda primära och sekundära regionerna hantera repliker i separata feldomäner och uppgraderingsdomäner i en lagringsskalningsenhet. Skala lagringsenheten är grundläggande kvarhållningsenheten inom datacentret. Replikering på den här nivån kommer från LRS; Mer information finns i [lokalt redundant lagring (LRS): Dataredundans med låg kostnad för Azure Storage](../common/storage-redundancy-lrs.md).

Ha de här punkterna i åtanke när du bestämmer vilket replikeringsalternativ du använder:

* Zonredundant lagring (ZRS) ger hög tillgänglighet med synkron replikering och kan vara ett bättre alternativ för vissa scenarier än GRS. Mer information om ZRS finns i [ZRS](../common/storage-redundancy-zrs.md).
* Asynkron replikering innebär en fördröjning från den tidpunkt då data skrivs till den primära regionen till när de replikeras till den sekundära regionen. I händelse av ett regionalt haveri kan ändringar som ännu inte har replikerats till den sekundära regionen gå förlorade om dessa data inte kan återställas från den primära regionen.
* Med GRS repliken är inte tillgängligt för Läs- eller skrivbehörighet om inte Microsoft initierar en växling till den sekundära regionen. När det gäller en redundans, du kommer har läs- och skrivåtkomst till dessa data efter växlingen har slutförts. Mer information finns i [vägledning om Haveriberedskap](../common/storage-disaster-recovery-guidance.md).

## <a name="data-growth-pattern"></a>Tillväxt datamönster

Idag är den maximala storleken för en Azure-filresurs är 5 TiB (100 TiB för premium-filen dela begränsad offentlig förhandsversion). På grund av den här tillfälliga begränsningen måste du överväga att den förväntade datatillväxten när du distribuerar en Azure-filresurs.

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

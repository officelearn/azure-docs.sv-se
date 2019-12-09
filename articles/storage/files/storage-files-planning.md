---
title: Planera för distribution av Azure Files | Microsoft Docs
description: Lär dig vad du ska tänka på när du planerar för en Azure Files distribution.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fdfa01a45c0dd35da65b2ad7ce8b0d291148af1a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931106"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planera för distribution av Azure Files

[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet som är branschstandard. Eftersom Azure Files är fullständigt hanterat är det mycket enklare att distribuera den i produktions scenarier än att distribuera och hantera en fil server eller NAS-enhet. Den här artikeln handlar om de ämnen som du bör tänka på när du distribuerar en Azure-filresurs för produktions användning i din organisation.

## <a name="management-concepts"></a>Hanterings begrepp

 Följande diagram illustrerar Azure Files hanterings konstruktioner:

![Filstruktur](./media/storage-files-introduction/files-concepts.png)

* **Lagringskonto**: All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för information om kapacitet för lagringskonton.

* **Resurs**: En File Storage-resurs är en SMB-filresurs i Azure. Alla kataloger och filer måste skapas i en överordnad resurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till fil resursens totala kapacitet. Den totala kapaciteten för Premium-och standard fil resurser är 100 TiB.

* **Katalog:** En valfri hierarki med kataloger.

* **Fil:** En fil i resursen. En fil kan vara upp till 1 TiB storlek.

* **URL-format**: för begär anden till en Azure-filresurs som görs med fil rest-protokollet kan filerna adresseras med hjälp av följande URL-format:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Data åtkomst metod

Azure Files erbjuder två inbyggda, smidiga data åtkomst metoder som du kan använda separat eller i kombination med varandra för att få åtkomst till dina data:

1. **Direkt åtkomst till molnet**: alla Azure-filresurser kan monteras av [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)och/eller [Linux](storage-how-to-use-files-linux.md) med SMB-protokollet (Server Message Block) eller via filen REST API. Med SMB görs läsningar och skrivningar till filer på resursen direkt på fil resursen i Azure. SMB-klienten i operativ systemet måste ha stöd för minst SMB 2,1 för att monteras av en virtuell dator i Azure. För att montera lokalt, till exempel på en användares arbets Station, måste SMB-klienten som stöds av arbets stationen ha stöd för minst SMB 3,0 (med kryptering). Förutom SMB kan nya program eller tjänster komma åt fil resursen direkt via filen REST, vilket ger en enkel och skalbar application programming interface för program utveckling.
2. **Azure File Sync**: med Azure File Sync kan resurser replikeras till Windows-servrar lokalt eller i Azure. Dina användare skulle kunna komma åt fil resursen via Windows Server, t. ex. via en SMB-eller NFS-resurs. Detta är användbart för scenarier där data kommer att kommas åt och ändras långt bort från ett Azure-datacenter, t. ex. i ett scenario med ett avdelnings kontor. Data kan replikeras mellan flera Windows Server-slutpunkter, till exempel mellan flera avdelnings kontor. Slutligen kan data ligga i nivå till Azure Files, så att alla data fortfarande är tillgängliga via servern, men servern har ingen fullständig kopia av data. I stället anropas data sömlöst när användaren öppnar den.

I följande tabell visas hur dina användare och program kan komma åt din Azure-fil resurs:

| | Direkt åtkomst till molnet | Azure File Sync |
|------------------------|------------|-----------------|
| Vilka protokoll behöver du använda? | Azure Files stöder SMB 2,1, SMB 3,0 och File REST API. | Få åtkomst till Azure-filresursen via alla protokoll som stöds på Windows Server (SMB, NFS, FTPS osv.) |  
| Var kör du arbets belastningen? | **I Azure**: Azure Files ger direkt åtkomst till dina data. | **Lokalt med långsamt nätverk**: Windows-, Linux-och MacOS-klienter kan montera en lokal lokal Windows-filresurs som ett snabbt cacheminne för Azure-filresursen. |
| Vilken nivå av ACL: er behöver du? | Delnings-och filnivå. | Delnings-, fil-och användar nivå. |

## <a name="data-security"></a>Datasäkerhet

Azure Files har flera inbyggda alternativ för att säkerställa data säkerhet:

* Stöd för kryptering i både över-kabel-protokoll: SMB 3,0-kryptering och fil REST över HTTPS. Som standard: 
    * Klienter som stöder SMB 3,0-kryptering skickar och tar emot data via en krypterad kanal.
    * Klienter som inte har stöd för SMB 3,0 med kryptering kan kommunicera inom data Center via SMB 2,1 eller SMB 3,0 utan kryptering. SMB-klienter får inte kommunicera mellan data Center via SMB 2,1 eller SMB 3,0 utan kryptering.
    * Klienter kan kommunicera via fil REST med antingen HTTP eller HTTPS.
* Kryptering vid vila ([Azure Storage tjänst kryptering](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): kryptering för lagringstjänst (SSE) har Aktiver ATS för alla lagrings konton. Data i vila krypteras med fullständigt hanterade nycklar. Kryptering i vila ökar inte lagrings kostnaderna eller minskar prestandan. 
* Valfritt krav på krypterad data överföring: när det här alternativet är markerat nekar Azure Files åtkomst till data via okrypterade kanaler. Mer specifikt tillåts endast HTTPS och SMB 3,0 med krypterings anslutningar.

    > [!Important]  
    > Om du kräver säker data överföring kommer äldre SMB-klienter inte att kunna kommunicera med SMB 3,0 med kryptering för att fungera. Mer information finns i [montera på Windows](storage-how-to-use-files-windows.md), [montera på Linux](storage-how-to-use-files-linux.md)och [montera på MacOS](storage-how-to-use-files-mac.md).

För maximal säkerhet rekommenderar vi starkt att du alltid aktiverar både kryptering i vila och aktiverar kryptering av data under överföring när du använder moderna klienter för att komma åt dina data. Om du till exempel behöver montera en resurs på en virtuell dator med Windows Server 2008 R2, som endast stöder SMB 2,1, måste du tillåta okrypterad trafik till ditt lagrings konto eftersom SMB 2,1 inte stöder kryptering.

Om du använder Azure File Sync för att komma åt Azure-filresursen använder vi alltid HTTPS och SMB 3,0 med kryptering för att synkronisera dina data till dina Windows-servrar, oavsett om du behöver kryptering av data i vila.

## <a name="file-share-performance-tiers"></a>Prestanda nivåer för fil resurs

Azure Files erbjuder två prestanda nivåer: standard och Premium.

### <a name="standard-file-shares"></a>Standard fil resurser

Standard fil resurser backas upp av hård diskar (HDD). Standard fil resurser ger tillförlitlig prestanda för i/o-arbetsbelastningar som är mindre känsliga för prestanda variationer som generella fil resurser och utvecklings-och test miljöer. Standard fil resurser är bara tillgängliga i en fakturerings modell enligt principen betala per användning.

> [!IMPORTANT]
> Om du vill använda fil resurser som är större än 5 TiB går du till avsnittet [Publicera till större fil resurser (standard nivå)](#onboard-to-larger-file-shares-standard-tier) för att få anvisningar om hur du integrerar, samt regionala tillgänglighet och begränsningar.

### <a name="premium-file-shares"></a>Premium fil resurser

Premium fil resurser backas upp av solid state-hårddiskar (SSD). Premium-filresurser ger konsekvent höga prestanda och låg latens i ensiffriga millisekunder för de flesta i/o-åtgärder för i/o-intensiva arbets belastningar. Detta gör dem lämpliga för en mängd olika arbets belastningar, t. ex. databaser, webbplats värdar och utvecklings miljöer. Premium-filresurser är bara tillgängliga i en etablerad fakturerings modell. Premium-filresurser använder en distributions modell separat från standard fil resurser.

Azure Backup är tillgängligt för Premium-filresurser och Azure Kubernetes-tjänsten stöder Premium-filresurser i version 1,13 och senare.

Om du vill lära dig hur du skapar en Premium-filresurs, se vår artikel på ämnes raden: [så här skapar du ett Azure Premium File Storage-konto](storage-how-to-create-premium-fileshare.md).

För närvarande kan du inte direkt konvertera mellan en standard fil resurs och en Premium-filresurs. Om du vill växla till någon av nivåerna måste du skapa en ny fil resurs på den nivån och manuellt kopiera data från den ursprungliga resursen till den nya resurs som du har skapat. Du kan göra detta med hjälp av någon av de Azure Files kopierings verktyg som stöds, till exempel Robocopy eller AzCopy.

> [!IMPORTANT]
> Premium-filresurser är tillgängliga med LRS i de flesta regioner som erbjuder lagrings konton och med ZRS i en mindre delmängd regioner. För att ta reda på om Premium-filresurser är tillgängliga i din region, se sidan [produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) för Azure. För att ta reda på vilka regioner som stöder ZRS, se [support täckning och regional tillgänglighet](../common/storage-redundancy-zrs.md#support-coverage-and-regional-availability).
>
> För att hjälpa oss att prioritera nya regioner och funktioner på Premium-nivå kan du fylla i den här [undersökningen](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>Etablerade resurser

Premium-filresurser tillhandahålls baserat på en fast GiB/IOPS/data flödes kvot. För varje GiB tilldelas resursen en IOPS och 0,1 MiB/s-genomflöde upp till de maximala gränserna per resurs. Den minsta tillåtna etableringen är 100 GiB med lägsta IOPS/data flöde.

På bästa möjliga sätt kan alla resurser överföra upp till tre IOPS per GiB lagrings utrymme i 60 minuter eller längre beroende på resursens storlek. Nya resurser börjar med full burst-kredit baserat på den tillhandahållna kapaciteten.

Resurser måste tillhandahållas i steg om 1 GiB. Minimi storleken är 100 GiB, nästa storlek är 101 GiB och så vidare.

> [!TIP]
> Bas linje IOPS = 1 * etablerad GiB. (Upp till högst 100 000 IOPS).
>
> Burst-gräns = 3 * bas linje IOPS. (Upp till högst 100 000 IOPS).
>
> utgående taxa = 60 MiB/s + 0,06 * etablerad GiB
>
> ingress-taxa = 40 MiB/s + 0,04 * etablerad GiB

En etablerad resurs storlek anges av resurs kvoten. Du kan öka resurs kvoten när som helst, men den kan bara minskas efter 24 timmar sedan den senaste ökningen. Efter att ha väntat 24 timmar utan en kvot ökning kan du minska delnings kvoten så många gånger du vill, tills du ökar den igen. Ändringar av IOPS/data flödes skalning börjar gälla inom några minuter efter att storleken ändrats.

Det går att minska storleken på den allokerade resursen under den använda GiB. Om du gör detta kommer du inte att förlora data, men du kommer fortfarande att faktureras för den storlek som används och ta emot prestanda (bas linje, data flöde och burst-IOPS) för den etablerade resursen, inte den storlek som används.

I följande tabell visas några exempel på dessa formler för de allokerade resurs storlekarna:

|Kapacitet (GiB) | Baslinje-IOPS | Burst IOPS | Utgående (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Upp till 300     | 66   | 44   |
|500         | 500     | Upp till 1 500   | 90   | 60   |
|1,024       | 1,024   | Upp till 3 072   | 122   | 81   |
|5 120       | 5 120   | Upp till 15 360  | 368   | 245   |
|10 240      | 10 240  | Upp till 30 720  | 675 | 450   |
|33 792      | 33 792  | Upp till 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | Upp till 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Upp till 100 000 | 6 204 | 4 136   |

> [!NOTE]
> Fil resursernas prestanda är beroende av dator nätverks begränsningar, tillgänglig nätverks bandbredd, i/o-storlekar, parallellitet, bland många andra faktorer. Till exempel, baserat på intern testning med 8 KiB i/o-storlek, kan en virtuell Windows-dator, *Standard F16s_v2*som är anslutna till Premium-filresurs via SMB uppnå 20 000 Read IOPS och 15 000 Skriv-IOPS. Med 512 MiB Läs-/skriv-i/o-storlekar kan samma virtuella dator uppnå 1,1 GiB/s utgående och 370 MiB/s ingress-genomflöde. För att uppnå maximal prestanda skalning sprider du belastningen över flera virtuella datorer. Se [fel söknings guiden](storage-troubleshooting-files-performance.md) för några vanliga prestanda problem och lösningar.

#### <a name="bursting"></a>Bursting "

Premium-filresurser kan överföra sina IOPS upp till en faktor på tre. Burst-överföring automatiseras och fungerar baserat på ett kredit system. Burst-överföring fungerar på bästa möjliga sätt och burst-gränsen är inte en garanti, fil resurser kan överföras *till* gränsen.

Krediterna ackumuleras i en burst-Bucket när trafiken för fil resursen är under bas linje IOPS. Till exempel har en 100 GiB-resurs 100 bas linje IOPS. Om den faktiska trafiken på resursen var 40 IOPS för ett angivet intervall på 1 sekund, krediteras 60 oanvända IOPS till en burst-Bucket. Dessa krediter kommer sedan att användas senare när åtgärder skulle överskrida bas linjens IOPs.

> [!TIP]
> Storlek på burst-Bucket = bas linje för IOPS * 2 * 3600.

När en resurs överskrider bas linjens IOPS och har krediter i en burst-Bucket, överförs den till burst. Resurser kan fortsätta att överföra så länge krediterna är kvar, men resurser som är mindre än 50 TiB hålls bara vid burst-gränsen i upp till en timme. Resurser som är större än 50 TiB får tekniskt ut en Tim gräns på upp till två timmar, men detta baseras på antalet överförda burst-krediter. Varje i/o utöver bas linje IOPS förbrukar en kredit och när alla krediter förbrukas kommer resursen att återgå till bas linje IOPS.

Dela krediter har tre tillstånd:

- Påförs när fil resursen använder mindre än bas linjens IOPS.
- Avböjande, när fil resursen är burst-överföring.
- Återstående konstant, när det inte finns några krediter eller bas linje IOPS som används.

Nya fil resurser börjar med det fullständiga antalet krediter i sin burst-Bucket. Burst-krediter kommer inte att periodiseras om resursens IOPS sjunker under bas linje IOPS på grund av begränsning av servern.

## <a name="file-share-redundancy"></a>Redundans för fil resurs

Azure Files standard resurser stöder fyra alternativ för dataredundans: lokalt redundant lagring (LRS), Zone-redundant lagring (ZRS), Geo-redundant lagring (GRS) och geo-Zone-redundant lagring (GZRS) (för hands version).

Azure Files Premium-resurser stöder både LRS och ZRS, ZRS är för närvarande tillgänglig i en mindre delmängd regioner.

I följande avsnitt beskrivs skillnaderna mellan olika alternativ för redundans:

### <a name="locally-redundant-storage"></a>Lokalt redundant lagring

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Zon redundant lagring

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Geografiskt redundant lagring.

> [!Warning]  
> Om du använder Azure-filresursen som en moln slut punkt i ett GRS lagrings konto bör du inte initiera redundans för lagrings konto. Om du gör det upphör synkroniseringen att fungera och det kan också leda till oväntad data förlust när det gäller nynivåbaserade filer. Om en Azure-region förloras utlöser Microsoft lagrings kontots redundans på ett sätt som är kompatibelt med Azure File Sync.

Geo-redundant lagring (GRS) är utformat för att tillhandahålla minst 99.99999999999999% (16 9) objekt hållbarhet för objekt under ett år genom att replikera dina data till en sekundär region som är hundratals mil bort från den primära regionen. Om ditt lagrings konto har GRS aktiverat, är dina data varaktiga även vid ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte går att återvinna.

Om du väljer Geo-redundant lagring med Läs behörighet (RA-GRS) bör du känna till att Azure-filen inte har stöd för Geo-redundant lagring med Läs åtkomst (RA-GRS) i vilken region som helst. Fil resurser i RA-GRS-lagrings kontot fungerar som de skulle ha i GRS-konton och debiteras GRS-priserna.

GRS replikerar dina data till ett annat data Center i en sekundär region, men dessa data är tillgängliga för läsning endast om Microsoft initierar en redundansväxling från den primära till den sekundära regionen.

För ett lagrings konto med GRS aktiverat replikeras alla data först med lokalt redundant lagring (LRS). En uppdatering allokeras först till den primära platsen och replikeras med hjälp av LRS. Uppdateringen replikeras sedan asynkront till den sekundära regionen med hjälp av GRS. När data skrivs till den sekundära platsen replikeras de också inom den platsen med hjälp av LRS.

Både den primära och sekundära regionen hanterar repliker mellan olika fel domäner och uppgraderings domäner inom en lagrings skalnings enhet. Enhetens skalnings enhet är den grundläggande replikeringstjänsten i data centret. Replikering på den här nivån tillhandahålls av LRS; Mer information finns i [Lokalt Redundant lagring (LRS): låg kostnads data redundans för Azure Storage](../common/storage-redundancy-lrs.md).

Tänk på följande när du bestämmer vilket replikeringsalternativ som ska användas:

* Geo-Zone-redundant lagring (GZRS) (för hands version) ger hög tillgänglighet tillsammans med maximal hållbarhet genom att replikera data synkront över tre tillgänglighets zoner i Azure och sedan replikera data asynkront till den sekundära regionen. Du kan också aktivera Läs behörighet till den sekundära regionen. GZRS har utformats för att ge minst 99.99999999999999% (16 9) objekts hållbarhet under ett angivet år. Mer information om GZRS finns i [geo-Zone-redundant lagring för hög tillgänglighet och maximal hållbarhet (för hands version)](../common/storage-redundancy-gzrs.md).
* Zone-redundant lagring (ZRS) ger hög tillgänglighet med synkron replikering och kan vara ett bättre alternativ för vissa scenarier än GRS. Mer information om ZRS finns i [ZRS](../common/storage-redundancy-zrs.md).
* Asynkron replikering innebär en fördröjning från den tid som data skrivs till den primära regionen till när den replikeras till den sekundära regionen. I händelse av en regional katastrof kan ändringar som ännu inte har repliker ATS till den sekundära regionen gå förlorade om data inte kan återställas från den primära regionen.
* Med GRS är repliken inte tillgänglig för Läs-eller skriv åtkomst om inte Microsoft initierar en redundansväxling till den sekundära regionen. Om det är en redundansväxling har du Läs-och skriv åtkomst till dessa data när redundansväxlingen har slutförts. Mer information finns i [rikt linjerna för haveri beredskap](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Publicera till större fil resurser (standard nivån)

Det här avsnittet gäller endast för standard fil resurser. Alla Premium-filresurser är tillgängliga med 100 TiB-kapacitet.

### <a name="restrictions"></a>Begränsningar

- Det går inte att konvertera LRS/ZRS till GRS/GZRS för alla lagrings konton där stora fil resurser är aktiverade.

### <a name="regional-availability"></a>Regional tillgänglighet

Standard fil resurser är tillgängliga i alla regioner upp till 5 TiB. I vissa regioner är de tillgängliga med en 100 TiB-gräns, dessa regioner visas i följande tabell:

|Region |Redundans stöds |
|-------|---------|
|Australien, östra |LRS     |
|Australien, sydöstra|LRS |
|Kanada, centrala  |LRS     |
|Kanada, östra     |LRS     |
|Indien, centrala  |LRS     |
|USA, centrala *   |LRS     |
|Asien, östra      |LRS     |
|USA, östra *        |LRS     |
|USA, östra 2 *      |LRS     |
|Frankrike, centrala |LRS, ZRS|
|Frankrike, södra   |LRS     |
|USA, norra centrala |LRS     |
|Europa, norra   |LRS     |
|Indien, södra    |LRS     |
|Asien, sydöstra |LRS, ZRS|
|USA, västra centrala|LRS     |
|Västeuropa *    |LRS, ZRS|
|USA, västra *        |LRS     |
|USA, västra 2      |LRS, ZRS|

\* som stöds för nya konton, har inte alla befintliga konton slutfört uppgraderings processen. Du kan kontrol lera om dina befintliga lagrings konton har slutfört uppgraderings processen genom att försöka [Aktivera stora fil resurser](storage-files-how-to-create-large-file-share.md).

För att hjälpa oss att prioritera nya regioner och funktioner kan du fylla i den här [undersökningen](https://aka.ms/azurefilesatscalesurvey).

### <a name="enable-and-create-larger-file-shares"></a>Aktivera och skapa större fil resurser

Om du vill börja använda större fil resurser kan du läsa artikeln [så här aktiverar och skapar du stora fil resurser](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>Data tillväxt mönster

Idag är den maximala storleken för en Azure-filresurs 100 TiB. På grund av den här aktuella begränsningen måste du ta hänsyn till den förväntade data tillväxten när du distribuerar en Azure-filresurs.

Det går att synkronisera flera Azure-filresurser till en enda Windows-fil server med Azure File Sync. På så sätt kan du se till att äldre och stora fil resurser som du kan ha lokala kan läggas till Azure File Sync. Mer information finns i [Planera för en Azure File Sync distribution](storage-files-planning.md).

## <a name="data-transfer-method"></a>Data överförings metod

Det finns många enkla alternativ för att överföra data från en befintlig fil resurs, till exempel en lokal fil resurs, till Azure Files. Några populära exempel är (ej fullständig lista):

* **Azure File Sync**: som en del av en första synkronisering mellan en Azure-filresurs (en "moln slut punkt") och ett namn område i Windows-katalogen (en "Server slut punkt") replikerar Azure File Sync alla data från den befintliga fil resursen till Azure Files.
* **[Azure import/export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : tjänsten Azure import/export gör att du på ett säkert sätt kan överföra stora mängder data till en Azure-filresurs genom att leverera hård diskar till ett Azure-datacenter. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy är ett välkänt kopierings verktyg som levereras med Windows och Windows Server. Robocopy kan användas för att överföra data till Azure Files genom att montera fil resursen lokalt och sedan använda den monterade platsen som mål i Robocopy-kommandot.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy är ett kommando rads verktyg som utformats för att kopiera data till och från Azure Files, samt Azure Blob Storage med hjälp av enkla kommandon med optimala prestanda.

## <a name="next-steps"></a>Nästa steg
* [Planera för en Azure File Sync distribution](storage-sync-files-planning.md)
* [Distribuera Azure Files](storage-files-deployment-guide.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)

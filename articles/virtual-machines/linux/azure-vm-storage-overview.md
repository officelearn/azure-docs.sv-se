---
title: Virtuella Azure Linux-datorer och Azure Storage | Microsoft Docs
description: "Beskriver Azure Standard och Premium-lagring och både hanterade och ohanterade diskar med Linux-datorer."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/7/2017
ms.author: rasquill
ms.openlocfilehash: 5e8a62bb180de7288531139594cb61440cab04c5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="azure-and-linux-vm-storage"></a>Azure-och Linux VM
Azure Storage är molnlagringslösningen för moderna program som kräver hållbarhet, tillgänglighet och skalbarhet för att uppfylla kundernas behov.  Förutom att göra det möjligt för utvecklare att bygga storskaliga program som stöd för nya scenarier, innehåller Azure Storage också lagringsgrunden för Azure Virtual Machines.

## <a name="managed-disks"></a>Managed Disks

Virtuella Azure-datorer är nu tillgängliga med [Azure hanterade diskar](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), vilket innebär att du kan skapa dina virtuella datorer utan att skapa eller hantera alla [Azure Storage-konton](../../storage/common/storage-introduction.md) själv. Anger du om du vill använda Premium eller standardlagring och hur stor disken bör vara och Azure skapar Virtuella diskar åt dig. Virtuella datorer med hanterade diskar har många viktiga funktioner, inklusive:

- Stöd för automatisk skalbarhet. Azure skapar diskarna och hanterar det underliggande lagringsutrymmet för att stödja upp till 10 000 diskar per prenumeration.
- Ökar tillförlitligheten med Tillgänglighetsuppsättningar. Azure säkerställer att Virtuella diskar är isolerade från varandra i Tillgänglighetsuppsättningar automatiskt.
- Ökad åtkomstkontroll. Hanterade diskar exponera olika åtgärder som styrs av [rollbaserad åtkomstkontroll (RBAC)](../../active-directory/role-based-access-control-what-is.md).

Priser för hanterade diskar skiljer sig för den ohanterade diskar. Den här informationen finns [priser och fakturering för hanterade diskar](../windows/managed-disks-overview.md#pricing-and-billing).

Du kan konvertera befintliga virtuella datorer med ohanterad diskar som använder hanterade diskar med [az vm konvertera](/cli/azure/vm#convert). Mer information finns i [konvertera en Linux VM från ohanterade diskar till Azure-hanterade diskar](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Du kan inte konvertera en ohanterad disk till en hanterad disk om ohanterade disken är i ett lagringskonto som är, eller när som helst har, krypteras med [Azure Storage Service kryptering (SSE)](../../storage/common/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Följ anvisningarna nedan hur du konverterar ohanterad diskar som är eller har gjorts i en krypterad storage-konto:

- Kopiera den virtuella hårddisken (VHD) med [az storage blob kopiera start](/cli/azure/storage/blob/copy#start) till ett lagringskonto som aldrig har aktiverats för Azure Storage Service-kryptering.
- Skapa en virtuell dator som använder hanterade diskar och ange att VHD-filen när du skapar med [az vm skapa](/cli/azure/vm#create), eller
- Anslut den kopierade virtuella Hårddisken med [az vm disk bifoga](/cli/azure/vm/disk#attach) till en aktiv virtuell dator med hanterade diskar.


## <a name="azure-storage-standard-and-premium"></a>Azure Storage: Standard och Premium
Virtuella Azure-datorer – om du använder hanterade diskar eller ohanterad--kan baseras på lagringsdiskar som standard eller premium-lagringsdiskar. När du använder portalen för att välja den virtuella datorn måste du växla en listrutan på den **grunderna** skärmen för att visa både standard och premium-diskar. När växlas till SSD, endast premium-lagring som är aktiverade virtuella datorer visas alla backas upp av SSD enheter.  När växlas till Hårddisk, standard-storage-aktiverade virtuella datorer som backas upp av snurrande diskenheter visas, tillsammans med premium storage VMs backas upp av SSD.

När du skapar en virtuell dator från den `azure-cli` du kan välja mellan standard och premium när du väljer VM-storlek via den `-z` eller `--vm-size` cli-flaggan.

## <a name="creating-a-vm-with-a-managed-disk"></a>Skapa en virtuell dator med en hanterad Disk

I följande exempel kräver Azure CLI 2.0, vilket du kan [installera här](/cli/azure/install-azure-cli).

Börja med att skapa en resursgrupp för att hantera resurser med [az gruppen skapa](/cli/azure/group#create):

```azurecli
az group create --location westus --name myResourceGroup
```

Nu skapa den virtuella datorn med [az vm skapa](/cli/azure/vm#create). Ange ett unikt `--public-ip-address-dns-name` argument som `mypublicdns` utförs troligen.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns
```

Exemplet ovan skapar en virtuell dator med en hanterad disk i ett standardlagringskonto. Om du vill använda ett premiumlagringskonto, lägger du till den `--storage-sku Premium_LRS` argument, som i följande exempel:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns \
    --storage-sku Premium_LRS
```

## <a name="standard-storage"></a>Standard Storage
Azure Storage som Standard är standardtypen för lagring.  Standardlagring är kostnadseffektiv samtidigt som de performant.  

## <a name="premium-storage"></a>Premium Storage
Azure Premium Storage ger stöd för virtuella datorer som körs I/O-intensiva arbetsbelastningar diskar med hög prestanda, låg latens. Virtuell dator (VM)-diskar som använder Premium-lagring kan du lagra data på solid state-hårddiskar (SSD). Du kan migrera programmets Virtuella diskar till Azure Premium-lagring för att dra nytta av hastighet och prestanda för dessa diskar.

Premium-lagringsfunktioner:

* Premium lagringsdiskar: Azure Premium-lagring stöder VM-diskar som kan kopplas till DS, DSv2 eller GS-serien virtuella Azure-datorer.
* Premium Sidblob: Premium-lagring stöder Azure Sidblobbar som används för att hålla beständiga diskar för virtuella Azure-datorer (VM).
* Premium lokalt Redundant lagring: Ett Premium Storage-konto bara har stöd för lokalt Redundant lagring (LRS) som replikeringsalternativet och fortsätter tre kopior av data inom en enskild region.
* [Premium-lagring](../../storage/common/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Premium-lagring stöds virtuella datorer
Premium-lagring stöder DS-serien, DSv2-serien GS-serien och Fs-serien Azure virtuella datorer (VM). Du kan använda Standard- och Premium diskar med lagringsutrymme med Premium-lagring som stöds av virtuella datorer. Men du kan inte använda Premium-lagring diskar med VM-serien, som inte är kompatibel Premium-lagring.

Följande är de Linux-distributioner som vi verifieras med Premium-lagring.

| Distribution | Version | Stöds Kernel |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x, 8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| Centos |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+, 7.2+ | |

## <a name="azure-file-storage"></a>Azure File storage
Azure File storage erbjuder filresurser i molnet genom att använda SMB-standardprotokollet. Du kan migrera företagsprogram som förlitar sig på filservrar till Azure med Azure-filer. Program som körs i Azure kan enkelt montera filresurser från Azure virtuella datorer som kör Linux. Och med den senaste versionen av fillagring, du kan också montera en filresurs från ett lokalt program som stöder SMB 3.0.  Eftersom filresurser är SMB-resurser, kan du komma åt dem via standardfilsystemet API: er.

Fillagring bygger på samma teknik som lagring av Blob-, tabell- och kön, så File storage erbjuder tillgänglighet, hållbarhet, skalbarhet och geo-redundans som är inbyggd i Azure storage-plattformen. Mer information om prestandamål för File storage och begränsningar finns i Azure Storage skalbarhets- och prestandamål.

* [Använd Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Frekvent
Azure frekventa lagringsnivå är optimerad för att lagra data som används ofta.  Frekvent är standardtypen för lagring för blob-Arkiv.

## <a name="cool-storage"></a>Lågfrekvent
Azure lågfrekvent åtkomstnivå är optimerad för att lagra data som används ofta och långlivade. Exempel på användningsområden för lågfrekvent innehåller säkerhetskopior, medieinnehåll, vetenskapliga data, efterlevnad och arkiveringsdata. I allmänhet är alla data som används sällan en perfekt kandidat för lågfrekvent.

|  | Frekvent lagringsnivå | Lågfrekvent lagringsnivå |
|:--- |:---:|:---:|
| Tillgänglighet |99,9 % |99 % |
| Tillgänglighet (RA-GRS-läsningar) |99,99 % |99,9 % |
| Avgifter för användning |Högre kostnader för lagring |Lägre kostnader för lagring |
| Lägre åtkomst |Högre åtkomst | |
| och transaktionskostnader |och transaktionskostnader | |

## <a name="redundancy"></a>Redundans
Data i ditt Microsoft Azure storage-konto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet, uppfyller Azure-serviceavtalet för lagring även i händelse av tillfälliga maskinvarufel.

När du skapar ett lagringskonto måste du välja något av följande replikeringsalternativ:

* Lokalt redundant lagring (LRS)
* Zonredundant lagring (ZRS)
* Geo-redundant lagring (GRS)
* Geo-redundant lagring med läsbehörighet (RA-GRS)

### <a name="locally-redundant-storage"></a>Lokalt redundant lagring
Lokalt redundant lagring (LRS) replikerar data inom den region där du skapade ditt lagringskonto. Om du vill maximera hållbarhet replikeras alla begäranden som görs mot data i ditt lagringskonto tre gånger. Dessa tre repliker finns i separata feldomäner och uppgraderingsdomäner.  En begäran returnerar har endast när den har skrivits till alla tre repliker.

### <a name="zone-redundant-storage"></a>Zonredundant lagring
Zonredundant lagring (ZRS) replikerar data mellan två eller tre anläggningar, antingen i en enda region eller mellan två regioner, vilket ger högre hållbarhet än LRS. Om ditt lagringskonto har ZRS aktiverat, sedan dina data skyddas även i händelse av fel på någon av funktionerna.

### <a name="geo-redundant-storage"></a>Geografiskt redundant lagring.
GEO-redundant lagring (GRS) replikerar data till en sekundär region hundratals mil bort från den primära regionen är. Om ditt lagringskonto har GRS aktiverat, sedan dina data skyddas även vid en fullständig regionalt strömavbrott eller en katastrof där den primära regionen inte kan återställas.

### <a name="read-access-geo-redundant-storage"></a>Geo-redundant lagring med läsbehörighet
Geo-redundant lagring med läsbehörighet (RA-GRS) maximerar tillgänglighet för ditt lagringskonto genom att ange skrivskyddad åtkomst till data i den sekundära platsen, förutom replikering mellan två regioner som tillhandahålls av GRS. I händelse av att data inte är tillgänglig i den primära regionen kan ditt program läsa data från den sekundära regionen.

För en djupdykning i Azure storage redundans finns:

* [Azure Storage-replikering](../../storage/common/storage-redundancy.md)

## <a name="scalability"></a>Skalbarhet
Azure Storage är extremt skalbart. Du kan lagra och bearbeta många hundra terabyte data för att ge stöd för stordatascenarier som krävs av vetenskapsprogram, medieprogram och program för ekonomisk analys. Men du kan också lagra små mängder data som behövs för en liten företagswebbplats. Oavsett dina behov så betalar du bara för de data du lagrar. Azure Storage lagrar för närvarande flera biljoner unika kundobjekt och hanterar många miljoner förfrågningar per sekund i genomsnitt.

För standardlagring konton: ett standardlagringskonto har en maximal Totalt antal förfrågningar av 20 000 IOPS. Det totala antalet IOPS för alla virtuella datordiskar på ett standardlagringskonto bör inte överskrida den här gränsen.

För premium storage-konton: ett premiumlagringskonto har en maximal totala genomflödet andel 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.

## <a name="availability"></a>Tillgänglighet
Vi garanterar att vi minst 99,99 % (99,9 % för kalla åtkomstnivåer) av tiden kommer att framgångsrikt bearbeta begäranden om att läsa data från Read Access-Geo Redundant (RA-GRS)-lagringskonton förutsatt att misslyckade försök att läsa data från den primära regionen försöks igen på den sekundära regionen.

Vi garanterar att vi minst 99,9 % (99 % för kalla åtkomstnivåer) av tiden kommer att framgångsrikt bearbeta begäranden om att läsa data till konton för lokalt redundant lagring (LRS), Zonredundant lagring (ZRS) och geografiskt redundant lagring (GRS).

Vi garanterar att vi minst 99,9 % (99 % för kalla åtkomstnivåer) av tiden kommer att framgångsrikt bearbeta begäranden om att skriva data till konton för lokalt redundant lagring (LRS), Zonredundant lagring (ZRS), geografiskt redundant lagring (GRS) och Read Access-Geo Redundant Storage (RA-GRS)-konton.

* [Azure SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>Regioner
Azure är allmänt tillgänglig i 30 regioner runtom i världen och har tillkännagivit planer för 4 ytterligare regioner. Geografisk expansion är prioriterat för Azure, eftersom detta gör det möjligt för våra kunder att få bättre prestanda, och det stöder deras krav och önskemål gällande dataplacering.  Azures senaste region att starta är i Tyskland.

Microsoft Cloud-Tyskland ger en differentierad möjlighet att Microsoft Cloud-tjänsterna redan tillgängliga över Europa, skapa större möjligheter för innovation och tillväxt för hög reglerade partner och kunder i Tyskland Europeiska unionen (EU) och den europeiska associationen (EFTA).

Kundinformation i dessa nya datacenter i Magdeburg och Frankfurt, hanteras kontrolleras av en data-förvaltare, T-Systems internationella, ett oberoende tyska företag och dotterbolag till Deutsche Telekom. Microsofts kommersiella molntjänster i dessa Datacenter följa tyska föreskrifter för datahantering och ge kunderna ytterligare alternativ för hur och var data bearbetas.

* [Azure-regioner karta](https://azure.microsoft.com/regions/)

## <a name="security"></a>Säkerhet
Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner som tillsammans ger utvecklare möjligheten att skapa säkra program. Lagringskontot själva kan skyddas med hjälp av rollbaserad åtkomstkontroll och Azure Active Directory. Data kan skyddas vid överföring mellan en program- och Azure med hjälp av kryptering på klientsidan, HTTPS och SMB 3.0. Data kan anges krypteras automatiskt när skrivs till Azure Storage med Storage Service kryptering (SSE). Operativsystemet och datadiskarna som används av virtuella datorer kan anges krypteras med hjälp av Azure Disk Encryption. Delegerad åtkomst till dataobjekt i Azure Storage kan tilldelas använder signaturer för delad åtkomst.

### <a name="management-plane-security"></a>Hantering av plan säkerhet
Management-plan består av de resurser som används för att hantera ditt lagringskonto. I det här avsnittet kommer vi distributionsmodell Azure Resource Manager och hur du använder rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till dina lagringskonton. Vi kommer även tala om hur du hanterar din lagringskontonycklar och hur du återskapa dem.

### <a name="data-plane-security"></a>Säkerhet för data-plan
I det här avsnittet ska vi titta på att tillåta åtkomst till de faktiska dataobjekt i ditt lagringskonto, till exempel blobbar, filer, köer och tabeller som använder signaturer för delad åtkomst och åtkomstregler lagras. Vi tar upp både servicenivåer SAS och konto-nivå SAS. Vi också se hur du begränsar åtkomsten till en specifik IP-adress (eller intervall av IP-adresser), hur du begränsar det protokoll som används för HTTPS och hur återkalla en signatur för delad åtkomst utan att vänta på att gå ut.

## <a name="encryption-in-transit"></a>Kryptering under överföring
Det här avsnittet beskrivs hur du skyddar data vid överföring till eller från Azure Storage. Lär dig om att rekommenderade använda HTTPS och kryptering som används av SMB 3.0 för Azure-filresurser. Vi kommer också ta en titt på klientsidan kryptering, som gör det möjligt att kryptera data innan det överförs till lagring i ett klientprogram och dekryptera data när det överförs utanför lagring.

## <a name="encryption-at-rest"></a>Kryptering i vila
Vi pratar om Storage Service-kryptering (SSE) och hur du kan aktivera det för ett lagringskonto, vilket resulterar i din blockblobbar, sidblobbar, och tilläggsblobar som krypteras automatiskt när skrivs till Azure Storage. Vi kommer också titta på hur du kan använda Azure Disk Encryption och utforska grundläggande skillnader och fall av diskkryptering jämfört med SSE jämfört med kryptering på klientsidan. Vi ser kort på FIPS-kompatibilitet för USA Offentliga datorer.

* [Azure Storage-säkerhetsguiden](../../storage/common/storage-security-guide.md)

## <a name="temporary-disk"></a>Diskutrymme
Varje virtuell dator innehåller en tillfällig disk. Den tillfälliga disken tillhandahåller kortsiktig lagring för program och processer och är avsedd att bara lagra data, till exempel sida eller växla filer. Data på den tillfälliga disken kan gå förlorade under en [underhållshändelse](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) eller när du [distribuera en virtuell dator](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Data på den tillfälliga enheten under en standard omstart av den virtuella datorn ska sparas.

På virtuella Linux-datorer disken är vanligtvis **/dev/sdb** har formaterats och monteras **/mnt** av Azure Linux-agenten. Storleken på den tillfälliga disken beror på storleken på den virtuella datorn. Mer information finns i [storlekar för virtuella Linux-datorer](sizes.md).

Mer information om hur Azure använder temporär disk finns [förstå den tillfälliga enheten på virtuella datorer i Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="cost-savings"></a>Besparingar
* [Kostnad för datalagring](https://azure.microsoft.com/pricing/details/storage/)
* [Lagringsberäknaren kostnad](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Lagringsgränser
* [Lagringsgränser för tjänsten](../../azure-subscription-service-limits.md#storage-limits)

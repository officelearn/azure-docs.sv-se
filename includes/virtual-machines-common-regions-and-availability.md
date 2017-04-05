# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Regioner och tillgänglighet för virtuella datorer i Azure
Det är viktigt att förstå hur och var dina virtuella datorer körs i Azure och vilka alternativ du har för att maximera prestanda, tillgänglighet och redundans. Azure körs på ett antal datacenter över hela världen. Dessa datacenter är grupperade i geografiska regioner så att du kan välja var du vill bygga dina program. Den här artikeln ger en översikt över funktionerna för tillgänglighet och redundans i Azure.

## <a name="what-are-azure-regions"></a>Vad är Azure-regioner?
Med Azure kan du skapa resurser, till exempel virtuella datorer, i definierade geografiska områden som ”USA, västra”, ”Europa, norra” eller ”Asien, sydöstra”. Det finns för närvarande 30 Azure-regioner runtom i världen. Se [listan över regioner och deras platser](https://azure.microsoft.com/regions/). I varje region finns flera datacenter för att skapa förutsättningar för redundans och tillgänglighet. Detta ger dig flexibilitet när du bygger dina program. Du kan skapa virtuella datorer som ligger nära användarna och du kan uppfylla juridiska och skattemässiga krav samt krav på regelefterlevnad.

## <a name="special-azure-regions"></a>Särskilda Azure-regioner
Det finns några särskilda Azure-regioner för regelefterlevnad och juridiska krav som du kanske vill använda när du bygger dina program. De särskilda regionerna innefattar:

* **Virginia (USA-förvaltad region)** och **Iowa (USA-förvaltad region)**
  * En fysisk och logisk nätverksisolerad instans av Azure för amerikanska myndigheter och partner som drivs av säkerhetskontrollerad amerikansk personal. Innefattar ytterligare efterlevnadscertifieringar som [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) och [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Läs mer om [Azure Government](https://azure.microsoft.com/features/gov/).
* **Centrala Indien**, **Södra Indien** och **Västra Indien**
  * Dessa regioner är för närvarande endast tillgängliga för kunder med volymlicenser och partner med en lokal registrering i Indien. 2016 är dessa tillgängliga för användare som har köpt onlineprenumerationer.
* **Östra Kina** och **Norra Kina**
  * Dessa regioner är tillgängliga via ett unikt partnerskap mellan Microsoft och 21Vianet, vilket innebär att Microsoft inte direkt underhåller dessa datacenter. Läs mer om [Microsoft Azure i Kina](http://www.windowsazure.cn/).
* **Centrala Tyskland** och **Nordöstra Tyskland**
  * Dessa regioner är för närvarande tillgängliga via en dataförvaltningsmodell där kundernas data blir kvar i Tyskland och kontrolleras av T-Systems, ett företag som tillhör Deutsche Telekom, som fungerar som den tyska dataförvaltningen.

## <a name="region-pairs"></a>Regionpar
Varje Azure-region är kopplad till en annan region inom samma geografiska område (till exempel USA, Europa och Asien). På så sätt kan resurser som VM-lagring replikeras över geografiska områden som inte troligtvis påverkas samtidigt av naturkatastrofer, oroligheter i landet, strömavbrott eller avbrott i fysiska nätverk. Ytterligare fördelar med regionpar:

* Vid ett större Azure-avbrott prioriteras en region i varje par för att minska tiden för programåterställning. 
* Planerade Azure-uppdateringar distribueras en i taget till kopplade regioner för att minimera nedtid och risk för programavbrott.
* Data finns kvar i samma geografiska region som den andra regionen i paret (med undantag för Södra Brasilien) av skatte- och jurisdiktionsmässiga skäl.

Exempel på regionpar:

| Primär | Sekundär |
|:--- |:--- |
| Västra USA |Östra USA |
| Norra Europa |Västra Europa |
| Sydostasien |Östasien |

Se [den fullständiga listan över regionpar](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Funktionstillgänglighet
Vissa tjänster eller VM-funktioner är endast tillgängliga i vissa regioner, till exempel särskilda VM-storlekar eller lagringstyper. Det finns också vissa globala Azure-tjänster som inte kräver att du väljer en viss region, till exempel [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) och [Azure DNS](../articles/dns/dns-overview.md). När du utformar din programmiljö kan du kontrollera [tillgängligheten av Azure-tjänster för varje region](https://azure.microsoft.com/regions/#services). 

## <a name="storage-availability"></a>Lagringstillgänglighet
Det är viktigt att förstå Azure-regioner och geografiska områden när du ska välja bland de tillgängliga alternativen för lagringsreplikering. Det finns olika replikeringsalternativ beroende på vilken lagringstyp du har.

**Azure Managed Disks**
* Lokalt redundant lagring (LRS)
  * Replikerar data tre gånger inom den region där du skapade ditt lagringskonto.

**Diskar baserade på lagringskonto**
* Lokalt redundant lagring (LRS)
  * Replikerar data tre gånger inom den region där du skapade ditt lagringskonto.
* Zonredundant lagring (ZRS)
  * Replikerar data tre gånger mellan två eller tre anläggningar, antingen inom en enda region eller mellan två regioner.
* Geo-redundant lagring (GRS)
  * Replikerar data till en sekundär region som ligger hundratals kilometer från den primära regionen.
* Geo-redundant lagring med läsbehörighet (RA-GRS)
  * Replikerar data till en sekundär region som med GRS, och ger även skrivskyddad åtkomst till data på den sekundära platsen.

Följande tabell ger en snabb översikt över skillnaderna mellan lagringsreplikeringstyperna:

| Replikeringsstrategi | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Data replikeras över flera anläggningar. |Nej |Ja |Ja |Ja |
| Data kan läsas från den sekundära platsen och från den primära platsen. |Nej |Nej |Nej |Ja |
| Antal kopior av data som finns på olika noder. |3 |3 |6 |6 |

Du kan läsa mer om [Azure Storage-replikeringsalternativen här](../articles/storage/storage-redundancy.md). Mer information om hanterade diskar finns i [Översikt över Azure Managed Disks](../articles/storage/storage-managed-disks-overview.md).

### <a name="storage-costs"></a>Lagringskostnader
Priserna varierar beroende på vilken lagringstyp och tillgänglighet du väljer.

**Azure Managed Disks**
* Premium Managed Disks använder SSD-enheter och Standard Managed Disks använder vanliga roterande diskar. Både Premium och Standard Managed Disks debiteras baserat på etablerad kapacitet för disken.

**Ohanterade diskar**
* Premium Storage använder SSD-enheter och debiteras baserat på diskens kapacitet.
* Standard Storage använder roterande diskar och debiteras baserat på den kapacitet som används och önskad lagringstillgänglighet.
  * För RA-GRS tillkommer en extra dataöverföringsavgift för geo-replikering för bandbredden som används för att replikera dessa data till en annan Azure-region.

Se [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/) för prisinformation för olika lagringstyper och tillgänglighetsalternativ.

## <a name="azure-images"></a>Azure-avbildningar
I Azure skapas virtuella datorer från en avbildning. Avbildningar hämtas oftast från [Azure Marketplace](https://azure.microsoft.com/marketplace/) där partners kan tillhandahålla förkonfigurerade fullständiga operativsystem eller programavbildningar.

När du skapar en virtuell dator från en avbildning i Azure Marketplace arbetar du i själva verket med mallar. Azure Resource Manager-mallar är deklarativa JSON-filer (JavaScript Object Notation) som kan användas för att skapa komplexa programmiljöer med virtuella datorer, lagring, virtuella nätverk m.m. Läs mer om hur du använder [Azure Resource Manager-mallar](../articles/azure-resource-manager/resource-group-overview.md) och hur du [skapar egna mallar](../articles/resource-group-authoring-templates.md).

Du kan också skapa egna anpassade bilder och överföra dem med hjälp av [Azure CLI](../articles/virtual-machines/linux/upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [Azure PowerShell](../articles/virtual-machines/windows/upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att snabbt skapa egna virtuella datorer anpassade till dina specifika krav.

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
En tillgänglighetsuppsättning är en logisk gruppering av virtuella datorer som gör att Azure kan förstå hur ett program är utformat och kan tillhandahålla redundans och tillgänglighet. Vi rekommenderar att två eller fler virtuella datorer skapas i en tillgänglighetsuppsättning för att ge programmet hög tillgänglighet och uppfylla [99,95 procent drifttid enligt serviceavtalet för Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). När en enskild virtuell dator använder [Azure Premium Storage](../articles/storage/storage-premium-storage.md) gäller serviceavtalet för Azure för oplanerat underhåll. En tillgänglighetsuppsättning består av två ytterligare grupperingar som skyddar mot maskinvarufel och gör att uppdateringar kan utföras på ett säkert sätt: feldomäner och uppdateringsdomäner.

![Skiss på en konfiguration med uppdateringsdomäner och feldomäner](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

Läs mer om hur du hanterar tillgänglighet för [virtuella Linux-datorer](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [virtuella Windows-datorer](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="fault-domains"></a>Feldomäner
En feldomän är en logisk grupp av underliggande maskinvara som delar en gemensam strömkälla och nätverksswitch, ungefär som ett rack i ett lokalt datacenter. När du skapar virtuella datorer i en tillgänglighetsuppsättning distribuerar Azure-plattformen automatiskt dina virtuella datorer mellan dessa feldomäner. På så sätt begränsas påverkan av potentiella fel på fysisk maskinvara, nätverksavbrott och strömavbrott.

#### <a name="managed-disk-fault-domains-and-availability-sets"></a>Feldomäner och tillgänglighetsuppsättningar för hanterad disk
För virtuella datorer som använder [Azure Managed Disks](../articles/storage/storage-faq-for-disks.md) justeras de virtuella datorerna efter feldomänerna för hanterade diskar när en hanterad tillgänglighetsuppsättning används. På så sätt säkerställs att alla hanterade diskar som är kopplade till en virtuell dator finns i samma feldomän. Endast virtuella datorer med hanterade diskar kan skapas i en hanterad tillgänglighetsuppsättning. Antalet feldomäner kan vara två eller tre, beroende på region.

### <a name="update-domains"></a>Uppdateringsdomäner
En uppdateringsdomän är en logisk grupp av underliggande maskinvara som kan underhållas eller startas om samtidigt. När du skapar virtuella datorer i en tillgänglighetsuppsättning distribuerar Azure-plattformen automatiskt de virtuella datorerna mellan dessa uppdateringsdomäner. På så sätt säkerställs att minst en instans av ditt program alltid körs vid ett periodiskt underhåll av Azure-plattformen. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om.

## <a name="next-steps"></a>Nästa steg
Nu kan du börja använda dessa tillgänglighets- och redundansfunktioner till att bygga din egen Azure-miljö. Metodtips hittar du i [Metodtips för Azure-tillgänglighet](../articles/best-practices-availability-checklist.md).


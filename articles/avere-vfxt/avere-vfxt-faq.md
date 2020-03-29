---
title: Vanliga frågor och svar - Avere vFXT för Azure
description: Vanliga frågor och svar om Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 186b2c048a9de42318e4af287393d731a4eb16f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153470"
---
# <a name="avere-vfxt-for-azure-faq"></a>Vanliga frågor och svar om Avere vFXT för Azure

Den här artikeln svarar på frågor som kan hjälpa dig att avgöra om Avere vFXT för Azure är rätt för dina behov. Den ger grundläggande information om Avere vFXT och förklarar hur det fungerar med andra Azure-komponenter och med produkter från externa leverantörer.

## <a name="general"></a>Allmänt

### <a name="what-is-avere-vfxt-for-azure"></a>Vad är Avere vFXT för Azure?

Avere vFXT för Azure är ett högpresterande filsystem som cachelagrar aktiva data i Azure-beräkning för effektiv bearbetning av kritiska arbetsbelastningar.

### <a name="is-avere-vfxt-a-storage-solution"></a>Är Avere vFXT en lagringslösning?

Nej. Avere vFXT för Azure är en *filsystemcache* som ansluter till lagringsmiljöer, till exempel din EMC- eller NetApp NAS-fil eller en Azure blob-behållare. Avere vFXT effektiviserar databegäranden från klienter och cachelagrar de data som används för att förbättra prestanda i stor skala och över tid. Avere vFXT själv lagrar inte data. Den har ingen information om mängden data som lagras bakom den.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Är Avere vFXT en nivåindelningslösning?

Avere vFXT för Azure nivåar inte automatiskt data mellan frekventa och svala nivåer.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Hur vet jag om en miljö är rätt för Avere vFXT?

Det bästa sättet att tänka på denna fråga är att fråga, "Är arbetsbelastningen cacheable?" Det vill säga, har arbetsbelastningen en hög läs-till-skriv-förhållande? Ett exempel är 80/20 eller 70/30 läsningar/skrivningar.

Tänk på Avere vFXT för Azure om du har en filbaserad analytisk pipeline som körs över ett stort antal virtuella Azure-datorer och uppfyller ett eller flera av följande villkor:

* Den totala prestandan är långsam eller inkonsekvent på grund av långa åtkomsttider för filer (tiotals millisekunder eller sekunder, beroende på kraven). Den här svarstiden är oacceptabel för kunden.

* Data som krävs för bearbetning finns längst bort i en WAN-miljö, och det är opraktiskt att flytta dessa data permanent. Data kan finnas i en annan Azure-region eller i ett kunddatacenter.

* Ett stort antal klienter begär data , till exempel i ett HPC-kluster (High-Performance Computing). Det stora antalet samtidiga begäranden kan öka svarstiden.

* Kunden vill köra sin nuvarande pipeline "i det vill dua" i virtuella Azure-datorer och behöver en POSIX-baserad delad lagringslösning (eller cachelagring) för skalbarhet. Genom att använda Avere vFXT för Azure behöver du inte bakåtlista arbetspipelinen för att ringa inbyggda anrop till Azure Blob-lagring.

* Ditt HPC-program är baserat på NFSv3-klienter. (I vissa fall kan den använda SMB 2.1-klienter, men prestandan är begränsad.)

Följande diagram kan hjälpa dig att svara på den här frågan. Ju närmare arbetsflödet är längst upp till höger, desto troligare är det att Avere vFXT för Azure-cachelagringslösningen är rätt för din miljö.

![Diagramdiagram som visar att lästunga laster med tusentals klienter är bättre lämpade för Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>I vilken skala av kunder gör Avere vFXT-lösningen mest meningsfull?

Avere vFXT-cachelösningen är byggd för att hantera hundratals, tusentals eller tiotusentals beräkningskärnor. Om du har några maskiner som kör lätt arbete, avere vFXT är inte rätt lösning.

Typiska Avere vFXT-kunder kör krävande arbetsbelastningar som börjar på cirka 1 000 PROCESSORKÄRNor. Dessa miljöer kan vara så stora som 50.000 kärnor eller mer. Eftersom Avere vFXT är skalbart kan du lägga till noder för att stödja dessa arbetsbelastningar när de växer för att kräva mer dataflöde eller mer IOPS.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Hur mycket data kan en Avere vFXT-miljö lagra?

Avere vFXT för Azure är en cache. Den lagrar inte specifikt data. Den använder en kombination av RAM och SSD för att lagra cachelagrade data. Data lagras permanent på ett backend-lagringssystem (till exempel ett NetApp NAS-system eller en blob-behållare). Avere vFXT-systemet har ingen information om mängden data som lagras bakom det. Avere vFXT cachelagrar bara delmängden av de data som klienterna begär.  

### <a name="what-regions-are-supported"></a>Vilka regioner stöds?

Avere vFXT för Azure stöds i alla regioner utom suveräna regioner (Kina, Tyskland). Kontrollera att den region du vill använda kan stödja den stora mängden beräkningskärnor och de VM-instanser som behövs för att skapa Avere vFXT-klustret.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Hur får jag hjälp med Avere vFXT?

En specialiserad grupp supportpersonal erbjuder hjälp med Avere vFXT för Azure. Följ instruktionerna i [Få hjälp med ditt system](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) för att öppna en supportbiljett från Azure-portalen.

### <a name="is-avere-vfxt-highly-available"></a>Är Avere vFXT högtillgängliga?

Ja, Avere vFXT körs uteslutande som en HA-lösning.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Stöder Avere vFXT för Azure även andra molntjänster?

Ja, kunder kan använda mer än en molnleverantör med Avere vFXT-klustret. Den stöder AWS S3-standard buckets, Google Cloud Services standard buckets och Azure blob containers.

> [!NOTE]
> En programvaruavgift gäller för användning av Avere vFXT med AWS eller Google Cloud-lagring. Det finns ingen extra programvaruavgift för att använda Azure blob storage.

## <a name="technical-compute"></a>Teknisk: Beräkna

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Kan du beskriva hur en Avere vFXT miljö "ser ut"?

Avere vFXT är en klustrad uppsättning som består av flera virtuella Azure-datorer. Ett Python-bibliotek hanterar skapande, borttagning och ändring av kluster. Läs [Vad är Avere vFXT för Azure?](avere-vfxt-overview.md)

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Vilken typ av virtuella Azure-datorer körs Avere vFXT på?  

Ett Avere vFXT för Azure-kluster använder Microsoft Azure E32s_v3 virtuella datorer.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Skalas Avere vFXT-miljön?

Avere vFXT-klustret kan vara så litet som tre virtuella datornoder eller så stora som 24 noder. Kontakta Azures tekniska support för hjälp med planering om du tror att du behöver ett kluster med mer än nio noder. Det större antalet noder kräver en större distributionsarkitektur.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Har Avere vFXT miljön "autoscale"?

Nej. Du kan skala klusterstorleken uppåt och nedåt, men att lägga till eller ta bort klusternoder är ett manuellt steg.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Kan jag köra Avere vFXT-klustret som en skalauppsättning för virtuella datorer?

Avere vFXT stöder inte distribution av en skalningsuppsättning för virtuella datorer. Flera inbyggda tillgänglighetsstödmekanismer är endast utformade för atomära virtuella datorer som deltar i ett kluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Kan jag köra Avere vFXT-klustret på virtuella datorer med låg prioritet?

Nej, systemet kräver en underliggande stabil uppsättning virtuella datorer.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Kan jag köra Avere vFXT-klustret i behållare?

Nej, Avere vFXT måste distribueras som ett oberoende program.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Räknas virtuella avere vFXT-virtuella datorer mot min beräkningskvot?

Ja. Kontrollera att du har en tillräcklig kvot i regionen för att stödja klustret.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Kan jag köra Avere vFXT-klusterdatorer i olika tillgänglighetszoner?

Nej. Modellen med hög tillgänglighet i Avere vFXT stöder för närvarande inte enskilda Avere vFXT-klustermedlemmar som finns i olika tillgänglighetszoner.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Kan jag klona virtuella avere vFXT-datorer?

Nej, du måste använda python-skriptet som stöds för att lägga till eller ta bort noder i Avere vFXT-klustret. Mer information finns [i Hantera Avere vFXT-klustret](avere-vfxt-manage-cluster.md).

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Finns det en "VM"-version av programvaran jag kan köra i min egen lokala miljö?

Nej, systemet erbjuds som en klustrade installation och testas på specifika typer av virtuella datorer. Den här begränsningen hjälper kunder att undvika att skapa ett system som inte kan stödja de högpresterande kraven i ett typiskt Avere vFXT-arbetsflöde.

## <a name="technical-disks"></a>Teknisk: Diskar

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Vilka typer av diskar stöds för virtuella Azure-datorer?

Avere vFXT för Azure kan använda 1 TB- eller 4 TB premium-SSD-konfigurationer. Premium SSD-konfigurationen kan distribueras som flera hanterade diskar.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Stöder klustret ohanterat diskar?

Nej, klustret kräver hanterade diskar.

### <a name="does-the-system-support-local-attached-ssds"></a>Stöder systemet lokala (bifogade) SSD-enheter?

Avere vFXT för Azure stöder för närvarande inte lokala SSD:er. Diskar som används för Avere vFXT måste kunna stänga av och starta om, men lokala anslutna SSD:er i den här konfigurationen kan bara avslutas.

### <a name="does-the-system-support-ultra-ssds"></a>Stöder systemet ultra-SSD?Does the system support ultra SSDs?

Nej, systemet stöder endast premium-SSD-konfigurationer.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Kan jag koppla från mina premium-SSD:er och sätta tillbaka dem senare för att bevara cacheinnehållet mellan användning?

Det går inte att koppla bort och sätta tillbaka SSD:er. Metadata eller filinnehåll på källan kan ha ändrats mellan användningar, vilket kan orsaka problem med dataintegritet.

### <a name="does-the-system-encrypt-the-cache"></a>Krypterar systemet cachen?

Data är randiga över diskarna men är inte krypterade. Diskarna själva kan dock krypteras. Mer information finns i [Säkra principer och använda principer för virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Teknisk: Nätverk

### <a name="what-network-is-recommended"></a>Vilket nätverk rekommenderas?

Om du använder lokal lagring med Avere vFXT bör du ha en 1-Gbps eller bättre nätverksanslutning mellan din lagring och klustret. Om du har en liten mängd data och är villig att kopiera data till molnet innan du kör jobb kan VPN-anslutning vara tillräcklig.

> [!TIP]
> Ju långsammare nätverkslänken är, desto långsammare blir de första "kalla" läsningarna. Långsamma läsningar ökar svarstiden för arbetspipelinen.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Kan jag köra Avere vFXT i ett annat virtuellt nätverk än mitt beräkningskluster?

Ja, du kan skapa ditt Avere vFXT-system i ett annat virtuellt nätverk. Läs [Planera ditt Avere vFXT-system](avere-vfxt-deploy-plan.md) för mer information.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Kräver Avere vFXT ett eget undernät?

Ja. Avere vFXT körs strikt som ett HA-kluster (High Availability) och kräver flera IP-adresser för att fungera. Om klustret finns i sitt eget undernät undviker du risken för IP-adresskonflikter, vilket kan orsaka problem för installation och normal drift. Klustrets undernät kan finnas i ett virtuellt nätverk som används av andra resurser, så länge inga IP-adresser överlappar varandra.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Kan jag köra Avere vFXT på InfiniBand?

Nej, Avere vFXT använder endast Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Hur kommer jag åt min lokala NAS-miljö från Avere vFXT?

Avere vFXT-miljön är som alla andra Virtuella Azure-datorer genom att den kräver dirigerad åtkomst via en nätverksgateway eller VPN till kunddatacentret (och tillbaka). Överväg att använda Azure ExpressRoute-anslutning om den är tillgänglig i din miljö.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Vilka är bandbreddskraven för Avere vFXT?

Det totala bandbreddskravet beror på två faktorer:

* Mängden data som begärs från källan
* Klientsystemets tolerans för svarstid vid inledande datainläsning  

För latenskänsliga miljöer bör du använda en fiberlösning med en minsta länkhastighet på 1 Gbit/s. Använd ExpressRoute om det är tillgängligt.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Kan jag köra Avere vFXT med offentliga IP-adresser?

Nej, Avere vFXT är tänkt att drivas i en nätverksmiljö som säkras genom bästa praxis.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Kan jag begränsa internetåtkomsten från klustrets virtuella nätverk?

I allmänhet kan du konfigurera ytterligare säkerhet i det virtuella nätverket efter behov, men vissa begränsningar kan störa funktionen för klustret.

Om du till exempel begränsar utgående internetåtkomst från det virtuella nätverket uppstår problem för klustret om du inte också lägger till en regel som uttryckligen tillåter åtkomst till AzureCloud. Den här situationen beskrivs i [kompletterande dokumentation på GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Om du vill ha hjälp med anpassad säkerhet kontaktar du supporten enligt beskrivningen i [Få hjälp med ditt system.](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)

## <a name="technical-back-end-storage-core-filers"></a>Teknisk: Back-end lagring (core filers)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hur många kärnfilers stöder en enda Avere vFXT-miljö?

Ett Avere vFXT-kluster stöder upp till 20 kärnfilers.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Hur lagrar Avere vFXT-miljön data?

Avere vFXT är inte lagring. Det är en cache som läser och skriver data från flera lagringsmål som kallas kärnfilers. Data som lagras på premium SSD-diskar i Avere vFXT är övergående och så småningom spolas till back-end core filer lagring.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Vilka kärnfilers stöder Avere vFXT?

Generellt sett stöder Avere vFXT för Azure följande system som kärnfilers:

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 och 8.1) 
* NETApp ONTAP (Klustrade läge 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) och (7-läge 7.*, 8.0-8.3)

* Azure blob-behållare (endast lokalt redundant lagring)
* AWS S3 hinkar
* Google Cloud hinkar

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Varför stöder inte Avere vFXT alla NFS filers?

Även om alla NFS-plattformar uppfyller samma IETF-standarder har varje implementering i praktiken sina egna egenheter. Dessa detaljer påverkar hur Avere vFXT interagerar med lagringssystemet. De system som stöds är de mest använda plattformarna på marknaden.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Stöder Avere vFXT privat objektlagring (till exempel SwiftStack)?

Avere vFXT stöder inte privat objektlagring.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hur kan jag få en specifik lagringsprodukt under support?

Supporten baseras på mängden efterfrågan i fältet. Om det finns tillräckligt med intäktsbaserade förfrågningar för att stödja en NAS-lösning överväger vi det. Gör förfrågningar via Azure-support.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Kan jag använda Azure Blob-lagring som kärnfiler?

Ja, Avere vFXT för Azure kan använda en blockblolob-behållare som en cloud core filer.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Vilka är lagringskontokraven för en blob core filer?

Ditt lagringskonto måste vara ett GPv2-konto (General Purpose) och konfigureras endast för lokalt redundant lagring. Geo redundant lagring och zon redundant lagring stöds inte.

Läs [Azure Blob Storage cloud core filer](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) för mer information om kraven för lagringskonto.

### <a name="can-i-use-archive-blob-storage"></a>Kan jag använda arkivblobblagring?

Nej. Servicenivåavtalet (SLA) för arkivlagring är inte kompatibelt med realtidskatalog- och filåtkomstbehoven i Avere vFXT-systemet.

### <a name="can-i-use-cool-blob-storage"></a>Kan jag använda cool blob lagring?

Blob-lagring på den häftiga nivån rekommenderas vanligtvis inte för en Avere vFXT för Azure core filer. Cool nivå erbjuder lägre lagringskostnader men högre driftskostnader. (Se [Blockera blob-priser](<https://azure.microsoft.com/pricing/details/storage/blobs/>) för mer information.) Om data kommer att nås och ändras eller tas bort ofta bör du överväga att använda hotnivån.

[Åtkomstnivåer](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) ger mer information om när det kan vara meningsfullt att använda lagring på Cool-nivån som en vFXT-kärnfiler.

### <a name="how-do-i-encrypt-the-blob-container"></a>Hur krypterar jag blob-behållaren?

Du kan konfigurera blob-kryptering antingen i Azure (föredras) eller på Avere vFXT-kärnfilernivå.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Kan jag använda min egen krypteringsnyckel för en blob core filer?

Som standard krypteras data via Microsoft-hanterade nycklar för Azure Blob- och Table- och Queue-lagring samt Azure-filer. Du kan ta med din egen nyckel för kryptering för Blob-lagring och Azure-filer. Om du väljer att använda Avere vFXT-kryptering måste du använda den Avere-genererade nyckeln och lagra den lokalt.

## <a name="purchasing"></a>Inköp

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hur skaffar jag Avere vFXT för Azure-licensiering?

Det är enkelt att få en Avere vFXT för Azure-licens via Azure Marketplace. Registrera dig för ett Azure-konto och följ sedan instruktionerna i [Distribuera Avere vFXT-klustret](avere-vfxt-deploy.md) för att skapa ett Avere vFXT-kluster.

### <a name="how-much-does-avere-vfxt-cost"></a>Hur mycket kostar Avere vFXT?

I Azure finns det ingen extra licensavgift för att använda Avere vFXT-kluster. Kunderna ansvarar för lagring och andra Azure-förbrukningsavgifter.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Kan Avere vFXT virtuella datorer köras som låg prioritet?

Nej, Avere vFXT-kluster kräver "alltid på"-tjänst. Kluster kan stängas av när det inte behövs.

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med Avere vFXT för Azure läser du följande artiklar om du vill lära dig hur du planerar och distribuerar ditt eget system:

* [Planera för ditt Avere vFXT-system](avere-vfxt-deploy-plan.md)
* [Distributionsöversikt](avere-vfxt-deploy-overview.md)
* [Förbereda för att skapa ett Avere vFXT-kluster](avere-vfxt-prereqs.md)
* [Distribuera Avere vFXT-klustret](avere-vfxt-deploy.md)

Mer information om funktioner och användningsfall för Avere vFXT finns i [Avere vFXT för Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).

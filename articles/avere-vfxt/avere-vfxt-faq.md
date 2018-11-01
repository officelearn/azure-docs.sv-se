---
title: Vanliga frågor och svar - Avere vFXT för Azure
description: Vanliga frågor om Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 334b4c912c40949cbecab2173425927d46350d07
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634526"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT för vanliga frågor om Azure

Den här artikeln ger svar på frågor som kan hjälpa dig avgöra om Avere vFXT lösning är rätt för dina behov. Den ger grundläggande information om Avere vFXT funktioner och förklarar hur den fungerar med andra Azure-komponenter och produkter från fristående leverantörer. 

## <a name="general"></a>Allmänt 

### <a name="what-is-avere-vfxt-for-azure"></a>Vad är Avere vFXT för Azure?

Avere vFXT för Azure är en högpresterande filsystem som cachelagrar aktiva data i Azure Compute för effektiv bearbetning av kritiska arbetsbelastningar.

### <a name="is-the-avere-vfxt-a-storage-solution"></a>Är Avere vFXT en lagringslösning?

Nej. Avere vFXT är ett filsystem **cache** som kopplar till storage-miljöer, till exempel din EMC eller NetApp NAS eller en Blob-behållare. VFXT förenklar begäranden från klienter och cachelagrar data som den används för att förbättra prestanda i stor skala och med tiden. VFXT själva lagrar inte data. Det har ingen information om hur mycket data som lagras bakom den.

### <a name="is-the-avere-vfxt-a-tiering-solution"></a>Är Avere vFXT en lagringsnivåer lösning?

Avere vFXT görs inte automatiskt nivån data mellan frekventa och lågfrekventa nivåer.  

### <a name="how-do-i-know-if-an-environment-is-right-for-the-avere-vfxt"></a>Hur vet jag om en miljö är rätt för Avere vFXT?

Det bästa sättet att tänka på den här frågan är att ställa, ”är arbetsbelastningen komma”? Det vill säga har en hög läsning att skriva förhållande – till exempel 80/20 eller 70/30 läsningar/skrivningar i arbetsbelastningen.

Överväg Avere vFXT för Azure om du har en filbaserad analytiska pipeline som körs i ett stort antal Azure-datorer och den uppfyller en eller flera av följande villkor:

* Övergripande är långsam eller inkonsekvent på grund av lång fil åtkomsttider (tio för millisekunder eller sekunder, beroende på kraven). Den här fördröjningen är acceptabel för.

* Data som krävs för bearbetning är placerad längst till slutet av en WAN-miljö och är det opraktiskt att flytta data permanent. Data kan vara i en annan Azure-region eller i ett datacenter för kunden.

* Ett stort antal klienter begär data – till exempel i ett kluster för databehandling med höga prestanda (HPC). Det stora antalet samtidiga begäranden kan öka svarstiden.

* Kunden vill köra sina aktuella pipeline ”som – är” Azure-datorer och behov ett POSIX-baserade delade lagring (eller cachelagring) lösning för skalbarhet. Du behöver inte Omforma arbete-pipeline för att göra interna anrop till Azure Blob storage med hjälp av Avere vFXT för Azure.

* HPC-programmet baseras på NFSv3 klienter. (SMB 2.1-klienter kan användas i vissa fall, men prestanda är begränsad.)

   I bilden nedan försök att förenkla besvara den här frågan. Ju närmare arbetsflödet är att det övre högra hörnet, desto troligare är det att Avere cachelagring lösning som passar din miljö.

   ![diagram som visar att läsa tunga belastningar med tusentals olika klienter är det bättre passar för Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>På vilka skalan för klienter har Avere gör vFXT lösningen passar bäst?

VFXT cache-lösningen är utformat för att hantera hundratals eller tusentals beräkningskärnor tiotusentals. Om du har några datorer lätt arbete är Avere vFXT inte rätt lösning.

Vanliga Avere vFXT kunderna köra krävande arbetsbelastningar med början vid ca 1 000 CPU-kärnor. De här miljöerna kan vara så stora som 50 000 kärnor eller mer. Eftersom vFXT är skalbara, kan du lägga till noder för att stödja de här arbetsbelastningarna när de blir att kräva större dataflöde eller fler IOPS.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Hur mycket data kan en Avere vFXT miljö lagra?

Avere vFXT är ett cacheminne, den specifikt lagra inte data. Den använder en kombination av RAM-minne och SSD för att lagra de cachelagra data. Data lagras permanent på en backend-storage-system (till exempel ett NetApp NAS-system eller en Blob-behållare). Avere vFXT systemet har inte information om hur mycket data som lagras bakom. vFXT cachelagrar endast delmängd av dessa data som klienten begär.  

### <a name="what-regions-are-supported"></a>Vilka regioner stöds?

Från och med den 1 November 2018 stöds Avere vFXT för Azure i alla regioner utom landsbaserade regioner (Kina, Tyskland) och government-regioner. Kontrollera att den region som du vill använda har stöd för stora antalet beräkningskärnor samt VM-instanser som behövs för att skapa Avere vFXT klustret. Landsbaserade regioner och government-moln stöds inte ännu.

### <a name="how-do-i-get-help-with-the-avere-vfxt"></a>Hur jag för att få hjälp med Avere vFXT?

En specialiserad supportgrupp hjälper dig med Avere vFXT för Azure. Följ instruktionerna i [få hjälp med ditt system](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) att öppna ett supportärende i Azure Portal. 

### <a name="is-the-avere-vfxt-highly-available"></a>Är Avere vFXT med hög tillgänglighet?

Ja, Avere vFXT fungerar enbart som en lösning för hög tillgänglighet.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Stöder Avere vFXT for Azure också andra molntjänster?

Ja, kunder kan använda mer än en molnleverantör med Avere vFXT klustret. Det stöder AWS S3 standard buckets och Google Cloud Services standard buckets samt Azure Blob-behållare. 

> [!NOTE] 
> En avgift för programvara gäller om du vill använda Avere vFXT i AWS och Google Cloud, men inte med Azure.

## <a name="technical---compute"></a>Teknisk - beräkning

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Kan du beskriva vad en Avere vFXT miljö ”ut”?

Avere vFXT är en klustrad installation består av flera virtuella Azure-datorer. Ett Python-bibliotek hanterar klustret har skapats, borttagning och ändring av. Läs [vad är Avere vFXT för Azure?](avere-vfxt-overview.md) vill veta mer. 

### <a name="what-kind-of-azure-virtual-machines-does-the-avere-vfxt-run-on"></a>Vilken typ av Azure-datorer fungerar Avere vFXT körs på?  

Avere vFXT för Azure-kluster använder Microsoft Azure E32s_v3 eller D16s_v3 virtuella datorer. 

### <a name="can-i-mix-and-match-virtual-machine-types-for-my-cluster"></a>Kan jag blanda och matcha virtuella datortyper för mitt kluster?

Nej, måste du välja en typ av virtuell dator eller den andra.
    
### <a name="can-i-move-between-virtual-machine-types"></a>Kan jag flytta mellan typer av virtuella datorer?

Ja, det finns en migreringsvägen att flytta från en virtuell dator till en annan. [Öppna ett supportärende](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) att lära dig hur.

### <a name="does-the-avere-vfxt-environment-scale"></a>Skalas Avere vFXT miljön?

Avere vFXT klustret kan vara så litet som tre virtuella noder eller så stora som 24 noder. Kontakta teknisk support för Azure för att få hjälp att planera om du tror att du behöver ett kluster på fler än nio noder. Större antal noder kräver större distributionsarkitektur.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Fungerar Avere vFXT miljö ”Autoskala”?

Nej. Du kan skala klusterstorleken upp och ned, men att lägga till eller ta bort noder är en manuell åtgärd.

### <a name="can-i-run-the-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Kan jag köra vFXT klustret som en skalningsuppsättning för virtuell dator?

Avere vFXT stöder inte distribution av virtuella datorer scale set (VMSS). Stöd för inbyggd tillgänglighet av mekanismer är utvecklade för atomiska virtuella datorer som deltar i ett kluster.  

### <a name="can-i-run-the-vfxt-cluster-on-low-priority-vms"></a>Kan jag köra vFXT klustret på lågprioriterade virtuella datorer?

Nej, systemet kräver en underliggande stabil uppsättning virtuella datorer.

### <a name="can-i-run-the-vfxt-cluster-in-containers"></a>Kan jag köra vFXT klustret i behållare?

Nej, Avere vFXT måste distribueras som ett fristående program.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Göra Avere vFXT mot min compute-kvoten för antalet virtuella datorer?

Ja. Kontrollera att du har tillräckligt stor kvot i regionen för klustret.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Kan jag köra Avere vFXT kluster datorer i olika tillgänglighetszoner?

Nej. Hög tillgänglighet-modellen som används i Avere vFXT för närvarande stöder inte enskilda vFXT gruppmedlemmar som finns i olika tillgänglighetszoner.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Kan jag klona Avere vFXT virtuella datorer?

Nej, måste du använda stöds Python-skriptet för att lägga till eller ta bort noder i klustret Avere vFXT. Mer information finns i [hantera Avere vFXT klustret](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Finns det en ”VM” version av programvaran som jag kan köra i min egen lokala miljö?

Nej, systemet erbjuds som en klustrad installation och testas och typer av specifika virtuella datorer. Den här begränsningen hjälper kunderna att undvika att skapa ett system som inte stöder kraven för höga prestanda för ett typiskt Avere vFXT arbetsflöde. 

## <a name="technical---disks"></a>Teknik – diskar

### <a name="what-type-of-disks-are-supported-for-the-azure-vms"></a>Vilken typ av diskar stöds för virtuella Azure-datorer?

Avere vFXT för Azure kan använda 1 TB och 4 TB premium SSD-konfigurationer. Premium SSD-konfiguration kan distribueras som flera hanterade diskar.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Klustret har stöd för ohanterade diskar?

Nej, behöver de hanterade diskar.

### <a name="does-the-system-support-local-attached-ssds"></a>Systemet har stöd för lokala (anslutna) SSD: er?

Avere vFXT för Azure stöder för närvarande inte lokala SSD-enheter. Diskar som används för Avere vFXT måste kunna starta om, men lokala SSD-anslutna enheter i den här konfigurationen kan bara avslutas.

### <a name="does-the-system-support-ultra-ssds"></a>Stöder systemet ultra SSD: er?

Nej, systemet har stöd för premium SSD konfigurationer.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Kan jag koppla från min premium SSD och återansluta dem senare för att bevara cacheinnehåll mellan användning?

Koppla från och återansluta SSD: er stöds inte. Metadata och innehållet på källan kan ha ändrats mellan används, vilket kan leda till problem med dataintegriteten.

### <a name="does-the-system-encrypt-the-cache"></a>Krypterar systemet cachen?

Data fördelas på diskarna men krypteras inte. Dock kan diskarna själva krypteras. Mer information hittar du [här](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical---networking"></a>Teknisk - nätverk

### <a name="what-network-is-recommended"></a>Vilka nätverk rekommenderas?

Om användningen av lokal lagring med Avere vFXT, bör du ha en 1 Gbit/s eller bättre nätverksanslutning. Om du har en liten mängd data och är villig att kopiera data till molnet innan du kör jobb, kan VPN-anslutningen vara tillräckligt. 

> [!TIP] 
> Ju långsammare Nätverkslänken är, desto långsammare inledande kalla läsningar. Långsam läsningar ökar svarstiden för arbete-pipeline. 

### <a name="can-i-run-the-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Kan jag köra Avere vFXT i ett annat virtuellt nätverk än min beräkningskluster?

Ja, du kan skapa Avere vFXT systemet i ett annat virtuellt nätverk. Läs [planera Avere vFXT systemet](avere-vfxt-deploy-plan.md) mer information.

### <a name="does-the-avere-vfxt-require-its-own-subnet"></a>Kräver Avere vFXT sitt eget undernät?

Ja. Avere vFXT körs strikt som ett kluster med hög tillgänglighet och kräver flera IP-adresser ska fungera. Om klustret är i ett eget undernät kan undvika du risken för IP-adresskonflikter uppstå, vilket kan orsaka problem för installation och normal drift. Klustrets undernät kan vara det befintliga virtuella nätverket så länge det finns inga IP-adresser överlappar varandra.

### <a name="can-i-run-the-avere-vfxt-on-infiniband"></a>Kan jag köra Avere vFXT på Infiniband?

Nej, Avere vFXT använder Ethernet-/ IP endast.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-the-avere-vfxt"></a>Hur kommer jag åt min lokala NAS-miljö från Avere vFXT?

Avere vFXT miljön är inte skiljer sig från andra Azure virtuella datorer eftersom den kräver routade åtkomst via en nätverks-gateway eller VPN till kunden datacenter (och tillbaka). Överväg att använda Azure ExpressRoute-anslutningen om den är tillgänglig i din miljö.

### <a name="what-are-the-bandwidth-requirements-for-the-avere-vfxt"></a>Vilka är kraven på nätverksbandbredd för Avere vFXT?

Den övergripande kraven på bandbredd är beroende av två faktorer: 

* Mängden data som begärs från källan 
* Den klientsystemet tolerans för svarstid under inledande datainläsning  

För latenskänsliga miljöer, bör du använda en fiber-lösning med en lägsta länkhastighet 1 Gbit/s. Använd ExpressRoute om den är tillgänglig.  

### <a name="can-i-run-the-vfxt-with-public-ip-addresses"></a>Kan jag köra vFXT med offentliga IP-adresser?

Nej, vFXT är avsedd att köras i en nätverksmiljö som skyddas med bästa praxis.  

## <a name="technical---backend-storage-core-filers"></a>Teknik – serverdelslagring (core-filter)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hur många kärnor filter har stöd för en enda Avere vFXT miljö?

Ett Avere vFXT kluster har stöd för upp till 20 filter för kärnor. 

### <a name="how-does-the-avere-environment-store-data"></a>Hur lagrar Avere miljön data?

Avere vFXT är inte ett lagringsutrymme. Det är ett cacheminne som läser och skriver data från flera prestandamål i storage kallas core filter. Data som lagras på den Avere vFXT premium SSD-diskar är tillfälligt och skickas slutligen till serverdelslagring för core-filer.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Vilka core filter stöder Avere vFXT?

Generellt sett Avere vFXT för Azure har stöd för följande system som core filter: 

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 och 8.1) 
* NetApp ONTAP (klustrad läge 9.4, 9.3, 9.2, 9.1P1 8.0 8.3) och (7-läge 7.* 8.0 8.3) 
* Azure Blob-behållare (LRS) 
* AWS S3 buckets 
* Google Cloud-buckets

### <a name="why-doesnt-the-avere-vfxt-support-all-nfs-filers"></a>Varför inte Avere vFXT stöd för alla NFS-filter?

Även om alla NFS-plattformar uppfyller samma IETF-standarderna, har varje implementering sin egen paketberoenden i praktiken. Informationen påverkar hur Avere vFXT interagerar med storage-system. System som stöds är de vanligaste plattformarna i marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Stöder Avere vFXT lagring av privata objekt (till exempel Swiftstack)?

Avere vFXT stöder inte privata objektlagring.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hur får jag en specifik storage produkt under support?

Support är baserad på mängden begäran i fältet. Om det finns tillräckligt med intäkter-baserade begäranden för en viss NAS-lösning, förblir. Göra begäranden via Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Kan jag använda Azure Blob storage som en core-filer?

Ja, använda Avere vFXT för Azure block Blob-behållare som molnet core arkiverar.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Vilka är kraven för storage-konto för en Blob core filer?

Ditt lagringskonto måste vara en generell användning v2 (GPv2)-konto och konfigurerade för endast lokalt redundant lagring (LRS). GRS och ZRS stöds inte.

### <a name="can-i-use-archive-blob-storage"></a>Kan jag använda Archive Blob storage?

Nej. SERVICEAVTALET för arkivlagring är inte kompatibel med i realtid directory och filåtkomst måste vFXT systemets. 

### <a name="can-i-use-cool-blob-storage"></a>Kan jag använda cool Blob storage?

Du kan använda den lågfrekventa nivån, men Observera att frekvensen för åtgärder vara mycket högre. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Hur jag för att kryptera Blob-behållare?

Du kan antingen konfigurera blobbkryptering i Azure (rekommenderas) eller på nivån vFXT core filer.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Kan jag använda min egen krypteringsnyckeln för en Blob core filer?

Som standard krypteras data med hjälp av Microsoft-hanterade nycklar för Azure Blobs, tabeller, filer och köer. Du kan använda en egen nyckel för kryptering för Azure-Blobbar och filer. Om du väljer att använda vFXT kryptering måste du använda den genererade Avere nyckeln och lagra den lokalt. 

## <a name="purchasing"></a>Köpa

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hur får jag Avere vFXT för licensiering för Azure?

Hämta en Avere vFXT för Azure-licens är enkelt via Azure Marketplace. Registrera dig för ett Azure-konto och följ sedan instruktionerna i [distribuera vFXT klustret](avere-vfxt-deploy.md) att skapa ett Avere vFXT kluster. 

### <a name="how-much-does-the-avere-vfxt-cost"></a>Hur mycket kostar Avere vFXT?

I Azure finns det inga ytterligare licenser avgift för att använda Avere vFXT kluster. Kunderna ansvarar för lagring och andra avgifter för Azure-förbrukning.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Kan köras Avere vFXT virtuella datorer med låg prioritet?

Nej, Avere vFXT kluster kräver ”alltid på” service. Klustren kan stängas av när de inte behövs. 

## <a name="next-steps"></a>Nästa steg

Läs dessa länkar om du vill veta hur du planerar och distribuerar ditt system för att komma igång med Avere vFXT för Azure:

* [Planera din Avere vFXT system](avere-vfxt-deploy-plan.md)
* [Distributionsöversikt](avere-vfxt-deploy-overview.md)
* [Förbereda för att skapa Avere vFXT](avere-vfxt-prereqs.md)
* [Distribuera vFXT-kluster](avere-vfxt-deploy.md)

Om du vill lära dig mer om Avere vFXT funktioner och användningsfall, besök [Avere vFXT för Azure (förhandsversion)](https://azure.microsoft.com/services/storage/avere-vfxt/).

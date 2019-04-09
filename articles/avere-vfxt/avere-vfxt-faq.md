---
title: Vanliga frågor och svar - Avere vFXT för Azure
description: Vanliga frågor om Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: v-erkell
ms.openlocfilehash: 69921300163bd9a326f3baedd3182da887ad02c4
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057157"
---
# <a name="avere-vfxt-for-azure-faq"></a>Vanliga frågor och svar om Avere vFXT för Azure

Den här artikeln ger svar på frågor som kan hjälpa dig avgöra om Avere vFXT för Azure är rätt för dina behov. Den ger grundläggande information om Avere vFXT och förklarar hur den fungerar med andra Azure-komponenter och produkter från fristående leverantörer. 

## <a name="general"></a>Allmänt 

### <a name="what-is-avere-vfxt-for-azure"></a>Vad är Avere vFXT för Azure?

Avere vFXT för Azure är en högpresterande filsystem som cachelagrar aktiva data i Azure-beräkning för effektiv bearbetning av kritiska arbetsbelastningar.

### <a name="is-avere-vfxt-a-storage-solution"></a>Är Avere vFXT en lagringslösning?

Nej. Avere vFXT är ett filsystem *cache* som kopplar till storage-miljöer, till exempel din EMC eller NetApp NAS eller en Azure blob-behållare. Avere vFXT effektiviserar begäranden från klienter och den lagrar dessa data som den arbetar för att förbättra prestanda i stor skala och med tiden. Avere vFXT själva lagrar inte data. Det har ingen information om hur mycket data som lagras bakom den.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Är Avere vFXT en lagringsnivåer lösning?

Avere vFXT görs inte automatiskt nivån data mellan frekventa och lågfrekventa nivåer.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Hur vet jag om en miljö är rätt för Avere vFXT?

Det bästa sättet att tänka på den här frågan är att ställa, ”är arbetsbelastningen komma”? Det vill säga har arbetsbelastningen till oskyddad högt förhållande? Ett exempel är 80/20 eller 70/30 läsningar/skrivningar.

Överväg Avere vFXT för Azure om du har en filbaserad analytiska pipeline som körs i ett stort antal Azure-datorer och den uppfyller en eller flera av följande villkor:

* Övergripande är långsam eller inkonsekvent på grund av lång fil åtkomsttider (tio för millisekunder eller sekunder, beroende på kraven). Den här fördröjningen är acceptabel för kunden.

* Data som krävs för bearbetning är placerad längst till slutet av en WAN-miljö och är det opraktiskt att flytta data permanent. Data kan vara i en annan Azure-region eller i ett datacenter för kunden.

* Ett stort antal klienter begär data – till exempel i ett kluster för databehandling med höga prestanda (HPC). Det stora antalet samtidiga begäranden kan öka svarstiden.

* Kunden vill köra sina aktuella pipeline ”som är” i Azure virtual machines och behöver ett POSIX-baserade delade lagring (eller cachelagring) lösning för skalbarhet. Genom att använda Avere vFXT för Azure kan behöver du inte Omforma arbete-pipeline för att göra interna anrop till Azure Blob storage.

* HPC-programmet baseras på NFSv3 klienter. (I vissa fall kan använda SMB 2.1-klienter, men prestanda är begränsad.)

Följande diagram gör det enklare för svaret på den här frågan. Ju närmare arbetsflödet är att det övre högra hörnet, desto troligare är det att Avere cachelagring lösning som passar din miljö.

![diagram som visar att läsa tunga belastningar med tusentals olika klienter är det bättre passar för Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>På vilka skalan för klienter har Avere gör vFXT lösningen passar bäst?

Avere vFXT cache lösningen är utformat för att hantera hundratals eller tusentals beräkningskärnor tiotusentals. Om du har några datorer lätt arbete är Avere vFXT inte rätt lösning.

Vanliga Avere vFXT kunderna köra krävande arbetsbelastningar med början vid ca 1 000 CPU-kärnor. De här miljöerna kan vara så stora som 50 000 kärnor eller mer. Eftersom Avere vFXT är skalbara, kan du lägga till noder för att stödja de här arbetsbelastningarna när de blir att kräva större dataflöde eller fler IOPS.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Hur mycket data kan en Avere vFXT miljö lagra?

Avere vFXT är ett cacheminne. Informationen lagras inte specifikt data. Den använder en kombination av RAM-minne och SSD för att lagra de cachelagra data. Data lagras permanent på en backend-storage-system (till exempel ett NetApp NAS-system eller en blob-behållare). Avere vFXT systemet har inte information om hur mycket data som lagras bakom den. Avere vFXT cachelagrar endast delmängd av dessa data som klienten begär.  

### <a name="what-regions-are-supported"></a>Vilka regioner stöds?

Avere vFXT för Azure stöds i alla regioner utom landsbaserade regioner (Kina, Tyskland). Kontrollera att den region som du vill använda har stöd för stora antalet beräkningskärnor och VM-instanser som behövs för att skapa Avere vFXT klustret.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Hur jag för att få hjälp med Avere vFXT?

En specialiserad supportgrupp hjälper dig med Avere vFXT för Azure. Följ instruktionerna i [få hjälp med ditt system](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) att öppna ett supportärende i Azure Portal. 

### <a name="is-avere-vfxt-highly-available"></a>Är Avere vFXT med hög tillgänglighet?

Ja, Avere vFXT fungerar enbart som en lösning för hög tillgänglighet.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Stöder Avere vFXT for Azure också andra molntjänster?

Ja, kunder kan använda mer än en molnleverantör med Avere vFXT klustret. Den stöder AWS S3 standard buckets, standard buckets för Google Cloud Services och Azure blob-behållare. 

> [!NOTE] 
> En avgift för programvara gäller om du vill använda Avere vFXT i AWS och Google Cloud, men inte med Azure.

## <a name="technical-compute"></a>Teknisk: Compute

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Kan du beskriva vad en Avere vFXT miljö ”ut”?

Avere vFXT är en klustrad installation består av flera virtuella Azure-datorer. Ett Python-bibliotek hanterar klustret har skapats, borttagning och ändring av. Läs [vad är Avere vFXT för Azure?](avere-vfxt-overview.md) vill veta mer. 

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Vilken typ av Azure-datorer Avere vFXT kör på  

En Avere vFXT för Azure-kluster använder Microsoft Azure E32s_v3 virtuella datorer. 

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.

-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Skalas Avere vFXT miljön?

Avere vFXT klustret kan vara så litet som tre virtuella noder eller så stora som 24 noder. Kontakta teknisk support för Azure för hur du planerar om du tror att du behöver ett kluster på fler än nio noder. Större antal noder kräver en större distributionsarkitektur för.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Fungerar Avere vFXT miljö ”Autoskala”?

Nej. Du kan skala klusterstorleken upp och ned, men att lägga till eller ta bort noder är en manuell åtgärd.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Kan jag köra Avere vFXT klustret som en skalningsuppsättning för virtuell dator?

Avere vFXT stöder inte distribution av en VM-skalningsuppsättning. Stöd för inbyggd tillgänglighet av mekanismer är utvecklade för atomiska virtuella datorer som deltar i ett kluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Kan jag köra Avere vFXT klustret på lågprioriterade virtuella datorer?

Nej, systemet kräver en underliggande stabil uppsättning virtuella datorer.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Kan jag köra Avere vFXT klustret i behållare?

Nej, Avere vFXT måste distribueras som ett fristående program.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Göra Avere vFXT mot min compute-kvoten för antalet virtuella datorer?

Ja. Kontrollera att du har en tillräcklig kvot i regionen för klustret.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Kan jag köra Avere vFXT kluster datorer i olika tillgänglighetszoner?

Nej. Modell med hög tillgänglighet i Avere vFXT för närvarande stöder inte enskilda Avere vFXT gruppmedlemmar som finns i olika tillgänglighetszoner.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Kan jag klona Avere vFXT virtuella datorer?

Nej, måste du använda stöds Python-skriptet för att lägga till eller ta bort noder i klustret Avere vFXT. Mer information finns i [hantera Avere vFXT klustret](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Finns det en ”VM” version av programvaran som jag kan köra i min egen lokala miljö?

Nej, systemet erbjuds som en klustrad installation och testas och typer av specifika virtuella datorer. Den här begränsningen hjälper kunderna att undvika att skapa ett system som inte stöder kraven för höga prestanda för ett typiskt Avere vFXT arbetsflöde. 

## <a name="technical-disks"></a>Teknisk: Diskar

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Vilka typer av diskar stöds för virtuella Azure-datorer?

Avere vFXT för Azure kan använda 1 TB och 4 TB premium SSD-konfigurationer. Premium SSD-konfiguration kan distribueras som flera hanterade diskar.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Klustret har stöd för ohanterade diskar?

Nej, behöver de hanterade diskar.

### <a name="does-the-system-support-local-attached-ssds"></a>Systemet har stöd för lokala (anslutna) SSD: er?

Avere vFXT för Azure stöder för närvarande inte lokala SSD-enheter. Diskar som används för Avere vFXT måste kunna starta om, men lokala SSD-anslutna enheter i den här konfigurationen kan bara avslutas.

### <a name="does-the-system-support-ultra-ssds"></a>Stöder systemet ultra SSD: er?

Nej, systemet har stöd för premium SSD konfigurationer.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Kan jag koppla från min premium SSD och återansluta dem senare för att bevara cacheinnehåll mellan användning?

Koppla från och återansluta SSD: er stöds inte. Metadata och innehållet på källan kan ha ändrats mellan används, vilket kan orsaka problem med dataintegriteten.

### <a name="does-the-system-encrypt-the-cache"></a>Krypterar systemet cachen?

Data fördelas på diskarna men krypteras inte. Dock kan diskarna själva krypteras. Mer information finns i [säker och använder principer på virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Teknisk: Nätverk

### <a name="what-network-is-recommended"></a>Vilka nätverk rekommenderas?

Om du använder en lokal lagring med Avere vFXT, bör du ha en 1 Gbit/s eller bättre nätverksanslutning. Om du har en liten mängd data och är villig att kopiera data till molnet innan du kör jobb, kan VPN-anslutningen vara tillräckligt. 

> [!TIP] 
> Ju långsammare Nätverkslänken är, desto långsammare inledande kalla läsningar. Långsam läsningar ökar svarstiden för arbete-pipeline. 

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Kan jag köra Avere vFXT i ett annat virtuellt nätverk än min beräkningskluster?

Ja, du kan skapa Avere vFXT systemet i ett annat virtuellt nätverk. Läs [planera Avere vFXT systemet](avere-vfxt-deploy-plan.md) mer information.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Kräver Avere vFXT sitt eget undernät?

Ja. Avere vFXT körs strikt som ett kluster med hög tillgänglighet (HA) och kräver flera IP-adresser ska fungera. Om klustret är i ett eget undernät kan undvika du risken för IP-adresskonflikter uppstå, vilket kan orsaka problem för installation och normal drift. Klustrets undernät kan vara i det befintliga virtuella nätverket så länge det finns inga IP-adresser överlappar varandra.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Kan jag köra Avere vFXT på InfiniBand?

Nej, Avere vFXT använder Ethernet-/ IP endast.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Hur kommer jag åt min lokala NAS-miljö från Avere vFXT?

Avere vFXT miljön är som andra Azure virtuella datorer eftersom den kräver routade åtkomst via en nätverks-gateway eller VPN till kunden datacenter (och tillbaka). Överväg att använda Azure ExpressRoute-anslutningen om den är tillgänglig i din miljö.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Vilka är kraven på nätverksbandbredd för Avere vFXT?

Den övergripande kraven på bandbredd är beroende av två faktorer: 

* Mängden data som begärs från källan 
* Den klientsystemet tolerans för svarstid under inledande datainläsning  

För latenskänsliga miljöer, bör du använda en fiber-lösning med en lägsta länkhastighet 1 Gbit/s. Använd ExpressRoute om den är tillgänglig.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Kan jag köra Avere vFXT med offentliga IP-adresser?

Nej, Avere vFXT är avsedd att köras i en nätverksmiljö skyddas genom bästa praxis.  

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Kan jag begränsa åtkomst till internet från virtuellt nätverk för mitt kluster? 

I allmänhet kan du konfigurera ytterligare säkerhet för dina virtuella nätverk efter behov, men vissa begränsningar kan störa driften av klustret.

Till exempel orsakar att begränsa utgående Internetåtkomst från ditt vnet problem för klustret såvida inte du också lägga till regler som uttryckligen tillåter åtkomst till AzureConnectors och AzureCloud. Den här situationen beskrivs i [kompletterande dokumentation på GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Kontakta supporten för hjälp med anpassade säkerhet, enligt beskrivningen i [få hjälp med ditt system](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Teknisk: Backend-lagring (core-filter)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hur många kärnor filter har stöd för en enda Avere vFXT miljö?

Ett Avere vFXT kluster har stöd för upp till 20 filter för kärnor. 

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Hur lagrar Avere vFXT miljön data?

Avere vFXT är inte ett lagringsutrymme. Det är ett cacheminne som läser och skriver data från flera prestandamål i storage kallas core filter. Data som lagras på premium SSD-diskar i Avere vFXT är tillfälligt och skickas slutligen till backend-kärna filer lagring.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Vilka core filter stöder Avere vFXT?

Generellt sett Avere vFXT för Azure har stöd för följande system som core filter: 

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 och 8.1) 
* NetApp ONTAP (klustrad läge 9.4, 9.3, 9.2, 9.1P1 8.0 8.3) och (7-läge 7.* 8.0 8.3) 

  > [!NOTE] 
  > Azure NetApp-filer stöds inte för närvarande. 

* Azure blob-behållare (endast lokalt redundant lagring) 
* AWS S3 buckets 
* Google Cloud-buckets

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Varför inte Avere vFXT stöd för alla NFS-filter?

Även om alla NFS-plattformar uppfyller samma IETF-standarderna, har varje implementering sin egen paketberoenden i praktiken. Informationen påverkar hur Avere vFXT interagerar med storage-system. System som stöds är de vanligaste plattformarna i marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Stöder Avere vFXT lagring av privata objekt (till exempel SwiftStack)?

Avere vFXT stöder inte privata objektlagring.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hur får jag en specifik storage produkt under support?

Support är baserad på mängden begäran i fältet. Om det finns tillräckligt med intäkter-baserade begäranden att stödja en NAS-lösning kan vi att överväga den. Göra begäranden via Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Kan jag använda Azure Blob storage som en core-filer?

Ja, Avere vFXT för Azure kan använda en block blob-behållare som en cloud core-filer.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Vilka är kraven för storage-konto för en blob core filer?

Ditt lagringskonto måste vara en generell användning v2 (GPv2)-konto och konfigurerade för lokalt redundant lagring. GEO-redundant lagring och zonredundant lagring stöds inte.

### <a name="can-i-use-archive-blob-storage"></a>Kan jag använda archive blob storage?

Nej. Servicenivåavtal (SLA) för arkivlagring är inte kompatibel med i realtid katalog- och filen åtkomst behoven hos Avere vFXT system. 

### <a name="can-i-use-cool-blob-storage"></a>Kan jag använda lågfrekvent åtkomstnivå av blob-lagring?

Du kan använda den lågfrekventa nivån, men Observera att frekvensen för åtgärder vara mycket högre. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Hur jag för att kryptera blob-behållare?

Du kan konfigurera blobbkryptering i Azure (rekommenderas) eller på nivån Avere vFXT core filer.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Kan jag använda min egen krypteringsnyckeln för en blob core filer?

Som standard krypteras data via Microsoft-hanterade nycklar för Azure Blob, tabell och kö-lagring, plus Azure Files. Du kan använda en egen nyckel för kryptering för Blob storage och Azure Files. Om du väljer att använda Avere vFXT kryptering måste du använda den genererade Avere nyckeln och lagra den lokalt. 

## <a name="purchasing"></a>Inköp

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hur får jag Avere vFXT för licensiering för Azure?

Hämta en Avere vFXT för Azure-licens är enkelt via Azure Marketplace. Registrera dig för ett Azure-konto och följ sedan instruktionerna i [distribuera Avere vFXT klustret](avere-vfxt-deploy.md) att skapa ett Avere vFXT kluster. 

### <a name="how-much-does-avere-vfxt-cost"></a>Hur mycket kostar Avere vFXT?

I Azure finns det inga ytterligare licenser avgift för att använda Avere vFXT kluster. Kunderna ansvarar för lagring och andra avgifter för Azure-förbrukning.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Kan köras Avere vFXT virtuella datorer med låg prioritet?

Nej, Avere vFXT kluster kräver ”alltid på” service. Klustren kan stängas av när de inte behövs. 

## <a name="next-steps"></a>Nästa steg

Kom igång med Avere vFXT för Azure genom dessa artiklar innehåller information om hur du planerar och distribuerar ditt system:

* [Planera för ditt Avere vFXT-system](avere-vfxt-deploy-plan.md)
* [Distributionsöversikt](avere-vfxt-deploy-overview.md)
* [Förbereda för att skapa ett Avere vFXT-kluster](avere-vfxt-prereqs.md)
* [Distribuera Avere vFXT kluster](avere-vfxt-deploy.md)

Mer information om funktioner och användningsfall för Avere vFXT genom att gå till [Avere vFXT för Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).

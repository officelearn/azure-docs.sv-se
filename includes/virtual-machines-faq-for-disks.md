---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/31/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ad36547b0cf9457d7ff4fdd919a0bb1362539eb5
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522728"
---
Den här artikeln besvarar några vanliga frågor om Azure Managed Disks och Azure Premium SSD-diskar.

## <a name="managed-disks"></a>Managed Disks

**Vad är Azure Managed Disks?**

Hanterade diskar är en funktion som förenklar diskhantering för virtuella Azure IaaS-datorer genom att hantera hantering av lagringskonto åt dig. Mer information finns i [översikten Hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md).

**Om jag skapar en standardhanterad disk från en befintlig virtuell hårddisk som är 80 GB, hur mycket kostar det mig?**

En standardhanterad disk som skapats från en virtuell hårddisk på 80 GB behandlas som nästa tillgängliga standarddiskstorlek, som är en S10-disk. Du debiteras enligt S10 disk prissättning. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Finns det några transaktionskostnader för vanliga hanterade diskar?**

Ja. Du debiteras för varje transaktion. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**För en standardhanterad disk, debiteras jag för den faktiska storleken på data på disken eller för diskens etablerade kapacitet?**

Du debiteras baserat på diskens etablerade kapacitet. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Hur skiljer sig prissättningen av premiumhanterade diskar från ohanterade diskar?**

Prissättningen av premiumhanterade diskar är samma som ohanterade premiumdiskar.

**Kan jag ändra lagringskontotypen (Standard eller Premium) för mina hanterade diskar?**

Ja. Du kan ändra lagringskontotypen för dina hanterade diskar med hjälp av Azure-portalen, PowerShell eller Azure CLI.

**Kan jag använda en VHD-fil i ett Azure-lagringskonto för att skapa en hanterad disk med en annan prenumeration?**

Ja.

**Kan jag använda en VHD-fil i ett Azure-lagringskonto för att skapa en hanterad disk i en annan region?**

Nej.

**Finns det några skalbegränsningar för kunder som använder hanterade diskar?**

Hanterade diskar eliminerar de gränser som är associerade med lagringskonton. Den maximala gränsen är dock 50 000 hanterade diskar per region och disktyp för en prenumeration.

**Kan virtuella datorer i en tillgänglighetsuppsättning bestå av en kombination av hanterade och ohanterade diskar?**

Nej. De virtuella datorerna i en tillgänglighetsuppsättning måste använda antingen alla hanterade diskar eller alla ohanterade diskar. När du skapar en tillgänglighetsuppsättning kan du välja vilken typ av diskar du vill använda.

**Är hanterade diskar standardalternativet i Azure-portalen?**

Ja.

**Kan jag skapa en tom hanterad disk?**

Ja. Du kan skapa en tom disk. En hanterad disk kan skapas oberoende av en virtuell dator, till exempel utan att koppla den till en virtuell dator.

**Vad är antalet feldomäner som stöds för en tillgänglighetsuppsättning som använder hanterade diskar?**

Beroende på vilken region tillgänglighetsuppsättningen som använder hanterade diskar finns, är antalet feldomäner som stöds 2 eller 3 som stöds.

**Hur konfigureras standardlagringskontot för diagnostik?**

Du kan ställa in ett privat lagringskonto för VM-diagnostik.

**Vilken typ av rollbaserad åtkomstkontroll stöd är tillgänglig för hanterade diskar?**

Hanterade diskar stöder tre viktiga standardroller:

* Ägare: Kan hantera allt, inklusive åtkomst
* Deltagare: Kan hantera allt utom åtkomst
* Läsare: Kan visa allt, men kan inte göra ändringar

**Finns det något sätt att kopiera eller exportera en hanterad disk till ett privat lagringskonto?**

Du kan generera en SAS-signatur (Shared Access Signature) för den hanterade disken och använda den för att kopiera innehållet till ett privat lagringskonto eller lokalt lagringsutrymme. Du kan använda SAS URI med Azure-portalen, Azure PowerShell, Azure CLI eller [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Kan jag skapa en kopia av min hanterade disk?**

Kunder kan ta en ögonblicksbild av sina hanterade diskar och sedan använda ögonblicksbilden för att skapa en annan hanterad disk.

**Stöds ohanterliga diskar fortfarande?**

Ja, både ohanterade och hanterade diskar stöds. Vi rekommenderar att du använder hanterade diskar för nya arbetsbelastningar och migrerar dina aktuella arbetsbelastningar till hanterade diskar.

**Kan jag samlokalisera ohanterade och hanterade diskar på samma virtuella dator?**

Nej.

**Om jag skapar en 128 GB disk och sedan öka storleken till 130 gibibytes (GiB), kommer jag att debiteras för nästa diskstorlek (256 GiB)?**

Ja.

**Kan jag skapa lokalt redundanta lagrings-, geo-redundanta lagrings- och zonupptundanta lagringshanterade diskar?**

Azure Managed Disks stöder för närvarande endast lokalt redundanta lagringshanterade diskar.

**Kan jag krympa eller minska mina hanterade diskar?**

Nej. Den här funktionen stöds inte för tillfället.

**Kan jag bryta ett hyresavtal på min disk?**

Nej. Detta stöds för närvarande inte eftersom ett lån finns för att förhindra oavsiktlig borttagning när disken används.

**Kan jag ändra egenskapen för datornamn när en specialiserad (inte skapad med hjälp av systemförberedelseverktyget eller generaliserad) operativsystemdisk används för att etablera en virtuell dator?**

Nej. Du kan inte uppdatera egenskapen datornamn. Den nya virtuella datorn ärver den från den överordnade virtuella datorn, som användes för att skapa operativsystemdisken. 

**Var hittar jag exempel på Azure Resource Manager-mallar för att skapa virtuella datorer med hanterade diskar?**
* [Lista över mallar med hanterade diskar](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**När du skapar en disk från en blob, finns det någon ständigt befintlig relation med den källblobben?**

Nej, när den nya disken skapas är det en fullständig fristående kopia av den bloben vid den tidpunkten och det finns ingen koppling mellan de två. Om du vill kan källblobben tas bort när du har skapat disken utan att den nyskapade disken påverkas på något sätt.

**Kan jag byta namn på en hanterad eller ohanterad disk efter att den har skapats?**

För hanterade diskar kan du inte byta namn på dem. Du kan dock byta namn på en ohanterad disk så länge den för närvarande inte är kopplad till en virtuell dator eller virtuell dator.

**Kan jag använda GPT-partitionering på en Azure Disk?**

Generation 1-avbildningar kan bara använda GPT-partitionering på datadiskar, inte OS-diskar. OS-diskar måste använda MBR-partitionsstilen.

[Generation 2-avbildningar](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) kan använda GPT-partitionering på OS-disken samt datadiskarna.

**Vilka disktyper stöder ögonblicksbilder?**

Premium SSD, standard SSD och standard HDD stöder ögonblicksbilder. För dessa tre disktyper stöds ögonblicksbilder för alla diskstorlekar (inklusive diskar upp till 32 TiB i storlek). Ultra diskar stöder inte ögonblicksbilder.

**Vad är Azure-diskreservationer?**
Diskbokning är möjligheten att köpa ett års disklagring i förväg, vilket minskar din totala kostnad. Mer information om Azure-diskbokningar finns i vår artikel i ämnet: [Förstå hur din reservationsrabatt tillämpas på Azure Disk](../articles/cost-management-billing/reservations/understand-disk-reservations.md).

**Vilka alternativ erbjuder Azure diskreservation?**    
Azure disk reservation ger möjlighet att köpa Premium SSD:er i de angivna SKU:erna från P30 (1 TiB) upp till P80 (32 TiB) under en ettårsperiod. Det finns ingen begränsning av den minsta mängd diskar som krävs för att köpa en diskreservation. Dessutom kan du välja att betala med en enda, förskottsbetalning eller månatliga betalningar. Det finns ingen extra transaktionskostnad för Premium SSD-hanterade diskar.    

Bokningar görs i form av diskar, inte kapacitet. Med andra ord, när du reserverar en P80 (32 TiB) disk, får du en enda P80-disk, kan du inte dela upp den specifika reservationen upp till två mindre P70 (16 TiB) diskar. Du kan naturligtvis reservera så många eller så få diskar som du vill, inklusive två separata P70 (16 TiB) diskar.

**Hur tillämpas Azure-diskreservation?**    
Disksreservation följer en modell som liknar reserverade vm-instanser (Virtual Machine). Skillnaden är att en diskreservation inte kan tillämpas på olika SKU: er, medan en VM-instans kan. Se [Spara kostnader med Azure Reserved VM-instanser](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) för mer information om VM-instanser.     

**Kan jag använda min datalagring som köpts via Azure-diskar reservation över flera regioner?**    
Azure-diskarsreservationen köps för en viss region och SKU (som P30 i östra US 2) och kan därför inte användas utanför dessa konstruktioner. Du kan alltid köpa ytterligare en Azure Disks Reservation för dina disklagringsbehov i andra regioner eller SKU: er.    

**Vad händer när min Azure-diskar reservation löper ut?**    
Du kommer att få e-postmeddelanden 30 dagar före utgångsdatumet och igen på utgångsdatumet. När reservationen går ut fortsätter distribuerade diskar att köras och debiteras med de senaste [priserna för senaste 1-00.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="azure-shared-disks"></a>Delade Azure-diskar

**Stöds funktionen delade diskar för ohanterat diskar eller sidblobar?**

Nej, det stöds bara för premium SSD-hanterade diskar.

**Vilka regioner har stöd för delade diskar?**

För närvarande endast västra centralEN US.

**Kan delade diskar användas som en OS-disk?**

Nej, delade diskar stöds bara för datadiskar.

**Vilka diskstorlekar stöder delade diskar?**

Endast premium-SSD:er som är P15 eller större stöder delade diskar.

**Kan jag aktivera delade diskar på den om jag har en befintlig premium-SSD?**

Alla hanterade diskar som skapats med API-version 2019-07-01 eller senare kan aktivera delade diskar. För att göra detta måste du avmontera disken från alla virtuella datorer som den är kopplad till. Redigera sedan `maxShares` egenskapen på disken.

**Om jag inte längre vill använda en disk i delat läge, hur inaktiverar jag den?**

Avmontera disken från alla virtuella datorer som den är kopplad till. Redigera sedan egenskapen maxShare på disken till 1.

**Kan du ändra storlek på en delad disk?**

Ja.

**Kan jag aktivera skrivaccelerator på en disk som också har delade diskar aktiverade?**

Nej.

**Kan jag aktivera värdcachelagring för en disk som har delad disk aktiverad?**

Det enda värdcachealternativet som stöds är "Ingen".

## <a name="ultra-disks"></a>Ultradiskar

**Vad ska jag ställa in mitt ultradiskdataflöde på?**
Om du är osäker på vad du ska ställa in diskgenomströmningen till rekommenderar vi att du börjar med att anta en IO-storlek på 16 KiB och justera prestanda därifrån när du övervakar ditt program. Formeln är: Dataflöde i MBps = # av IOPS * 16 / 1000.

**Jag konfigurerade min disk till 40000 IOPS men jag ser bara 12800 IOPS, varför ser jag inte prestanda på disken?**
Förutom diskbegränsningen finns det en IO-begränsning som lägger på vm-nivå. Kontrollera att den virtuella datorns storlek som du använder kan stödja de nivåer som är konfigurerade på diskarna. Mer information om IO-begränsningar som införts av den virtuella datorn finns [i Storlekar för virtuella Windows-datorer i Azure](../articles/virtual-machines/windows/sizes.md).

**Kan jag använda cachelagringsnivåer med en ultradisk?**
Nej, ultradiskar stöder inte de olika cachelagringsmetoder som stöds på andra disktyper. Ställ in diskcachelagringen på Ingen.

**Kan jag koppla en ultradisk till min befintliga virtuella dator?**
Kanske måste din virtuella dator vara i en region och tillgänglighet zon par som stöder Ultra diskar. Mer information [finns i Komma igång med ultradiskar.](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md)

**Kan jag använda en ultradisk som OS-disk för min virtuella dator?**
Nej, ultradiskar stöds bara som datadiskar och stöds endast som 4K-inbyggda diskar.

**Kan jag konvertera en befintlig disk till en ultradisk?**
Nej, men du kan migrera data från en befintlig disk till en ultradisk. Om du vill migrera en befintlig disk till en ultradisk ansluter du båda diskarna till samma virtuella dator och kopierar diskens data från en disk till en annan eller använder en tredjepartslösning för datamigrering.

**Kan jag skapa ögonblicksbilder för ultradiskar?**
Nej, ögonblicksbilder är ännu inte tillgängliga.

**Är Azure Backup tillgängligt för ultradiskar?**
Support för Azure Backup är ännu inte tillgänglig.

**Kan jag koppla en ultradisk till en virtuell dator som körs i en tillgänglighetsuppsättning?**
Nej, detta stöds ännu inte.

**Kan jag aktivera Azure Site Recovery för virtuella datorer med ultradiskar?**
Nej, Azure Site Recovery stöds ännu inte för ultradiskar.

## <a name="uploading-to-a-managed-disk"></a>Ladda upp till en hanterad disk

**Kan jag ladda upp data till en befintlig hanterad disk?**

Nej, överföring kan bara användas när en ny tom disk skapas med tillståndet **ReadyToUpload.**

**Hur laddar jag upp till en hanterad disk?**

Skapa en hanterad disk med egenskapen [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) för [creationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) inställd på "Ladda upp", sedan kan du ladda upp data till den.

**Kan jag koppla en disk till en virtuell dator medan den är i ett uppladdningstillstånd?**

Nej.

**Kan jag ta en ögonblicksbild av en manged disk i en uppladdningstillstånd?**

Nej.

## <a name="standard-ssd-disks"></a>Standard-SSD-diskar

**Vad är Azure Standard SSD-diskar?**
Standard-SSD-diskar är standarddiskar som backas upp av solid state-media, optimerade som kostnadseffektiv lagring för arbetsbelastningar som kräver konsekvent prestanda på lägre IOPS-nivåer.

<a id="standard-ssds-azure-regions"></a>**Vilka regioner stöds för närvarande för Standard SSD-diskar?**
Alla Azure-regioner stöder nu Standard SSD-diskar.

**Är Azure Backup tillgängligt när standard-SSD:er ska vara tillgängliga?**
Ja, Azure Backup är nu tillgängligt.

**Hur skapar jag Standard SSD-diskar?**
Du kan skapa Standard SSD-diskar med Azure Resource Manager-mallar, SDK, PowerShell eller CLI. Nedan finns de parametrar som behövs i resource manager-mallen för att skapa Standard SSD-diskar:

* *apiVersion* för Microsoft.Compute måste `2018-04-01` anges som (eller senare)
* Ange *managedDisk.storageAccountType* som`StandardSSD_LRS`

I följande exempel visas avsnittet *properties.storageProfile.osDisk* för en virtuell dator som använder Standard SSD-diskar:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Ett fullständigt mallexempel på hur du skapar en Standard SSD-disk med en mall finns i [Skapa en virtuell dator från en Windows-avbildning med standard-SSD-datadiskar](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Kan jag konvertera mina befintliga diskar till Standard SSD?**
Ja, det kan du. Se [Konvertera Azure-hanterad disklagring från standard till premium och vice versa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) för de allmänna riktlinjerna för konvertering av hanterade diskar. Och använd följande värde för att uppdatera disktypen till Standard SSD.
-AccountType StandardSSD_LRS

**Vad är fördelen med att använda Standard SSD-diskar istället för hårddisk?**
Standard-SSD-diskar ger bättre svarstid, konsekvens, tillgänglighet och tillförlitlighet jämfört med hårddiskar. Programarbetsbelastningar körs mycket smidigare på Standard SSD på grund av det. Premium SSD-diskar är den rekommenderade lösningen för de flesta IO-intensiva produktionsarbetsbelastningar.

**Kan jag använda standard-SSD:er som ohanterade diskar?**
Nej, Standard-SSD-diskar är endast tillgängliga som hanterade diskar.

**Stöder Standard SSD-diskar "VM-sla för en instans"?**
Nej, standard-SSD-enheter har inte en enda instans VM SLA. Använd Premium SSD-diskar för VM-SLA med en instans.

## <a name="migrate-to-managed-disks"></a>Migrera till Managed Disks

**Finns det någon inverkan av migreringen på prestanda för hanterade diskar?**

Migrering innebär förflyttning av disken från en lagringsplats till en annan. Detta är iscensatt via bakgrundskopia av data, vilket kan ta flera timmar att slutföra, vanligtvis mindre än 24 timmar beroende på mängden data i diskarna. Under den tiden kan ditt program uppleva högre läsfördröjning än vanligt eftersom vissa läsningar kan omdirigeras till den ursprungliga platsen och kan ta längre tid att slutföra. Det finns ingen inverkan på skrivfördröjningen under den här perioden.  

**Vilka ändringar krävs i en befintlig Azure Backup-tjänstkonfiguration före/efter migreringen till hanterade diskar?**

Inga ändringar krävs.

**Kommer mina VM-säkerhetskopior som skapats via Azure Backup-tjänsten innan migreringen fortsätter att fungera?**

Ja, säkerhetskopior fungerar sömlöst.

**Vilka ändringar krävs i en befintlig Azure Disks-krypteringskonfiguration före/efter migreringen till hanterade diskar?**

Inga ändringar krävs.

**Stöds automatisk migrering av en befintlig skala för virtuell dator från ohanterade diskar till hanterade diskar?**

Nej. Du kan skapa en ny skalningsuppsättning med hanterade diskar med hjälp av avbildningen från den gamla skalningsuppsättningen med ohanterade diskar.

**Kan jag skapa en hanterad disk från en ögonblicksbild av sidblobb som tagits innan jag migrerade till Hanterade diskar?**

Nej. Du kan exportera en ögonblicksbild av sidblobben som en sidblob och sedan skapa en hanterad disk från den exporterade sidblobben.

**Kan jag växla över mina lokala datorer som skyddas av Azure Site Recovery till en virtuell dator med hanterade diskar?**

Ja, du kan välja att redundans till en virtuell dator med hanterade diskar.

**Finns det någon påverkan av migrering på virtuella Azure-datorer som skyddas av Azure Site Recovery via Azure till Azure replication?**

Nej. Azure Site Recovery Azure till Azure-skydd för virtuella datorer med hanterade diskar är tillgängligt.

**Kan jag migrera virtuella datorer med ohanterade diskar som finns på lagringskonton som är eller tidigare krypterats till hanterade diskar?**

Ja

## <a name="managed-disks-and-storage-service-encryption"></a>Kryptering av hanterade diskar och lagringstjänster

**Är Azure Storage Service-kryptering aktiverat som standard när jag skapar en hanterad disk?**

Ja.

**Är startvolymen krypterad som standard på en hanterad disk?**

Ja. Som standard krypteras alla hanterade diskar, inklusive OS-disken.

**Vem hanterar krypteringsnycklarna?**

Microsoft hanterar krypteringsnycklarna.

**Kan jag inaktivera lagringstjänstkryptering för mina hanterade diskar?**

Nej.

**Är Storage Service Encryption endast tillgänglig i specifika regioner?**

Nej. Den är tillgänglig i alla regioner där hanterade diskar är tillgängliga. Hanterade diskar är tillgängliga i alla offentliga regioner och Tyskland. Det är också tillgängligt i Kina, men endast för Microsoft hanterade nycklar, inte kundhanterade nycklar.

**Hur kan jag ta reda på om min hanterade disk är krypterad?**

Du kan ta reda på när en hanterad disk skapades från Azure-portalen, Azure CLI och PowerShell. Om tiden är över den 9 juni 2017 krypteras disken.

**Hur krypterar jag mina befintliga diskar som skapades före den 10 juni 2017?**

Från och med den 10 juni 2017 krypteras automatiskt nya data som skrivs till befintliga hanterade diskar. Vi planerar också att kryptera befintliga data, och krypteringen kommer att ske asynkront i bakgrunden. Om du måste kryptera befintliga data nu skapar du en kopia av disken. Nya diskar krypteras.

* [Kopiera hanterade diskar med hjälp av Azure CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopiera hanterade diskar med PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Är hanterade ögonblicksbilder och bilder krypterade?**

Ja. Alla hanterade ögonblicksbilder och bilder som skapats efter den 9 juni 2017 krypteras automatiskt. 

**Kan jag konvertera virtuella datorer med ohanterade diskar som finns på lagringskonton som är eller tidigare krypterats till hanterade diskar?**

Ja

**Kommer en exporterad virtuell hårddisk från en hanterad disk eller en ögonblicksbild också att krypteras?**

Nej. Men om du exporterar en virtuell hårddisk till ett krypterat lagringskonto från en krypterad hanterad disk eller ögonblicksbild krypteras den. 

## <a name="premium-disks-managed-and-unmanaged"></a>Premiumdiskar: Hanterade och ohanterade

**Om en virtuell dator använder en storleksserie som stöder Premium SSD-diskar, till exempel en DSv2, kan jag bifoga både premium- och standarddatadiskar?** 

Ja.

**Kan jag koppla både premium- och standarddatadiskar till en storleksserie som inte stöder Premium SSD-diskar, till exempel D-, Dv2-, G- eller F-serien?**

Nej. Du kan bara koppla standarddatadiskar till virtuella datorer som inte använder en storleksserie som stöder Premium SSD-diskar.

**Om jag skapar en premiumdatadisk från en befintlig virtuell hårddisk som var 80 GB, hur mycket kostar det?**

En premiumdatadisk som skapats från en virtuell hårddisk på 80 GB behandlas som nästa tillgängliga premiumdiskstorlek, som är en P10-disk. Du debiteras enligt P10 disk prissättning.

**Finns det transaktionskostnader för att använda Premium SSD-diskar?**

Det finns en fast kostnad för varje diskstorlek, som kommer etablerad med specifika gränser för IOPS och dataflöde. De andra kostnaderna är utgående bandbredd och ögonblicksbildkapacitet, om tillämpligt. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Vilka är gränserna för IOPS och dataflöde som jag kan få från diskcachen?**

De kombinerade gränserna för cache och lokala SSD för en DS-serie är 4 000 IOPS per kärna och 33 MiB per sekund per kärna. GS-serien erbjuder 5 000 IOPS per kärna och 50 MiB per sekund per kärna.

**Stöds den lokala SSD för en vm-skiva med hanterade diskar?**

Den lokala SSD är tillfällig lagring som ingår i en hanterad disk vm. Det kostar inget extra för det här tillfälliga lagringsutrymmet. Vi rekommenderar att du inte använder den här lokala SSD för att lagra dina programdata eftersom de inte sparas i Azure Blob-lagring.

**Finns det några konsekvenser för användningen av TRIM på premium diskar?**

Det finns ingen nackdel med användningen av TRIM på Azure-diskar på antingen premium- eller standarddiskar.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nya diskstorlekar: Hanterade och ohanterade

**Vilka regioner stöder sprängningsfunktioner för tillämplig premium-SSD-diskstorlek?**

Sprängningsfunktionen stöds för närvarande i alla regioner i Azure Public Cloud, med stöd för suveräna moln som kommer snart. 

**Vilka regioner stöds 4/8/16 GiB Managed Disk storlekar (P1/P2/P3, E1/E2/E3) i?**

Dessa nya diskstorlekar stöds för närvarande i alla regioner i Azure Public Cloud, med stöd för suveräna moln som kommer snart. 

**Stöds P1/P2/P3-diskstorlekar för ohanterat diskar eller sidblobar?**

Nej, det stöds bara på premium SSD-hanterade diskar. 

**Stöds E1/E2/E3-diskstorlekar för ohanterat diskar eller sidblobar?**

Nej, standard-SSD-hanterade diskar av alla storlekar kan inte användas med ohanterade diskar eller sidblobar.

**Vilken är den största hanterade diskstorleken som stöds för operativsystem och datadiskar?**

Den partitionstyp som Azure stöder för en operativsystemdisk är mbr (Master Boot Record). MBR-formatet stöder en diskstorlek på upp till 2 TiB. Den största storleken som Azure stöder för en operativsystemdisk är 2 TiB. Azure stöder upp till 32 TiB för hanterade datadiskar.

**Vilken är den största ohanterliga diskstorleken som stöds för operativsystem och datadiskar?**

Den partitionstyp som Azure stöder för en operativsystemdisk är mbr (Master Boot Record). MBR-formatet stöder en diskstorlek på upp till 2 TiB. Den största storleken som Azure stöder för ett operativsystem Ohanterat disk är 2 TiB. Azure stöder upp till 4 TiB för data Ohanterat diskar.

**Vilken är den största sidblobbstorleken som stöds?**

Den största sidblobbstorleken som Azure stöder är 8 TiB (8 191 GiB). Den maximala sidblobstorleken när den är kopplad till en virtuell dator som data- eller operativsystemdiskar är 4 TiB (4 095 GiB).

**Måste jag använda en ny version av Azure-verktyg för att skapa, bifoga, ändra storlek på och ladda upp diskar som är större än 1 TiB?**

Du behöver inte uppgradera dina befintliga Azure-verktyg för att skapa, bifoga eller ändra storlek på diskar som är större än 1 TiB. Om du vill ladda upp vhd-filen från lokalt direkt till Azure som en sidblobb eller ohanterad disk måste du använda de senaste verktygsuppsättningarna nedan. Vi stöder endast VHD-uppladdningar på upp till 8 TiB.

|Azure-verktyg      | Versioner som stöds                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versionsnummer 4.1.0: Juni 2017 släpps eller senare|
|Azure CLI v1     | Versionsnummer 0.10.13: Maj 2017 släpps eller senare|
|Azure CLI v2     | Versionsnummer 2.0.12: Juli 2017 släpps eller senare|
|AzCopy              | Versionsnummer 6.1.0: Juni 2017 släpps eller senare|

**Stöds P4- och P6-diskstorlekar för ohanterat diskar eller sidblobar?**

P4 -diskstorlekar (32 GiB) och P6 (64 GiB) stöds inte som standarddisknivåer för ohanterade diskar och sidblobar. Du måste uttryckligen [ställa in Blob-nivån](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) till P4 och P6 för att disken ska vara mappad till dessa nivåer. Om du distribuerar en ohanterad disk- eller sidblob med diskstorleken eller innehållslängden mindre än 32 GiB eller mellan 32 GiB till 64 GiB utan att ange Blob-nivån fortsätter du att landa på P10 med 500 IOPS och 100 MiB/s och den mappade prisnivån.

**Om min befintliga premiumhanterade disk mindre än 64 GiB skapades innan den lilla disken aktiverades (runt 15 juni 2017), hur faktureras den?**

Befintliga små premiumdiskar som är mindre än 64 GiB fortsätter att faktureras enligt P10-prisnivån.

**Hur kan jag byta disknivå av små premiumdiskar mindre än 64 GiB från P10 till P4 eller P6?**

Du kan ta en ögonblicksbild av dina små diskar och sedan skapa en disk för att automatiskt växla prisnivån till P4 eller P6 baserat på den etablerade storleken.

**Kan du ändra storlek på befintliga hanterade diskar från storlekar som är färre än 4 tebibyte (TiB) till nya nyinförda diskstorlekar upp till 32 TiB?**

Ja.

**Vilka är de största diskstorlekarna som stöds av Azure Backup och Azure Site Recovery Service?**

Den största diskstorleken som stöds av Azure Backup är 32 TiB (4 TiB för krypterade diskar). Den största diskstorleken som stöds av Azure Site Recovery är 8 TiB. Stöd för större diskar upp till 32 TiB är ännu inte tillgängligt i Azure Site Recovery.

**Vilka är de rekommenderade VM-storlekarna för större diskstorlekar (>4 TiB) för Standard SSD- och Standard HDD-diskar för att uppnå optimerad disk IOPS och bandbredd?**

För att uppnå diskgenomströmningen för standardstorlekar för SSD och standard HDD stora diskstorlekar (>4 TiB) utöver 500 IOPS och 60 MiB/s, Vi rekommenderar att du distribuerar en ny virtuell dator från någon av följande VM-storlekar för att optimera dina prestanda: B-serien, DSv2-serien, Dsv3-serien, ESv3-serien, Fsv2-serien, Fsv2-serien, M-serien, GS-serien, NCv2-serien, NCv3-serien eller virtuella datorer i Ls-serien. Om du ansluter stora diskar till befintliga virtuella datorer eller virtuella datorer som inte använder de rekommenderade storlekarna ovan kan prestanda få lägre prestanda.

**Hur kan jag uppgradera mina diskar (>4 TiB) som distribuerades under den större diskstorleksförhandsgranskningen för att få den högre IOPS-& bandbredden på GA?**

Du kan antingen stoppa och starta den virtuella datorn som disken är ansluten till eller ta bort och återansluta disken. Prestandamålen för större diskstorlekar har höjts för både premium-SSD och standard-SSD:er på GA.

**Vilka regioner stöds de hanterade diskstorlekarna 8 TiB, 16 TiB och 32 TiB i?**

8 TiB, 16 TiB och 32 TiB-diskSKU:er stöds i alla regioner under globala Azure, Microsoft Azure Government och Azure China 21Vianet.

**Stöder vi att värdcachelagring kan aktiveras på alla diskstorlekar?**

Vi stöder Host Caching av ReadOnly och Läs / Skriv på diskstorlekar mindre än 4 TiB. För diskstorlekar som är mer än 4 TiB stöder vi inte att ange cachelagringsalternativ än Ingen. Vi rekommenderar att du använder cachelagring för mindre diskstorlekar där du kan förvänta dig bättre prestandaökning med data som cachelagras till den virtuella datorn.

## <a name="what-if-my-question-isnt-answered-here"></a>Tänk om min fråga inte besvaras här?

Om din fråga inte finns med här, låt oss veta så hjälper vi dig att hitta ett svar. Du kan ställa en fråga i slutet av den här artikeln i kommentarerna. Om du vill samarbeta med Azure Storage-teamet och andra community-medlemmar om den här artikeln använder du FORUMET MSDN [Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Om du vill begära funktioner skickar du dina förfrågningar och idéer till [feedbackforumet för Azure Storage](https://feedback.azure.com/forums/217298-storage).

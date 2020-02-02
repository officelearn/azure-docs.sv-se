---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39bcaac2ca94eedebd991a1c4e93f324ef651888
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961607"
---
Den här artikeln ger svar på några vanliga frågor om Azure Managed Disks och Azure Premium SSD-diskar.

## <a name="managed-disks"></a>Managed Disks

**Vad är Azure Managed Disks?**

Managed Disks är en funktion som fören klar disk hanteringen för virtuella Azure IaaS-datorer genom att hantera lagrings konto hantering åt dig. Mer information finns i [Översikt över Managed disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Hur mycket kostar mig om jag skapar en standard-hanterad disk från en befintlig virtuell hård disk som är 80 GB?**

En standard-hanterad disk som skapas från en 80 GB-hårddisk behandlas som nästa tillgängliga standard disk storlek, som är en S10 disk. Du debiteras enligt priserna för S10-disken. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Kostar några transaktioner för standard Managed disks?**

Ja. Du debiteras för varje transaktion. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Kommer jag att debiteras för den faktiska storleken på data på disken eller för den allokerade kapaciteten på disken för en standard hanterad disk?**

Du debiteras baserat på diskens etablerade kapacitet. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Hur skiljer sig priserna från Premium Managed disks från ohanterade diskar?**

Priserna för Premium Managed disks är samma som för ohanterade Premium diskar.

**Kan jag ändra lagrings konto typen (standard eller Premium) för mina hanterade diskar?**

Ja. Du kan ändra lagrings konto typen för dina hanterade diskar med hjälp av Azure Portal, PowerShell eller Azure CLI.

**Kan jag använda en VHD-fil i ett Azure Storage-konto för att skapa en hanterad disk med en annan prenumeration?**

Ja.

**Kan jag använda en VHD-fil i ett Azure Storage-konto för att skapa en hanterad disk i en annan region?**

Nej.

**Finns det några skalnings begränsningar för kunder som använder Managed disks?**

Managed Disks eliminerar de gränser som är kopplade till lagrings konton. Den maximala gränsen är dock 50 000 Managed disks per region och per disk typ för en prenumeration.

**Kan jag ta en stegvis ögonblicks bild av en hanterad disk?**

Nej. Den aktuella ögonblicks bild funktionen gör en fullständig kopia av en hanterad disk.

**Kan virtuella datorer i en tillgänglighets uppsättning bestå av en kombination av hanterade och ohanterade diskar?**

Nej. De virtuella datorerna i en tillgänglighets uppsättning måste använda antingen alla hanterade diskar eller alla ohanterade diskar. När du skapar en tillgänglighets uppsättning kan du välja vilken typ av diskar du vill använda.

**Är Managed Disks standard alternativet i Azure Portal?**

Ja.

**Kan jag skapa en tom hanterad disk?**

Ja. Du kan skapa en tom disk. En hanterad disk kan skapas oberoende av en virtuell dator, till exempel utan att koppla den till en virtuell dator.

**Vad är antalet fel domäner som stöds för en tillgänglighets uppsättning som använder Managed Disks?**

Beroende på i vilken region den tillgänglighets uppsättning som använder Managed Disks finns, är antalet fel domäner som stöds 2 eller 3.

**Hur konfigureras standard lagrings kontot för diagnostik?**

Du konfigurerar ett privat lagrings konto för VM-diagnostik.

**Vilken typ av rollbaserad Access Control support är tillgänglig för Managed Disks?**

Managed Disks stöder tre nyckel standard roller:

* Ägare: kan hantera allt, inklusive åtkomst
* Deltagare: kan hantera allt utom åtkomst
* Läsare: kan visa allt, men kan inte göra ändringar

**Finns det något sätt som jag kan kopiera eller exportera en hanterad disk till ett privat lagrings konto?**

Du kan generera en skrivskyddad URL för signatur för delad åtkomst (SAS) för den hanterade disken och använda den för att kopiera innehållet till ett privat lagrings konto eller lokalt lagrings utrymme. Du kan använda SAS-URI: n med hjälp av Azure Portal, Azure PowerShell, Azure CLI eller [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Kan jag skapa en kopia av min hanterade disk?**

Kunder kan ta en ögonblicks bild av sina hanterade diskar och sedan använda ögonblicks bilden för att skapa en annan hanterad disk.

**Stöds fortfarande ohanterade diskar?**

Ja, både ohanterade och hanterade diskar stöds. Vi rekommenderar att du använder Managed disks för nya arbets belastningar och migrerar dina aktuella arbets belastningar till hanterade diskar.

**Kan jag samplacera ohanterade och hanterade diskar på samma virtuella dator?**

Nej.

**Kommer jag att debiteras för nästa disk storlek (256 GiB) om jag skapar en 128 GB-disk och sedan ökar storleken till 130 gibibyte (GiB)?**

Ja.

**Kan jag skapa lokalt redundant lagring, Geo-redundant lagring och zoner som är redundanta lagrings enheter?**

Azure Managed Disks stöder för närvarande endast lokalt redundanta lagrings diskar.

**Kan jag krympa eller downsize mina Managed disks?**

Nej. Den här funktionen stöds inte för närvarande.

**Kan jag avbryta ett lån på disken?**

Nej. Detta stöds inte för närvarande eftersom det finns ett lån för att förhindra oavsiktlig borttagning när disken används.

**Kan jag ändra egenskapen för dator namn när en specialiserad (inte skapad med system förberedelse verktyget eller den generaliserade) operativ system disken används för att etablera en virtuell dator?**

Nej. Du kan inte uppdatera egenskapen dator namn. Den nya virtuella datorn ärver den från den överordnade virtuella datorn som användes för att skapa operativ system disken. 

**Var kan jag hitta exempel Azure Resource Manager mallar för att skapa virtuella datorer med Managed disks?**
* [Lista med mallar som använder Managed Disks](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Finns det någon kontinuerlig befintlig relation med den käll-bloben när du skapar en disk från en BLOB?**

Nej, när den nya disken skapas, är en fullständig kopia av denna BLOB vid den tidpunkten och det finns ingen anslutning mellan de två. Om du vill kan du ta bort käll-bloben utan att påverka den nyligen skapade disken på något sätt när du har skapat disken.

**Kan jag byta namn på en hanterad eller ohanterad disk när den har skapats?**

Du kan inte byta namn på hanterade diskar. Men du kan byta namn på en ohanterad disk så länge den inte är ansluten till en virtuell hård disk eller virtuell dator.

**Kan jag använda GPT-partitionering på en Azure-disk?**

Generation 1-avbildningar kan bara använda GPT-partitionering på data diskar, inte på OS-diskar. OS-diskar måste ha partitionstypen MBR-partition.

[Generation 2-avbildningar](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) kan använda GPT-partitionering på OS-disken och data diskarna.

**Vilka disk typer stöder ögonblicks bilder?**

Ögonblicks bilder av Premium SSD, standard SSD och standard-HDD stöder. För dessa tre disk typer stöds ögonblicks bilder för alla disk storlekar (inklusive diskar upp till 32 TiB i storlek). Ultra disks stöder inte ögonblicks bilder.

**Vad är Azure disk-reservationer?**
Disk reservation är möjligheten att köpa ett års disk lagring i förväg, vilket minskar den totala kostnaden. Mer information om Azure disk reservations finns i vår artikel om ämnet: [förstå hur reservations rabatten tillämpas på Azure-diskar](../articles/cost-management-billing/reservations/understand-disk-reservations.md).

**Vilka alternativ erbjuder Azure disk reservation?** Med Azure disk reservation får du möjlighet att köpa Premium-SSD i de angivna SKU: erna från P30 (1 TiB) upp till P80 (32 TiB) under ett år. Det finns ingen begränsning för den minsta mängd diskar som krävs för att köpa en disk reservation. Dessutom kan du betala med en enskild förskottsbetalning eller månatligen. Ingen ytterligare transaktionell kostnad tillämpas för Premium SSD Managed Disks. 

Reservationer görs i form av diskar, inte kapacitet. Med andra ord får du en enda P80-disk när du reserverar en P80-disk (32 TiB). du kan sedan inte dela upp den här reservationen i två mindre P70-diskar (16 TiB). Du kan naturligtvis reservera så många eller så få diskar som du vill, inklusive två separata P70-diskar (16 TiB).

**Hur tillämpas Azure disk reservation?**  
Disk reservationen följer en modell liknande de reserverade virtuella dator instanserna. Skillnaden är att en disk reservation inte kan tillämpas på olika SKU: er, medan en VM-instans kan. Mer information om VM-instanser finns i [Spara kostnader med Azure Reserved VM instances](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) .    

**Kan jag använda min data lagring via reservationen av Azure-diskar i flera regioner?**     
Azure disks-reservationen köps för en bestämd region och SKU (som P30 i USA, östra 2) och kan därför inte användas utanför dessa konstruktioner. Du kan alltid köpa ytterligare en Azure disks-reservation för dina disk lagrings behov i andra regioner eller SKU: er. 

**Vad händer när reservationen för Azure disks upphör att gälla?**    
Du får e-postmeddelanden 30 dagar innan det går ut och sedan på förfallo datumet. När reservationen går ut fortsätter de distribuerade diskarna att köras och debiteras enligt de senaste priserna enligt principen [betala per](https://azure.microsoft.com/pricing/details/managed-disks/)användning.

## <a name="ultra-disks"></a>Ultra disks

**Vad ska jag ställa in mitt hård disk data flöde på?**
Om du är osäker på vad du ska ställa in disk data flödet på rekommenderar vi att du börjar med att anta en i/o-storlek på 16 KiB och justerar prestandan därifrån när du övervakar ditt program. Formeln är: data flöde i Mbit/s = # av IOPS * 16/1000.

**Jag har konfigurerat min disk till 40000 IOPS men jag ser bara 12800 IOPS, varför ser jag inte diskens prestanda?**
Förutom disk begränsningen finns det en IO-begränsning som läggs på VM-nivån. Kontrol lera att den virtuella dator storleken som du använder stöder de nivåer som har kon figurer ATS på diskarna. Information om de IO-gränser som den virtuella datorn har infört finns i [storlekar för virtuella Windows-datorer i Azure](../articles/virtual-machines/windows/sizes.md).

**Kan jag använda lagrings nivåer med en Ultra disk?**
Nej, Ultra disks stöder inte de olika metoderna för cachelagring som stöds på andra disk typer. Ställ in diskcachelagring på ingen.

**Kan jag ansluta en Ultra disk till min befintliga virtuella dator?**
Din virtuella dator måste kanske finnas i en region och ett tillgänglighets zon par som stöder Ultra disks. Mer information finns i [komma igång med Ultra disks](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) .

**Kan jag använda en Ultra disk som OS-disk för den virtuella datorn?**
Nej, Ultra disks stöds bara som data diskar och stöds endast som 4K-inhemska diskar.

**Kan jag konvertera en befintlig disk till en Ultra disk?**
Nej, men du kan migrera data från en befintlig disk till en Ultra disk. Om du vill migrera en befintlig disk till en Ultra disk ansluter du båda diskarna till samma virtuella dator och kopierar diskens data från en disk till en annan eller använder en lösning från tredje part för datamigrering.

**Kan jag skapa ögonblicks bilder för Ultra disks?**
Nej, ögonblicks bilder är inte tillgängliga än.

**Är Azure Backup tillgängligt för Ultra disks?**
Nej, Azure Backup support är inte tillgängligt ännu.

**Kan jag ansluta en Ultra disk till en virtuell dator som körs i en tillgänglighets uppsättning?**
Nej, detta stöds inte ännu.

**Kan jag aktivera Azure Site Recovery för virtuella datorer med hjälp av Ultra disks?**
Nej, Azure Site Recovery stöds ännu inte för Ultra disks.

## <a name="uploading-to-a-managed-disk"></a>Laddar upp till en hanterad disk

**Kan jag ladda upp data till en befintlig hanterad disk?**

Nej, uppladdning kan bara användas när en ny tom disk skapas med **ReadyToUpload** -tillstånd.

**Hur gör jag för att ladda upp till en hanterad disk?**

Skapa en hanterad disk med egenskapen [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) för [creationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) inställt på "Ladda upp". sedan kan du ladda upp data till den.

**Kan jag ansluta en disk till en virtuell dator när den är i ett överförings tillstånd?**

Nej.

**Kan jag ta en ögonblicks bild av en hanterade-disk i ett uppladdnings tillstånd?**

Nej.

## <a name="standard-ssd-disks"></a>Standard SSD diskar

**Vad är Azure Standard SSD-diskar?**
Standard SSD diskar är standard diskar som backas upp av Solid State-medier, optimerade som kostnads effektiv lagring för arbets belastningar som kräver konsekvent prestanda på lägre IOPS-nivåer.

<a id="standard-ssds-azure-regions"></a>**Vilka regioner stöds för närvarande Standard SSD diskar?**
Alla Azure-regioner stöder nu Standard SSD diskar.

**Är Azure Backup tillgängligt när du använder standard-SSD?**
Ja, Azure Backup är nu tillgängligt.

**Hur gör jag för att skapa Standard SSD diskar?**
Du kan skapa Standard SSD diskar med Azure Resource Manager mallar, SDK, PowerShell eller CLI. Nedan visas de parametrar som behövs i Resource Manager-mallen för att skapa Standard SSD diskar:

* *API version* för Microsoft. Compute måste anges som `2018-04-01` (eller senare)
* Ange *managedDisk. storageAccountType* som `StandardSSD_LRS`

I följande exempel visas avsnittet *Properties. storageProfile. osDisk* för en virtuell dator som använder standard SSD diskar:

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

Ett komplett mall exempel på hur du skapar en Standard SSD disk med en mall finns i [skapa en virtuell dator från en Windows-avbildning med standard SSD data diskar](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Kan jag konvertera mina befintliga diskar till Standard SSD?**
Ja, det kan du. Se [konvertera Azure Managed disks Storage från standard till Premium och vice versa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) för de allmänna rikt linjerna för att konvertera Managed disks. Och Använd följande värde för att uppdatera disk typen till Standard SSD.
-Den StandardSSD_LRS

**Vad är fördelen med att använda Standard SSD diskar i stället för hård diskar?**
Standard SSD diskar ger bättre svars tid, konsekvens, tillgänglighet och tillförlitlighet jämfört med hård diskar. Program arbets belastningar kör mycket mer smidigt på Standard SSD på grund av det. Obs! Premium SSD diskar är den rekommenderade lösningen för de flesta i/o-intensiva produktions arbets belastningar.

**Kan jag använda standard SSD som ohanterade diskar?**
Nej, standard SSD-diskar är bara tillgängliga som Managed Disks.

**Stöder Standard SSD diskar "service nivå avtal för virtuell instans"?**
Nej, standard-SSD har inte något service avtal för VM med en instans. Använd Premium SSD diskar för VM service avtal för enskild instans.

## <a name="migrate-to-managed-disks"></a>Migrera till Managed Disks

**Påverkar migreringen Managed Disks prestanda?**

Migrering innebär att disken flyttas från en lagrings plats till en annan. Detta dirigeras via bakgrunds kopia av data, vilket kan ta flera timmar att slutföra, vanligt vis mindre än 24 timmar beroende på mängden data i diskarna. Under den tiden kan ditt program uppleva högre än vanliga Läs fördröjningar eftersom vissa läsningar kan omdirigeras till den ursprungliga platsen, och det kan ta längre tid att slutföra. Skriv fördröjningen påverkas inte under den här perioden.  

**Vilka ändringar krävs i en befintlig Azure Backup tjänst konfiguration före/efter migreringen till Managed Disks?**

Inga ändringar krävs.

**Kommer mina VM-säkerhetskopieringar som skapats via Azure Backup tjänst innan migreringen fortsätta att fungera?**

Ja, säkerhets kopieringarna fungerar sömlöst.

**Vilka ändringar krävs i den befintliga krypterings konfigurationen för Azure disks före/efter migreringen till Managed Disks?**

Inga ändringar krävs.

**Är automatisk migrering av en befintlig virtuell dators skalnings uppsättning från ohanterade diskar till Managed Disks som stöds?**

Nej. Du kan skapa en ny skalnings uppsättning med Managed Disks att använda avbildningen från din gamla skalnings uppsättning med ohanterade diskar.

**Kan jag skapa en hanterad disk från en sid ögonblicks bild som tagits innan migrering till Managed Disks?**

Nej. Du kan exportera en sid-BLOB-ögonblicksbild som en Page blob och sedan skapa en hanterad disk från den exporterade sid-blobben.

**Kan jag redundansväxla mina lokala datorer som skyddas av Azure Site Recovery till en virtuell dator med Managed Disks?**

Ja, du kan välja att redundansväxla till en virtuell dator med Managed Disks.

**Påverkar migreringen på virtuella Azure-datorer som skyddas av Azure Site Recovery via Azure till Azure-replikering?**

Nej. Azure Site Recovery Azure till Azure-skydd för virtuella datorer med Managed Disks tillgängligt.

**Kan jag migrera virtuella datorer med ohanterade diskar som finns på lagrings konton som eller tidigare har krypterats till Managed disks?**

Ja

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks och Kryptering för lagringstjänst

**Är Azure Storage tjänst kryptering aktive rad som standard när jag skapar en hanterad disk?**

Ja.

**Är start volymen krypterad som standard på en hanterad disk?**

Ja. Som standard krypteras alla hanterade diskar, inklusive OS-disken.

**Vem hanterar krypterings nycklarna?**

Microsoft hanterar krypterings nycklarna.

**Kan jag inaktivera Kryptering för lagringstjänst för mina hanterade diskar?**

Nej.

**Är Kryptering för lagringstjänst endast tillgängligt i vissa regioner?**

Nej. Den är tillgänglig i alla regioner där Managed Disks är tillgängliga. Managed Disks är tillgängligt i alla offentliga regioner och Tyskland. Den är också tillgänglig i Kina, men endast för Microsoft-hanterade nycklar, inte kund hanterade nycklar.

**Hur kan jag ta reda på om min hanterade disk är krypterad?**

Du kan ta reda på hur lång tid det tar innan en hanterad disk skapades från Azure Portal, Azure CLI och PowerShell. Om tiden är senare än den 9 juni 2017 krypteras din disk.

**Hur kan jag kryptera mina befintliga diskar som skapats före den 10 juni 2017?**

Från och med den 10 juni 2017 krypteras nya data som skrivs till befintliga hanterade diskar automatiskt. Vi planerar också att kryptera befintliga data och krypteringen sker asynkront i bakgrunden. Om du måste kryptera befintliga data nu skapar du en kopia av disken. Nya diskar kommer att krypteras.

* [Kopiera hanterade diskar med hjälp av Azure CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopiera hanterade diskar med hjälp av PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Är hanterade ögonblicks bilder och bilder krypterade?**

Ja. Alla hanterade ögonblicks bilder och avbildningar som skapats efter den 9 juni 2017 krypteras automatiskt. 

**Kan jag konvertera virtuella datorer med ohanterade diskar som finns på lagrings konton som eller tidigare har krypterats till Managed disks?**

Ja

**Kommer en exporterad virtuell hård disk från en hanterad disk eller en ögonblicks bild också att krypteras?**

Nej. Men om du exporterar en virtuell hård disk till ett krypterat lagrings konto från en krypterad hanterad disk eller ögonblicks bild är den krypterad. 

## <a name="premium-disks-managed-and-unmanaged"></a>Premium diskar: hanterade och ohanterade

**Om en virtuell dator använder en storleks serie som stöder Premium SSD diskar, t. ex. en DSv2, kan jag koppla både Premium-och standard data diskar?** 

Ja.

**Kan jag koppla både Premium-och standard data diskar till en storleks serie som inte stöder Premium SSD diskar, till exempel D, Dv2, G eller F-serien?**

Nej. Du kan bara koppla standard data diskar till virtuella datorer som inte använder en storleks serie som stöder Premium SSD diskar.

**Hur mycket kostar det om jag skapar en Premium-datadisk från en befintlig virtuell hård disk som var 80 GB?**

En Premium-datadisk som skapas från en virtuell 80 GB-hårddisk behandlas som nästa tillgängliga Premium disk storlek, som är en P10 disk. Du debiteras enligt priserna för P10-disken.

**Använder transaktions kostnader för Premium SSD diskar?**

Det finns en fast kostnad för varje disk storlek, som tillhandahålls med bestämda gränser för IOPS och data flöde. Övriga kostnader är utgående bandbredd och ögonblicks bild kapacitet, om tillämpligt. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Vilka är gränserna för IOPS och data flöde som jag kan hämta från disk-cachen?**

De kombinerade gränserna för cache och lokal SSD för en DS-serie är 4 000 IOPS per kärna och 33 MiB per sekund per kärna. GS-serien erbjuder 5 000 IOPS per kärna och 50 MiB per sekund per kärna.

**Stöds lokal SSD för en Managed Disks virtuell dator?**

Den lokala SSD-enheten är tillfällig lagring som ingår i en Managed Disks VM. Det finns ingen extra kostnad för den här tillfälliga lagringen. Vi rekommenderar att du inte använder den här lokala SSD för att lagra dina program data eftersom de inte är sparade i Azure Blob Storage.

**Finns det några konsekvenser för användningen av TRIM på Premium-diskar?**

Det finns ingen nack delar med att trimma på Azure-diskar på antingen Premium-eller standard diskar.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nya disk storlekar: hanterade och ohanterade

**Vilka regioner stöder burst-kapacitet för tillämplig Premium SSD-disk storlek?**

Burst-funktionen stöds för närvarande i Azure västra centrala USA.

**Vilka regioner är 4/8/16 GiB-hanterade disk storlekar (P1/P2/P3, E1/E2/E3) som stöds i?**

Dessa nya disk storlekar stöds för närvarande i Azure västra centrala USA.

**Stöds disk storlekarna P1/P2/P3 för ohanterade diskar eller sid-blobar?**

Nej, det stöds bara på Premium SSD-hanterade diskar. 

**Stöds E1/E2/E3 disk storlekar för ohanterade diskar eller sid-blobar?**

Nej, standard SSD-hanterade diskar i valfri storlek kan inte användas med ohanterade diskar eller sid-blobar.

**Vilken är den största hanterade disk storleken som stöds för operativ system och data diskar?**

Partitionstypen som Azure stöder för en operativ system disk är Master Boot Record (MBR). MBR-formatet stöder en disk storlek upp till 2 TiB. Den största storleken som Azure stöder för en operativ system disk är 2 TiB. Azure har stöd för upp till 32 TiB för hanterade data diskar.

**Vilken är den största ohanterade disk storleken som stöds för operativ system och data diskar?**

Partitionstypen som Azure stöder för en operativ system disk är Master Boot Record (MBR). MBR-formatet stöder en disk storlek upp till 2 TiB. Den största storleken som Azure stöder för en ohanterad disk för operativ system är 2 TiB. Azure har stöd för upp till 4 TiB för ohanterade diskar.

**Vilken är den största sid-BLOB-storlek som stöds?**

Den största sid-BLOB-storlek som stöds av Azure är 8 TiB (8 191 GiB). Den maximala sid-BLOB-storlek som är kopplad till en virtuell dator som data eller operativ system diskar är 4 TiB (4 095 GiB).

**Behöver jag använda en ny version av Azure-verktyg för att skapa, ansluta, ändra storlek på och ladda upp diskar som är större än 1 TiB?**

Du behöver inte uppgradera dina befintliga Azure-verktyg för att skapa, bifoga eller ändra storlek på diskar som är större än 1 TiB. Om du vill ladda upp VHD-filen från lokala platser direkt till Azure som en sid-BLOB eller ohanterad disk, måste du använda de senaste verktygen i listan nedan. Vi stöder bara VHD-uppladdningar av upp till 8 TiB.

|Azure-verktyg      | Versioner som stöds                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versions nummer 4.1.0: utgåva 2017 juni eller senare|
|Azure CLI v1     | Versions nummer 0.10.13: maj 2017 release eller senare|
|Azure CLI v2     | Versions nummer 2.0.12: juli 2017-utgåva eller senare|
|AzCopy           | Versions nummer 6.1.0: utgåva 2017 juni eller senare|

**Stöds P4-och P6-disk storlekar för ohanterade diskar eller sid-blobar?**

Disk storlekarna P4 (32 GiB) och P6 (64 GiB) stöds inte som standard disk nivåer för ohanterade diskar och sid-blobar. Du måste uttryckligen [Ange BLOB-nivån](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) till P4 och P6 för att disken ska mappas till dessa nivåer. Om du distribuerar en ohanterad disk eller sid-BLOB med disk storlek eller innehålls längd som är mindre än 32 GiB eller mellan 32 GiB och 64 GiB utan att ange BLOB-nivån, kommer du att fortsätta att använda P10 med 500 IOPS och 100 MiB/s och den mappade pris nivån.

**Om min befintliga Premium-hanterade disk som är mindre än 64 GiB skapades innan den lilla disken var aktive rad (cirka 15 juni 2017), hur faktureras den?**

Befintliga små Premium diskar som är mindre än 64-GiB fortsätter att faktureras enligt pris nivån för P10.

**Hur kan jag byta disk nivå för små Premium diskar som är mindre än 64 GiB från P10 till P4 eller P6?**

Du kan ta en ögonblicks bild av dina små diskar och sedan skapa en disk för att automatiskt växla pris nivån till P4 eller P6 baserat på den etablerade storleken.

**Kan du ändra storlek på befintliga Managed Disks från storlekar färre än 4 tebibyte (TiB) till nya nyligen introducerade disk storlekar upp till 32 TiB?**

Ja.

**Vilka är de största disk storlekarna som stöds av Azure Backup-och Azure Site Recovery-tjänsten?**

Den största disk storleken som stöds av Azure Backup är 32 TiB (4 TiB för krypterade diskar). Den största disk storleken som stöds av Azure Site Recovery är 8 TiB. Stöd för de större diskarna på upp till 32 TiB är ännu inte tillgängligt i Azure Site Recovery.

**Vilka är de rekommenderade VM-storlekarna för större disk storlekar (> 4 TiB) för Standard SSD och Standard HDD diskar för att åstadkomma optimerad disk-IOPS och bandbredd?**

För att uppnå disk data flödet för Standard SSD och Standard HDD stora disk storlekar (> 4 TiB) utöver 500 IOPS och 60 MiB/s, vi rekommenderar att du distribuerar en ny virtuell dator från någon av följande storlekar för virtuella datorer för att optimera prestanda: B-serien, DSv2-serien, Dsv3-serien, ESv3-serien, FS-serien, Fsv2-serien,-serien, GS-serien, NCv2-serien, NCv3-serien eller LS-seriens virtuella datorer. Om du bifogar stora diskar till befintliga virtuella datorer eller virtuella datorer som inte använder de rekommenderade storlekarna ovan kan det uppstå lägre prestanda.

**Hur uppgraderar jag mina diskar (> 4 TiB) som distribuerades under för hands versionen av större disk storlekar för att få högre IOPS & bandbredd i GA?**

Du kan antingen stoppa och starta den virtuella datorn som disken är ansluten till eller, koppla från och ansluta disken igen. Prestanda målen för större disk storlekar har ökat för både Premium-SSD och standard-SSD på GA.

**Vilka regioner är de hanterade disk storlekarna på 8 TiB, 16 TiB och 32 TiB stöds i?**

8-TiB, 16 TiB och 32 TiB disk SKU: er stöds i alla regioner under Global Azure, Microsoft Azure Government och Azure Kina 21Vianet.

**Stöder vi aktivering av cachelagring av värdar på alla disk storlekar?**

Vi stöder cachelagring av värdar för ReadOnly och läsning/skrivning på disk storlekar som är mindre än 4 TiB. För disk storlekar på mer än 4 TiB har vi inte stöd för alternativ för cachelagring förutom ingen. Vi rekommenderar att du använder cachelagring för mindre disk storlekar där du kan vänta på att få bättre prestanda ökning med cachelagrade data i den virtuella datorn.

## <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?

Om din fråga inte visas här kan vi hjälpa oss och vi hjälper dig att hitta ett svar. Du kan skicka en fråga i slutet av den här artikeln i kommentarerna. Om du vill delta i Azure Storage teamet och andra community-medlemmar om den här artikeln använder du MSDN [Azure Storage-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Om du vill begära funktioner skickar du dina förfrågningar och idéer till [Azure Storage feedback-forumet](https://feedback.azure.com/forums/217298-storage).

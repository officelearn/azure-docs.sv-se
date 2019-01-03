---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5835e8275bedebaca01d585a992930d6856edfd7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53553480"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Vanliga frågor och svar om virtuella Azure IaaS-datorer och hanterade och ohanterade premiumdiskar

Den här artikeln får du svar på några vanliga frågor om Azure Managed Disks och Azure Premium SSD-diskar.

## <a name="managed-disks"></a>Managed Disks

**Vad är Azure Managed Disks?**

Hanterade diskar är en funktion som förenklar Diskhanteringen för virtuella Azure IaaS-datorer genom att hantera lagringskontohantering åt dig. Mer information finns i den [översikt över Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Om jag skapar en standard hanterad disk från en befintlig virtuell Hårddisk som är 80 GB, hur mycket som kostar mig?**

En standard hanterad disk som skapats från en virtuell Hårddisk på 80 GB behandlas som nästa tillgängliga standard diskens storlek, vilket är en S10-disk. Du debiteras enligt prissättningen för S10 disk. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Finns det några transaktionskostnader för hanterade standarddiskar?**

Ja. Du debiteras för varje transaktion. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**För en standard managed disk kommer jag att debiteras för den verkliga storleken hos data på disken eller etablerad kapacitet av disken?**

Du kommer att debiteras baserat på etablerad kapacitet på disken. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Hur är priser för premium managed disks skiljer sig från ohanterade diskar?**

Priser för premium-hanterade diskar är samma som ohanterade premium-diskar.

**Kan jag ändra lagringskontotypen (Standard eller Premium) av min hanterade diskar?**

Ja. Du kan ändra typ av lagringskonto av dina hanterade diskar med hjälp av Azure portal, PowerShell eller Azure CLI.

**Kan jag använda en VHD-fil i ett Azure storage-konto för att skapa en hanterad disk med en annan prenumeration?**

Ja.

**Kan jag använda en VHD-fil i ett Azure storage-konto för att skapa en hanterad disk i en annan region?**

Nej.

**Finns det några begränsningar för skalning för kunder som använder hanterade diskar?**

Hanterade diskar eliminerar begränsningar med storage-konton. Den maximala gränsen är dock 50 000 hanterade diskar per region och per typ av disk för en prenumeration.

**Kan jag göra en inkrementell ögonblicksbild av en hanterad disk?**

Nej. Den aktuella kapaciteten för ögonblicksbild gör en fullständig kopia av en hanterad disk.

**Kan virtuella datorer i en tillgänglighetsuppsättning består av en kombination av hanterade och ohanterade diskar?**

Nej. De virtuella datorerna i en tillgänglighetsuppsättning måste använda alla hanterade diskar eller alla ohanterade diskar. När du skapar en tillgänglighetsuppsättning, kan du välja vilken typ av diskar som du vill använda.

**Är Managed Disks standardalternativet i Azure-portalen?**

Ja.

**Kan jag skapa en tom hanterade disk?**

Ja. Du kan skapa en tom disk. En hanterad disk kan skapas oberoende av en virtuell dator, till exempel utan kopplar den till en virtuell dator.

**Vad stöds feldomänsantal för en tillgänglighet anges som använder Managed Disks?**

Beroende på den region där tillgänglighetsuppsättningen som använder Managed Disks finns, är antalet feldomäner som stöds 2 eller 3.

**Vad är standard storage-konto om du ställer in diagnostik?**

Du kan skapa ett privat lagringskonto för diagnostik för virtuella datorer.

**Vilken typ av stöd för rollbaserad åtkomstkontroll är tillgängligt för Managed Disks?**

Hanterade diskar stöder tre viktiga standardroller:

* Ägare: Kan hantera allt, inklusive åtkomst
* Deltagare: Kan hantera allt förutom åtkomst
* Läsare: Kan visa allt, men det går inte att göra ändringar

**Finns det ett sätt att jag kan kopiera eller exportera en hanterad disk till en privat storage-konto?**

Du kan generera en skrivskyddad delad åtkomstsignatur (SAS) URI för den hantera disken och använda den för att kopiera innehållet till ett privat konto eller en lokal lagring. Du kan använda SAS-URI med Azure portal, Azure PowerShell, Azure CLI, eller [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Kan jag skapa en kopia av min hanterad disk?**

Kunder kan ta en ögonblicksbild av deras hanterade diskar och sedan använda ögonblicksbilden för att skapa en annan hanterad disk.

**Ohanterade diskar stöds fortfarande?**

Ja, både ohanterade och hanterade diskar stöds. Vi rekommenderar att du använder hanterade diskar för nya arbetsbelastningar och migrera dina aktuella arbetsbelastningar till hanterade diskar.

**Om jag skapar en 128 GB-disk och sedan öka storleken till 130 GiB, debiteras jag för nästa diskstorleken (256 GB)?**

Ja.

**Kan jag skapa lokalt redundant lagring, geo-redundant lagring och zonredundant lagring hanterade diskar?**

Azure Managed Disks stöder för närvarande endast lokalt redundant lagring hanterade diskar.

**Kan jag minska eller lågsäsong min hanterade diskar?**

Nej. Den här funktionen stöds inte för närvarande. 

**Kan jag dela ett lån på disken?**

Nej. Detta stöds inte för närvarande eftersom ett lån är att förhindra oavsiktlig borttagning när disken används.

**Kan jag ändra namnegenskapen datorn när en specialiserad (inte skapats med hjälp av verktyget eller generaliserad) operativsystemdisken används för att etablera en virtuell dator?**

Nej. Du kan inte uppdatera egenskapen name för datorn. Den nya virtuella datorn ärver den från överordnat virtuella datorn som används för att skapa operativsystemdisken. 

**Var hittar jag exempel Azure Resource Manager-mallar för att skapa virtuella datorer med hanterade diskar?**
* [Lista över mallar med Managed Disks](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Kan jag placera ohanterade och hanterade diskar på samma virtuella dator?**

Nej.

**När du skapar en disk från en blob finns det någon kontinuerligt befintlig relation med den källbloben?**

Nej, när den nya disken skapas det är en fullständig fristående kopia av blobben vid den tidpunkten och det finns ingen anslutning mellan de två. Om du vill, när du har skapat disken kan källbloben tas bort utan att påverka den nyligen skapade disken på något sätt.

**Kan jag byta namn på en hanterad eller ohanterad disk när den har skapats?**

För hanterade diskar du kan inte byta namn på dem. Du kan dock byta namn på en ohanterad disk så länge den inte är för närvarande ansluten till en virtuell Hårddisk eller virtuell dator.

**Kan jag använda GPT partitionering på en Disk i Azure?**

GPT partitionering kan användas på datadiskar, inte OS-diskar. OS-diskar måste använda partitionstypen MBR.

## <a name="standard-ssd-disks"></a>Standard SSD-diskar

**Vad är Azure Standard SSD-diskar?**
Standard SSD-diskar är standarddiskar som backas upp av solid-state media, optimerad som kostnadseffektiv lagring för arbetsbelastningar som behöver konsekvent prestanda på lägre nivåer av IOPS.

<a id="standard-ssds-azure-regions"></a>**Vilka är de regioner som stöds för närvarande för Standard SSD-diskar?**
Alla Azure-regioner har nu stöd för Standard SSD-diskar.

**Är Azure Backup tillgängligt när du använder Standard SSD-enheter?**
Ja, Azure Backup är nu tillgängligt.

**Hur gör jag för att skapa Standard SSD-diskar?**
Du kan skapa Standard SSD-diskar med hjälp av Azure Resource Manager-mallar, SDK, PowerShell eller CLI. Nedan visas de parametrar som behövs i Resource Manager-mallen för att skapa Standard SSD-diskar:

* *apiVersion* för Microsoft.Compute måste anges som `2018-04-01` (eller senare)
* Ange *managedDisk.storageAccountType* som `StandardSSD_LRS`

I följande exempel visas den *properties.storageProfile.osDisk* avsnittet för en virtuell dator som använder Standard SSD-diskar:

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

En fullständig mall exempel på hur du skapar en Standard SSD-disk med en mall finns i [skapa en virtuell dator från en Windows-avbildning med Standard SSD-Datadiskar](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Kan jag omvandla mitt befintliga diskar till Standard SSD?**
Ja, det kan du. Referera till [konvertera Azure managed disks-lagring från standard till premium, och vice versa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) för allmänna riktlinjer för att konvertera Managed Disks. Och Använd följande värde för att uppdatera disktypen till SSD som Standard.
-AccountType StandardSSD_LRS

**Vad är fördelen med att använda Standard SSD-diskar i stället för HDD?**
Standard SSD-diskar leverera bättre svarstid, konsekvens, tillgänglighet och tillförlitlighet jämfört med HDD-diskar. Arbetsbelastningar för program körs mycket smidigare på Standard SSD på grund av detta. Observera att Premium SSD-diskar är den rekommenderade lösningen för de flesta i/o-intensiva produktionsarbetsbelastningar. 

**Kan jag använda Standard SSD-enheter som ohanterade diskar?**
Standard SSD-diskar är Nej, endast tillgängliga som Managed Disks.

**Standard SSD-diskar som har stöd för ”instans VM SLA”?**
Standard SSD-enheter har Nej, inte instans VM SLA. Använd Premium SSD-diskar för enskild instans VM SLA.

## <a name="migrate-to-managed-disks"></a>Migrera till Managed Disks

**Finns det någon effekt av migrering på Managed Disks prestanda?**

Migrering innebär att flödet av Disk från en lagringsplats till en annan. Detta är orkestreras via bakgrund kopia av data som kan ta flera timmar att slutföra, vanligtvis mindre än 24 timmar beroende på mängden data på diskarna. Under den tiden kan ditt program högre än vanligt lässvarstid uppleva eftersom vissa läsning kan hämta omdirigeras till den ursprungliga platsen och kan ta längre tid att slutföra. Det finns ingen inverkan på skrivfördröjningen under denna period.  

**Vilka ändringar krävs i en befintlig Azure Backup service configuration före och efter migrering till Managed Disks?**

Inga ändringar krävs.

**Kommer min VM-säkerhetskopior som har skapats via Azure Backup-tjänsten före migreringen fortsätta att fungera?**

Ja, säkerhetskopieringen fungerar smidigt.

**Vilka ändringar krävs i en befintlig Azure diskar Encryption configuration före och efter migrering till Managed Disks?**

Inga ändringar krävs.

**Är automatisk migrering av en befintlig VM-skalningsuppsättning uppsättningar från ohanterade diskar till Managed Disks stöds?**

Nej. Du kan skapa en ny skalningsuppsättning med hanterade diskar med hjälp av avbildningen från din gamla skalningsuppsättning med ohanterade diskar.

**Kan jag skapa en hanterad Disk från en sida blob-ögonblicksbild vidtas innan du migrerar till Managed Disks?**

Nej. Du kan exportera en sida blob-ögonblicksbild som en sidblobb och sedan skapa en hanterad Disk från den exporterade sidblob.

**Kan jag växla över min lokala datorer som skyddas av Azure Site Recovery till en virtuell dator med Managed Disks?**

Ja, du kan välja att redundansväxla till en virtuell dator med Managed Disks.

**Finns det några konsekvenserna av migrering på Azure-datorer som skyddas av Azure Site Recovery via replikering från Azure till Azure?**

Ja. Azure Site Recovery Azure till Azure-skydd för virtuella datorer med Managed Disks är för närvarande bara tillgängligt som en tjänst i offentlig förhandsversion.

**Kan jag migrera virtuella datorer med ohanterade diskar som finns på storage-konton som eller krypterats till managed disks?**

Ja

## <a name="managed-disks-and-storage-service-encryption"></a>Hanterade diskar och kryptering av lagringstjänst

**Är Azure Storage Service Encryption aktiverat som standard när jag skapar en hanterad disk**

Ja.

**Vem som hanterar krypteringsnycklarna?**

Microsoft hanterar krypteringsnycklarna.

**Kan jag inaktivera kryptering av lagringstjänst för min hanterade diskar?**

Nej.

**Kryptering av lagringstjänst endast är tillgänglig i vissa regioner?**

Nej. Den är tillgänglig i alla regioner där hanterade diskar är tillgängliga. Hanterade diskar är tillgängligt i alla offentliga regioner och Tyskland. Det är också tillgängligt i Kina, men endast för Microsoft-inte hanterade nycklar, Kundhanterade nycklar.

**Hur kan jag ta reda om min hanterad disk krypteras?**

Du hittar den tidpunkt då en hanterad disk skapades från Azure-portalen, Azure CLI och PowerShell. Om tiden efter den 9 juni 2017 krypteras disken.

**Hur kan jag kryptera min befintliga diskar som har skapats före 10 juni 2017?**

Från och med den 10 juni 2017 krypteras automatiskt nya data som skrivs till befintliga hanterade diskar. Planerar vi också att kryptera befintliga data och kryptering ska ske asynkront i bakgrunden. Om du måste kryptera befintliga data nu kan du skapa en kopia av disken. Nya diskar krypteras.

* [Kopiera hanterade diskar med hjälp av Azure CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopiera hanterade diskar med hjälp av PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Är hanterade ögonblicksbilder och avbildningar krypteras?**

Ja. Alla hanterade ögonblicksbilder och avbildningar som skapats efter den 9 juni 2017 krypteras automatiskt. 

**Kan jag omvandla virtuella datorer med ohanterade diskar som finns på storage-konton som eller krypterats till managed disks?**

Ja

**En exporterad virtuell Hårddisk från en hanterad disk eller en ögonblicksbild även krypteras?**

Nej. Men om du exporterar en virtuell Hårddisk till ett krypterat lagringskonto från ett krypterat managed disk eller ögonblicksbild och det är krypterat. 

## <a name="premium-disks-managed-and-unmanaged"></a>Premium-diskar: Hanterade och ohanterade

**Om en virtuell dator använder en serie med storlek som har stöd för Premium SSD-diskar, till exempel en DSv2 kan jag koppla premium och standard datadiskar?** 

Ja.

**Kan jag koppla premium och standard datadiskar till en serie med storlek som inte stöder Premium SSD-diskar, till exempel D, Dv2, G eller F-serien?**

Nej. Du kan koppla endast standard datadiskar till virtuella datorer som inte använder en serie med storlek som har stöd för Premium SSD-diskar.

**Om jag skapar en premiumdisk data från en befintlig virtuell Hårddisk som var 80 GB, hur mycket som kostar?**

En premiumdisk för data som skapats från en virtuell Hårddisk på 80 GB behandlas som nästa tillgängliga premium-diskstorleken, vilket är en P10-disk. Du debiteras enligt P10-disk prissättningen.

**Finns det transaktionskostnader kan använda Premium SSD-diskar?**

Det finns en fast kostnad för varje diskstorlek som är etablerade med specifika gränser på IOPS och dataflöden. Övriga kostnader är utgående bandbredd och kapacitet för ögonblicksbilder, om tillämpligt. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Vilka är begränsningarna för IOPS och dataflöde som jag kan få från diskcache?**

Den kombinerade gränserna för cache och lokal SSD för DS-serien är 4 000 IOPS per kärna och 33 MiB per sekund per kärna. GS-serien erbjuder 5 000 IOPS per kärna och 50 MiB per sekund per kärna.

**Lokal SSD som stöds för en hanterad diskar i virtuell dator?**

Lokal SSD är tillfälligt lagringsutrymme som ingår i en hanterad diskar i virtuell dator. Det är utan extra kostnad för den här tillfälliga lagringen. Vi rekommenderar att du inte använder den här lokal SSD-lagring för att lagra programdata eftersom det inte är beständiga i Azure Blob storage.

**Finns det några konsekvenser för användning av TRIMNING på premium-diskar?**

Det finns inga Nackdelen med att användningen av TRIMNING på Azure-diskar på antingen premium eller standard-diskar.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nya diskstorlekar: Hanterade och ohanterade

**Vad är den största Managed diskstorlek som stöds för operativsystem och datadiskar?**

Partitionstypen som stöds av Azure för en operativsystemdisk är master boot record (MBR). MBR-formatet stöder en diskstorlek upp till 2 TiB. Den största storlek som stöds av Azure för en operativsystemdisk är 2 TiB. Azure stöder upp till 32 TiB för hanterade diskar. Hanterade diskar som är större än 4 TiB finns i förhandsversion. Mer information om dem finns i vår [blogginlägget](https://aka.ms/azure-large-disk-32TB-preview-blog).

**Vad är den största ohanterad Disk-storlek som stöds för operativsystem och datadiskar?**

Partitionstypen som stöds av Azure för en operativsystemdisk är master boot record (MBR). MBR-formatet stöder en diskstorlek upp till 2 TiB. Den största storlek som stöds av Azure för en ohanterad disk operativsystemet är 2 TiB. Azure har stöd för upp till 4 TiB för ohanterade datadiskar.

**Vad är den största sidblobens storlek som stöds?**

Största sidblobens storlek som stöds av Azure är 8 TiB (8191 GiB). Maximal sidblobens storlek när ansluten till en virtuell dator som data eller operativsystemdiskar är 4 TiB (4095 GiB).

**Måste jag använda en ny version av Azure-verktyg för att skapa, ansluta, ändra storlek på och ladda upp diskar som är större än 1 TiB?**

Du behöver inte uppgradera din befintliga Azure-verktyg för att skapa, koppla eller ändra storlek på diskar som är större än 1 TiB. Du måste använda de senaste verktygsuppsättningar som anges nedan för att ladda upp VHD-fil från en lokal plats direkt till Azure som en sidblobb eller ohanterad disk. Vi har endast stöd för VHD-uppladdningar av upp till 8 TiB.

|Azure-verktyg      | Versioner som stöds                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versionsnummer 4.1.0: Juni 2017-versionen eller senare|
|Azure CLI v1     | Versionsnummer 0.10.13: Maj 2017-versionen eller senare|
|Azure CLI v2     | Versionsnummer 2.0.12: Versionen för juli 2017 eller senare|
|AzCopy           | Versionsnummer 6.1.0: Juni 2017-versionen eller senare|

**Stöds P4 och P6 diskstorlekar för ohanterade diskar och sidblobar?**

P4 (32 GiB) och P6 (64 GiB) diskstorlekar stöds inte som standard disk nivåer för ohanterade diskar och sidblobar. Du måste uttryckligen [ange Blob-nivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) P4 och P6 ha disken mappas till dessa nivåer. Om du distribuerar en ohanterad disk- eller blob med diskens storlek eller innehållslängd mindre än 32 GiB eller mellan 32 GiB till 64 GiB utan att ange Blob-nivå, fortsätter du hamnar på P10 med 500 IOPS och 100 MiB/s och den mappade prisnivån.

**Om min befintliga premium managed disk mindre än 64 GiB skapades innan liten disk aktiverades (cirka 15 juni 2017), hur faktureras den?**

Befintliga små premium-diskar mindre än 64 GiB fortsättningsvis att debiteras enligt P10 prisnivån.

**Hur kan jag byta disk-nivå för små premium disks högst 64 GiB från P10 P4 eller P6?**

Du kan ta en ögonblicksbild av dina små diskar och sedan skapa en disk för att automatiskt växla prisnivån till P4 eller P6 baserat på den valda storleken.

**Kan du ändra storlek på befintliga hanterade diskar från storlekar mindre än 4 TiB till nya Nyintroducerade diskstorlekar upp till 32 TiB?**

Nya hanterade diskar som är 8 TiB och 16 TiB 32 TiB finns för närvarande i förhandsversion. Vi ännu stöd inte för storleksändring befintliga diskstorlekar till den nya diskstorleken.

**Vad är den största diskstorleken som stöds av Azure Backup och Azure Site Recovery-tjänsten?**

Den största diskstorleken som stöds av Azure Backup och Azure Site Recovery-tjänsten är 4 TiB.

**Vad är den rekommenderade VM-storlekar för stora diskar (> 4TiB) för Standard SSD och HDD-Standard-diskar för att uppnå optimerade disk-IOPS och bandbredd?**

Att uppnå diskgenomflöde i Standard SSD och HDD-Standard storlekar för stora diskar (> 4TB) utöver 500 IOPS och 60 MiB/s, bör du använda en av de följande storlekarna optimerade prestanda: B-serien, DSv2-serien, Dsv3-serien, ESv3-serien, Fs-serien, Fsv2-serien, M-serien GS-serien, NCv2-serien, NCv3-serien och virtuella datorer i Ls-serien.

**Vilka regioner är de hanterade diskar som är större än 4 TiB stöds i?**

För närvarande i förhandsversionen av stöds den hantera diskstorleken i västra USA, Central endast.

**Vi har stöd för att aktivera cachelagring av värden på den nya diskstorleken?**

Vi stöder värd cachelagring av ReadOnly och Läs/Skriv diskar som är mindre än 4TiB. För diskstorlekar fler än 4 TiB vi stöder inte att ange alternativet än None för cachelagring. Vi rekommenderar att utnyttja cachelagring för mindre diskstorlekar där du kan förvänta dig att Observera bättre prestandaökning med data cachelagras till den virtuella datorn.

## <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?

Om din fråga inte visas kan för oss berätta och vi hjälper dig att hitta något svar. Du kan ställa en fråga i slutet av den här artikeln i kommentarerna. Använd MSDN för att interagera med Azure Storage-teamet och andra community-medlemmar om den här artikeln, [Azure Storage-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Skicka dina förfrågningar och idéer om du vill begära funktioner i [Azure Storage-Feedbackforum](https://feedback.azure.com/forums/217298-storage).

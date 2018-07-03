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
ms.openlocfilehash: 66964e4ed0877cc47dd7d2b5f3c6a62f4fa006b1
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37348192"
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

Du kan generera en skrivskyddad delad åtkomstsignatur (SAS) URI för den hantera disken och använda den för att kopiera innehållet till ett privat konto eller en lokal lagring. Du kan använda SAS-URI med hjälp av Azure portal, Azure PowerShell, Azure CLI eller [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Kan jag skapa en kopia av min hanterad disk?**

Kunder kan ta en ögonblicksbild av deras hanterade diskar och sedan använda ögonblicksbilden för att skapa en annan hanterad disk.

**Ohanterade diskar stöds fortfarande?**

Ja, både ohanterade och hanterade diskar stöds. Vi rekommenderar att du använder hanterade diskar för nya arbetsbelastningar och migrera dina aktuella arbetsbelastningar till hanterade diskar.


**Om jag skapar en 128 GB-disk och sedan öka storleken till 130 GB, debiteras jag för nästa diskstorleken (256 GB)?**

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

## <a name="standard-ssd-disks-preview"></a>Standard SSD-diskar (förhandsversion)

**Vad är Azure Standard SSD-diskar?**
Standard SSD-diskar är standarddiskar som backas upp av solid-state media, optimerad som kostnadseffektiv lagring för arbetsbelastningar som behöver konsekvent prestanda på lägre nivåer av IOPS. I förhandsversion är de tillgängliga i ett begränsat antal regioner med begränsad hantering (tillgängligt via Resource Manager-mallar).

<a id="standard-ssds-azure-regions"></a>**Vilka är de regioner som stöds för närvarande för Standard SSD-diskar (förhandsversion)?**
* Norra Europa
* Frankrike, centrala
* Östra USA 2
* Centrala USA
* Centrala Kanada
* Östasien
* Sydkorea
* Östra Australien

**Hur gör jag för att skapa Standard SSD-diskar?**
För närvarande kan du skapa Standard SSD-diskar med hjälp av Azure Resource Manager-mallar. Nedan visas de parametrar som behövs i Resource Manager-mallen för att skapa Standard SSD-diskar:

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
Ja, det kan du. Referera till [konvertera Azure managed disks-lagring från standard till premium, och vice versa](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/convert-disk-storage) för allmänna riktlinjer för att konvertera Managed Disks. Och Använd följande värde för att uppdatera disktypen till SSD som Standard.
-AccountType StandardSSD_LRS

**Kan jag använda Standard SSD-enheter som ohanterade diskar?**
Standard SSD-diskar är Nej, endast tillgängliga som Managed Disks.

**Standard SSD-diskar som har stöd för ”instans VM SLA”?**
Standard SSD-enheter har Nej, inte instans VM SLA. Använd Premium SSD-diskar för enskild instans VM SLA.

## <a name="migrate-to-managed-disks"></a>Migrera till Managed Disks 

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

## <a name="premium-disks-managed-and-unmanaged"></a>Premium-diskar: hanterade och ohanterade

**Om en virtuell dator använder en serie med storlek som har stöd för Premium SSD-diskar, till exempel en DSv2 kan jag koppla premium och standard datadiskar?** 

Ja.

**Kan jag koppla premium och standard datadiskar till en serie med storlek som inte stöder Premium SSD-diskar, till exempel D, Dv2, G eller F-serien?**

Nej. Du kan koppla endast standard datadiskar till virtuella datorer som inte använder en serie med storlek som har stöd för Premium SSD-diskar.

**Om jag skapar en premiumdisk data från en befintlig virtuell Hårddisk som var 80 GB, hur mycket som kostar?**

En premiumdisk för data som skapats från en virtuell Hårddisk på 80 GB behandlas som nästa tillgängliga premium-diskstorleken, vilket är en P10-disk. Du debiteras enligt P10-disk prissättningen.

**Finns det transaktionskostnader kan använda Premium SSD-diskar?**

Det finns en fast kostnad för varje diskstorlek som är etablerade med specifika gränser på IOPS och dataflöden. Övriga kostnader är utgående bandbredd och kapacitet för ögonblicksbilder, om tillämpligt. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Vilka är begränsningarna för IOPS och dataflöde som jag kan få från diskcache?**

Den kombinerade gränserna för cache och lokal SSD för DS-serien är 4 000 IOPS per kärna och 33 MB per sekund per kärna. GS-serien erbjuder 5 000 IOPS per kärna och 50 MB per sekund per kärna.

**Lokal SSD som stöds för en hanterad diskar i virtuell dator?**

Lokal SSD är tillfälligt lagringsutrymme som ingår i en hanterad diskar i virtuell dator. Det är utan extra kostnad för den här tillfälliga lagringen. Vi rekommenderar att du inte använder den här lokal SSD-lagring för att lagra programdata eftersom det inte är beständiga i Azure Blob storage.

**Finns det några konsekvenser för användning av TRIMNING på premium-diskar?**

Det finns inga Nackdelen med att användningen av TRIMNING på Azure-diskar på antingen premium eller standard-diskar.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nya diskstorlekar: hanterade och ohanterade

**Vad är den största diskstorleken användas för operativsystemet och datadiskarna?**

Partitionstypen som stöds av Azure för en operativsystemdisk är master boot record (MBR). MBR-formatet stöder en disk ändra storlek på upp till 2 TB. Den största storlek som stöds av Azure för en operativsystemdisk är 2 TB. Azure har stöd för upp till 4 TB för datadiskar. 

**Vad är den största sidblobens storlek som stöds?**

Största sidblobens storlek som stöds av Azure är 8 TB (8191 GB). Maxmium blogg sidstorleken när ansluten till en virtuell dator som data eller operativsystemdiskar är 4 TB (4095 GB).

**Måste jag använda en ny version av Azure-verktyg för att skapa, ansluta, ändra storlek på och ladda upp diskar som är större än 1 TB?**

Du behöver inte uppgradera din befintliga Azure-verktyg för att skapa, koppla eller ändra storlek på diskar som är större än 1 TB. Om du vill överföra VHD-fil från en lokal plats direkt till Azure som en sidblobb eller ohanterad disk måste du använda senaste verktyget:

|Azure-verktyg      | Versioner som stöds                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versionsnumret 4.1.0: juni 2017-versionen eller senare|
|Azure CLI v1     | Versionsnumret 0.10.13: maj 2017-versionen eller senare|
|AzCopy           | Versionsnumret 6.1.0: juni 2017-versionen eller senare|

Stöd för Azure CLI v2 och Azure Storage Explorer kommer snart. 

**Stöds P4 och P6 diskstorlekar för ohanterade diskar och sidblobar?**

Nej. P4 (32 GB) och P6 (64 GB) diskstorlekar stöds endast för hanterade diskar. Stöd för ohanterade diskar och sidblobar kommer snart.

**Om min befintliga premium managed disk mindre än 64 GB skapades innan liten disk aktiverades (cirka 15 juni 2017), hur faktureras den?**

Befintliga små premium-diskar mindre än 64 GB fortsättningsvis att debiteras enligt P10 prisnivån. 

**Hur kan jag byta disk-nivå för små premium-diskar som är mindre än 64 GB från P10 P4 eller P6?**

Du kan ta en ögonblicksbild av dina små diskar och sedan skapa en disk för att automatiskt växla prisnivån till P4 eller P6 baserat på den valda storleken. 


## <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?

Om din fråga inte visas kan för oss berätta och vi hjälper dig att hitta något svar. Du kan ställa en fråga i slutet av den här artikeln i kommentarerna. Använd MSDN för att interagera med Azure Storage-teamet och andra community-medlemmar om den här artikeln, [Azure Storage-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Skicka dina förfrågningar och idéer om du vill begära funktioner i [Azure Storage-Feedbackforum](https://feedback.azure.com/forums/217298-storage).

# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Vanliga frågor och svar om Azure IaaS-VM och hanterade och ohanterade premiumdiskar

Den här artikeln besvarar några vanliga frågor om Azure hanterade diskar och Azure Premium-lagring.

## <a name="managed-disks"></a>Managed Disks

**Vad är Azure hanterade diskar?**

Hanterade diskar är en funktion som förenklar Diskhantering för virtuella Azure IaaS-datorer genom att hantera lagringshantering konto för dig. Mer information finns i [översikt för hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md).

**Om jag skapa en standard hanterade diskar från en befintlig virtuell Hårddisk som är 80 GB, hur mycket som kostar mig?**

En standard hanterade diskar som skapats från en virtuell Hårddisk på 80 GB behandlas som nästa tillgängliga standard diskens storlek, vilket är en S10 disk. Du är debiteras enligt S10 disk priser. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Finns det några transaktionskostnader för hanterade standarddiskar?**

Ja. Du är debiteras för varje transaktion. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**För en standard hanterade disk jag debiteras för den verkliga storleken hos data på disken eller diskens etablerad kapacitet?**

Du är debiteras baserat på diskens etablerad kapacitet. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Hur är prissättningen av hanterade premiumdiskar skiljer sig från ohanterade diskar?**

Priser för premiumdiskar hanteras är samma som ohanterad premiumdiskar.

**Kan jag ändra lagringskontotypen (Standard eller Premium) av min hanterade diskar?**

Ja. Du kan ändra lagringskontotypen hanterade diskar med hjälp av Azure portal, PowerShell eller Azure CLI.

**Finns det ett sätt att jag kan kopiera eller exportera hanterade diskar till ett privat storage-konto?**

Ja. Du kan exportera dina hanterade diskar med hjälp av Azure portal, PowerShell eller Azure CLI.

**Kan jag använda en VHD-fil i ett Azure storage-konto för att skapa en hanterad disk med en annan prenumeration?**

Nej.

**Kan jag använda en VHD-fil i ett Azure storage-konto för att skapa en hanterad disk i en annan region?**

Nej.

**Finns det några begränsningar för skalan för kunder som använder hanterade diskar?**

Hanterade diskar eliminerar de gränser som är kopplade till storage-konton. Antalet hanterade diskar per prenumeration är dock begränsad till 2 000 som standard. Du kan kontakta support om du vill öka antalet.

**Kan jag göra en inkrementell ögonblicksbild av hanterade diskar?**

Nej. Den aktuella kapaciteten för ögonblicksbild gör en fullständig kopia av hanterade diskar. Men planerar vi att stödja inkrementell ögonblicksbilder i framtiden.

**Virtuella datorer i en tillgänglighetsuppsättning kan bestå av en kombination av hanterade och ohanterade diskar?**

Nej. De virtuella datorerna i en tillgänglighetsuppsättning måste använda alla hanterade diskar eller ohanterad diskarna. När du skapar en tillgänglighetsuppsättning, kan du välja vilken typ av diskar som du vill använda.

**Är standardalternativet i Azure-portalen för hanterade diskar?**

Ja. 

**Kan jag skapa en tom disk som hanterade?**

Ja. Du kan skapa en tom disk. Hanterade diskar kan skapas oberoende av en virtuell dator, till exempel utan kopplar den till en virtuell dator.

**Vad stöds feldomänsantalet för tillgänglighet anges som använder hanterade diskar?**

Beroende på den region där den tillgänglighetsuppsättningen som använder hanterade diskar finns, är stöds feldomänsantalet 2 eller 3.

**Hur är standard lagringskontot för diagnostik konfigurera?**

Du kan konfigurera ett konto för privat lagring för diagnostik för Virtuella datorer. I framtiden kommer planerar vi att växla diagnostik samt till hanterade diskar.

**Vilken typ av rollbaserad åtkomstkontroll support är tillgänglig för hanterade diskar?**

Hanterade diskar stöder tre viktiga standardroller:

* Ägare: Kan hantera allt, inklusive åtkomst
* Deltagare: Kan hantera allt utom åtkomst
* Läsare: Kan visa allt, men det går inte att göra ändringar

**Finns det ett sätt att jag kan kopiera eller exportera hanterade diskar till ett privat storage-konto?**

Du kan hämta en skrivskyddad delad åtkomstsignatur URI för hanterade diskar och använda den för att kopiera innehållet till en privat lagring konto eller lokal lagring.

**Kan jag skapa en kopia av min hanterade diskar?**

Kunder kan ta en ögonblicksbild av deras hanterade diskar och sedan använda ögonblicksbilden för att skapa en annan hanterade diskar.

**Ohanterad diskar stöds fortfarande?**

Ja. Vi stöder ohanterade och hanterade diskar. Vi rekommenderar att du använder hanterade diskar för nya arbetsbelastningar och migrera dina aktuella arbetsbelastningar till hanterade diskar.


**Om jag skapa en 128 GB disk och sedan öka storleken till 130 GB jag debiteras för nästa diskens storlek (512 GB)?**

Ja.

**Kan jag skapa lokalt redundant lagring, geo-redundant lagring och zonredundant lagring hanteras diskar?**

Azure-hanterade diskar stöder för närvarande endast lokalt redundant lagring hanterade diskar.

**Kan jag krympa eller downsize min hanterade diskar?**

Nej. Den här funktionen stöds inte för närvarande. 

**Kan jag ändra egenskapen name för datorn när en särskild (inte skapats med hjälp av systemförberedelseverktyget eller generaliserad) operativsystemdisk används för att etablera en virtuell dator?**

Nej. Du kan inte uppdatera egenskapen name för datorn. Den nya virtuella datorn ärver den från överordnat virtuella datorn som användes för att skapa operativsystemets disk. 

**Var hittar jag exempel Azure Resource Manager-mallar för att skapa virtuella datorer med hanterade diskar?**
* [Lista över mallar med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

## <a name="migrate-to-managed-disks"></a>Migrera till Managed Disks 

**Vilka ändringar som krävs i en befintlig Azure Backup service configuration före/efter migrering till hanterade diskar?**

Inga ändringar krävs. 

**Min VM-säkerhetskopior som har skapats via Azure Backup-tjänsten före migreringen kommer fortsätta att fungera?**

Ja, säkerhetskopieringen fungerar sömlöst.

**Vilka ändringar som krävs i en befintlig Azure-diskar Encryption configuration före/efter migrering till hanterade diskar?**

Inga ändringar krävs. 

**Är automatisk migrering av en befintlig VM Scale uppsättningar (VMSS) från ohanterade diskar till hanterade diskar stöds?**

Nej. Du kan skapa en ny VMSS med hanterade diskar med hjälp av bilden från ditt gamla VMSS med ohanterad diskar. 

**Kan jag skapa en Disk som hanteras från en sida blob ögonblicksbild som togs innan du migrerar till hanterade diskar?**

Nej. Du kan exportera en ögonblicksbild av sidan blob som en sidblobb och sedan skapa en Disk som hanteras från den exporterade sidblob. 

**Kan jag växla över min lokala datorer som skyddas av Azure Site Recovery till en virtuell dator med hanterade diskar?**

Ja, du kan välja att gå över till en virtuell dator med hanterade diskar.

**Finns det någon effekt av migrering på virtuella Azure-datorer skyddas av Azure Site Recovery (ASR) via Azure Azure replikering?**

Ja. ASR Azure till Azure-skydd stöds inte för virtuella datorer med hanterade diskar. Den kommer att stödjas i slutet av CY2018. 

**Kan jag migrera virtuella datorer med ohanterad diskar som finns på lagringskonton som är eller krypterats till hanterade diskar?**

Ja

## <a name="managed-disks-and-storage-service-encryption"></a>Hanterade diskar och Storage Service-kryptering 

**Är Azure Storage Service-kryptering aktiverat som standard när jag skapar hanterade diskar?**

Ja.

**Som hanterar krypteringsnycklarna?**

Microsoft hanterar krypteringsnycklarna.

**Kan jag inaktivera Storage Service-kryptering för min hanterade diskar?**

Nej.

**Är Lagringstjänstens kryptering endast tillgänglig i vissa områden?**

Nej. Den är tillgänglig i alla regioner där hanterade diskar är tillgänglig. Hanterade diskar är tillgänglig i alla offentliga regioner och Tyskland.

**Hur kan jag ta reda om hanterade disken krypteras?**

Du hittar den tid då en hanterad disk skapades från Azure-portalen, Azure CLI och PowerShell. Om tiden efter den 9 juni 2017 krypteras disken. 

**Hur kan jag kryptera Mina befintliga diskar som skapades före 10 juni 2017?**

Från och med 10 juni 2017 krypteras automatiskt nya data skrivs till befintliga hanterade diskar. Vi också planerar att kryptera befintliga data och kryptering sker asynkront i bakgrunden. Om du måste nu krypterar befintliga data måste du skapa en kopia av disken. Nya diskar krypteras.

* [Kopiera hanterade diskar med hjälp av Azure CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopiera hanterade diskar med hjälp av PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Är hanterad ögonblicksbilder och bilder som krypteras?**

Ja. Alla hanterade ögonblicksbilder och bilder som skapats efter den 9 juni 2017 krypteras automatiskt. 

**Kan jag konvertera virtuella datorer med ohanterad diskar som finns på lagringskonton som är eller krypterats till hanterade diskar?**

Ja

**En exporterad VHD från en hanterad disk eller en ögonblicksbild även krypteras?**

Nej. Men om du exporterar en virtuell Hårddisk till en krypterad storage-konto från ett krypterat hanteras disk eller en ögonblicksbild och är krypterad. 

## <a name="premium-disks-managed-and-unmanaged"></a>Premiumdiskar: hanterade och ohanterade

**Om en virtuell dator använder en serie med storlek som har stöd för Premium-lagring, till exempel en DSv2 kan jag koppla premium- och datadiskar som standard?** 

Ja.

**Kan jag koppla premium- och datadiskar som standard till en rad med storleken som inte stöder Premium-lagring, till exempel D, Dv2, G eller F serien?**

Nej. Du kan koppla endast standard datadiskar till virtuella datorer som inte använder en serie med storlek som har stöd för Premium-lagring.

**Om jag skapa en disk för premium-data från en befintlig virtuell Hårddisk som är 80 GB, hur mycket som kostar?**

En disk för premium-data som skapas från en virtuell Hårddisk på 80 GB behandlas som nästa tillgängliga premium diskens storlek är en P10 disk. Du är debiteras enligt P10 disk priser.

**Finns det transaktionskostnader använda Premium Storage?**

Det finns en fast kostnad för varje diskstorlek som hämtas etablerade med specifika gränser på IOPS och genomflöde. Övriga kostnader är utgående bandbredd och ögonblicksbild kapacitet, om tillämpligt. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage).

**Vad är gränser för IOPS och dataflöde som jag kan från diskcache?**

De kombinerade gränsvärdena för cache och lokala SSD för DS-serien är 4 000 IOPS per kärna och 33 MB per sekund per kärna. GS-serien ger 5 000 IOPS per kärna och 50 MB per sekund per kärna.

**Stöds lokal SSD för en virtuell för hanterade diskar?**

Lokal SSD är tillfälligt lagringsutrymme som ingår i en hanterad diskar i virtuell dator. Det finns inget extra kostnad för den här tillfällig lagring. Vi rekommenderar att du inte använder den här lokala SSD för att lagra dina programdata eftersom det inte finns kvar i Azure Blob storage.

**Finns det några konsekvenser för användning av TRIMNING på premiumdiskar?**

Det finns inga Nackdelen med att användningen av TRIMNING på Azure-diskar på antingen premium eller standarddiskar.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nya diskstorlekar: hanterade och ohanterade

**Vad är den största diskstorleken användas för operativsystemet och datadiskarna?**

Den partitionstypen som Azure har stöd för en operativsystemdisk är master boot record (MBR). MBR-formatet stöder en disk ändra storlek på upp till 2 TB. Den största storlek som Azure har stöd för en operativsystemdisk är 2 TB. Azure har stöd för upp till 4 TB för datadiskar. 

**Vad är den största blob sidstorlek som stöds?**

Den största blob sidstorlek som har stöd för Azure är 8 TB (8191 GB). Vi stöder inte sidblobbar som är större än 4 TB (4,095 GB) ansluten till en virtuell dator som data eller operativsystemet diskar.

**Måste jag använda en ny version av Azure-verktyg för att skapa, bifoga, ändra storlek och överför diskar som är större än 1 TB?**

Du behöver inte uppgradera din befintliga Azure-verktyg för att skapa, koppla eller ändra storlek på diskar som är större än 1 TB. Om du vill överföra VHD-filen från lokala direkt till Azure som en sidblob eller ohanterad disk måste du använda senaste verktyget:

|Azure-verktyg      | Versioner som stöds                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versionsnumret 4.1.0: juni 2017 släpper eller senare|
|Azure CLI v1     | Versionsnumret 0.10.13: släpp kan 2017 eller senare|
|AzCopy           | Versionsnumret 6.1.0: juni 2017 släpper eller senare|

Stöd för Azure CLI v2 och Azure Lagringsutforskaren kommer snart. 

**Stöds P4 och P6 diskstorlekar för ohanterade diskar eller sidblobbar?**

Nej. P4 (32 GB) och P6 diskstorlekar (64 GB) stöds endast för hanterade diskar. Stöd för ohanterade diskar och sidblobbar kommer snart.

**Om min befintliga premium hanteras disk mindre än 64 GB skapades innan liten disk har aktiverats (runt den 15 juni 2017), hur den faktureras?**

Befintliga små premium diskar mindre än 64 GB fortsätta debiteras enligt P10 prisnivå. 

**Hur kan jag byta disk nivån av små premiumdiskar som är mindre än 64 GB från P10 P4 eller P6?**

Du kan ta en ögonblicksbild av små diskar och sedan skapa en disk för att automatiskt växla prisnivån till P4 eller P6 baserat på etablerade storlek. 


## <a name="what-if-my-question-isnt-answered-here"></a>Vad gör jag om min fråga inte besvaras här?

Om din fråga inte finns med här kan för oss berätta och vi hjälper dig att hitta ett svar. Du kan ställa en fråga i slutet av den här artikeln i kommentarerna. Om du vill interagera med Azure Storage-teamet och andra gruppmedlemmar om den här artikeln använder MSDN [Azure Storage-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Skicka din begäran och idéer om du vill begära funktioner i [Azure Storage Feedbackforum](https://feedback.azure.com/forums/217298-storage).

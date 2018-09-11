---
title: Azure Compute - Linux-Diagnostiktillägg | Microsoft Docs
description: Så här konfigurerar du den Azure Linux diagnostiska tillägget (LAD) för att samla in mått och loggar händelser från virtuella Linux-datorer som körs i Azure.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: agaiha
ms.openlocfilehash: a51e4e61b8d0a9f9a80acc513dbba32c74372f5e
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348862"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Använda Linux-Diagnostiktillägget för att övervaka mått och loggar

Det här dokumentet beskrivs version 3.0 och senare av Linux-Diagnostiktillägget.

> [!IMPORTANT]
> Läs om hur version 2.3 och äldre [det här dokumentet](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introduktion

Linux-Diagnostiktillägget kan en användare övervaka hälsotillståndet för en Linux VM som körs på Microsoft Azure. Den har följande funktioner:

* Samlar in prestanda systemmått från den virtuella datorn och lagrar dem i en viss tabell i ett avsedda storage-konto.
* Hämtar händelser från syslog och lagrar dem i en viss tabell i det angivna lagringskontot.
* Kan du anpassa mätvärden för data som samlas in och laddat upp.
* Kan du anpassa syslog anläggningar och allvarlighetsgrader för händelser som samlas in och laddat upp.
* Gör att användarna kan ladda upp angivna loggfiler till en avsedda lagringstabell.
* Har stöd för mått och loggfiler händelser skickades till valfri EventHub-slutpunkter och JSON-formaterade blobar i det angivna lagringskontot.

Det här tillägget fungerar med båda modellerna för Azure-distribution.

## <a name="installing-the-extension-in-your-vm"></a>Installera tillägget på den virtuella datorn

Du kan aktivera det här tillägget med hjälp av Azure PowerShell-cmdletar, Azure CLI-skript eller mallar för Azure-distribution. Mer information finns i [tillägg funktioner](features-linux.md).

Azure-portalen kan inte användas för att aktivera eller konfigurera LAD 3.0. Istället installerar och konfigurerar den version 2.3. Azure portal diagram och aviseringar kan du arbeta med data från båda versioner av tillägget.

Dessa instruktioner för installation och en [nedladdningsbara exempelkonfiguration](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurera LAD 3.0 som:

* avbilda och lagra samma mått som har angetts av LAD 2.3;
* avbilda en användbar uppsättning filen systemmått, använt LAD 3.0;
* samla in syslog standardmängden aktiveras med LAD 2.3;
* Aktivera Azure-portalen tidigare för diagram och aviseringar på VM-mått.

Nedladdningsbara konfigurationen är bara ett exempel. Anpassa efter dina egna behov.

### <a name="prerequisites"></a>Förutsättningar

* **Azure Linux-Agent version 2.2.0 eller senare**. De flesta Azure VM Linux galleriavbildningar innehåller versionen 2.2.7 eller senare. Kör `/usr/sbin/waagent -version` att bekräfta versionen som installerats på den virtuella datorn. Om den virtuella datorn kör en äldre version av gästagenten, Följ [instruktionerna](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) att uppdatera den.
* **Azure CLI**. [Konfigurera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) miljö på din dator.
* Wget-kommandot, om du inte redan har det.: kör `sudo apt-get install wget`.
* En befintlig prenumeration och ett befintligt lagringskonto i den för att lagra data.
* Lista med Linux-distributioner som stöds finns på https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Exempel-installation

Fyll i rätt parametrar på de tre första raderna, och sedan köra det här skriptet som rot:

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

URL-Adressen för exempelkonfiguration och dess innehåll kan ändras. Hämta en kopia av portalinställningar JSON-fil och anpassa den efter dina behov. Alla mallar eller automation som du bör använda din egen kopia i stället för att hämta URL: en varje gång.

### <a name="updating-the-extension-settings"></a>Uppdaterar inställningarna för tillägg

När du har ändrat skyddade eller offentliga inställningar, distribuera du dem till den virtuella datorn genom att köra samma kommando. Om något ändras i inställningarna för skickas de uppdaterade inställningarna till tillägget. LAD läser konfigurationen och startar om sig själv.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrera från tidigare versioner av tillägget

Den senaste versionen av tillägget är **3.0**. **Eventuella gamla versioner (2.x) är föråldrade och kan vara opublicerad på eller efter den 31 juli 2018**.

> [!IMPORTANT]
> Det här tillägget introducerar ändringar i konfigurationen av tillägget. En sådan ändring har gjorts för att förbättra säkerheten för tillägget; Därför bakåtkompatibilitet kompatibilitet med 2.x gick inte att finnas kvar. Tilläggsutgivare för detta tillägg är också skiljer sig från utgivaren för 2.x-versioner.
>
> Om du vill migrera från 2.x till den nya versionen av tillägget måste du avinstallera det gamla tillägget (under gamla utgivarens namn) och sedan installera version 3 av tillägget.

Rekommendationer:

* Installera tillägget med automatisk mindre versionsuppgradering aktiverat.
  * På klassiska distributionsmodellen virtuella datorer, anger du ”3.*” som versionen om du installerar tillägget via Azure XPLAT CLI eller Powershell.
  * Modellera virtuella datorer på Azure Resource Manager-distribution, inkludera ' ”autoUpgradeMinorVersion”: true' i mallen för distribution av virtuell dator.
* Använd en ny/annan lagringskonto för LAD 3.0. Det finns flera små inkonsekvenser mellan LAD 2.3 och LAD 3.0 som gör att dela ett konto som är problematiska:
  * LAD 3.0 lagrar syslog-händelser i en tabell med ett annat namn.
  * CounterSpecifier strängar för `builtin` mått skiljer sig åt i LAD 3.0.

## <a name="protected-settings"></a>Skyddade inställningarna

Den här uppsättningen konfigurationsinformation innehåller känslig information som borde vara skyddad från offentligt, till exempel storage-autentiseringsuppgifter. Inställningarna skickas till och lagras av tillägget i krypterad form.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Namn | Värde
---- | -----
storageAccountName | Namnet på lagringskontot där data ska skrivas till av tillägget.
storageAccountEndPoint | (valfritt) Den slutpunkt som identifierar det moln där lagringskontot finns. Om den här inställningen inte finns, LAD som standard i Azures offentliga moln, `https://core.windows.net`. Om du vill använda ett lagringskonto i Azure Germany, Azure Government eller Azure Kina, ange ett värde i enlighet med detta.
storageAccountSasToken | En [konto-SAS-token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) för Blob och Tabelltjänster (`ss='bt'`), gäller för behållare och objekt (`srt='co'`), vilket ger lägga till, skapa, lista, uppdatera och skrivbehörighet (`sp='acluw'`). Gör *inte* inkluderar det ledande frågetecknet (?).
mdsdHttpProxy | (valfritt) HTTP-proxyinformation som krävs för att aktivera tillägget att ansluta till det angivna lagringskontot och slutpunkt.
sinksConfig | (valfritt) Information om alternativa mål som mått och händelser kan levereras. Specifik information om varje datamellanlagringsplats som stöds av tillägget beskrivs i avsnitten som följer.


> [!NOTE]
> När du distribuerar tillägget med en mall för Azure-distribution, måste lagringskontot och SAS-token skapas i förväg och sedan skickas till mallen. Du kan inte distribuera en virtuell dator, storage-konto och konfigurera tillägget i samma mall. Skapa en SAS-token i en mall stöds inte för närvarande.

Du kan enkelt skapa nödvändiga SAS-token via Azure portal.

1. Välj det allmänna lagringskontot som du vill att tillägget för att skriva
1. Välj ”signaturen för delad åtkomst” från inställningarna för en del av den vänstra menyn
1. Gör relevanta avsnitt enligt beskrivningen ovan
1. Klicka på knappen ”Generate-SAS”.

![image](./media/diagnostics-linux/make_sas.png)

Kopiera den genererade SAS till fältet storageAccountSasToken. ta bort det ledande frågetecknet (””?).

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Det här valfria avsnittet definierar ytterligare mål som tillägget skickar informationen som samlas in. ”Mottagare”-matrisen innehåller ett-objekt för varje ytterligare mottagare. Attributet ”type” anger andra attribut i objektet.

Element | Värde
------- | -----
namn | En sträng som används för att referera till den här mottagare någon annanstans i konfigurationen av tillägget.
typ | Typ av mottagare som definieras. Anger de andra värdena (om sådan finns) i instanser av den här typen.

Version 3.0 av Linux-Diagnostiktillägget stöder två typer av mottagare: EventHub- och JsonBlob.

#### <a name="the-eventhub-sink"></a>EventHub-mottagare

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

Posten ”sasURL” innehåller en fullständig URL, inklusive SAS-token för Event Hub som data ska publiceras. LAD kräver en SAS namngivning av en princip som gör att skicka anspråk. Ett exempel:

* Skapa ett namnområde för Event Hubs som kallas `contosohub`
* Skapa en Händelsehubb i namnområdet som kallas `syslogmsgs`
* Skapa en princip för delad åtkomst i Event Hub med namnet `writer` som gör att skicka anspråk

Om du har skapat en SAS bra till midnatt UTC på 1 januari 2018 kan sasURL värdet vara:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Mer information om hur du genererar SAS-token för Event Hubs finns i [den här webbsidan](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>JsonBlob mottagare

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data som skickas till mottagare JsonBlob lagras i blobbar i Azure storage. Varje instans av LAD skapar en blob varje timme för varje mottagare namn. Varje blob innehåller alltid en syntaktiskt giltig JSON-matris av objekt. Atomiskt läggs nya poster i matrisen. Blobbar lagras i en behållare med samma namn som mottagare. Azure storage-regler för blob-behållarnamn gäller för namnen på JsonBlob mottagare: mellan 3 och 63 gemena alfanumeriska ASCII-tecken eller tankstreck.

## <a name="public-settings"></a>Offentliga inställningar

Den här strukturen innehåller olika block med inställningar som styr den information som samlas in av tillägget. Varje inställning är valfritt. Om du anger `ladCfg`, måste du även ange `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Element | Värde
------- | -----
StorageAccount | Namnet på lagringskontot där data ska skrivas till av tillägget. Måste vara samma namn som har angetts i den [inställningarna för Replikeringsskyddade](#protected-settings).
mdsdHttpProxy | (valfritt) Samma som i den [inställningarna för Replikeringsskyddade](#protected-settings). Offentliga värde åsidosätts av det privata värdet om ange. Placera proxyinställningar som innehåller en hemlighet, till exempel ett lösenord, i den [inställningarna för Replikeringsskyddade](#protected-settings).

De återstående elementen beskrivs detaljerat i följande avsnitt.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Den här valfria struktur kontroller egenskaperna för insamling av mått och loggar för leverans till tjänsten Azure mått och till andra data. Du måste ange antingen `performanceCounters` eller `syslogEvents` eller båda. Du måste ange den `metrics` struktur.

Element | Värde
------- | -----
eventVolume | (valfritt) Styr antalet partitioner som skapas i lagringstabellen. Måste vara något av `"Large"`, `"Medium"`, eller `"Small"`. Om inte anges är standardvärdet `"Medium"`.
sampleRateInSeconds | (valfritt) Standardintervallet mellan insamling av rådata (unaggregated) mått. Minsta stöds samplingsfrekvensen är 15 sekunder. Om inte anges är standardvärdet `15`.

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Element | Värde
------- | -----
resourceId | Azure Resource Manager resurs-ID för den virtuella datorn eller VM-skalningsuppsättningen inställd som den virtuella datorn tillhör. Den här inställningen måste vara också anges om valfri JsonBlob mottagare används i konfigurationen.
scheduledTransferPeriod | Frekvensen där sammanställd mått är beräknad och överförs till Azure-mått, uttryckt i form av en är 8601 tidsintervall. Den minsta överföringsperioden är 60 sekunder, det vill säga PT1M. Du måste ange minst en scheduledTransferPeriod.

Exempel på de mått som anges i avsnittet performanceCounters har samlats in var 15: e sekund eller på exemplet Betygsätt explicit definierats för räknaren. Om flera scheduledTransferPeriod frekvenser visas (som i exemplet), beräknas varje aggregerings oberoende av varandra.

#### <a name="performancecounters"></a>PerformanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Det här valfria avsnittet kontroll över insamlingen av mått. Rå exempel räknas samman för varje [scheduledTransferPeriod](#metrics) att skapa dessa värden:

* medelvärde
* min
* max
* senaste insamlade värde
* Antal raw exempel som används för att beräkna mängden

Element | Värde
------- | -----
mottagare | (valfritt) En kommaavgränsad lista över namnen på mottagare till vilka LAD skickar aggregerade mätvärden resultat. Alla aggregerade mätvärden publiceras till varje listad mottagare. Se [sinksConfig](#sinksconfig). Exempel: `"EHsink1, myjsonsink"`.
typ | Identifierar den faktiska leverantören av måttet.
klass | Identifierar det specifika måttet i leverantörens namnområde tillsammans med ”räknaren”.
Räknaren | Identifierar det specifika måttet i leverantörens namnområde tillsammans med ”class”.
counterSpecifier | Identifierar det specifika måttet i ett namnområde för Azure-mått.
villkor | (valfritt) Väljer en specifik instans av objektet som måttet gäller eller väljer aggregering i alla instanser av objektet. Mer information finns i den [ `builtin` måttdefinitioner](#metrics-supported-by-builtin).
sampleRate | ÄR 8601 intervall som anger den hastighet som raw-exempel för det här måttet har samlats in. Om inte har angetts samling intervallet anges av värdet för [sampleRateInSeconds](#ladcfg). Kortaste stöds samplingsfrekvensen är 15 sekunder (PT15S).
enhet | Ska vara någon av de här strängarna: ”Count”, ”byte”, ”sekunder”, ”procent”, ”CountPerSecond”, ”BytesPerSecond”, ”Millisekunds”. Definierar enheten för måttet. Konsumenter av insamlade data förväntar sig värdena insamlade data för att matcha den här enheten. LAD ignorerar det här fältet.
displayName | Etiketten (på det språk som anges av de associera nationella inställningarna) som ska kopplas till dessa data i Azure-mått. LAD ignorerar det här fältet.

CounterSpecifier är ett valfritt ID. Konsumenter av mätvärden, som Azure portal diagram och aviseringar funktion, använda counterSpecifier som ”nyckeln” som identifierar ett mått eller en instans av ett mått. För `builtin` mätvärden, rekommenderar vi du använder counterSpecifier värden som börjar med `/builtin/`. Om du kan samla in en specifik instans av ett mått, rekommenderar vi du bifoga identifierare för instansen till counterSpecifier-värde. Några exempel:

* `/builtin/Processor/PercentIdleTime` -Inaktivitetstid i genomsnitt över alla virtuella processorer
* `/builtin/Disk/FreeSpace(/mnt)` -Ledigt utrymme för /mnt filsystemet
* `/builtin/Disk/FreeSpace` -Ledigt utrymme i genomsnitt över alla monterade filsystem

Förväntar sig counterSpecifier-värde som matchar alla mönstret varken LAD eller Azure-portalen. Var konsekvent i hur du konstruerar counterSpecifier värden.

När du anger `performanceCounters`, LAD alltid skriver data till en tabell i Azure storage. Du kan ha samma data skrivs till JSON-blobar och/eller Event Hubs, men du kan inte inaktivera lagrar data i en tabell. Alla instanser av diagnostiktillägget som konfigurerats för att använda samma lagringskontonamn och slutpunkten lägga till sina mått och loggar i samma tabell. Om för många virtuella datorer skriver till samma tabellpartition, Azure kan begränsa skrivningar till partitionen. Inställningen eventVolume gör poster ska spridas över 1 (liten), 10 (medel) eller 100 (stor) olika partitioner. Vanligtvis räcker ”medel” så inte begränsas trafik. Funktionen Azure mått i Azure-portalen använder informationen i den här tabellen att skapa diagram eller att utlösa aviseringar. Tabellnamnet är en sammanfogning av de här strängarna:

* `WADMetrics`
* ”ScheduledTransferPeriod” för de sammanställda värdena lagras i tabellen
* `P10DV2S`
* Ett datum i formatet ”ååååmmdd” som ändrar var 10: e dag

Exempel är `WADMetricsPT1HP10DV2S20170410` och `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Det här valfria avsnittet kontroll över insamlingen av händelser från syslog. Syslog-händelser samlas inte alls om avsnittet utelämnas.

Samlingen syslogEventConfiguration har en post för varje syslog-funktion av intresse. Om minSeverity är ”ingen” för en viss anläggning, eller om den anläggningen inte visas alls i elementet, som inga händelser från den resurs avbildas.

Element | Värde
------- | -----
mottagare | En kommaavgränsad lista över namnen på de mottagare som enskilda logghändelser publiceras. Alla logghändelser matchar begränsningarna i syslogEventConfiguration publiceras till varje listad mottagare. Exempel: ”EHforsyslog”
funktionen %{facilityname/ | Namn på en syslog-resurs (till exempel ”LOG\_användare” eller ”LOG\_LOCAL0”). Se avsnittet ”anläggning” i den [syslog man sidan](http://man7.org/linux/man-pages/man3/syslog.3.html) för en fullständig lista.
minSeverity | En syslog-allvarlighetsgrad (till exempel ”LOG\_ERR” eller ”LOG\_information”). Se avsnittet ”nivå” i den [syslog man sidan](http://man7.org/linux/man-pages/man3/syslog.3.html) för en fullständig lista. Tillägget samlar in händelser som skickas till funktionen vid eller över den angivna nivån.

När du anger `syslogEvents`, LAD alltid skriver data till en tabell i Azure storage. Du kan ha samma data skrivs till JSON-blobar och/eller Event Hubs, men du kan inte inaktivera lagrar data i en tabell. Partitionering beteendet för den här tabellen är densamma som beskrivs för `performanceCounters`. Tabellnamnet är en sammanfogning av de här strängarna:

* `LinuxSyslog`
* Ett datum i formatet ”ååååmmdd” som ändrar var 10: e dag

Exempel är `LinuxSyslog20170410` och `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Det här valfria avsnittet styr körningen av godtycklig [OMI](https://github.com/Microsoft/omi) frågor.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Element | Värde
------- | -----
namnområde | (valfritt) OMI-namnområde som frågan ska köras. Om inget anges standardvärdet är ”root/scx”, implementeras av den [System Center plattformsoberoende Providers](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
DocumentDB | OMI-fråga som ska köras.
tabell | (valfritt) Azure storage-tabell i avsedda storage-konto (se [inställningarna för Replikeringsskyddade](#protected-settings)).
frequency | (valfritt) Antal sekunder mellan körning av frågan. Standardvärdet är 300 (5 minuter). minsta värde är 15 sekunder.
mottagare | (valfritt) En kommaavgränsad lista över namnen på ytterligare mottagare som raw mått exempelresultat ska publiceras. Ingen sammansättning av exemplen raw beräknas genom att tillägget eller genom Azure-mått.

Antingen ”tabell” eller ”egenskaperna”, eller både och måste anges.

### <a name="filelogs"></a>fileLogs

Styr infångandet av loggfiler. LAD samlar in ny textrader som de skrivs till filen och skriver dem till tabellrader och/eller några angivna mottagare (JsonBlob eller EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Element | Värde
------- | -----
fil | Den fullständiga sökvägen till loggfilen som sett och avbildas. Sökvägen måste namnge en enstaka fil. Det går inte att namnge en katalog eller innehålla jokertecken.
tabell | (valfritt) Azure storage tabellen i avsedda storage-konto (som anges i konfigurationen av skyddade), som nya rader från ”pilslut” i filen skrivs.
mottagare | (valfritt) En kommaavgränsad lista över namnen på ytterligare mottagare till vilka log linjer som skickas.

Antingen ”tabell” eller ”egenskaperna”, eller både och måste anges.

## <a name="metrics-supported-by-the-builtin-provider"></a>Mått som stöds av providern builtin

Builtin mått providern är en källa för mätvärden som är mest intressanta till en bred uppsättning användare. De här måtten delas in i fem bred klasser:

* Processor
* Minne
* Nätverk
* Filsystem
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>Builtin mått för Processor-klass

Processor-klassen för mått innehåller information om processoranvändning på den virtuella datorn. Resultatet är medelvärdet över alla CPU: er vid sammanställning procenttal. I en virtuell dator i två vCPU, om en virtuell processor var 100% upptagen och den andra 100% inaktiv, skulle rapporterade PercentIdleTime vara 50. Om varje vCPU var 50% upptagen för samma period, skulle det rapporterade resultatet också vara 50. I en fyra vCPU virtuell dator, med en virtuell processor 100% upptagen och inaktiv andra, är rapporterade PercentIdleTime 75.

Räknaren | Betydelse
------- | -------
PercentIdleTime | Procentandel av tiden under fönstret aggregering att processorer kör kernel inaktiv loop
PercentProcessorTime | Procentandelen tid att köra en icke-inaktiv tråd
PercentIOWaitTime | Procentandelen tid som väntar på i/o-åtgärder att slutföra
PercentInterruptTime | Procentandelen tid som kör maskin-och programvara avbrott och DPC-anrop (uppskjutna proceduranrop)
PercentUserTime | Icke-inaktiv tid under fönstret aggregering, procentandelen tid som tillbringats i användare mer med normal prioritet
PercentNiceTime | Icke-inaktiv tid i procent som går åt till sänkt (bra) prioritet
PercentPrivilegedTime | Icke-inaktiv tid i procent har använt i läget för privilegierad (kernel)

De fyra första räknarna ska summeras till 100%. Tre senaste prestandaräknare också sum och 100%. de dela upp summan av PercentProcessorTime och PercentIOWaitTime PercentInterruptTime.

Om du vill ha ett enda mått som aggregeras över alla processorer, ange `"condition": "IsAggregate=TRUE"`. Om du vill ha ett mått för en viss processor, till exempel andra logisk processor för en virtuell dator i fyra vCPU, ange `"condition": "Name=\\"1\\""`. Logisk processor siffror är i intervallet `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>Builtin mått för minne-klass

Klassen minne över mått som innehåller information om minnesanvändningen, växling och växlar.

Räknaren | Betydelse
------- | -------
AvailableMemory | Tillgängligt fysiskt minne i MiB
PercentAvailableMemory | Tillgängligt fysiskt minne i procent av det totala minnet
UsedMemory | Använd fysiskt minne (MiB)
PercentUsedMemory | Och används fysiskt minne i procent av det totala minnet
PagesPerSec | Total växling (läsa/skriva)
PagesReadPerSec | Sidorna läses från säkerhetskopiering store (växlingsfilen, programfil, mappade filen osv.)
PagesWrittenPerSec | Sidor som skrivs till lagringsenheten (växlingsfilen, mappade filen osv.)
AvailableSwap | Oanvända växlingsutrymme (MiB)
PercentAvailableSwap | Oanvända växlingsutrymme som en procentandel av totalt växlingsutrymme
UsedSwap | Använd växlingsutrymme (MiB)
PercentUsedSwap | Och används växlingsutrymme som en procentandel av totalt växlingsutrymme

Den här klassen mått har en enda instans. Attributet ”villkor” har inga användbara inställningar och ska utelämnas.

### <a name="builtin-metrics-for-the-network-class"></a>Builtin mått för klassen nätverk

Klassen nätverk över mått som innehåller information om nätverksaktivitet på en enskild nätverksgränssnitt sedan start. LAD exponerar inte bandbredd mätvärden, som kan hämtas från värdmått.

Räknaren | Betydelse
------- | -------
BytesTransmitted | Totalt antal byte som skickats sedan start
BytesReceived | Totalt antal byte mottaget sedan start
BytesTotal | Totalt antal byte som skickas eller tas emot sedan start
PacketsTransmitted | Totalt antal paket som skickats sedan start
PacketsReceived | Totalt antal paket som tagits emot sedan start
TotalRxErrors | Antal Mottagningsfel sedan start
TotalTxErrors | Antal sända fel sedan start
TotalCollisions | Antal kollisioner som rapporteras av nätverksportar sedan start

 Även om den här klassen är instanced stöder inte LAD in nätverk måtten sammanställs i alla nätverksenheter. Om du vill hämta mått för ett visst gränssnitt, till exempel eth0, ange `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>Builtin mått för filsystem-klass

Filsystem-klassen för mått innehåller information om användningen för filsystem. Absoluta och procentuella värden rapporteras som de skulle visas för en vanlig användare (inte rot).

Räknaren | Betydelse
------- | -------
FreeSpace | Ledigt diskutrymme i byte
UsedSpace | Använt diskutrymme i byte
PercentFreeSpace | Procent ledigt utrymme
PercentUsedSpace | Använt utrymme i procent
PercentFreeInodes | Procentandel oanvänt noder i procent
PercentUsedInodes | Procentandel tilldelade (som används) i-noder i summeras över alla filsystem
BytesReadPerSecond | Lästa byte per sekund
BytesWrittenPerSecond | Byte som skrivs per sekund
BytesPerSecond | Byte som lästs eller skrivits per sekund
ReadsPerSecond | Läsåtgärder per sekund
WritesPerSecond | Skrivåtgärder per sekund
TransfersPerSecond | Läsa eller skriva-åtgärder per sekund

Sammanställda värden över alla filsystem som kan hämtas genom att ange `"condition": "IsAggregate=True"`. Värden för en specifik monterade filsystem, till exempel ”/ mnt”, kan hämtas genom att ange `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>Builtin mått för Disk-klass

Disk-klassen för mått innehåller information om diskanvändning för enheten. Statistiken gäller för hela enheten. Om det finns flera filsystem på en enhet, är räknare för enheten ett effektivt sätt, samman alla.

Räknaren | Betydelse
------- | -------
ReadsPerSecond | Läsåtgärder per sekund
WritesPerSecond | Skrivåtgärder per sekund
TransfersPerSecond | Totalt antal åtgärder per sekund
AverageReadTime | Genomsnittlig sekunder per läsning
AverageWriteTime | Genomsnittlig sekunder per skrivåtgärd
AverageTransferTime | Det genomsnittliga antalet sekunder per åtgärd
AverageDiskQueueLength | Genomsnittligt antal köade diskåtgärder
ReadBytesPerSecond | Antalet lästa byte per sekund
WriteBytesPerSecond | Antalet byte som skrivs per sekund
BytesPerSecond | Antalet byte som lästs eller skrivits per sekund

Sammanställda värden för alla diskar som kan hämtas genom att ange `"condition": "IsAggregate=True"`. Om du vill ha information för en specifik enhet (till exempel/dev/sdf1) kan ange `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Installera och konfigurera LAD 3.0 via CLI

Anta att dina skyddade inställningar finns i filen PrivateConfig.json och din offentliga konfigurationsinformation är i PublicConfig.json och kör följande kommando:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Kommandot förutsätter att du använder Azure Resource Manager-läge (arm) av Azure CLI. Konfigurera LAD för klassisk distribution modellera (ASM) virtuella datorer, växla till läget för ”asm” (`azure config mode asm`) och utelämna resursgruppens namn i kommandot. Mer information finns i den [plattformsoberoende CLI-dokumentationen](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>En exempelkonfiguration LAD 3.0

Utifrån föregående definitioner är här en LAD 3.0 tillägget exempelkonfiguration med förklaringar. Om du vill använda det här exemplet i ditt fall bör du använda egna lagringskontonamn och konto-SAS-token EventHubs SAS-token.

### <a name="privateconfigjson"></a>PrivateConfig.json

Konfigurera inställningarna för privat:

* Ett lagringskonto
* en matchande konto-SAS-token
* flera mottagare (JsonBlob eller EventHubs med SAS-token)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

Inställningarna offentliga orsaka LAD till:

* Ladda upp procent processortid och använt diskutrymme mått till den `WADMetrics*` tabell
* Ladda upp meddelanden från syslog anläggning ”användare” och allvarlighetsgraden ”information” till den `LinuxSyslog*` tabell
* Ladda upp raw OMI-frågeresultat (PercentProcessorTime och PercentIdleTime) till den namngivna `LinuxCPU` tabell
* Ladda upp tillagda rader i filen `/var/log/myladtestlog` till den `MyLadTestLog` tabell

I båda fallen överförs även data till:

* Azure Blob storage (behållarnamn är enligt definitionen i JsonBlob mottagare)
* EventHubs-slutpunkt (som anges i EventHubs-mottagare)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

Den `resourceId` i konfigurationen måste matcha att ange den virtuella datorn eller VM-skalningsuppsättningen.

* Azure-plattformen mått diagram och aviseringar vet resourceId för den virtuella datorn som du arbetar med. Det förväntar sig att hitta data för den virtuella datorn med hjälp av resourceId lookup-nyckel.
* Om du använder automatisk skalning i Azure, måste resourceId i konfigurationen för automatisk skalning matcha resourceId som används av LAD.
* ResourceId är inbyggt i namnen på JsonBlobs som skrivits av LAD.

## <a name="view-your-data"></a>Visa dina data

Använd Azure portal om du vill visa prestandadata eller ställa in aviseringar:

![image](./media/diagnostics-linux/graph_metrics.png)

Den `performanceCounters` data måste alltid lagras på en Azure Storage-tabell. Azure Storage-API: er är tillgängliga för många språk och plattformar.

Data som skickas till mottagare JsonBlob lagras i blobbar i lagringskontot med namnet i den [inställningarna för Replikeringsskyddade](#protected-settings). Du kan använda blob-data med hjälp av alla API: er för Azure Blob Storage.

Dessutom kan du använda verktygen Användargränssnittet för att komma åt data i Azure Storage:

* Visual Studio Server Explorer.
* [Microsoft Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Den här ögonblicksbilden av en Microsoft Azure Storage Explorer-session visar genererade Azure Storage-tabeller och behållarna från ett korrekt konfigurerat LAD 3.0-tillägg på en virtuell testdator. Bilden matchar inte exakt den [LAD 3.0 exempelkonfiguration](#an-example-lad-30-configuration).

![image](./media/diagnostics-linux/stg_explorer.png)

Se den relevanta [EventHubs dokumentation](../../event-hubs/event-hubs-what-is-event-hubs.md) att lära dig hur du använder meddelanden som publiceras till en slutpunkt för EventHubs.

## <a name="next-steps"></a>Nästa steg

* Skapa måttaviseringar i [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) för de mått som du samlar in.
* Skapa [övervakning diagram](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) för dina mått.
* Lär dig hur du [skapa VM scale Sets](../linux/tutorial-create-vmss.md) använder dina mått för att styra automatisk skalning.

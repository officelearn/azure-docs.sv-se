---
title: Azure Compute – Linux-diagnostiskt tillägg
description: Så här konfigurerar du LAD (Azure Linux Diagnostic Extension) för att samla in mått och logg händelser från virtuella Linux-datorer som körs i Azure.
services: virtual-machines-linux
author: abhijeetgaiha
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: gwallace
ms.openlocfilehash: 046e61d82893bf1fcdb2d6697cfaaa9f5bde8c2c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359370"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Använd Linux-diagnostiskt tillägg för att övervaka mått och loggar

I det här dokumentet beskrivs version 3,0 och senare av Linux-diagnostik.

> [!IMPORTANT]
> Information om version 2,3 och äldre finns i [det här dokumentet](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introduktion

Tillägget för Linux-diagnostik hjälper en användare att övervaka hälsan hos en virtuell Linux-dator som körs på Microsoft Azure. Den har följande funktioner:

* Samlar in system prestanda mått från den virtuella datorn och lagrar dem i en viss tabell i ett särskilt lagrings konto.
* Hämtar logg händelser från syslog och lagrar dem i en viss tabell i det angivna lagrings kontot.
* Gör det möjligt för användare att anpassa de data mått som samlas in och överförs.
* Gör det möjligt för användare att anpassa syslog-funktionerna och allvarlighets graden för händelser som samlas in och överförs.
* Gör det möjligt för användare att överföra angivna loggfiler till en angiven lagrings tabell.
* Stöder sändning av mått och logg händelser till godtyckliga EventHub-slutpunkter och JSON-formaterade blobbar i det angivna lagrings kontot.

Det här tillägget fungerar med både Azures distributions modeller.

## <a name="installing-the-extension-in-your-vm"></a>Installera tillägget på den virtuella datorn

Du kan aktivera det här tillägget med hjälp av Azure PowerShell-cmdletar, Azure CLI-skript, ARM-mallar eller Azure Portal. Mer information finns i [tillägg-funktioner](features-linux.md).

Dessa installationsinstruktioner och en [nedladdnings bar exempel konfiguration](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurerar lad 3,0 till:

* avbilda och lagra samma mått som tillhandahölls av LAD 2,3;
* avbilda en användbar uppsättning med fil Systems mått, nytt till LAD 3,0;
* avbilda Standard-syslog-samlingen som aktive ras av LAD 2,3;
* Aktivera Azure Portal upplevelse för diagram och aviseringar på VM-mått.

Den nedladdnings bara konfigurationen är bara ett exempel. ändra den så att den passar dina egna behov.

### <a name="prerequisites"></a>Krav

* **Azure Linux-agentens version 2.2.0 eller senare**. De flesta Azure VM Linux-avbildningar innehåller version 2.2.7 eller senare. Kör `/usr/sbin/waagent -version` för att bekräfta versionen som är installerad på den virtuella datorn. Om den virtuella datorn kör en äldre version av gäst agenten följer du [de här anvisningarna](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) för att uppdatera den.
* **Azure CLI**. [Konfigurera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -miljön på din dator.
* Kommandot wget, om du inte redan har det: kör `sudo apt-get install wget`.
* En befintlig Azure-prenumeration och ett befintligt lagrings konto i den för att lagra data.
* Lista över Linux-distributioner som stöds finns på https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Exempel installation

Fyll i rätt värden för variablerna i det första avsnittet innan du kör:

```bash
# Set your Azure VM diagnostic variables correctly below
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

URL: en för exempel konfigurationen och dess innehåll kan komma att ändras. Ladda ned en kopia av JSON-filen med Portal inställningar och anpassa den efter dina behov. Alla mallar eller automatiseringar som du skapar bör använda din egen kopia i stället för att hämta URL: en varje gången.

### <a name="updating-the-extension-settings"></a>Uppdaterar tilläggs inställningarna

När du har ändrat dina skyddade eller offentliga inställningar distribuerar du dem till den virtuella datorn genom att köra samma kommando. Om något ändras i inställningarna skickas de uppdaterade inställningarna till tillägget. LAD laddar om konfigurationen och startar om sig själv.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrering från tidigare versioner av tillägget

Den senaste versionen av tillägget är **3,0**. **Alla gamla versioner (2. x) är inaktuella och kan tas bort från och med den 31 juli 2018**.

> [!IMPORTANT]
> Tillägget introducerar ändringar i tilläggets konfiguration. En sådan ändring gjordes för att förbättra säkerheten för tillägget. Det innebär att det inte går att behålla bakåtkompatibilitet med 2. x. Tilläggs utgivaren för det här tillägget skiljer sig också från utgivaren för 2. x-versionerna.
>
> Om du vill migrera från 2. x till den här nya versionen av tillägget måste du avinstallera det gamla tillägget (under det gamla utgivar namnet) och sedan installera version 3 av tillägget.

Rekommenderade

* Installera tillägget med automatisk uppgradering av lägre version aktiverat.
  * På klassiska virtuella datorer för distributions modell anger du ' 3. * ' som version om du installerar tillägget via Azure XPLAT CLI eller PowerShell.
  * På Azure Resource Manager distributions modellens virtuella datorer inkluderar du "" aktiverat autoupgrademinorversion ": true" i mallen för VM-distribution.
* Använd ett nytt/annat lagrings konto för LAD 3,0. Det finns flera små inkompatibiliteter mellan LAD 2,3 och LAD 3,0 som gör det enkelt att dela ett konto problematiska:
  * LAD 3,0 lagrar Syslog-händelser i en tabell med ett annat namn.
  * CounterSpecifier-strängarna för `builtin` mått skiljer sig åt i LAD 3,0.

## <a name="protected-settings"></a>Skyddade inställningar

Den här uppsättningen konfigurations information innehåller känslig information som bör skyddas från offentlig vy, till exempel autentiseringsuppgifter för lagring. De här inställningarna överförs till och lagras av tillägget i krypterad form.

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
storageAccountName | Namnet på det lagrings konto där data skrivs av tillägget.
storageAccountEndPoint | valfritt Slut punkten som identifierar molnet där lagrings kontot finns. Om den här inställningen saknas, LAD standardvärdet för Azures offentliga moln `https://core.windows.net`. Om du vill använda ett lagrings konto i Azure Germany, Azure Government eller Azure Kina anger du detta värde i enlighet med detta.
storageAccountSasToken | En [SAS-token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) för blob-och tabell tjänster (`ss='bt'`), som gäller för behållare och objekt (`srt='co'`), som ger behörigheterna Lägg till, skapa, lista, uppdatera och skriv (`sp='acluw'`). Ta *inte* med det inledande fråga-tecknet (?).
mdsdHttpProxy | valfritt Information om HTTP-proxy som krävs för att aktivera tillägget för att ansluta till det angivna lagrings kontot och slut punkten.
sinksConfig | valfritt Information om alternativa destinationer till vilka mått och händelser som kan levereras. Detaljerad information om varje data mottagare som stöds av tillägget beskrivs i avsnitten som följer.

Om du vill hämta en SAS-token i en Resource Manager-mall använder du funktionen **listAccountSas** . En exempel-mall finns i [exempel på en lista funktion](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Du kan enkelt skapa den SAS-token som krävs via Azure Portal.

1. Välj det allmänna lagrings konto som du vill att tillägget ska skrivas till
1. Välj "signatur för delad åtkomst" från inställningar-delen av den vänstra menyn
1. Gör lämpliga avsnitt enligt beskrivningen ovan
1. Klicka på knappen generera SAS.

![mallar](./media/diagnostics-linux/make_sas.png)

Kopiera de genererade SAS: erna till fältet storageAccountSasToken; ta bort det inledande fråga-tecknet ("?").

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

Det här valfria avsnittet definierar ytterligare destinationer dit tillägget skickar den information som samlas in. Matrisen "Sink" innehåller ett objekt för varje ytterligare data mottagare. Attributet "Type" bestämmer de andra attributen i objektet.

Element | Värde
------- | -----
namn | En sträng som används för att referera till denna mottagare någon annan stans i tilläggs konfigurationen.
typ | Typ av mottagare som definieras. Bestämmer de andra värdena (om sådana finns) i instanser av den här typen.

Version 3,0 av Linux Diagnostic-tillägget har stöd för två typer av mottagare: EventHub och JsonBlob.

#### <a name="the-eventhub-sink"></a>EventHub-sinken

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

Posten "sasURL" innehåller den fullständiga URL: en, inklusive SAS-token, för den Händelsehubben som data ska publiceras till. LAD kräver en SAS som namnger en princip som aktiverar Send-anspråk. Ett exempel:

* Skapa ett Event Hubs-namnområde som kallas `contosohub`
* Skapa en Event Hub i namn området som heter `syslogmsgs`
* Skapa en princip för delad åtkomst på Händelsehubben med namnet `writer` som aktiverar Send-anspråk

Om du har skapat ett SAS-värde till och med midnatt UTC den 1 januari 2018 kan sasURL-värdet vara:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Mer information om hur du skapar SAS-token för Event Hubs finns på [den här webb sidan](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>JsonBlob-mottagare

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data som dirigeras till en JsonBlob-mottagare lagras i blobbar i Azure Storage. Varje instans av LAD skapar en BLOB varje timme för varje mottagar namn. Varje Blob innehåller alltid en syntaktiskt giltig JSON-matris med objekt. Nya poster läggs atomict till i matrisen. Blobbar lagras i en behållare med samma namn som mottagaren. Reglerna för Azure Storage för BLOB container-namn gäller för namnen på JsonBlob-mottagare: mellan 3 och 63 gemena alfanumeriska ASCII-tecken eller bindestreck.

## <a name="public-settings"></a>Offentliga inställningar

Den här strukturen innehåller olika block med inställningar som styr den information som samlas in av tillägget. Varje inställning är valfri. Om du anger `ladCfg`måste du också ange `StorageAccount`.

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
StorageAccount | Namnet på det lagrings konto där data skrivs av tillägget. Måste vara samma namn som anges i de [skyddade inställningarna](#protected-settings).
mdsdHttpProxy | valfritt Samma som i de [skyddade inställningarna](#protected-settings). Det offentliga värdet åsidosätts av det privata värdet, om det är inställt. Placera proxyinställningar som innehåller en hemlighet, till exempel ett lösen ord, i de [skyddade inställningarna](#protected-settings).

Återstående element beskrivs i detalj i följande avsnitt.

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

Den här valfria strukturen styr insamlingen av mått och loggar för leverans till Azure Metrics-tjänsten och till andra data mottagare. Du måste ange antingen `performanceCounters` eller `syslogEvents` eller både och. Du måste ange `metrics`s strukturen.

Element | Värde
------- | -----
eventVolume | valfritt Styr antalet partitioner som skapats i lagrings tabellen. Måste vara en av `"Large"`, `"Medium"`eller `"Small"`. Om inget värde anges `"Medium"`standardvärdet.
sampleRateInSeconds | valfritt Standard intervallet mellan samling av RAW-mått (unaggregerade). Den minsta samplings frekvensen som stöds är 15 sekunder. Om inget värde anges `15`standardvärdet.

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
resourceId | Azure Resource Manager resurs-ID för den virtuella datorn eller den virtuella datorns skalnings uppsättning som den virtuella datorn tillhör. Den här inställningen måste också anges om en JsonBlob-mottagare används i konfigurationen.
scheduledTransferPeriod | Den frekvens med vilken aggregerade mått ska beräknas och överföras till Azure-mått, uttryckt som ett 8601-tidsintervall. Den minsta överförings perioden är 60 sekunder, det vill säga PT1M. Du måste ange minst en scheduledTransferPeriod.

Exempel på de mått som anges i avsnittet performanceCounters samlas in var 15: e sekund eller vid den samplings frekvens som uttryckligen definierats för räknaren. Om flera scheduledTransferPeriod frekvenser visas (som i exemplet) beräknas varje agg regering oberoende av varandra.

#### <a name="performancecounters"></a>performanceCounters

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

Det här valfria avsnittet styr insamlingen av mått. RAW-exempel sammanställs för varje [scheduledTransferPeriod](#metrics) för att producera följande värden:

* medelvärde
* min
* max
* senast insamlat värde
* antal RAW-exempel som används för att beräkna mängden

Element | Värde
------- | -----
mottagare | valfritt En kommaavgränsad lista över namn på mottagare som LAD skickar sammanställda mått resultat till. Alla sammansatta mått publiceras till varje mottagen mottagare. Se [sinksConfig](#sinksconfig). Exempel: `"EHsink1, myjsonsink"`.
typ | Identifierar den faktiska providern för måttet.
klass | Tillsammans med "Counter" identifierar det angivna måttet i namn området för providern.
räknare | Tillsammans med "Class" identifierar det angivna måttet i namn området för providern.
counterSpecifier | Identifierar det speciella måttet i namn området för Azure-mått.
condition | valfritt Väljer en speciell instans av objektet som måttet gäller för eller väljer agg regeringen för alla instanser av objektet. Mer information finns i mått definitionerna för `builtin`.
sampleRate | ÄR 8601 intervall som anger med vilken hastighet rå samplingar för det här måttet samlas in. Om den inte anges anges samlings intervallet av värdet för [sampleRateInSeconds](#ladcfg). Den kortaste samplings frekvensen är 15 sekunder (PT15S).
enhet | Måste vara en av följande strängar: "count", "bytes", "Seconds", "percent", "CountPerSecond", "BytesPerSecond", "Millisekunde". Definierar måttets enhet. Användare av insamlade data förväntar sig insamlade datavärden som matchar den här enheten. LAD ignorerar det här fältet.
displayName | Etiketten (på det språk som anges av den associerade språk inställningen) som ska kopplas till dessa data i Azure-mått. LAD ignorerar det här fältet.

CounterSpecifier är en godtycklig identifierare. Konsumenter av mått, som Azure Portal funktion för diagram och avisering, använder counterSpecifier som "Key" som identifierar ett mått eller en instans av ett mått. För `builtin` mått rekommenderar vi att du använder counterSpecifier-värden som börjar med `/builtin/`. Om du samlar in en speciell instans av ett mått rekommenderar vi att du kopplar instansens identifierare till counterSpecifier-värdet. Några exempel:

* tiden för `/builtin/Processor/PercentIdleTime`-inaktivitet i genomsnitt för alla virtuella processorer
* `/builtin/Disk/FreeSpace(/mnt)` – ledigt utrymme för/mnt-filsystem
* Genomsnittligt `/builtin/Disk/FreeSpace` ledigt utrymme i alla monterade fil system

Varken LAD eller Azure Portal förväntar sig att counterSpecifier-värdet matchar eventuella mönster. Var konsekvent i hur du skapar counterSpecifier-värden.

När du anger `performanceCounters`skriver LAD alltid data till en tabell i Azure Storage. Du kan ha samma data som skrivits till JSON-blobbar och/eller Event Hubs, men du kan inte inaktivera lagring av data i en tabell. Alla instanser av diagnostiskt tillägg som kon figurer ATS för att använda samma lagrings konto namn och slut punkt lägger till sina mått och loggar i samma tabell. Om för många virtuella datorer skrivs till samma Table-partition kan Azure begränsa skrivningar till den partitionen. Inställningen eventVolume gör att poster sprids över 1 (små), 10 (medel) eller 100 (stora) olika partitioner. Normalt räcker "medium" för att säkerställa att trafiken inte begränsas. Azure Metrics-funktionen i Azure Portal använder data i den här tabellen för att skapa grafer eller utlösa aviseringar. Tabell namnet är sammanfogningen av dessa strängar:

* `WADMetrics`
* "ScheduledTransferPeriod" för de sammanställda värdena som lagras i tabellen
* `P10DV2S`
* Ett datum, i formatet "ÅÅÅÅMMDD", som ändras var 10: e dag

Exempel på detta är `WADMetricsPT1HP10DV2S20170410` och `WADMetricsPT1MP10DV2S20170609`.

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

Det här valfria avsnittet styr samlingen av logg händelser från syslog. Om avsnittet utelämnas, samlas inga Syslog-händelser in alls.

SyslogEventConfiguration-samlingen har en post för varje syslog-funktion. Om minSeverity är "ingen" för en viss anläggning, eller om denna funktion inte visas i elementet alls, så fångas inga händelser från den funktionen.

Element | Värde
------- | -----
mottagare | En kommaavgränsad lista över namn på mottagare som enskilda logg händelser publiceras till. Alla logg händelser som matchar begränsningarna i syslogEventConfiguration publiceras till varje mottagen mottagare. Exempel: "EHforsyslog"
facilityName | Ett syslog-servernamn (till exempel "LOG\_användare" eller "Logga\_LOCAL0"). Se avsnittet "anläggning" på [sidan syslog-man](http://man7.org/linux/man-pages/man3/syslog.3.html) för den fullständiga listan.
minSeverity | En syslog-allvarlighets nivå (till exempel "LOG\_ERR" eller "Logga\_information"). Se avsnittet "nivå" på [sidan syslog-man](http://man7.org/linux/man-pages/man3/syslog.3.html) för den fullständiga listan. Tillägget fångar händelser som skickas till anläggningen på eller över den angivna nivån.

När du anger `syslogEvents`skriver LAD alltid data till en tabell i Azure Storage. Du kan ha samma data som skrivits till JSON-blobbar och/eller Event Hubs, men du kan inte inaktivera lagring av data i en tabell. Partitionerings beteendet för den här tabellen är detsamma som beskrivs för `performanceCounters`. Tabell namnet är sammanfogningen av dessa strängar:

* `LinuxSyslog`
* Ett datum, i formatet "ÅÅÅÅMMDD", som ändras var 10: e dag

Exempel på detta är `LinuxSyslog20170410` och `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Det här valfria avsnittet styr körningen av godtyckliga [OMI](https://github.com/Microsoft/omi) -frågor.

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
namnområde | valfritt OMI-namnområdet som frågan ska köras inom. Om inget anges är standardvärdet "root/SCX", implementerat av [System Center cross-platform-leverantörer](https://github.com/Microsoft/SCXcore).
DocumentDB | OMI-frågan som ska köras.
table | valfritt Azure Storage-tabellen i det angivna lagrings kontot (se [skyddade inställningar](#protected-settings)).
frequency | valfritt Antalet sekunder mellan körningen av frågan. Standardvärdet är 300 (5 minuter); Minimivärdet är 15 sekunder.
mottagare | valfritt En kommaavgränsad lista över namn på ytterligare mottagare som rå samplings mått resultat ska publiceras i. Ingen agg regering av dessa RAW-exempel beräknas av tillägget eller av Azure-mått.

Du måste ange antingen "table" eller "Sinks" eller båda.

### <a name="filelogs"></a>fileLogs

Styr avbildningen av loggfiler. LAD fångar nya text rader när de skrivs till filen och skriver dem till tabell rader och/eller angivna handfat (JsonBlob eller EventHub).

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
file | Den fullständiga sökvägen till logg filen som ska bevakas och fångas. Sökvägen måste ha ett namn på en enskild fil. den kan inte namnge en katalog eller innehålla jokertecken.
table | valfritt Azure Storage-tabellen, i det angivna lagrings kontot (enligt vad som anges i den skyddade konfigurationen), som nya rader från "änden" av filen skrivs till.
mottagare | valfritt En kommaavgränsad lista över namn på ytterligare mottagare som logg rader skickas till.

Du måste ange antingen "table" eller "Sinks" eller båda.

## <a name="metrics-supported-by-the-builtin-provider"></a>Mått som stöds av den inbyggda providern

Den inbyggda mått leverantören är en källa till mått som är mest intressanta för en bred uppsättning användare. Dessa mått delas in i fem breda klasser:

* Processor
* Minne
* Nätverk
* Filsystem
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>inbyggda mått för processor klassen

Processor klassen för mått ger information om processor användning på den virtuella datorn. När procent andelen beräknas är resultatet genomsnittet för alla CPU: er. I en vCPU virtuell dator, om en vCPU var 100% upptagen och den andra var 100% inaktiv, skulle den rapporterade PercentIdleTime vara 50. Om varje vCPU var 50% upptagen för samma period skulle det rapporterade resultatet också vara 50. I en vCPU virtuell dator med fyra virtuella datorer, med en vCPU på 100% upptagen och andra inaktiva, skulle den rapporterade PercentIdleTime vara 75.

räknare | Betydelse
------- | -------
PercentIdleTime | Procent andel av tiden under agg regerings perioden som processorerna körde kernel Idle-slingan
PercentProcessorTime | Procent andel av tiden som en icke-inaktiv tråd körs
PercentIOWaitTime | Procent andel av tid i väntan på att IO-åtgärder ska slutföras
PercentInterruptTime | Procent andel av tiden som maskin vara/program avbrott och DPC-anrop (uppskjutna steg) körs
PercentUserTime | Vid icke-inaktivitet under agg regerings perioden, den procent andel av tiden som ägnats åt användare mer med normal prioritet
PercentNiceTime | För icke-inaktivitet, procent andelen för sänkt (snyggt) prioritet
PercentPrivilegedTime | För icke-inaktivitet, procent andelen förbrukat i kernelläge

De första fyra räknarna ska summera till 100%. De sista tre räknarna summerar också till 100%; de sammanslager summan av PercentProcessorTime, PercentIOWaitTime och PercentInterruptTime.

Ange `"condition": "IsAggregate=TRUE"`om du vill få ett enda mått som sammanställs för alla processorer. För att få ett mått för en speciell processor, till exempel den andra logiska processorn för en vCPU virtuell dator, ange `"condition": "Name=\\"1\\""`. Logiska processor nummer ligger inom intervallet `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>inbyggda mått för minnes klassen

Minnes klassen för mått ger information om minnes användning, växling och växling.

räknare | Betydelse
------- | -------
AvailableMemory | Tillgängligt fysiskt minne i MiB
PercentAvailableMemory | Tillgängligt fysiskt minne som en procent andel av det totala minnet
UsedMemory | Förbrukat fysiskt minne (MiB)
PercentUsedMemory | Fysiskt minne i bruk som en procent andel av det totala minnet
PagesPerSec | Total växling (Läs/skriv)
PagesReadPerSec | Sidor läses från lagrings plats (växlings fil, program fil, mappad fil osv.)
PagesWrittenPerSec | Sidor som skrivs till lagrings platsen (växlings fil, mappad fil osv.)
AvailableSwap | Oanvänt växlings utrymme (MiB)
PercentAvailableSwap | Oanvänt växlings utrymme i procent av total växling
UsedSwap | Använd växlings utrymme (MiB)
PercentUsedSwap | Använd växlings utrymme i procent av total växling

Den här klassen av mått har bara en enda instans. Attributet "Condition" har inga användbara inställningar och bör utelämnas.

### <a name="builtin-metrics-for-the-network-class"></a>inbyggda mått för nätverks klassen

Nätverks klassen för mått ger information om nätverks aktivitet på ett enskilt nätverks gränssnitt sedan start. LAD visar inte bandbredds mått som kan hämtas från värd mått.

räknare | Betydelse
------- | -------
BytesTransmitted | Totalt antal byte som har skickats sedan start
BytesReceived | Totalt antal mottagna byte sedan start
BytesTotal | Totalt antal byte som skickats eller tagits emot sedan start
PacketsTransmitted | Totalt antal paket som har skickats sedan start
PacketsReceived | Totalt antal mottagna paket sedan start
TotalRxErrors | Antal mottagna fel sedan start
TotalTxErrors | Antal överförings fel sedan start
TotalCollisions | Antal kollisioner som rapporter ATS av nätverks portarna sedan start

 Även om den här klassen är inställd, stöder inte LAD insamlade nätverks mått i alla nätverks enheter. Om du vill hämta måtten för ett speciellt gränssnitt, till exempel eth0, anger du `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>inbyggda mått för klassen fil system

Klassen system för mått innehåller information om fil Systems användning. Absoluta och procentuella värden rapporteras när de visas för en vanlig användare (inte rot).

räknare | Betydelse
------- | -------
LedigtUtrymme | Tillgängligt disk utrymme i byte
UsedSpace | Använt disk utrymme i byte
PercentFreeSpace | Ledigt utrymme i procent
PercentUsedSpace | Använt utrymme i procent
PercentFreeInodes | Procent andel oanvända noder i procent
PercentUsedInodes | Procent andel allokerade (används) noder i procent summeras i alla fil system
BytesReadPerSecond | Lästa byte per sekund
BytesWrittenPerSecond | Skrivna byte per sekund
BytesPerSecond | Lästa byte eller skrivna byte per sekund
ReadsPerSecond | Läs åtgärder per sekund
WritesPerSecond | Skriv åtgärder per sekund
TransfersPerSecond | Läs-eller Skriv åtgärder per sekund

Du kan hämta sammanställda värden för alla fil system genom att ange `"condition": "IsAggregate=True"`. Värdena för ett bestämt monterat fil system, till exempel "/mnt", kan hämtas genom att ange `"condition": 'Name="/mnt"'`. 

**Obs**: om du använder Azure-portalen i stället för JSON, är rätt villkors fält formulär namn = '/mnt '

### <a name="builtin-metrics-for-the-disk-class"></a>inbyggda mått för disk klassen

Disk klassen för mått innehåller information om disk enhets användning. Den här statistiken gäller hela enheten. Om det finns flera fil system på en enhet, är räknarna för enheten på ett effektivt sätt sammantaget över alla.

räknare | Betydelse
------- | -------
ReadsPerSecond | Läs åtgärder per sekund
WritesPerSecond | Skriv åtgärder per sekund
TransfersPerSecond | Totalt antal åtgärder per sekund
AverageReadTime | Genomsnittligt antal sekunder per Läs åtgärd
AverageWriteTime | Genomsnittligt antal sekunder per Skriv åtgärd
AverageTransferTime | Genomsnittligt antal sekunder per åtgärd
AverageDiskQueueLength | Genomsnittligt antal disk åtgärder i kö
ReadBytesPerSecond | Antal lästa byte per sekund
WriteBytesPerSecond | Antal skrivna byte per sekund
BytesPerSecond | Antal lästa byte eller skrivna per sekund

Du kan hämta sammanställda värden för alla diskar genom att ange `"condition": "IsAggregate=True"`. Ange `"condition": "Name=\\"/dev/sdf1\\""`för att hämta information om en speciell enhet (till exempel/dev/sdf1).

## <a name="installing-and-configuring-lad-30-via-cli"></a>Installera och konfigurera LAD 3,0 via CLI

Förutsatt att dina skyddade inställningar finns i filen PrivateConfig. JSON och din offentliga konfigurations information finns i PublicConfig. JSON kör du det här kommandot:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Kommandot förutsätter att du använder Azures resurs hanterings läge (arm) i Azure CLI. Om du vill konfigurera LAD för virtuella datorer med klassisk distributions modell (ASM) växlar du till "ASM"-läge (`azure config mode asm`) och utelämnar resurs grupps namnet i kommandot. Mer information finns i dokumentationen för plattforms [oberoende CLI](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Ett exempel på en LAD 3,0-konfiguration

Baserat på föregående definitioner är här ett exempel på en LAD 3,0-tilläggs konfiguration med en förklaring. Om du vill använda det här exemplet i ditt fall bör du använda ditt eget lagrings konto namn, SAS-token för konto och EventHubs SAS-token.

### <a name="privateconfigjson"></a>PrivateConfig.json

De här privata inställningarna konfigureras:

* Ett lagrings konto
* en SAS-token för ett matchande konto
* flera handfat (JsonBlob eller EventHubs med SAS-token)

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

Dessa offentliga inställningar gör att LAD:

* Ladda upp måtten för procent-processor-och användnings disk utrymme i `WADMetrics*`s tabellen
* Ladda upp meddelanden från syslog-funktionen "User" och allvarlighets grad "info" till `LinuxSyslog*`s tabellen
* Ladda upp frågeresultat för RAW-OMI (PercentProcessorTime och PercentIdleTime) till den namngivna `LinuxCPU`s tabellen
* Överför rader som lagts till i fil `/var/log/myladtestlog` till `MyLadTestLog`s tabellen

I varje enskilt fall överförs data också till:

* Azure Blob Storage (behållarens namn definieras i JsonBlob-sinken)
* EventHubs-slutpunkt (som anges i EventHubs-mottagaren)

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

`resourceId` i konfigurationen måste matcha den virtuella DATORns eller skalnings uppsättningens virtuella dator.

* Azures plattforms mått diagram och aviseringar känner till resourceId för den virtuella dator som du arbetar med. Det förväntar sig att hitta data för din virtuella dator med hjälp av Sök nyckeln resourceId.
* Om du använder automatisk skalning i Azure måste resourceId i konfigurationen för automatisk skalning matcha de resourceId som används av LAD.
* ResourceId är inbyggt i namnen på JsonBlobs som skrivits av LAD.

## <a name="view-your-data"></a>Visa dina data

Använd Azure Portal för att visa prestanda data eller ange aviseringar:

![mallar](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters` data lagras alltid i en Azure Storage tabell. Azure Storage-API: er är tillgängliga för många språk och plattformar.

Data som skickas till JsonBlob-mottagare lagras i blobbar i lagrings kontot med de [skyddade inställningarna](#protected-settings). Du kan använda BLOB-data med hjälp av alla Azure Blob Storage-API: er.

Dessutom kan du använda dessa UI-verktyg för att komma åt data i Azure Storage:

* Visual Studio-Server Explorer.
* [Microsoft Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Den här ögonblicks bilden av en Microsoft Azure Storage Explorer-session visar de genererade Azure Storage tabellerna och behållarna från ett korrekt konfigurerat LAD 3,0-tillägg på en virtuell test dator. Avbildningen stämmer inte exakt med [exemplet på LAD 3,0-konfigurationen](#an-example-lad-30-configuration).

![mallar](./media/diagnostics-linux/stg_explorer.png)

Se relevant [EventHubs-dokumentation](../../event-hubs/event-hubs-what-is-event-hubs.md) för att lära dig hur du använder meddelanden som publicerats till en EventHubs-slutpunkt.

## <a name="next-steps"></a>Nästa steg

* Skapa mått varningar i [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) för de mått som du samlar in.
* Skapa [övervaknings diagram](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) för dina mått.
* Lär dig hur du [skapar en skalnings uppsättning för virtuella datorer](../linux/tutorial-create-vmss.md) med hjälp av dina mått för att styra autoskalning.

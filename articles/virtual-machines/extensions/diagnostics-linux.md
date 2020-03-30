---
title: Azure Compute - Linux-diagnostiktillägg
description: Konfigurera Azure Linux Diagnostic Extension (LAD) för att samla in mått och logga händelser från virtuella Linux-datorer som körs i Azure.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 7a7c1af1193ba391550438229a22c4a8c116e6be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289183"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Använda Linux-diagnostiktillägget för att övervaka mått och loggar

Det här dokumentet beskriver version 3.0 och nyare av Linux Diagnostic Extension.

> [!IMPORTANT]
> Information om version 2.3 och äldre finns i [det här dokumentet](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introduktion

Linux Diagnostic Extension hjälper en användare att övervaka hälsan hos en Virtuell Linux-dator som körs på Microsoft Azure. Den har följande funktioner:

* Samlar in systemprestandamått från den virtuella datorn och lagrar dem i en viss tabell i ett angivet lagringskonto.
* Hämtar logghändelser från syslog och lagrar dem i en viss tabell i det angivna lagringskontot.
* Gör det möjligt för användare att anpassa datamått som samlas in och överförs.
* Gör det möjligt för användare att anpassa syslog-anläggningarna och allvarlighetsgraden för händelser som samlas in och överförs.
* Gör det möjligt för användare att överföra angivna loggfiler till en angiven lagringstabell.
* Stöder att skicka mått och logghändelser till godtyckliga EventHub-slutpunkter och JSON-formaterade blobbar i det angivna lagringskontot.

Det här tillägget fungerar med båda Azure-distributionsmodellerna.

## <a name="installing-the-extension-in-your-vm"></a>Installera tillägget på din virtuella dator

Du kan aktivera det här tillägget med hjälp av Azure PowerShell-cmdlets, Azure CLI-skript, ARM-mallar eller Azure-portalen. Mer information finns i [Tilläggsfunktioner](features-linux.md).

Dessa installationsanvisningar och en [nedladdningsbar exempelkonfiguration](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurerar LAD 3.0 till:

* fånga och lagra samma mått som tillhandahölls av LAD 2.3;
* samla in en användbar uppsättning filsystemmått, som är nya för LAD 3.0;
* fånga standard syslog-samlingen aktiverad av LAD 2.3;
* aktivera Azure-portalupplevelsen för att kartlägga och varna för VM-mått.

Den nedladdningsbara konfigurationen är bara ett exempel. ändra den för att passa dina egna behov.

### <a name="prerequisites"></a>Krav

* **Azure Linux Agent version 2.2.0 eller senare**. De flesta Azure VM Linux galleri avbildningar inkluderar version 2.2.7 eller senare. Kör `/usr/sbin/waagent -version` för att bekräfta den version som är installerad på den virtuella datorn. Om den virtuella datorn kör en äldre version av gästagenten följer du [dessa instruktioner](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) för att uppdatera den.
* **Azure CLI**. [Konfigurera Azure CLI-miljön](https://docs.microsoft.com/cli/azure/install-azure-cli) på din dator.
* Kommandot wget, om du inte redan har `sudo apt-get install wget`det: Kör .
* En befintlig Azure-prenumeration och ett befintligt lagringskonto i den för att lagra data.
* Listan över Linuxdistributioner som stöds finns påhttps://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Exempel på installation

Fyll i rätt värden för variablerna i det första avsnittet innan du kör:

```azurecli
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

Exempelkonfigurationen som hämtas i dessa exempel samlar in en uppsättning standarddata och skickar dem till tabelllagring. URL:en för exempelkonfigurationen och dess innehåll kan komma att ändras. I de flesta fall bör du ladda ner en kopia av portalinställningarna JSON-filen och anpassa den efter dina behov, sedan har några mallar eller automatisering du konstruerar använda din egen version av konfigurationsfilen i stället för att ladda ner den webbadressen varje gång.

#### <a name="powershell-sample"></a>PowerShell-exempel

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Uppdatera tilläggsinställningarna

När du har ändrat dina skyddade eller offentliga inställningar distribuerar du dem till den virtuella datorn genom att köra samma kommando. Om något har ändrats i inställningarna skickas de uppdaterade inställningarna till tillägget. LAD laddar om konfigurationen och startar om sig själv.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrering från tidigare versioner av tillägget

Den senaste versionen av tillägget är **3.0**. **Alla gamla versioner (2.x) är inaktuella och kan avpubliceras den 31 juli 2018 eller senare**.

> [!IMPORTANT]
> Det här tillägget introducerar bryta ändringar i konfigurationen av tillägget. En sådan ändring gjordes för att förbättra säkerheten i förlängningen; Därför kunde bakåtkompatibilitet med 2.x inte upprätthållas. Dessutom skiljer sig tillägget till tillägget från utgivaren för 2.x-versionerna.
>
> Om du vill migrera från 2.x till den nya versionen av tillägget måste du avinstallera det gamla tillägget (under det gamla utgivarens namn) och sedan installera version 3 av tillägget.

Rekommendationer:

* Installera tillägget med automatisk uppgradering av mindre versioner aktiverat.
  * På klassiska virtuella datorer för distributionsmodell anger du '3.*' som version om du installerar tillägget via Azure XPLAT CLI eller Powershell.
  * På virtuella azure Resource Manager-distributionsmodell virtuella datorer inkluderar du 'autoUpgradeMinorVersion': true' i vm-distributionsmallen.
* Använd ett nytt/annorlunda lagringskonto för LAD 3.0. Det finns flera små inkompatibiliteter mellan LAD 2.3 och LAD 3.0 som gör det besvärligt att dela ett konto:
  * LAD 3.0 lagrar syslog-händelser i en tabell med ett annat namn.
  * CounterSpecifier strängarna `builtin` för mått skiljer sig åt i LAD 3.0.

## <a name="protected-settings"></a>Skyddade inställningar

Den här uppsättningen konfigurationsinformation innehåller känslig information som ska skyddas från offentlig vy, till exempel lagringsautentiseringsuppgifter. Dessa inställningar överförs till och lagras av tillägget i krypterad form.

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
storageAccountName | Namnet på det lagringskonto där data skrivs av tillägget.
lagringAccountEndPoint | (valfritt) Slutpunkten som identifierar molnet där lagringskontot finns. Om den här inställningen saknas, LAD standardvärden till Azure offentliga molnet, `https://core.windows.net`. Om du vill använda ett lagringskonto i Azure Germany, Azure Government eller Azure China anger du det här värdet därefter.
lagringAssasToken | En [Account SAS-token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) för Blob- och Table-tjänster (`ss='bt'`), som gäller för behållare och objekt (`srt='co'`), som beviljar lägger till, skapar, listar, uppdaterar och skriver behörigheter (`sp='acluw'`). Ta *inte* med det inledande frågetecknet (?).
mdsdHttpProxy | (valfritt) HTTP-proxyinformation som behövs för att tillägget ska kunna ansluta till det angivna lagringskontot och slutpunkten.
sjunkerConfig | (valfritt) Information om alternativa destinationer till vilka mått och händelser kan levereras. De specifika detaljerna för varje datamottagare som stöds av tillägget beskrivs i de avsnitt som följer.

Om du vill hämta en SAS-token i en Resource Manager-mall använder du funktionen **listAccountSas.** En exempelmall finns i [Exempel på listfunktion](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Du kan enkelt skapa den nödvändiga SAS-token via Azure-portalen.

1. Välj det lagringskonto för allmänt ändamål som du vill att tillägget ska skriva
1. Välj "Signatur för delad åtkomst" i delen Inställningar på den vänstra menyn
1. Gör lämpliga avsnitt som tidigare beskrivits
1. Klicka på knappen "Generera SAS".

![image](./media/diagnostics-linux/make_sas.png)

Kopiera den genererade SAS-systemet till fältet storageAccountSasToken. ta bort det inledande frågetecknet ("?").

### <a name="sinksconfig"></a>sjunkerConfig

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

Det här valfria avsnittet definierar ytterligare destinationer som tillägget skickar den information som samlas in. Matrisen "sink" innehåller ett objekt för varje ytterligare datamottagare. Attributet "typ" bestämmer de andra attributen i objektet.

Element | Värde
------- | -----
namn | En sträng som används för att referera till den här diskhon någon annanstans i tilläggskonfigurationen.
typ | Den typ av diskho som definieras. Bestämmer de andra värdena (om några) i instanser av den här typen.

Version 3.0 av Linux Diagnostic Extension stöder två sink-typer: EventHub och JsonBlob.

#### <a name="the-eventhub-sink"></a>EventHub-diskbänken

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

Posten "sasURL" innehåller den fullständiga URL:en, inklusive SAS-token, för händelsehubben som data ska publiceras till. LAD kräver en SAS som namnger en princip som aktiverar skicka-anspråket. Ett exempel:

* Skapa ett namnområde för händelsehubbar som heter`contosohub`
* Skapa en händelsehubb i namnområdet som kallas`syslogmsgs`
* Skapa en princip för delad `writer` åtkomst på den namngivna händelsehubben som aktiverar skicka-anspråket

Om du skapade en SAS-vara fram till midnatt UTC den 1 januari 2018 kan sasURL-värdet vara:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Mer information om hur du genererar och hämtar information om SAS-token för eventhubbar finns på [den här webbsidan](https://docs.microsoft.com/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>JsonBlob-diskbänken

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data som dirigeras till en JsonBlob-diskho lagras i blobbar i Azure-lagring. Varje instans av LAD skapar en blob varje timme för varje sink-namn. Varje blob innehåller alltid en syntaktiskt giltig JSON-objektmatris. Nya poster läggs till atomärt i matrisen. Blobbar lagras i en behållare med samma namn som diskhon. Azure-lagringsreglerna för blob-behållarnamn gäller för namnen på JsonBlob-sänkor: mellan 3 och 63 gemener alfanumeriska ASCII-tecken eller streck.

## <a name="public-settings"></a>Offentliga inställningar

Den här strukturen innehåller olika block av inställningar som styr den information som samlas in av tillägget. Varje inställning är valfri. Om du `ladCfg`anger måste `StorageAccount`du också ange .

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
StorageAccount | Namnet på det lagringskonto där data skrivs av tillägget. Måste vara samma namn som anges i de [skyddade inställningarna](#protected-settings).
mdsdHttpProxy | (valfritt) Samma som i de [skyddade inställningarna](#protected-settings). Det offentliga värdet åsidosätts av det privata värdet, om värdet anges. Placera proxyinställningar som innehåller en hemlighet, till exempel ett lösenord, i [de skyddade inställningarna](#protected-settings).

De återstående elementen beskrivs i detalj i följande avsnitt.

### <a name="ladcfg"></a>ladCfg (på ett sätt)

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

Den här valfria strukturen styr insamlingen av mått och loggar för leverans till Azure Metrics-tjänsten och till andra datamottagare. Du måste `performanceCounters` ange `syslogEvents` antingen eller eller båda. Du måste `metrics` ange strukturen.

Element | Värde
------- | -----
eventVolume | (valfritt) Styr antalet partitioner som skapats i lagringstabellen. Måste vara `"Large"`en `"Medium"`av `"Small"`, eller . Om inget anges är `"Medium"`standardvärdet .
sampleRateInSeconds | (valfritt) Standardintervallet mellan insamling av råa (oaggerade) mått. Den minsta samplingsfrekvensen som stöds är 15 sekunder. Om inget anges är `15`standardvärdet .

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
resourceId | Azure Resource Manager-resurs-ID för den virtuella datorn eller för den virtuella datorns skalningsuppsättning som den virtuella datorn tillhör. Den här inställningen måste också anges om någon JsonBlob-diskho används i konfigurationen.
schemalagdtransferperiod | Den frekvens med vilken aggregerade mått ska beräknas och överföras till Azure-mått, uttryckt som ett IS 8601-tidsintervall. Den minsta överföringsperioden är 60 sekunder, det vill än PT1M. Du måste ange minst en schemalagdTransferPeriod.

Exempel på de mått som anges i avsnittet performanceCounters samlas in var 15:e sekund eller med den samplingsfrekvens som uttryckligen definierats för räknaren. Om flera schemalagdaTransferPeriod-frekvenser visas (som i exemplet) beräknas varje aggregering oberoende av varandra.

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

Det här valfria avsnittet styr insamlingen av mått. Råprover aggregeras för varje [schemalagdTransferPeriod](#metrics) för att producera dessa värden:

* medelvärde
* min
* max
* senast insamlad värde
* antal råprover som används för att beräkna

Element | Värde
------- | -----
Sjunker | (valfritt) En kommaavgränsad lista med namn på sänkor som LAD skickar aggregerade måttresultat till. Alla aggregerade mått publiceras i varje listad diskho. Se [sinksConfig](#sinksconfig). Exempel: `"EHsink1, myjsonsink"`.
typ | Identifierar den faktiska providern för måttet.
klass | Tillsammans med "räknare" identifierar du det specifika måttet i leverantörens namnområde.
räknare | Tillsammans med "klass" identifierar du det specifika måttet i leverantörens namnområde.
counterSpecifier (motSpecifier) | Identifierar det specifika måttet i Azure Metrics-namnområdet.
Villkor | (valfritt) Markerar en specifik instans av objektet som måttet gäller för eller väljer aggregering över alla instanser av objektet. Mer information finns `builtin` i måttdefinitionerna.
Samplerate | IS 8601-intervall som anger hastigheten med vilken råprover för det här måttet samlas in. Om inte inställt anges samlingsintervallet med värdet [för sampleRateInSeconds](#ladcfg). Den kortaste samplingsfrekvensen som stöds är 15 sekunder (PT15S).
unit | Bör vara en av dessa strängar: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Millisecond". Definierar enheten för måttet. Konsumenterna av insamlade data förväntar sig att de insamlade datavärdena matchar den här enheten. LAD ignorerar det här fältet.
displayName | Etiketten (på det språk som anges av den associerade språkinställningen) som ska kopplas till dessa data i Azure Metrics. LAD ignorerar det här fältet.

CounterSpecifier är en godtycklig identifierare. Konsumenter av mått, som Azure-portalens kart- och aviseringsfunktion, använder counterSpecifier som "nyckel" som identifierar ett mått eller en instans av ett mått. För `builtin` mått rekommenderar vi att du använder counterSpecifier-värden som börjar med `/builtin/`. Om du samlar in en viss instans av ett mått rekommenderar vi att du kopplar identifieraren för instansen till counterSpecifier-värdet. Några exempel:

* `/builtin/Processor/PercentIdleTime`- Inaktiv tid i genomsnitt för alla virtuella processorer
* `/builtin/Disk/FreeSpace(/mnt)`- Ledigt utrymme för filsystemet /mnt
* `/builtin/Disk/FreeSpace`- Fritt utrymme i genomsnitt över alla monterade filsystem

Varken LAD eller Azure-portalen förväntar sig att counterSpecifier-värdet matchar något mönster. Var konsekvent i hur du konstruerar motSpecifiervärden.

När du `performanceCounters`anger skriver LAD alltid data till en tabell i Azure-lagring. Du kan ha samma data skrivna till JSON-blobbar och/eller eventhubbar, men du kan inte inaktivera lagring av data till en tabell. Alla instanser av diagnostiktillägget som konfigurerats för att använda samma lagringskontonamn och slutpunkt lägger till sina mått och loggar i samma tabell. Om för många virtuella datorer skriver till samma tabellpartition kan Azure begränsa skrivningar till den partitionen. Inställningen eventVolume gör att posterna sprids över 1 (Small), 10 (Medium) eller 100 (Large) olika partitioner. Vanligtvis är "Medium" tillräckligt för att säkerställa att trafiken inte begränsas. Azure Metrics-funktionen i Azure-portalen använder data i den här tabellen för att producera diagram eller utlösa aviseringar. Tabellnamnet är sammanfogningen av dessa strängar:

* `WADMetrics`
* Den "schemalagdaTransferPeriod" för de aggregerade värden som lagras i tabellen
* `P10DV2S`
* Ett datum, i form av "YYYYMMDD", som ändras var 10:e dag

Exempel `WADMetricsPT1HP10DV2S20170410` är `WADMetricsPT1MP10DV2S20170609`och .

#### <a name="syslogevents"></a>syslogEvent

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

Det här valfria avsnittet styr insamlingen av logghändelser från syslog. Om avsnittet utelämnas fångas inte syslog-händelser alls.

SyslogEventConfiguration-samlingen har en post för varje syslog-anläggning av intresse. Om minSeverity är "NONE" för en viss anläggning, eller om den anläggningen inte visas i elementet alls, fångas inga händelser från den anläggningen.

Element | Värde
------- | -----
Sjunker | En kommaavgränsad lista med namn på sänkor som enskilda logghändelser publiceras till. Alla logghändelser som matchar begränsningarna i syslogEventKonfiguration publiceras i varje listad diskho. Exempel: "EHforsyslog"
facilityName (anläggningsnamn) | Ett syslog-anläggningsnamn (till exempel "LOG\_USER" eller "LOG\_LOCAL0"). Se avsnittet "anläggning" på [syslogmanssidan](http://man7.org/linux/man-pages/man3/syslog.3.html) för hela listan.
minSeverity | En systemlog allvarlighetsgrad (till exempel "LOG\_\_ERR" eller "LOG INFO"). Se avsnittet "nivå" på [syslogmanssidan](http://man7.org/linux/man-pages/man3/syslog.3.html) för hela listan. Tillägget fångar händelser som skickas till anläggningen på eller över den angivna nivån.

När du `syslogEvents`anger skriver LAD alltid data till en tabell i Azure-lagring. Du kan ha samma data skrivna till JSON-blobbar och/eller eventhubbar, men du kan inte inaktivera lagring av data till en tabell. Partitioneringsbeteendet för den här `performanceCounters`tabellen är detsamma som beskrivs för . Tabellnamnet är sammanfogningen av dessa strängar:

* `LinuxSyslog`
* Ett datum, i form av "YYYYMMDD", som ändras var 10:e dag

Exempel `LinuxSyslog20170410` är `LinuxSyslog20170609`och .

### <a name="perfcfg"></a>perfCfg (perfCfg)

Det här valfria avsnittet styr körningen av godtyckliga [OMI-frågor.](https://github.com/Microsoft/omi)

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
namnområde | (valfritt) Det OMI-namnområde där frågan ska köras. Om det inte anges är standardvärdet "root/scx", implementerat av [System Center Cross-platform Providers](https://github.com/Microsoft/SCXcore).
DocumentDB | OMI-frågan som ska köras.
tabell | (valfritt) Azure-lagringstabellen i det angivna lagringskontot (se [Skyddade inställningar](#protected-settings)).
frequency | (valfritt) Antalet sekunder mellan körningen av frågan. Standardvärdet är 300 (5 minuter). minimivärdet är 15 sekunder.
Sjunker | (valfritt) En kommaavgränsad lista med namn på ytterligare sänkor som råprovmåttresultat ska publiceras till. Ingen aggregering av dessa råa exempel beräknas av tillägget eller av Azure Metrics.

Antingen "tabell" eller "sänkor", eller båda, måste anges.

### <a name="filelogs"></a>fileLogs

Styr insamlingen av loggfiler. LAD fångar nya textrader när de skrivs till filen och skriver dem till tabellrader och/eller angivna sänkor (JsonBlob eller EventHub).

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
file | Loggfilens fullständiga sökvägsnamn som ska bevakas och fångas. Sökvägen måste namnge en enskild fil. Den kan inte namnge en katalog eller innehålla jokertecken.
tabell | (valfritt) Azure-lagringstabellen, i det angivna lagringskontot (som anges i den skyddade konfigurationen), där nya rader från "svansen" av filen skrivs.
Sjunker | (valfritt) En kommaavgränsad lista med namn på ytterligare sänkor som loggrader skickade till.

Antingen "tabell" eller "sänkor", eller båda, måste anges.

## <a name="metrics-supported-by-the-builtin-provider"></a>Mått som stöds av den inbyggda providern

Den inbyggda måttprovidern är en källa till mått som är mest intressant för en bred uppsättning användare. Dessa mått delas in i fem breda klasser:

* Processor
* Minne
* Nätverk
* Filsystem
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>inbyggda mätvärden för klassen Processor

Processorklassen med mått innehåller information om processoranvändning i den virtuella datorn. När du aggregerar procentsatser är resultatet genomsnittet för alla processorer. I en två-vCPU VM, om en vCPU var 100% upptagen och den andra var 100% inaktiv, den rapporterade PercentIdleTime skulle vara 50. Om varje vCPU var 50% upptagen för samma period, skulle det rapporterade resultatet också vara 50. I en virtuell virtuell dator med fyra vCPU, med en vCPU 100% upptagen och de andra inaktiva, skulle den rapporterade PercentIdleTime vara 75.

räknare | Betydelse
------- | -------
ProcentIdleTime | Procentandel av tiden under aggregeringsfönstret som processorer körde kernel-inaktiv loop
ProcentProcessorTime | Procentandel av tiden som kör en icke-inaktiv tråd
ProcentIOWaitTime | Procentandel av tiden som väntar på att I/O-åtgärder ska slutföras
ProcentInterruptTime | Procentandel av tiden för att utföra maskinvaru-/programvaruavbrott och DPC (uppskjutna proceduranrop)
ProcentAnvändareTime | Av ledig tid under aggregeringsfönstret, procentandelen tid som spenderas i användaren mer vid normal prioritet
ProcentNiceTime | Av ledig tid, den procentandel som spenderas på sänkt (trevlig) prioritet
ProcentPrivilegedTime | Av ledig tid har procentandelen som spenderats i privilegierat (kernel-läge)

De fyra första räknarna bör summeras till 100 procent. De tre sista räknarna uppgår också till 100 procent. De delar upp summan av PercentProcessorTime, PercentIOWaitTime och PercentInterruptTime.

Om du vill få ett enda mått `"condition": "IsAggregate=TRUE"`som aggregerats för alla processorer anger du . Om du vill hämta ett mått för en viss processor, till exempel `"condition": "Name=\\"1\\""`den andra logiska processorn för en virtuell dator med fyra vCPU, anger du . Logiska processornummer finns `[0..n-1]`i intervallet .

### <a name="builtin-metrics-for-the-memory-class"></a>inbyggda mätvärden för klassen Memory

Minnesklassen med mått innehåller information om minnesanvändning, växling och byte.

räknare | Betydelse
------- | -------
Tillgängligt Minne | Tillgängligt fysiskt minne i MiB
ProcentTillgängligt Minne | Tillgängligt fysiskt minne i procent av det totala minnet
UsedMemory | In-use fysiskt minne (MiB)
ProcentAnvändarnaMinne | In-use fysiskt minne som en procent av det totala minnet
SidorPerSec | Totalt personsökning (läs/skrivning)
SidorReadPerSec | Sidor som läss från stödarkivet (växlingsfil, programfil, mappad fil osv.)
PagesWrittenPerSec | Sidor skrivna till stödarkivet (växlingsfil, mappad fil osv.)
AvailableSwap | Oanvänt växlingsutrymme (MiB)
ProcentTillgängligWap | Oanvänt växlingsutrymme i procent av den totala swappen
UsedSwap | Växlingsutrymme vid användning (MiB)
ProcentUsedSwap | Swaputrymme i procent av den totala

Den här klassen av mått har bara en enda instans. Attributet "condition" har inga användbara inställningar och bör utelämnas.

### <a name="builtin-metrics-for-the-network-class"></a>inbyggda mätvärden för klassen Network

Klassen Network of metrics innehåller information om nätverksaktivitet i ett enskilt nätverksgränssnitt sedan start. LAD visar inte bandbreddsmått, som kan hämtas från värdmått.

räknare | Betydelse
------- | -------
BytesÖverförs | Totalt antal antal antal som skickats sedan start
ByteKom med | Totalt antal mottagna byte sedan start
BytesTotal | Totalt antal antal antal som skickats eller tagits emot sedan start
PaketÖversatt | Totalt antal paket som skickats sedan start
PaketKomreceived | Totalt antal paket som tagits emot sedan start
TotaltRxErrors | Antal mottagningsfel sedan start
TotalTxErrors | Antal överföringsfel sedan start
Totalt Antal | Antal kollisioner som rapporterats av nätverksportarna sedan start

 Även om den här klassen instanseras stöder LAD inte att samla in nätverksmått som aggregerats på alla nätverksenheter. Om du vill hämta måtten för ett visst `"condition": "InstanceID=\\"eth0\\""`gränssnitt, till exempel eth0, anger du .

### <a name="builtin-metrics-for-the-filesystem-class"></a>inbyggda mått för klassen Filesystem

Filsystemklassen med mått innehåller information om filsystemanvändning. Absoluta och procentuella värden rapporteras eftersom de skulle visas för en vanlig användare (inte rot).

räknare | Betydelse
------- | -------
LedigtUtrymme | Tillgängligt diskutrymme i byte
UsedSpace (UsedSpace) | Använt diskutrymme i byte
ProcentFritt utrymme | Procent ledigt utrymme
ProcentAnvändarutrymme | Använt utrymme för procentandel
ProcentFriinoder | Procentandel oanvända innoder
ProcentAnvändare | Procentandel allokerade (används) innoder summerade i alla filsystem
BytesReadPerSecond | Lästa byte per sekund
BytesWrittenPerSecond | Byte skrivna per sekund
BytesPerSecond | Lästa eller skrivna byte per sekund
LäserPerSecond | Läs åtgärder per sekund
SkriverPerSecond | Skrivåtgärder per sekund
ÖverföringarPerSecond | Läs- eller skrivåtgärder per sekund

Aggregerade värden i alla filsystem kan erhållas genom att ställa in `"condition": "IsAggregate=True"`. Värden för ett specifikt monterat filsystem, till exempel "/mnt", kan erhållas genom att ställa in `"condition": 'Name="/mnt"'`. 

**OM**du använder Azure Portal i stället för JSON är det korrekta villkorsfältformuläret Name='/mnt'

### <a name="builtin-metrics-for-the-disk-class"></a>inbyggda mått för klassen Disk

Klassen Disk med mått innehåller information om användning av diskenheter. Statistiken gäller för hela enheten. Om det finns flera filsystem på en enhet aggregeras räknarna för den enheten effektivt över dem alla.

räknare | Betydelse
------- | -------
LäserPerSecond | Läs åtgärder per sekund
SkriverPerSecond | Skrivåtgärder per sekund
ÖverföringarPerSecond | Totalt antal operationer per sekund
Genomsnittliglädstid | Genomsnittligt antal sekunder per läsåtgärd
AverageWriteTime | Genomsnittliga sekunder per skrivåtgärd
Genomsnittligtransfertime | Genomsnittliga sekunder per operation
Genomsnittligt DiskqueueLängd | Genomsnittligt antal diskåtgärder i kö
ReadBytesPerSecond | Antal lästa byte per sekund
WriteBytesPerSecond | Antal skrivna byte per sekund
BytesPerSecond | Antal lästa eller skrivna byte per sekund

Aggregerade värden på alla diskar kan erhållas genom att ställa in `"condition": "IsAggregate=True"`. Om du vill hämta information för en viss enhet (till exempel /dev/sdf1) anger du `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Installera och konfigurera LAD 3.0 via CLI

Om du antar att dina skyddade inställningar finns i filen PrivateConfig.json och din offentliga konfigurationsinformation finns i PublicConfig.json kör du det här kommandot:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Kommandot förutsätter att du använder Azure Resource Management-läget (arm) i Azure CLI. Om du vill konfigurera LAD för virtuella asm-datorer (Classic`azure config mode asm`Deployment Model) växlar du till "asm"-läge ( ) och utelämnar resursgruppsnamnet i kommandot. Mer information finns i [CLI-dokumentationen över flera plattformar](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Ett exempel PÅ LAD 3.0-konfiguration

Baserat på föregående definitioner, här är ett exempel LAD 3.0 förlängning konfiguration med någon förklaring. Om du vill använda det här exemplet i ditt ärende bör du använda ditt eget lagringskontonamn, sas-token för konto och EventHubs SAS-token.

### <a name="privateconfigjson"></a>PrivateConfig.json

Dessa privata inställningar konfigurerar:

* ett lagringskonto
* ett matchande konto SAS-token
* flera sänkor (JsonBlob eller EventHubs med SAS-token)

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

### <a name="publicconfigjson"></a>OffentligaConfig.json

De här offentliga inställningarna gör att LAD:

* Ladda upp mått för procentprocessortid och använt `WADMetrics*` diskutrymme till tabellen
* Ladda upp meddelanden från syslog-faciliteten "användare" `LinuxSyslog*` och allvarlighetsgrad "info" till tabellen
* Ladda upp råa OMI-frågeresultat (PercentProcessorTime och `LinuxCPU` PercentIdleTime) till den namngivna tabellen
* Ladda upp bifogade `/var/log/myladtestlog` rader `MyLadTestLog` i filen till tabellen

I varje enskilt fall laddas data också upp till:

* Azure Blob-lagring (behållarnamnet är som definierat i JsonBlob-diskhon)
* EventHubs-slutpunkt (som anges i EventHubs-diskbänken)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
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

I `resourceId` konfigurationen måste matcha den för den virtuella datorn eller den virtuella datorn skala uppsättning.

* Azure-plattformsmått som kartlägger och varnar känner till resursen För den virtuella datorn som du arbetar med. Den förväntar sig att hitta data för din virtuella dator med hjälp av resourceId uppslagsnyckeln.
* Om du använder automatisk Azure-skalning måste resourceId i konfigurationen för automatisk skalning matcha de resourceId som används av LAD.
* ResourceId är inbyggd i namnen på JsonBlobs skriven av LAD.

## <a name="view-your-data"></a>Visa dina data

Använd Azure-portalen för att visa prestandadata eller ställa in aviseringar:

![image](./media/diagnostics-linux/graph_metrics.png)

Data `performanceCounters` lagras alltid i en Azure Storage-tabell. Azure Storage API:er är tillgängliga för många språk och plattformar.

Data som skickas till JsonBlob-diskhoar lagras i blobbar i lagringskontot som [namnges](#protected-settings)i skyddade inställningar . Du kan använda blob-data med azure blob Storage API:er.

Dessutom kan du använda dessa gränssnittsverktyg för att komma åt data i Azure Storage:

* Visual Studio Server Explorer.
* [Microsoft Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Azure Lagringsutforskaren").

Den här ögonblicksbilden av en Microsoft Azure Storage Explorer-session visar genererade Azure Storage-tabeller och behållare från ett korrekt konfigurerat LAD 3.0-tillägg på en test-VM. Avbildningen matchar inte exakt [med exempelposs 3.0-konfigurationen](#an-example-lad-30-configuration).

![image](./media/diagnostics-linux/stg_explorer.png)

Se relevant [EventHubs-dokumentation](../../event-hubs/event-hubs-what-is-event-hubs.md) om du vill lära dig hur du använder meddelanden som publicerats till en EventHubs-slutpunkt.

## <a name="next-steps"></a>Nästa steg

* Skapa måttaviseringar i [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) för de mått du samlar in.
* Skapa [övervakningsdiagram](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) för dina mått.
* Lär dig hur du [skapar en skalningsuppsättning](../linux/tutorial-create-vmss.md) för virtuella datorer med hjälp av dina mått för att styra automatisk skalning.

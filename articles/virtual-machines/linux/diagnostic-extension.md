---
title: "Azure Compute - diagnostik för Linux-tillägg | Microsoft Docs"
description: "Så här konfigurerar du den Azure Linux diagnostiska tillägg (LAD) för att samla in mått och loggar händelser från virtuella Linux-datorer körs i Azure."
services: virtual-machines-linux
author: jasonzio
manager: anandram
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: jasonzio
ms.openlocfilehash: 1eae6d302827c977b9258174dec68fd8f3009a11
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2018
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Använd Linux diagnostiska tillägget för att övervaka mått och loggar

Det här dokumentet beskriver version 3.0 och senare av diagnostiska tillägget Linux.

> [!IMPORTANT]
> Information om version 2.3 och äldre finns [dokumentet](./classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introduktion

Tillägget Linux diagnostiska hjälper användare-övervaka hälsotillståndet för en Linux-VM som körs på Microsoft Azure. Det har följande funktioner:

* Samlar in system prestandamått från den virtuella datorn och lagrar dem i en viss tabell i ett avsedda storage-konto.
* Hämtar händelser från syslog och lagrar dem i en viss tabell i det angivna lagringskontot.
* Kan du anpassa de mätvärden för data som samlas in och överföra.
* Kan du anpassa syslog verksamhet och allvarlighetsgrader för händelser som samlas in och överföra.
* Gör att användarna kan ladda upp angivna loggfiler till en tabell med avsedda lagring.
* Har stöd för mått och logga händelser skickades till valfri EventHub-slutpunkter och JSON-formaterad blobbar i avsedda storage-konto.

Det här tillägget fungerar med båda modellerna i Azure-distribution.

## <a name="installing-the-extension-in-your-vm"></a>Installera tillägget på den virtuella datorn

Du kan aktivera det här tillägget med hjälp av Azure PowerShell-cmdlets, Azure CLI-skript eller mallar för Azure-distribution. Mer information finns i [tillägg funktioner](./extensions-features.md).

Azure-portalen kan inte användas för att aktivera eller konfigurera LAD 3.0. Istället installerar och konfigurerar version 2.3. Azure portal diagram och aviseringar kan du arbeta med data från båda versionerna av tillägget.

Dessa instruktioner för installation och en [nedladdningsbara exempelkonfiguration](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurera LAD 3.0 till:

* samla in och lagra samma mått som tillhandahålls av LAD 2.3;
* avbilda en användbar uppsättning filen system statistik, och nya att 3.0 LAD;
* Avbilda syslog standardsamling aktiveras med LAD 2.3;
* Aktivera Azure portal upplevelsen för diagram och Varna vid VM mått.

Nedladdningsbar konfigurationen är bara ett exempel. Anpassa efter dina behov.

### <a name="prerequisites"></a>Förutsättningar

* **Azure Linux-agentens version 2.2.0 eller senare**. De flesta Azure VM Linux galleriavbildningar inkluderar version 2.2.7 eller senare. Kör `/usr/sbin/waagent -version` för att bekräfta den version som installeras på den virtuella datorn. Om den virtuella datorn kör en äldre version av gästagenten, Följ [instruktionerna](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) att uppdatera den.
* **Azure CLI**. [Konfigurera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) miljö på din dator.
* Kommandot wget om du inte redan har det: kör `sudo apt-get install wget`.
* En befintlig Azure-prenumeration och ett befintligt lagringskonto i den för att lagra data.

### <a name="sample-installation"></a>Exempel installation

Fyll i rätt parametrar på de tre första raderna och sedan köra skriptet som rot:

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
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 9999-12-31T23:59Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

URL till exempel konfigurationen och dess innehåll kan ändras. Hämta en kopia av portalinställningar JSON-fil och anpassa den efter dina behov. Alla mallar eller automatisering som du bör använda din egen kopia, i stället för att hämta URL: en varje gång.

### <a name="updating-the-extension-settings"></a>Uppdaterar tilläggsinställningar

När du har ändrat din skyddade eller inställningar för offentliga, distribuerar du dem till den virtuella datorn genom att köra samma kommando. Om något ändras i inställningarna, skickas de uppdaterade inställningarna till tillägget. LAD läser konfigurationen och startar om sig själv.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrering från tidigare versioner av tillägget

Den senaste versionen av tillägget är **3.0**. **Eventuella gamla versioner (2.x) är föråldrade och kan vara opublicerad på eller efter den 31 juli 2018**.

> [!IMPORTANT]
> Det här tillägget introducerar viktiga förändringar i konfigurationen av filnamnstillägget. En ändring har gjorts för att förbättra säkerheten för tillägget; Därför kan bakåtkompatibilitet kompatibilitet med 2.x inte upprätthållas. Dessutom skiljer tillägget utgivaren för detta tillägg sig utgivaren för 2.x-versionerna.
>
> Om du vill migrera från 2.x till den nya versionen av tillägget måste du avinstallera det gamla tillägget (under gamla utgivarens namn) och sedan installera version 3 av tillägget.

Rekommendationer:

* Installera tillägget med automatisk mindre versionsuppgradering aktiverad.
  * Ange '3.*' som versionen på klassiska distributionsmodellen virtuella datorer om du installerar tillägget via Azure XPLAT CLI eller Powershell.
  * Modellen virtuella datorer på Azure Resource Manager-distribution kan du ange ' ”autoUpgradeMinorVersion”: true' i mallen för distribution av virtuell dator.
* Använd en ny/annat lagringskonto för LAD 3.0. Det finns flera små inkonsekvenser mellan LAD 2.3 och LAD 3.0 som gör att dela ett program som krånglar konto:
  * LAD 3.0 lagrar syslog-händelser i en tabell med ett annat namn.
  * CounterSpecifier strängarna för `builtin` mått skiljer sig åt i LAD 3.0.

## <a name="protected-settings"></a>Skyddade inställningar

Denna uppsättning konfigurationsinformation innehåller känslig information som borde vara skyddad från den gemensamma vyn, till exempel lagring autentiseringsuppgifter. De här inställningarna överförs till och lagras av tillägget i krypterad form.

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
storageAccountName | Namnet på det lagringskonto där data har skrivits av tillägget.
storageAccountEndPoint | (valfritt) Slutpunkten som identifierar det moln där lagringskontot finns. Om den här inställningen inte finns som standard LAD Azure offentliga moln, `https://core.windows.net`. Om du vill använda ett lagringskonto i Azure Tyskland ange Azure Government eller Azure Kina, värdet i enlighet med detta.
storageAccountSasToken | En [konto-SAS-token](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) för Blob- och tjänster (`ss='bt'`), gäller för behållare och objekt (`srt='co'`), vilket ger lägga till, skapa, visa, uppdatera och skrivbehörighet (`sp='acluw'`). Gör *inte* omfattar det inledande frågetecknet (?).
mdsdHttpProxy | (valfritt) HTTP-proxyinformation som behövs för att aktivera tillägget att ansluta till det angivna lagringskontot och slutpunkt.
sinksConfig | (valfritt) Information om alternativa mål som mått och händelser levereras. Specifik information om varje mottagare för data som stöds av tillägget beskrivs i avsnitten som följer.


> [!NOTE]
> När du distribuerar tillägget med en Distributionsmall av Azure måste storage-konto och SAS-token skapas i förväg och sedan skickas till mallen. Du kan inte distribuera en VM, storage-konto och konfigurera tillägget i samma mall. Skapa en SAS-token i en mall stöds inte för närvarande.

Du kan enkelt skapa nödvändiga SAS-token via Azure-portalen.

1. Välj Allmänt lagringskonto som du vill att tillägget ska kunna skriva
1. Välj ”delad åtkomstsignatur” från den vänstra menyn Inställningar tillhör
1. Gör relevanta avsnitt enligt beskrivningen ovan
1. Klicka på knappen ”Generera SAS”.

![Bild](./media/diagnostic-extension/make_sas.png)

Kopiera den genererade SAS i fältet storageAccountSasToken; ta bort det inledande frågetecknet (””?).

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

Det här valfria avsnittet definierar ytterligare mål som tillägget skickar den information som samlas in. Matrisen ”sink” innehåller ett objekt för varje ytterligare data sink. Attributet ”typ” anger andra attribut i objektet.

Element | Värde
------- | -----
namn | En sträng som används för att referera till den här sink någon annanstans i tilläggets konfiguration.
typ | Typ av mottagare som definieras. Anger de andra värdena (eventuella) i instanser av den här typen.

Version 3.0 av Linux diagnostiska tillägget stöder två typer av mottagare: EventHub och JsonBlob.

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

”SasURL”-post innehåller en fullständig URL, inklusive SAS-token för Händelsehubben som data ska publiceras. LAD kräver en SAS namngivning av en princip som gör att skicka ett anspråk. Ett exempel:

* Skapa ett namnområde för Händelsehubbar kallas `contosohub`
* Skapa en Händelsehubb i namnområdet som kallas `syslogmsgs`
* Skapa en princip för delad åtkomst på Händelsehubben med namnet `writer` som gör att skicka anspråk

Om du har skapat en SAS bra till midnatt UTC på den 1 januari 2018 kan sasURL värdet vara:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Mer information om hur du genererar SAS-token för Händelsehubbar finns [webbsidan](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>Sink JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data som riktas mot en JsonBlob sink lagras i BLOB i Azure-lagring. Varje instans av LAD skapar en blob varje timme för varje mottagare namn. Varje blobb innehåller alltid en syntaktiskt giltig JSON-matris med objektet. Nya poster läggs automatiskt till i matrisen. Blobbar som lagras i en behållare med samma namn som sink. Azure storage-regler för blob-behållarnamn gäller för namnen på JsonBlob sänkor: mellan 3 och 63 gemena alfanumeriska ASCII-tecken eller bindestreck.

## <a name="public-settings"></a>Inställningar för offentliga

Den här strukturen innehåller olika block med inställningar som styr den information som samlas in av tillägget. Varje inställningen är valfri. Om du anger `ladCfg`, måste du också ange `StorageAccount`.

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
StorageAccount | Namnet på det lagringskonto där data har skrivits av tillägget. Måste vara samma namn som har angetts i den [skyddade inställningarna](#protected-settings).
mdsdHttpProxy | (valfritt) Samma som i den [skyddade inställningarna](#protected-settings). Offentliga värdet åsidosätts av det privata värdet om ange. Placera proxyinställningar som innehåller en hemlighet, till exempel ett lösenord i den [skyddade inställningarna](#protected-settings).

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

Den här valfria struktur kontroller egenskaperna för insamling av loggar för leverans av tjänsten Azure mått och andra data och mått. Du måste ange antingen `performanceCounters` eller `syslogEvents` eller båda. Du måste ange den `metrics` struktur.

Element | Värde
------- | -----
eventVolume | (valfritt) Styr antalet partitioner som skapas i lagringstabellen. Måste vara något av `"Large"`, `"Medium"`, eller `"Small"`. Om inget anges är standardvärdet `"Medium"`.
sampleRateInSeconds | (valfritt) Standardintervallet mellan samling raw (unaggregated) mått. Minsta stöds samplingsfrekvens är 15 sekunder. Om inget anges är standardvärdet `15`.

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
resourceId | Azure Resource Manager resurs-ID för den virtuella datorn eller virtuella datorns skaluppsättning angetts som den virtuella datorn tillhör. Den här inställningen måste vara anges också om alla JsonBlob sink används i konfigurationen.
scheduledTransferPeriod | Frekvensen som sammanställd är beräknad och överförs till Azure statistik, uttryckt som en är 8601 tidsintervall. Minsta överföringsperioden är 60 sekunder, det vill säga PT1M. Du måste ange minst en scheduledTransferPeriod.

Exempel på mått som anges i avsnittet prestandaräknarna samlas var 15: e sekund eller på exemplet Betygsätt explicit definierats för räknaren. Om flera scheduledTransferPeriod frekvenser visas (som i exempel), beräknas varje aggregerings oberoende av varandra.

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

Det här valfria avsnittet kontroll över insamlingen av mått. Rådata exempel aggregeras för varje [scheduledTransferPeriod](#metrics) att generera dessa värden:

* medelvärde
* min
* max
* senaste samlas in värdet
* Antal raw exempel som används för att beräkna mängden

Element | Värde
------- | -----
egenskaperna | (valfritt) En kommaavgränsad lista över namnen på sänkor till vilka LAD skickar samman mått resultat. Alla sammanställda mått publiceras till varje listad mottagare. Se [sinksConfig](#sinksconfig). Exempel: `"EHsink1, myjsonsink"`.
typ | Identifierar den faktiska providern av måttet.
klass | Identifierar den specifika måtten i leverantörens namnrymd tillsammans med ”räknaren”.
Räknaren | Identifierar den specifika måtten i leverantörens namnrymd tillsammans med ”class”.
counterSpecifier | Identifierar specifikt mått i Azure mått-namnområdet.
tillstånd | (valfritt) Väljer en specifik instans av objektet som måttet gäller eller väljer sammanställning över alla instanser av objektet. Mer information finns i [ `builtin` måttdefinitioner](#metrics-supported-by-builtin).
sampleRate | ÄR 8601 tidsintervall som anger den hastighet som rådata prover för det här måttet har samlats in. Om inte ange intervall för insamling anges med värdet [sampleRateInSeconds](#ladcfg). Kortaste stöds samplingsfrekvens är 15 sekunder (PT15S).
enhet | Måste vara ett av de här strängarna: ”antal”, ”byte”, ”sekunder”, ”procent”, ”CountPerSecond”, ”BytesPerSecond”, ”millisekunder”. Definierar enheten för måttet. Konsumenter av insamlade data förväntas värdena insamlade data för att matcha den här enheten. LAD ignorerar det här fältet.
displayName | Etikett (på det språk som anges av den associerade språkinställningen) som ska kopplas till dessa data i Azure mått. LAD ignorerar det här fältet.

CounterSpecifier är ett valfritt ID. Konsumenter av statistik, som Azure portal diagram och aviseringar funktion använder counterSpecifier som ”nyckeln” som identifierar ett mått eller en instans av ett mått. För `builtin` statistik, rekommenderar vi du använder counterSpecifier värden som börjar med `/builtin/`. Om du samlar in en specifik instans av ett mått, rekommenderar vi du bifoga identifierare för instansen med värdet counterSpecifier. Några exempel:

* `/builtin/Processor/PercentIdleTime` -Inaktivitetstid var i genomsnitt över alla vCPUs
* `/builtin/Disk/FreeSpace(/mnt)` -Ledigt utrymme för /mnt filsystemet
* `/builtin/Disk/FreeSpace` -Ledigt utrymme som var i genomsnitt över alla monterade filsystem

Förväntar sig counterSpecifier-värde som matchar alla mönster varken LAD eller Azure-portalen. Vara konsekvent i hur du skapar counterSpecifier värden.

När du anger `performanceCounters`, LAD alltid skriver data till en tabell i Azure-lagring. Du kan ha samma data skrivs till JSON-blobbar och/eller Händelsehubbar, men du kan inte inaktivera lagra data i en tabell. Alla instanser av diagnostiska tillägget konfigurerats för att använda samma lagringskontots namn och slutpunkten lägga till sina mått och loggar i samma tabell. Om för många virtuella datorer skriver till samma partition i tabellen, Azure kan begränsa skrivningar till den aktuella partitionen. EventVolume-inställningen gör att poster ska spridas över 1 (liten), 10 (medel) eller 100 (stor) olika partitioner. Vanligtvis är ”medel” tillräckligt för att säkerställa att trafiken inte begränsas. Funktionen Azure mätvärden i Azure-portalen använder data i den här tabellen för att skapa diagram eller att utlösa varningar. Tabellnamnet är sammanfogning av dessa strängar:

* `WADMetrics`
* ”ScheduledTransferPeriod” för de sammanställda värden som lagras i tabellen
* `P10DV2S`
* Ett datum i formatet ”ååååmmdd” som ändrar var 10: e dag

Exempel inkluderar `WADMetricsPT1HP10DV2S20170410` och `WADMetricsPT1MP10DV2S20170609`.

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

Det här valfria avsnittet styr insamling av händelser från syslog. Syslog-händelser fångas inte alls om avsnittet utelämnas.

Samlingen syslogEventConfiguration har en post för varje syslog-funktion av intresse. Om minSeverity är ”NONE” för en viss funktion, eller om den anläggningen inte visas i elementet alls, fångas inga händelser från den anläggningen.

Element | Värde
------- | -----
egenskaperna | En kommaavgränsad lista över namnen på sänkor som enskilda logghändelser publiceras. Alla händelser matchar begränsningarna i syslogEventConfiguration publiceras till varje listad mottagare. Exempel: ”EHforsyslog”
Funktionen | En lokal syslog-namn (som ”LOGGA\_användare” eller ”LOGGA\_LOCAL0”). Se avsnittet ”och” i den [syslog man sidan](http://man7.org/linux/man-pages/man3/syslog.3.html) för hela listan.
minSeverity | En syslog-allvarlighetsgrad (som ”LOGGA\_fel” eller ”LOGGA\_INFO”). Se avsnittet ”nivå” i den [syslog man sidan](http://man7.org/linux/man-pages/man3/syslog.3.html) för hela listan. Tillägget samlar in händelser som skickas till funktionen vid eller över den angivna nivån.

När du anger `syslogEvents`, LAD alltid skriver data till en tabell i Azure-lagring. Du kan ha samma data skrivs till JSON-blobbar och/eller Händelsehubbar, men du kan inte inaktivera lagra data i en tabell. Partitionering beteendet för den här tabellen är densamma som för `performanceCounters`. Tabellnamnet är sammanfogning av dessa strängar:

* `LinuxSyslog`
* Ett datum i formatet ”ååååmmdd” som ändrar var 10: e dag

Exempel inkluderar `LinuxSyslog20170410` och `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Det här valfria avsnittet styr körning av godtycklig [OMI](https://github.com/Microsoft/omi) frågor.

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
namnområde | (valfritt) OMI namnområde där frågan ska köras. Om inget anges är standardvärdet ”root/scx”, implementeras av den [System Center plattformsoberoende Providers](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
DocumentDB | OMI-frågan ska köras.
tabell | (valfritt) Azure storage-tabellen i avsedda storage-konto (se [skyddade inställningarna](#protected-settings)).
frequency | (valfritt) Antalet sekunder mellan körning av frågan. Standardvärdet är 300 (5 minuter). lägsta värdet är 15 sekunder.
egenskaperna | (valfritt) En kommaavgränsad lista över namnen på ytterligare sänkor som rådata mått resultat ska publiceras. Ingen aggregering av exemplen rådata beräknas genom tillägget eller Azure mått.

Antingen ”table” eller ”egenskaperna”, eller både och måste anges.

### <a name="filelogs"></a>fileLogs

Styr infångandet av loggfiler. LAD in nya textrader som de skrivs till filen och skriver dem till rader och/eller alla angivna sänkor (JsonBlob eller EventHub).

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
fil | Den fullständiga sökvägen för loggfilen ska övervakas och hämtas. Sökvägen måste namnet på en enskild fil. Det går inte att namnge en katalog eller innehåller jokertecken.
tabell | (valfritt) Azure storage tabell i avsedda storage-konto (som anges i konfigurationen av skyddade) som nya rader från ”slutet” i filen skrivs.
egenskaperna | (valfritt) En kommaavgränsad lista över namnen på ytterligare sänkor till vilka loggen rader skickas.

Antingen ”table” eller ”egenskaperna”, eller både och måste anges.

## <a name="metrics-supported-by-the-builtin-provider"></a>Mått som stöds av builtin-providern

Inbyggd mått providern är en källa av mätvärden som är mest intressanta en bred uppsättning användare. De här måtten faller inom fem bred klasser:

* Processor
* Minne
* Nätverk
* Filsystem
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>inbyggd mått för Processor-klass

Processor-klassen för mått innehåller information om processoranvändning i den virtuella datorn. Resultatet är medelvärdet mellan alla processorer vid sammanställning procenttal. På en virtuell dator i två vCPU, om en vCPU var 100% upptagen och det andra var 100% inaktiv rapporterade PercentIdleTime skulle värdet vara 50. Om varje vCPU var 50% upptagen för samma period, skulle det rapporterade resultat också vara 50. I en VM på fyra vCPU, med en vCPU 100% upptagen och de andra inaktiv är rapporterade PercentIdleTime 75.

Räknaren | Betydelse
------- | -------
PercentIdleTime | Procentandel av tiden under fönstret aggregering att processorer köra kernel inaktiv loop
PercentProcessorTime | Procentandelen av tid att köra en icke-inaktiv tråd
PercentIOWaitTime | Procentandelen tid som väntar på att i/o-åtgärder att slutföra
PercentInterruptTime | Procentandelen tid som kör maskin-och programvara avbrott och DPC: er (uppskjutna proceduranrop)
PercentUserTime | Icke-inaktiv tid under fönstret aggregering procentandelen tid som ägnats åt användaren fler med normal prioritet
PercentNiceTime | För icke-ledig tid i procent som går åt till nedsänkt (bra) prioritet
PercentPrivilegedTime | För icke-ledig tid i procent använt i privilegierad (kernel)-läge

De fyra första räknarna bör sum till 100%. Tre senaste prestandaräknare också summan till 100%. de dela upp summan av PercentProcessorTime, PercentIOWaitTime och PercentInterruptTime.

Om du vill ha ett mått samman mellan alla processorer, ange `"condition": "IsAggregate=TRUE"`. Om du vill ha ett mått för en viss processor, till exempel andra logisk processor för en virtuell dator i fyra vCPU, ange `"condition": "Name=\\"1\\""`. Logisk processor tal som ligger inom intervallet `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>inbyggd mätvärden för klassen minne

Klassen minne för mått innehåller information om växling och växla minnesanvändning.

Räknaren | Betydelse
------- | -------
AvailableMemory | Tillgängligt fysiskt minne i MiB
PercentAvailableMemory | Tillgängligt fysiskt minne i procent av det totala minnet
UsedMemory | Använd fysiskt minne (MiB)
PercentUsedMemory | Används fysiskt minne i procent av det totala minnet
PagesPerSec | Total växling (läsa/skriva)
PagesReadPerSec | Sidorna läses säkerhetskopiering store (växlingsfil, fil, mappade filen, etc.)
PagesWrittenPerSec | Sidor som skrivs till stödjande store (växlingsfil, mappade filen osv.)
AvailableSwap | Oanvända växlingsutrymme (MiB)
PercentAvailableSwap | Oanvända växlingsutrymme som en procentandel av totalt växlingsutrymme
UsedSwap | Använd växlingsutrymme (MiB)
PercentUsedSwap | Används växlingsutrymme som en procentandel av totalt växlingsutrymme

Mått i den här klassen har en enda instans. Attributet ”villkor” har inga användbara inställningar och ska uteslutas.

### <a name="builtin-metrics-for-the-network-class"></a>inbyggd mätvärden för klassen nätverk

Klassen nätverk av mätvärden innehåller information om nätverksaktivitet på en enskild nätverksgränssnitt sedan start. LAD visar inte gränssnittshändelsen bandbredd statistik, som kan hämtas från värden mått.

Räknaren | Betydelse
------- | -------
BytesTransmitted | Totalt antal byte som skickats sedan start
BytesReceived | Totalt antal byte som tagits emot sedan start
BytesTotal | Totalt antal byte som skickats eller tagits emot sedan start
PacketsTransmitted | Totalt antal paket som skickats sedan start
PacketsReceived | Totalt antal paket som tagits emot sedan start
TotalRxErrors | Antal mottagna fel sedan start
TotalTxErrors | Antal överföringsfel sedan start
TotalCollisions | Antal kollisioner som rapporterats av nätverksportarna som sedan start

 Även om den här klassen är instanced stöder inte LAD sparandet nätverket mått samman över alla nätverksenheter. Om du vill hämta mått för ett visst gränssnitt, till exempel eth0, ange `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>inbyggd mätvärden för klassen filsystem

Filsystem-klassen för mått innehåller information om användning av filsystem. Absoluta och procentuella värden rapporteras som de visas för en vanlig användare (inte rot).

Räknaren | Betydelse
------- | -------
FreeSpace | Tillgängligt diskutrymme i byte
UsedSpace | Använda diskutrymmet i byte
PercentFreeSpace | Procent ledigt utrymme
PercentUsedSpace | Använt utrymme i procent
PercentFreeInodes | Procentandelen oanvända noder
PercentUsedInodes | Procentandelen allokerat (i används) noder summeras över alla filsystem
BytesReadPerSecond | Läsa antalet byte per sekund
BytesWrittenPerSecond | Byte som skrivs per sekund
BytesPerSecond | Byte läsas eller skrivas per sekund
ReadsPerSecond | Läsåtgärder per sekund
WritesPerSecond | Skrivåtgärder per sekund
TransfersPerSecond | Läsa eller skriva åtgärder per sekund

Sammanställda värden över alla filsystem kan erhållas genom att ange `"condition": "IsAggregate=True"`. Värden för en specifik monterade filsystem, som ”/ mnt”, kan erhållas genom att ange `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>inbyggd mått för Disk-klass

Disk-klassen för mått innehåller information om diskanvändning för enheten. Statistiken gäller för hela enheten. Om det finns flera filsystem på en enhet, är räknare för enheten ett effektivt sätt, samman över alla.

Räknaren | Betydelse
------- | -------
ReadsPerSecond | Läsåtgärder per sekund
WritesPerSecond | Skrivåtgärder per sekund
TransfersPerSecond | Totalt antal åtgärder per sekund
AverageReadTime | Det genomsnittliga antalet sekunder per läsning
AverageWriteTime | Det genomsnittliga antalet sekunder per skrivning
AverageTransferTime | Det genomsnittliga antalet sekunder per åtgärd
AverageDiskQueueLength | Genomsnittligt antal köade diskåtgärder
ReadBytesPerSecond | Antalet lästa byte per sekund
WriteBytesPerSecond | Antalet byte som skrivs per sekund
BytesPerSecond | Antalet byte som har lästs eller skrivits per sekund

Sammanställda värden över alla diskar kan erhållas genom att ange `"condition": "IsAggregate=True"`. Om du vill hämta information för en specifik enhet (till exempel/dev/sdf1), ange `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Installera och konfigurera LAD 3.0 via CLI

Under förutsättning att de skydda inställningarna är i filen PrivateConfig.json och din offentliga konfigurationsinformation finns i PublicConfig.json, kör kommandot:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Kommandot förutsätter att du använder läget Azure Resource Manager (arm) för Azure CLI. Konfigurera LAD för klassisk distribution modellen (ASM) virtuella datorer, växla till läget ”asm” (`azure config mode asm`) och utelämna resursgruppens namn i kommandot. Mer information finns i [plattformsoberoende CLI dokumentationen](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>En exempelkonfiguration LAD 3.0

Baserat på de föregående definitionerna, är här exempel LAD 3.0 tilläggets konfiguration med förklaringar. För att använda det här exemplet för ditt ärende, ska du använda egna lagringskontonamnet, konto SAS-token och EventHubs SAS-token.

### <a name="privateconfigjson"></a>PrivateConfig.json

Konfigurera inställningarna för privat:

* ett lagringskonto
* en matchande konto-SAS-token
* flera egenskaperna (JsonBlob eller EventHubs med SAS-token)

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

Dessa inställningar för offentliga orsaka LAD till:

* Överför procent processortid och använda diskutrymme mått till den `WADMetrics*` tabell
* Skicka meddelanden från syslog anläggning ”användare” och allvarlighetsgraden ”information” till den `LinuxSyslog*` tabell
* Överför rådata OMI frågeresultat (PercentProcessorTime och PercentIdleTime) till den namngivna `LinuxCPU` tabell
* Överför tillagda rader i filen `/var/log/myladtestlog` till den `MyLadTestLog` tabell

Data överförs också till i varje fall:

* Azure Blob storage (behållarens namn är enligt definitionen i JsonBlob sink)
* EventHubs slutpunkt (som anges i EventHubs sink)

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

Den `resourceId` i konfigurationen måste matcha som angetts av den virtuella datorn eller virtuella datorns skaluppsättning.

* Azure-plattformen mått diagram och avisering vet resourceId av den virtuella datorn som du arbetar med. Det förväntas att hitta data för den virtuella datorn med hjälp av resourceId nyckeln för sökning.
* Om du använder Azure Autoskala måste resourceId i Autoskala konfigurationen matcha resourceId som används av LAD.
* Resurs-ID är inbyggd i namnen på JsonBlobs som skrivits av LAD.

## <a name="view-your-data"></a>Granska dina data

Använd Azure-portalen om du vill visa prestandadata eller Ställ in aviseringar:

![Bild](./media/diagnostic-extension/graph_metrics.png)

Den `performanceCounters` data lagras alltid i en tabell i Azure Storage. Azure Storage-API: er är tillgängliga för många språk och plattformar.

Data som skickas till JsonBlob sänkor lagras i blobbar i lagringskontot med namnet i den [skyddade inställningarna](#protected-settings). Du kan använda blob-data med hjälp av alla Azure Blob Storage-API: er.

Dessutom kan du använda dessa UI-verktyg för åtkomst till data i Azure Storage:

* Visual Studio Server Explorer.
* [Microsoft Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/ "Azure Lagringsutforskaren").

Den här ögonblicksbild av en Microsoft Azure Lagringsutforskaren session visar genererade Azure Storage-tabeller och behållare från en korrekt konfigurerad LAD 3.0-tillägg på test VM. Bilden matchar inte exakt med den [LAD 3.0 exempelkonfiguration](#an-example-lad-30-configuration).

![Bild](./media/diagnostic-extension/stg_explorer.png)

Se relevant [EventHubs dokumentationen](../../event-hubs/event-hubs-what-is-event-hubs.md) information om hur du använder meddelanden som publiceras till en EventHubs slutpunkt.

## <a name="next-steps"></a>Nästa steg

* Skapa mått aviseringar i [Azure-Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) för de mätvärden som du samlar in.
* Skapa [övervakning diagram](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) för din mått.
* Lär dig hur du [skapa en virtuella datorns skaluppsättning](/azure/virtual-machines/linux/tutorial-create-vmss) använder din mått för att styra autoskalning.

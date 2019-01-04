---
title: Lägg till övervakning och diagnostik i Azure-datorer | Microsoft Docs
description: Använd en Azure Resource Manager-mall för att skapa en ny Windows virtuell dator med Azure diagnostics-tillägg.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85e9b49cb8be1a3f53ca0f3b4816e6165b68bde0
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993118"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Använda övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar
Azure Diagnostics-tillägget innehåller funktioner för övervakning och diagnostik på en Windows-baserade Azure--dator. Du kan aktivera de här funktionerna på den virtuella datorn genom att inkludera tillägget som en del av Azure Resource Manager-mallen. Se [redigera Azure Resource Manager-mallar med VM-tillägg](../windows/template-description.md#extensions) för mer information om bland annat alla tillägg som en del av en mall för virtuella datorer. Den här artikeln beskrivs hur du kan lägga till Azure-diagnostiktillägget i en windows-mall för virtuella datorer.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Lägg till Azure Diagnostics-tillägget i VM-resursdefinitionen
Om du vill aktivera diagnostiktillägget på en Windows-dator som du behöver lägga till tillägget som en VM-resurs i Resource Manager-mallen.

För en enkel resurshanterare baserat VM Lägg till tilläggskonfigurationen till den *resurser* matris för den virtuella datorn: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

En annan vanliga konventionen är att lägga till tilläggskonfigurationen på rotnoden för resurser i mallen i stället för att definiera den under noden för den virtuella datorns resurser. Med den här metoden måste du uttryckligen ange en hierarkisk relation mellan tillägget och den virtuella datorn med den *namn* och *typ* värden. Exempel: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Tillägget är alltid kopplad till den virtuella datorn kan du antingen direkt definiera den under noden för den virtuella datorns resursen direkt eller definiera den på den grundläggande nivån och använda hierarkisk namngivningskonvention för att associera det med den virtuella datorn.

För Virtual Machine Scale Sets tillägg-konfiguration har angetts i den *extensionProfile* egenskapen för den *VirtualMachineProfile*.

Den *publisher* egenskapen med värdet för **Microsoft.Azure.Diagnostics** och *typ* egenskapen med värdet för **IaaSDiagnostics**identifiera Azure Diagnostics-tillägget.

Värdet för den *namn* egenskapen kan användas för att referera till tillägget i resursgruppen. Ange den specifikt till **Microsoft.Insights.VMDiagnosticsSettings** gör det möjligt att lätt identifieras av Azure-portalen att säkerställa att övervakningen diagram visar upp korrekt i Azure-portalen.

Den *typeHandlerVersion* anger vilken version av tillägget som du vill använda. Ange *autoUpgradeMinorVersion* minor-version till **SANT** säkerställer att du får den senaste lägre versionen av tillägget som är tillgänglig. Vi rekommenderar starkt att du alltid ange *autoUpgradeMinorVersion* ska alltid vara **SANT** så att du alltid får du använder det senaste tillgängliga diagnostics-tillägget med alla nya funktioner och felkorrigeringar. 

Den *inställningar* element innehåller konfigurationer egenskaper för det tillägg som kan ange och läsa tillbaka från tillägget (kallas ibland offentliga konfiguration). Den *xmlcfg* egenskapen innehåller XML-baserad konfiguration för diagnostikloggar, prestandaräknare osv som samlas in av diagnostics-agenten. Se [diagnostik konfigurationsschema](https://msdn.microsoft.com/library/azure/dn782207.aspx) för mer information om det xml-schemat. En vanlig metod är att lagra den faktiska xml-konfigurationen som en variabel i Azure Resource Manager-mallen och sedan Sammanfoga och base64-koda dem att ange ett värde för *xmlcfg*. Se avsnittet om [diagnostik configuration variabler](#diagnostics-configuration-variables) vill veta mer om hur du lagrar XML-filen i variabler. Den *storageAccount* egenskapen anger namnet på lagringskontot på vilka felsökning data överförs. 

Egenskaperna i *protectedSettings* (ibland hänvisade till som privat konfiguration) kan anges men går inte att läsa tillbaka efter anges. Den lässkyddad natur *protectedSettings* är det praktiskt för att lagra hemligheter som lagringskontonyckeln där diagnostics-data skrivs.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Anger att diagnostiklagringskonto parametrar
Diagnostik tillägget json-kodfragmentet ovan förutsätter två parametrar *existingdiagnosticsStorageAccountName* och *existingdiagnosticsStorageResourceGroup* ange lagring för diagnostik konto diagnostikdata ska lagras. Anger diagnostiklagringskonto som en parameter gör det enkelt att ändra lagringskontot för gatewaydiagnostik i olika miljöer, till exempel vilja använda en annan diagnostiklagringskonto för testning och en för din Produktionsdistribution.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Det är bäst att ange ett lagringskonto för diagnostik i en annan resursgrupp än resursgruppen för den virtuella datorn. En resursgrupp kan betraktas som en distributionsenhet med sin egen livslängd, en virtuell dator kan distribueras och omdistribueras som nya konfigurationer uppdateringar blir det till den, men du kanske vill fortsätta lagra diagnostics-data i samma lagringskonto i dessa distributioner av virtuella datorer. Med storage-konto i en annan resurs kan lagringskontot för att ta emot data från olika distributioner av virtuella datorer som gör det enkelt att Felsök problem i olika versioner.

> [!NOTE]
> Om du skapar en mall för virtuell dator av windows från Visual Studio kan standardkontot för lagring anges att använda samma lagringskonto där den virtuella datorn virtuella Hårddisk har laddats upp. Det här är att underlätta installationen av den virtuella datorn. Ändra faktorerna mallen om du vill använda ett annat lagringskonto som kan skickas som en parameter. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Variabler för konfiguration av diagnostik
Föregående diagnostik tillägget json-kodfragmentet definierar en *accountid* variabeln för att förenkla komma lagringskontots åtkomstnyckel för lagring för diagnostik:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

Den *xmlcfg* -egenskapen för diagnostiktillägget definieras med hjälp av flera variabler som sammanfogas tillsammans. Värdena för dessa variabler finns i xml så de måste undantas korrekt när du ställer in json-variabler.

I följande exempel beskrivs den diagnostik i XML-format som samlar in standard system på prestandaräknare tillsammans med vissa windows-händelseloggar och diagnostikinfrastrukturloggar. Det har undantaget och formaterats korrekt så att konfigurationen direkt kan klistras in i variabelavsnitt i mallen. Se den [diagnostik konfigurationsschema](https://msdn.microsoft.com/library/azure/dn782207.aspx) ett mänskligare läsbara exempel på XML-konfigurationsfil.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Mått definition xml-nod i konfigurationen ovan är en viktig konfigurationsinformation del eftersom den definierar hur räknarna som definierats tidigare i XML-koden i *PerformanceCounter* noden aggregeras och lagras. 

> [!IMPORTANT]
> De här måtten enhet övervakning av diagram och aviseringar i Azure-portalen.  Den **mått** nod med den *resourceID* och **MetricAggregation** måste inkluderas i diagnostikkonfigurationen för den virtuella datorn om du vill se den virtuella datorn övervakningsdata i Azure-portalen. 
> 
> 

I följande exempel visas xml för måttdefinitioner av: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Den *resourceID* attribut som unikt identifierar den virtuella datorn i din prenumeration. Se till att använda funktionerna subscription() och resourceGroup() så att mallen uppdateras automatiskt dessa värden baserat på prenumerationen och resursgruppen som du distribuerar till.

Om du skapar flera virtuella datorer i en loop, måste du fylla i den *resourceID* värde med en funktionen copyIndex() korrekt skilja mellan varje enskild virtuell dator. Den *xmlCfg* kan uppdateras för att stödja detta på följande sätt:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Värdet för MetricAggregation *PT1M* och *PT1H* en obestämd en samling under en minut och en sammanställning över en timme.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics tabeller i storage
Mått konfigurationen ovan skapar tabeller i ditt lagringskonto för diagnostik med följande namngivningsregler:

* **WADMetrics**: Standard-prefix för alla WADMetrics tabeller
* **PT1H** eller **PT1M**: Innebär det att tabellen innehåller aggregerade data över en timme eller minut
* **P10D**: Innebär det att tabellen innehåller data för 10 dagar från när tabellen startade insamling av data
* **V2S**: Strängkonstant
* **ÅÅÅÅMMDD**: Det datum då tabellen igång insamling av data

Exempel: *WADMetricsPT1HP10DV2S20151108* innehåller mätvärden samman under en timme för 10 dagar från och med 11 november 2015    

Varje WADMetrics tabell innehåller följande kolumner:

* **PartitionKey**: Partitionsnyckeln skapas utifrån den *resourceID* värde att identifiera den Virtuella datorresursen. Exempel: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Följer formatet `<Descending time tick>:<Performance Counter Name>`. Fallande tid skalstreck beräkningen är max tid ticken minus tidpunkten för början av sammanställningsperioden. Till exempel om exemplet period igång på 10 november 2015 och 00:00Hrs UTC sedan beräkningen är: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Prestandaräknaren Radnyckeln ser ut för minne tillgängliga byte: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: Är namnet på prestandaräknaren. Detta matchar den *counterSpecifier* definieras i xml-konfigurationen.
* **Maximal**: Det högsta värdet för prestandaräknaren för över sammanställningsperioden.
* **Minsta**: Minimivärdet för prestandaräknare över sammanställningsperioden.
* **Totalt antal**: Summan av alla värden i prestandaräknaren rapporterat under sammanställningsperioden.
* **Antal**: Det totala antalet värden som rapporterats för prestandaräknaren.
* **Genomsnittlig**: Värdet för prestandaräknaren över sammanställningsperioden medelvärde (totalt/antal).

## <a name="next-steps"></a>Nästa steg
* En komplett exempelmall med en Windows-dator med diagnostiktillägget finns [201-vm-övervakning-diagnostics-tillägg](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Distribuera Azure Resource Manager-mall med [Azure PowerShell](../windows/ps-template.md) eller [Azure Command Line](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Läs mer om [skapa Azure Resource Manager-mallar](../../resource-group-authoring-templates.md)

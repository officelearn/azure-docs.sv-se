---
title: Lägga till övervakning & diagnostik på en virtuell Azure-dator
description: Använd en Azure Resource Manager-mall för att skapa en ny virtuell Windows-dator med Azure-diagnostiktillägg.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: mimckitt
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d100f054da5f82bc4dea51e054a28cca07f5de7b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258838"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Använda övervakning och diagnostik med mallar för Windows VM och Azure Resource Manager
Azure Diagnostics-tillägget tillhandahåller övervaknings- och diagnostikfunktionerna på en Windows-baserad virtuell Azure-dator. Du kan aktivera dessa funktioner på den virtuella datorn genom att inkludera tillägget som en del av Azure Resource Manager-mallen. Mer information om [hur du redigerar Azure Resource Manager-mallar med VM-tillägg](../windows/template-description.md#extensions) finns i en fil som en del av en mall för den virtuella datorn. I den här artikeln beskrivs hur du kan lägga till Azure Diagnostics-tillägget i en windows-mall för virtuella datorer.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Lägga till Azure Diagnostics-tillägget i vm-resursdefinitionen
Om du vill aktivera diagnostiktillägget på en virtuell Dator i Windows måste du lägga till tillägget som en VM-resurs i resource manager-mallen.

För en enkel Resource Manager-baserad virtuell *resources* dator lägga till tilläggskonfigurationen i resursmatrisen för den virtuella datorn: 

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

En annan vanlig konvention är att lägga till tilläggskonfigurationen vid rotresursnoden i mallen i stället för att definiera den under den virtuella datorns resursnod. Med den här metoden måste du uttryckligen ange en hierarkisk relation mellan tillägget och den virtuella datorn med *namn-* och *typvärdena.* Ett exempel: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Tillägget är alltid associerat med den virtuella datorn, du kan antingen direkt definiera det under den virtuella datorns resursnod direkt eller definiera den på basnivå och använda den hierarkiska namngivningskonventionen för att associera den med den virtuella datorn.

För skaning av virtuella datorer anges tilläggskonfigurationen i egenskapen *extensionProfile* för *VirtualMachineProfile*.

*Egenskapen publisher* med värdet **microsoft.Azure.Diagnostics** och *egenskapen type* med värdet **IaaSDiagnostics** identifierar azure diagnostics-tillägget unikt.

Värdet för *name* namnegenskapen kan användas för att referera till tillägget i resursgruppen. Genom att ställa in den specifikt på **Microsoft.Insights.VMDiagnosticsSettings** kan det enkelt identifieras av Azure-portalen som säkerställer att övervakningsdiagrammen visas korrekt i Azure-portalen.

*TypeHandlerVersion* anger vilken version av tillägget du vill använda. Ställa *autoUpgradeMinorVersion* delversion till **sant** säkerställer att du får den senaste delversionen av tillägget som är tillgänglig. Det rekommenderas starkt att du alltid ställa *in autoUpgradeMinorVersion* att alltid vara **sant** så att du alltid får använda den senaste tillgängliga diagnostik förlängning med alla nya funktioner och buggfixar. 

*Inställningselementet* innehåller konfigurationsegenskaper för tillägget som kan ställas in och läsas tillbaka från tillägget (kallas ibland offentlig konfiguration). *Xmlcfg-egenskapen* innehåller xml-baserad konfiguration för diagnostikloggar, prestandaräknare etc som samlas in av diagnostikagenten. Mer information om själva xml-schemat finns i [konfigurationsschemat](https://msdn.microsoft.com/library/azure/dn782207.aspx) för diagnostik. En vanlig metod är att lagra den faktiska xml-konfigurationen som en variabel i Azure Resource Manager-mallen och sedan sammanfoga och base64 koda dem för att ange värdet för *xmlcfg*. Se avsnittet om [konfigurationsvariabler](#diagnostics-configuration-variables) för diagnostik för att förstå mer om hur du lagrar xml i variabler. Egenskapen *storageAccount* anger namnet på det lagringskonto som diagnostikdata överförs till. 

Egenskaperna i *protectedSettings* (kallas ibland privat konfiguration) kan ställas in men kan inte läsas tillbaka efter att ha ställts in. Den skrivskyddade *nyckelsynken* gör det användbart för att lagra hemligheter som lagringskontonyckeln där diagnostikdata skrivs.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Ange diagnostiklagringskonto som parametrar
Diagnostiktillägget json-kodavsnittet ovan förutsätter två parametrar *befintligadiagnostikStorageAccountName* och *existingdiagnosticsStorageResourceGroup* för att ange diagnostiklagringskontot där diagnostikdata lagras. Genom att ange diagnostiklagringskontot som en parameter är det enkelt att ändra diagnostiklagringskontot i olika miljöer, till exempel kanske du vill använda ett annat diagnostiklagringskonto för testning och ett annat för din produktionsdistribution.  

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

Det är bäst att ange ett diagnostiklagringskonto i en annan resursgrupp än resursgruppen för den virtuella datorn. En resursgrupp kan betraktas som en distributionsenhet med sin egen livstid, en virtuell dator kan distribueras och distribueras om när nya konfigurationsuppdateringar görs till den, men du kanske vill fortsätta lagra diagnostikdata i samma lagringskonto över dessa distributioner av virtuella datorer. Om du har lagringskontot i en annan resurs kan lagringskontot acceptera data från olika distributioner av virtuella datorer, vilket gör det enkelt att felsöka problem i de olika versionerna.

> [!NOTE]
> Om du skapar en windows-mall för virtuella datorer från Visual Studio kan standardlagringskontot vara inställt på att använda samma lagringskonto där den virtuella datorns virtuella dator överförs. Detta för att förenkla den första installationen av den virtuella datorn. Räkna om mallen om du vill använda ett annat lagringskonto som kan skickas in som en parameter. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Konfigurationsvariabler för diagnostik
Det föregående diagnostiktillägget json-kodavsnitt definierar en *accountid-variabel* för att förenkla hämta lagringskontonyckeln för diagnostiklagringen:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

*Xmlcfg-egenskapen* för diagnostiktillägget definieras med hjälp av flera variabler som sammanfogas tillsammans. Värdena för dessa variabler är i xml så de måste flys korrekt när du ställer in json-variablerna.

I följande exempel beskrivs diagnostikkonfigurations xml som samlar in prestandaräknare på standardnivå tillsammans med vissa windows-händelseloggar och infrastrukturloggar för diagnostik. Den har rymts och formaterats korrekt så att konfigurationen direkt kan klistras in i variabelavsnittet i mallen. Se [konfigurationsschemat](https://msdn.microsoft.com/library/azure/dn782207.aspx) för diagnostik för ett mer läsbart exempel på konfigurations xml.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Xml-noden för måttdefinition i konfigurationen ovan är ett viktigt konfigurationselement eftersom det definierar hur prestandaräknarna som definierats tidigare i xml i *PerformanceCounter-noden* aggregeras och lagras. 

> [!IMPORTANT]
> Dessa mått driver övervakningsdiagram och aviseringar i Azure-portalen.  **Metrics-noden** med *resourceID* och **MetricAggregation** måste inkluderas i diagnostikkonfigurationen för din virtuella dator om du vill se vm-övervakningsdata i Azure-portalen. 
> 
> 

I följande exempel visas xml för måttdefinitioner: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Attributet *resourceID* identifierar den virtuella datorn i prenumerationen unikt. Se till att använda funktionerna subscription() och resourceGroup() så att mallen automatiskt uppdaterar dessa värden baserat på den prenumeration och resursgrupp som du distribuerar till.

Om du skapar flera virtuella datorer i en loop måste du fylla i *resurs-ID-värdet* med en copyIndex()-funktion för att korrekt skilja varje enskild virtuell dator. *XmlCfg-värdet* kan uppdateras för att stödja detta på följande sätt:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

MetricAggregation-värdet för *PT1M* och *PT1H* betyder en aggregering över en minut respektive en aggregering över en timme.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics-tabeller i lagring
Måttkonfigurationen ovan genererar tabeller i ditt diagnostiklagringskonto med följande namngivningskonventioner:

* **WADMetrics**: Standardprefix för alla WADMetrics-tabeller
* **PT1H** eller **PT1M**: Anger att tabellen innehåller aggregerade data över 1 timme eller 1 minut
* **P10D**: Anger att tabellen kommer att innehålla data i 10 dagar från det att tabellen började samla in data
* **V2S**: Sträng konstant
* **yyymmdd**: Det datum då tabellen började samla in data

Exempel: *WADMetricsPT1HP10DV2S20151108* innehåller måttdata aggregerade över en timme i 10 dagar från 11-nov-2015    

Varje WADMetrics-tabell innehåller följande kolumner:

* **PartitionKey**: Partitionsnyckeln är konstruerad baserat på *resurs-ID-värdet* för att unikt identifiera den virtuella datorns resurs. Exempel: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Följer `<Descending time tick>:<Performance Counter Name>`formatet . Beräkningen av fallande tidstecken är maxtidsbockar minus tiden för början av aggregeringsperioden. Om exempelvis om exempelperioden startade den 10-nov-2015 och 00:00hrs `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`UTC då beräkningen skulle vara: . För den minnes tillgängliga bytes prestandaräknaren kommer radnyckeln att se ut:`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: Är namnet på prestandaräknaren. Detta matchar *counterSpecifier* som definieras i xml-config.
* **Maximum**: Det maximala värdet för prestandaräknaren under aggregeringsperioden.
* **Minimum**: Det minsta värdet för prestandaräknaren under aggregeringsperioden.
* **Totalt**: Summan av alla värden för prestandaräknaren som rapporterats under aggregeringsperioden.
* **Antal**: Det totala antalet värden som rapporterats för prestandaräknaren.
* **Medel**: Medelvärdet (totalt/antal) för prestandaräknaren under aggregeringsperioden.

## <a name="next-steps"></a>Efterföljande moment
* En komplett exempelmall för en virtuell Windows-dator med diagnostiktillägg finns i [tillägget 201-vm-monitoring-diagnostics-](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Distribuera Azure Resource Manager-mallen med [Azure PowerShell](../windows/ps-template.md) eller [Azure Command Line](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Läs mer om [hur du skapar Azure Resource Manager-mallar](../../resource-group-authoring-templates.md)

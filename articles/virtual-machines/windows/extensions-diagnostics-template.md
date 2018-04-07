---
title: Lägg till övervakning och diagnostik i en virtuell Azure-dator | Microsoft Docs
description: Använd en Azure Resource Manager-mall för att skapa en ny Windows virtuell dator med Azure diagnostics tillägg.
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
ms.openlocfilehash: e54060769f19546ad3ccb8c52df928eeebf03776
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar
Azure Diagnostics tillägget tillhandahåller funktioner för övervakning och diagnostik på en Windows-baserad Azure virtuella. Du kan aktivera dessa funktioner på den virtuella datorn genom att inkludera tillägget som en del av Azure Resource Manager-mallen. Se [redigera Azure Resource Manager-mallar med VM-tillägg](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) mer information om inklusive alla tillägg som en del av en mall för virtuella datorer. Den här artikeln beskriver hur du lägger till tillägget Azure Diagnostics en windows-mall för virtuell dator.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Lägga till filnamnstillägget Azure Diagnostics resursdefinitionen VM
Om du vill aktivera tillägget diagnostik på en Windows-dator som du behöver lägga till tillägget som en VM-resurs i Resource Manager-mallen.

För en enkel Resource Manager baserat virtuella lägga till tillägget konfigurationen av den *resurser* matris för den virtuella datorn: 

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

En annan vanliga konventionen är att lägga till tillägget konfigurationen på rotnoden resurser för mallen i stället för att definiera under noden för den virtuella datorns resurser. Med den här metoden måste du uttryckligen ange en hierarkisk relation mellan tillägget och den virtuella datorn med den *namn* och *typen* värden. Exempel: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Tillägget är alltid associerad med den virtuella datorn, kan du antingen direkt definiera under noden för den virtuella resursen direkt eller definiera den grundläggande nivån och använda hierarkiska Namngivningskonventionen för att associera den med den virtuella datorn.

För virtuella datorer tillägg-konfiguration har angetts i den *extensionProfile* -egenskapen för den *VirtualMachineProfile*.

Den *publisher* egenskapen med värdet för **Microsoft.Azure.Diagnostics** och *typen* egenskapen med värdet för **IaaSDiagnostics**identifiera tillägget Azure-diagnostik.

Värdet för den *namn* egenskap som kan användas för att referera till tillägget i resursgruppen. Om du anger specifikt till **Microsoft.Insights.VMDiagnosticsSettings** gör det möjligt att lätt identifieras av Azure-portalen att säkerställa att övervakningen diagram visar in korrekt i Azure-portalen.

Den *typeHandlerVersion* anger versionen av tillägget som du vill använda. Ange *autoUpgradeMinorVersion* delversion till **SANT** garanterar att du får den senaste lägre versionen av det tillägg som är tillgängliga. Vi rekommenderar att du alltid anger *autoUpgradeMinorVersion* ska alltid vara **SANT** så att du får alltid använda det senaste tillgängliga diagnostik-tillägget med de nya funktionerna och korrigeringarna. 

Den *inställningar* element innehåller konfigurationer egenskaper för det tillägg som kan ange och läsa tillbaka från tillägget (kallas ibland offentliga konfiguration). Den *xmlcfg* egenskap innehåller XML-baserad konfiguration för diagnostik-loggar prestandaräknare etc som samlas in av diagnostik-agenten. Se [diagnostik Konfigurationsschemat](https://msdn.microsoft.com/library/azure/dn782207.aspx) för mer information om det xml-schemat. En vanlig metod är att lagra faktiska xml-konfigurationen som en variabel i Azure Resource Manager-mall och sedan Sammanfoga och base64 koda dem så att värdet för *xmlcfg*. Se avsnittet om [diagnostik configuration variabler](#diagnostics-configuration-variables) vill veta mer om hur du lagrar XML-filen i variabler. Den *storageAccount* egenskapen anger namnet på lagringskontot för vilka Diagnostics data överförs. 

Egenskaperna i *protectedSettings* (ibland som anges som privat konfiguration) kan ställas in men går inte att läsa efter ett anges. Lässkyddad komponenternas *protectedSettings* kan användas för att lagra hemligheter som lagringskontots åtkomstnyckel där diagnostikdata skrivs.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Ange diagnostiklagringskonto som parametrar
Diagnostik tillägget json fragment ovan förutsätter att två parametrar *existingdiagnosticsStorageAccountName* och *existingdiagnosticsStorageResourceGroup* ange diagnostik-lagring konto där diagnostikdata lagras. Anger diagnostiklagringskonto som en parameter gör det enkelt att ändra lagringskontot för gatewaydiagnostik mellan olika miljöer, till exempel du kanske vill använda en annan diagnostiklagringskonto för testning och en för din Produktionsdistribution.  

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

Det är bäst att ange en diagnostiklagringskonto i en annan resursgrupp än resursgruppen för den virtuella datorn. En resursgrupp kan anses vara en distributionsenhet med sin egen livslängd, en virtuell dator kan distribueras och omdistribueras som nya konfigurationer uppdateringar görs den till den men du kanske vill fortsätta lagra diagnostikdata i samma lagringskonto över dessa distributioner på virtuella datorer. Med storage-konto i en annan resurs kan lagringskontot för att ta emot data från olika distribution av virtuella datorer som gör det enkelt att felsöka problem mellan olika versioner.

> [!NOTE]
> Om du skapar en mall för virtuell dator av windows från Visual Studio är standardkontot för lagring inställd på att använda samma lagringskonto där den virtuella datorn virtuella Hårddisk har överförts. Detta är att underlätta installationen av den virtuella datorn. Ändra faktorerna mallen om du vill använda ett annat lagringskonto som kan skickas som en parameter. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Variabler för diagnostik-konfiguration
Diagnostik tillägget json fragment ovan definierar en *accountid* variabeln för att förenkla komma lagringskontots åtkomstnyckel för lagring av diagnostik:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

Den *xmlcfg* egenskapen för diagnostik-tillägg har definierats med flera variabler som sammanfogas tillsammans. Värdena för dessa variabler finns i xml så att de behöver hoppas korrekt när json-variabler.

Nedan beskrivs diagnostik konfigurations-xml som samlar in standard system nivån prestandaräknare tillsammans med vissa windows-händelseloggar och diagnostik infrastruktur loggar. Det har undantaget och formaterats korrekt så att konfigurationen direkt kan klistras in i avsnittet variabler i mallen. Finns det [diagnostik Konfigurationsschemat](https://msdn.microsoft.com/library/azure/dn782207.aspx) en mer mänsklig läsbar exempel på XML-konfigurationsfil.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Mått definition xml-nod i konfigurationen ovan är ett viktigt konfigurationselement som definierar hur räknarna som definierats tidigare i XML-koden i *PerformanceCounter* nod samman och lagras. 

> [!IMPORTANT]
> De här måtten enheten övervakning diagram och aviseringar i Azure-portalen.  Den **mått** nod med den *resourceID* och **MetricAggregation** måste ingå i konfigurationen av diagnostik för den virtuella datorn om du vill se den virtuella datorn övervakningsdata i Azure-portalen. 
> 
> 

Följande är ett exempel på xml för definitioner av mått: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Den *resourceID* attributet identifierar den virtuella datorn i din prenumeration. Se till att använda funktionerna subscription() och resourceGroup() så att dessa värden baserat på prenumerationen och resursgruppen som du distribuerar till uppdateras automatiskt i mallen.

Om du skapar flera virtuella datorer i en slinga, måste du fylla i *resourceID* värde med en funktionen copyIndex() korrekt skilja varje enskild virtuell dator. Den *xmlCfg* värde kan uppdateras för att göra detta på följande sätt:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Värdet för MetricAggregation *PT1M* och *PT1H* respektive obestämd en sammanställning över en minut och en sammanställning över en timme.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics tabeller i lagring
Hur mått skapar tabeller i diagnostik storage-konto med följande namngivningsregler:

* **WADMetrics**: Standard prefix för alla WADMetrics tabeller
* **PT1H** eller **PT1M**: innebär att tabellen innehåller sammanställda data över 1 timme eller 1 minut
* **P10D**: innebär det att tabellen innehåller data för 10 dagar från när tabellen startat datainsamling
* **V2S**: strängkonstant
* **ÅÅÅÅMMDD**: det datum då tabellen påbörjas datainsamling

Exempel: *WADMetricsPT1HP10DV2S20151108* innehåller mått data visar det sammanlagda resultatet för 10 dagar från 2015-11-Nov i timmen    

Varje WADMetrics tabellen innehåller följande kolumner:

* **PartitionKey**: Partitionsnyckeln konstrueras utifrån den *resourceID* värde för att identifiera den Virtuella datorresursen. Till exempel: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**: i formatet `<Descending time tick>:<Performance Counter Name>`. Fallande tid skalstreck beräkningen är max tid tick minus tiden i början av aggregering period. Till exempel om provtagningsperiod igång 10 Nov 2015 och 00:00Hrs UTC sedan beräkningen är: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Prestandaräknaren Radnyckeln kommer att se ut för minne tillgängliga byte: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: är namnet på prestandaräknaren. Detta matchar den *counterSpecifier* definieras i xml-konfigurationen.
* **Maximal**: det maximala värdet för prestandaräknaren för aggregering perioden.
* **Minsta**: det lägsta värdet för prestandaräknaren för aggregering perioden.
* **Totalt antal**: summan av alla värden i prestandaräknaren rapporterat under perioden aggregering.
* **Antal**: det totala antalet värden som rapporterats för prestandaräknaren.
* **Genomsnittlig**: medelvärde (totalt/antal) för prestandaräknaren för aggregering perioden.

## <a name="next-steps"></a>Nästa steg
* En fullständig exempelmall för en virtuell dator i Windows med diagnostik tillägg finns [201 och vm-övervakning-diagnostik-tillägg](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Distribuera Azure Resource Manager-mall med [Azure PowerShell](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [Azure Command Line](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Lär dig mer om [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md)

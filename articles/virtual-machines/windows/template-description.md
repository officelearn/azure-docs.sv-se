---
title: Virtuella datorer i en Azure Resource Manager-mall | Microsoft Azure
description: Läs mer om hur resursen för den virtuella datorn definieras i en Azure Resource Manager-mall.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: c9bf1cf0564655c932e066e5b74225382375e9c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235423"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuella datorer i en Azure Resource Manager-mall

I den här artikeln beskrivs aspekter av en Azure Resource Manager-mall som gäller för virtuella datorer. Den här artikeln beskriver inte en fullständig mall för att skapa en virtuell dator. För det behöver du resursdefinitioner för lagringskonton, nätverksgränssnitt, offentliga IP-adresser och virtuella nätverk. Mer information om hur dessa resurser kan definieras tillsammans finns i [mallgenomgången för Resurshanteraren](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Det finns många [mallar i galleriet](https://azure.microsoft.com/documentation/templates/?term=VM) som innehåller vm-resursen. Det är inte bara alla element som kan inkluderas i en mall som beskrivs här.

 

I det här exemplet visas ett typiskt resursavsnitt i en mall för att skapa ett angivet antal virtuella datorer:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Det här exemplet är beroende av ett lagringskonto som tidigare skapades. Du kan skapa lagringskontot genom att distribuera det från mallen. Exemplet är också beroende av ett nätverksgränssnitt och dess beroende resurser som skulle definieras i mallen. Dessa resurser visas inte i exemplet.
>
>

## <a name="api-version"></a>API-version

När du distribuerar resurser med hjälp av en mall måste du ange en version av API:et som ska användas. Exemplet visar resursen för den virtuella datorn med det här apiVersion-elementet:

```json
"apiVersion": "2016-04-30-preview",
```

Vilken version av API:et du anger i mallen påverkar vilka egenskaper du kan definiera i mallen. I allmänhet bör du välja den senaste API-versionen när du skapar mallar. För befintliga mallar kan du bestämma om du vill fortsätta använda en tidigare API-version eller uppdatera mallen för den senaste versionen för att dra nytta av nya funktioner.

Använd de här affärsmöjligheterna för att hämta de senaste API-versionerna:

- REST API - [Lista alla resursleverantörer](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell - [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI - [az provider show](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parametrar och variabler

[Parametrar](../../resource-group-authoring-templates.md) gör det enkelt för dig att ange värden för mallen när du kör den. Det här parameteravsnittet används i exemplet:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

När du distribuerar exempelmallen anger du värden för namn och lösenord för administratörskontot på varje virtuell dator och antalet virtuella datorer som ska skapas. Du kan välja att ange parametervärden i en separat fil som hanteras med mallen eller ange värden när du uppmanas att göra det.

[Variabler](../../resource-group-authoring-templates.md) gör det enkelt för dig att ställa in värden i mallen som används upprepade gånger i den eller som kan ändras med tiden. Det här variabelavsnittet används i exemplet:

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

När du distribuerar exempelmallen används variabelvärden för namn och identifierare för det tidigare skapade lagringskontot. Variabler används också för att ange inställningarna för diagnostiktillägget. Använd [metodtipsen för att skapa Azure Resource Manager-mallar](../../resource-manager-template-best-practices.md) som hjälper dig att bestämma hur du vill strukturera parametrarna och variablerna i mallen.

## <a name="resource-loops"></a>Resursloopar

När du behöver mer än en virtuell dator för ditt program kan du använda ett kopieringselement i en mall. Det här valfria elementet loopar genom att skapa antalet virtuella datorer som du har angett som en parameter:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Observera också i exemplet att loopindex används när du anger några av värdena för resursen. Om du till exempel har angett ett instansantal på tre är namnen på operativsystemdiskarna myOSDisk1, myOSDisk2 och myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>I det här exemplet används hanterade diskar för de virtuella datorerna.
>
>

Tänk på att skapa en loop för en resurs i mallen kan kräva att du använder loopen när du skapar eller använder andra resurser. Flera virtuella datorer kan till exempel inte använda samma nätverksgränssnitt, så om mallen loopar igenom att skapa tre virtuella datorer måste den också loopas genom att skapa tre nätverksgränssnitt. När du tilldelar ett nätverksgränssnitt till en virtuell dator används loopindexet för att identifiera det:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Beroenden

De flesta resurser är beroende av andra resurser för att fungera korrekt. Virtuella datorer måste associeras med ett virtuellt nätverk och för att göra det behöver ett nätverksgränssnitt. [Elementet dependsOn](../../resource-group-define-dependencies.md) används för att se till att nätverksgränssnittet är klart att användas innan de virtuella datorerna skapas:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager distribuerar parallellt alla resurser som inte är beroende av att en annan resurs distribueras. Var försiktig när du ställer in beroenden eftersom du av misstag kan sakta ned distributionen genom att ange onödiga beroenden. Beroenden kan kedja genom flera resurser. Nätverksgränssnittet är till exempel beroende av den offentliga IP-adressen och virtuella nätverksresurser.

Hur vet du om ett beroende krävs? Titta på de värden som du anger i mallen. Om ett element i resursdefinitionen för virtuella datorer pekar på en annan resurs som distribueras i samma mall behöver du ett beroende. Till exempel definierar din exempel virtuella dator en nätverksprofil:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Om du vill ange den här egenskapen måste nätverksgränssnittet finnas. Därför behöver du ett beroende. Du måste också ange ett beroende när en resurs (ett underordnat) definieras inom en annan resurs (en överordnad). Till exempel definieras diagnostikinställningarna och anpassade skripttillägg som underordnade resurser för den virtuella datorn. De kan inte skapas förrän den virtuella datorn finns. Därför markeras båda resurserna som beroende av den virtuella datorn.

## <a name="profiles"></a>Profiler

Flera profilelement används när du definierar en resurs för en virtuell dator. Vissa krävs och vissa är valfria. Till exempel krävs elementen hardwareProfile, osProfile, storageProfile och networkProfile, men diagnostikprofilen är valfri. Dessa profiler definierar inställningar som:
   
- [Storlek](sizes.md)
- [namn](/azure/architecture/best-practices/resource-naming) och autentiseringsuppgifter
- disk- och [operativsysteminställningar](cli-ps-findimage.md)
- [nätverksgränssnitt](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- starta diagnostik

## <a name="disks-and-images"></a>Diskar och bilder
   
I Azure kan vhd-filer representera [diskar eller avbildningar](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). När operativsystemet i en vhd-fil är specialiserat på att vara en specifik virtuell dator, kallas det en disk. När operativsystemet i en vhd-fil är generaliserad för att användas för att skapa många virtuella datorer kallas det för en avbildning.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Skapa nya virtuella datorer och nya diskar från en plattformsavbildning

När du skapar en virtuell dator måste du bestämma vilket operativsystem som ska användas. Elementet imageReference används för att definiera operativsystemet för en ny virtuell dator. Exemplet visar en definition för ett Windows Server-operativsystem:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Om du vill skapa ett Linux-operativsystem kan du använda den här definitionen:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Konfigurationsinställningar för operativsystemdisken tilldelas med elementet osDisk. Exemplet definierar en ny hanterad disk med cachelagringsläget inställt på **ReadWrite** och att disken skapas från en [plattformsavbildning:](cli-ps-findimage.md)

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Skapa nya virtuella datorer från befintliga hanterade diskar

Om du vill skapa virtuella datorer från befintliga diskar tar du bort imageReference och osProfile-elementen och definierar dessa diskinställningar:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Skapa nya virtuella datorer från en hanterad avbildning

Om du vill skapa en virtuell dator från en hanterad avbildning ändrar du elementet imageReference och definierar dessa diskinställningar:

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Bifoga datadiskar

Du kan också lägga till datadiskar i de virtuella datorerna. [Antalet diskar](sizes.md) beror på storleken på operativsystemets disk som du använder. Med storleken på de virtuella datorerna inställda på Standard_DS1_v2 är det maximala antalet datadiskar som kan läggas till dem två. I exemplet läggs en hanterad datadisk till i varje virtuell dator:

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Tillägg

Även om [tillägg](extensions-features.md) är en separat resurs är de nära knutna till virtuella datorer. Tillägg kan läggas till som en underordnad resurs för den virtuella datorn eller som en separat resurs. Exemplet visar [diagnostiktillägget](extensions-diagnostics-template.md) som läggs till i de virtuella datorerna:

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Den här tilläggsresursen använder variabeln storageName och diagnostikvariablerna för att ange värden. Om du vill ändra de data som samlas in av det här tillägget kan du lägga till fler prestandaräknare i variabeln wadperfcounters. Du kan också välja att placera diagnostikdata i ett annat lagringskonto än där VM-diskarna lagras.

Det finns många tillägg som du kan installera på en virtuell dator, men det mest användbara är förmodligen [custom script extension](extensions-customscript.md). I exemplet körs ett PowerShell-skript med namnet start.ps1 på varje virtuell dator när det startar första gången:

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

Start.ps1-skriptet kan utföra många konfigurationsuppgifter. Datadiskarna som läggs till i de virtuella datorerna i exemplet initieras till exempel inte. Du kan använda ett anpassat skript för att initiera dem. Om du har flera startuppgifter att utföra kan du använda filen start.ps1 för att anropa andra PowerShell-skript i Azure-lagring. I exemplet används PowerShell, men du kan använda vilken skriptmetod som helst som är tillgänglig i operativsystemet som du använder.

Du kan se status för de installerade tilläggen från inställningarna för tillägg i portalen:

![Hämta tilläggsstatus](./media/template-description/virtual-machines-show-extensions.png)

Du kan också hämta tilläggsinformation med kommandot **Get-AzVMExtension** PowerShell, **vm-tillägget får** Azure CLI-kommando eller REST API **för fåtillägg.**

## <a name="deployments"></a>Distributioner

När du distribuerar en mall spårar Azure de resurser som du har distribuerat som en grupp och tilldelar automatiskt ett namn till den här distribuerade gruppen. Namnet på distributionen är detsamma som namnet på mallen.

Om du är nyfiken på status för resurser i distributionen kan du visa resursgruppen i Azure-portalen:

![Hämta distributionsinformation](./media/template-description/virtual-machines-deployment-info.png)
    
Det är inte ett problem att använda samma mall för att skapa resurser eller för att uppdatera befintliga resurser. När du använder kommandon för att distribuera mallar kan du säga vilket [läge](../../resource-group-template-deploy.md) du vill använda. Läget kan ställas in på antingen **Slutför** eller **Inkrementell**. Standard är att göra inkrementella uppdateringar. Var försiktig när du använder **läget Slutför** eftersom du av misstag kan ta bort resurser. När du ställer in läget så att det **slutförs**tar Resource Manager bort alla resurser i resursgruppen som inte finns i mallen.

## <a name="next-steps"></a>Efterföljande moment

- Skapa en egen mall med [mallar för att skapa Azure Resource Manager](../../resource-group-authoring-templates.md).
- Distribuera mallen som du skapade med hjälp av [Skapa en virtuell Windows-dator med en Resource Manager-mall](ps-template.md).
- Lär dig hur du hanterar de virtuella datorer som du har skapat genom att granska [Skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- För JSON-syntaxen och egenskaperna för resurstyper i mallar finns i [Azure Resource Manager-mallreferens](/azure/templates/).

---
title: Virtuella datorer i en Azure Resource Manager-mall | Microsoft Azure
description: Läs mer om hur den virtuella datorresursen definieras i en Azure Resource Manager-mall.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: 094df21bb0264ebbd8ad23c594ceb52db19d9c2f
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979623"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuella datorer i en Azure Resource Manager-mall

Den här artikeln beskrivs olika aspekter av en Azure Resource Manager-mall som gäller för virtuella datorer. Den här artikeln beskriver inte en fullständig mall för att skapa en virtuell dator; för att du behöver resursdefinitionerna för lagringskonton, nätverksgränssnitt, offentliga IP-adresser och virtuella nätverk. Mer information om hur dessa resurser kan definieras tillsammans finns i den [genomgång av Resource Manager-mall](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Det finns många [mallar i galleriet](https://azure.microsoft.com/documentation/templates/?term=VM) som innehåller den Virtuella datorresursen. Här beskrivs inte alla element som kan ingå i en mall.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Det här exemplet visas en typisk resursavsnitt i en mall för att skapa ett angivet antal virtuella datorer:

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
>Det här exemplet är beroende av ett lagringskonto som skapades tidigare. Du kan skapa lagringskontot genom att distribuera den från mallen. I exempel förlitar sig även ett nätverksgränssnitt och dess beroende resurser som skulle definieras i mallen. Dessa resurser visas inte i det här exemplet.
>
>

## <a name="api-version"></a>API-version

Du måste ange en version av API för att använda när du distribuerar resurser med hjälp av en mall. Exemplet visar resursen för virtuella datorer med hjälp av det här apiVersion-elementet:

```
"apiVersion": "2016-04-30-preview",
```

Versionen av API: et som du anger i mallen påverkar vilka egenskaper som du kan definiera i mallen. I allmänhet bör du välja den senaste API-versionen när du skapar mallar. För befintliga mallar, kan du bestämma om du vill fortsätta att använda en äldre API-version eller uppdatera mallen för den senaste versionen för att dra nytta av nya funktioner.

Använd dessa möjligheter för att få de senaste API-versionerna:

- REST-API – [lista alla resursprovidrar](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell – [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI - [az provider show](https://docs.microsoft.com/cli/azure/provider#az_provider_show)


## <a name="parameters-and-variables"></a>Parametrar och variabler

[Parametrar](../../resource-group-authoring-templates.md) gör det enkelt för dig att ange värden för mallen när du kör den. Det här avsnittet parametrar används i det här exemplet:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

När du distribuerar mallen exempel kan ange du värden för namn och lösenord för administratörskontot på varje virtuell dator och hur många virtuella datorer för att skapa. Du har möjlighet att specificering av parametervärden i en separat fil som hanteras med hjälp av mallen, eller att tillhandahålla värden när du tillfrågas.

[Variabler](../../resource-group-authoring-templates.md) gör det enkelt för dig att ange värden i mallen som används flera gånger i den eller som kan ändras med tiden. Det här avsnittet för variabler används i det här exemplet:

```
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

När du distribuerar mallen exempel används variabelvärden för namn och ID för det tidigare skapade lagringskontot. Variabler används också för att ange inställningarna för det diagnostiska-tillägget. Använd den [bästa praxis för att skapa Azure Resource Manager-mallar](../../resource-manager-template-best-practices.md) för att avgöra hur du vill att strukturera parametrar och variabler i mallen.

## <a name="resource-loops"></a>Resurs-slingor

När du behöver mer än en virtuell dator för ditt program kan använda du en kopieringselement i en mall. Det här valfria elementet loopar genom att skapa hur många virtuella datorer som du har angett som en parameter:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Observera också i det här exemplet att loopindexet används när du anger en del av värden för resursen. Om du har angett ett instansantal på tre är till exempel namnen på operativsystemdiskar myOSDisk1 och myOSDisk2 myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Det här exemplet använder hanterade diskar för virtuella datorer.
>
>

Tänk på att skapa en loop för en resurs i mallen kan kräva att du kan använda slingan när du skapar eller ansluter till andra resurser. Flera virtuella datorer kan inte använda samma nätverksgränssnitt, så om din mall loopar genom att skapa tre virtuella datorer måste det också att loopa igenom skapa tre nätverksgränssnitt. När du tilldelar ett nätverksgränssnitt till en virtuell dator, används loopindexet för att identifiera den:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Beroenden

De flesta resurser är beroende av andra resurser ska fungera korrekt. Virtuella datorer måste vara associerad med ett virtuellt nätverk och göra att den behöver ett nätverksgränssnitt. Den [dependsOn](../../resource-group-define-dependencies.md) elementet används för att se till att nätverksgränssnittet är redo att användas innan de virtuella datorerna skapas:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager distribuerar parallellt alla resurser som inte är beroende av en annan resurs som ska distribueras. Var försiktig när du ställer in beroenden eftersom du av misstag kan påverka din distribution genom att ange onödiga beroenden. Beroenden kan länka via flera resurser. Till exempel beror nätverksgränssnittet på den offentliga IP-adressen och virtuella nätverksresurser.

Hur vet du om ett beroende krävs? Titta på värden som du angav i mallen. Om ett element i den virtuella dator resource definition pekar på en annan resurs som har distribuerats i samma mall måste ett beroende. Till exempel definierar den virtuella datorn exempel en nätverksprofil:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Om du vill använda den här egenskapen måste det finnas ett nätverksgränssnitt. Därför måste ett beroende. Du måste också ange ett beroende när en resurs (en underordnad) definieras i en annan resurs (överordnad). Till exempel definieras diagnostikinställningar och anpassade skripttillägg både som underordnade resurser till den virtuella datorn. De kan inte skapas förrän den virtuella datorn finns. Båda resurserna är därför markerade som är beroende av den virtuella datorn.

## <a name="profiles"></a>Profiler

Flera profil-element används när du definierar en VM-resurs. Några är obligatoriska och vissa är valfria. Till exempel hardwareProfile, osProfile, storageProfile och networkProfile-element krävs, men diagnosticsProfile är valfritt. Profilerna definierar inställningar som:
   
- [size](sizes.md)
- [namn på](/azure/architecture/best-practices/naming-conventions) och autentiseringsuppgifter
- disk och [inställningar för operativsystem](cli-ps-findimage.md)
- [Nätverksgränssnitt](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- Startdiagnostik

## <a name="disks-and-images"></a>Diskar och avbildningar
   
I Azure, vhd-filer kan representera [diskar eller avbildningar](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). När operativsystemet i en vhd-fil är specialiserat ska vara en specifik virtuell dator, kallas det för en disk. När operativsystemet i en vhd-fil är generaliserad som används för att skapa flera virtuella datorer, kallas det en bild.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Skapa nya virtuella datorer och nya diskar från en plattformsavbildning

När du skapar en virtuell dator, måste du bestämma vilket operativsystem du använder. ImageReference-elementet används för att definiera operativsystemet på en ny virtuell dator. Exemplet visar en definition för ett Windows Server-operativsystem:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Du kan använda den här definitionen om du vill skapa ett Linux-operativsystem:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Konfigurationsinställningar för operativsystemdisken tilldelas med osDisk-elementet. Exemplet definierar en ny hanterad disk med cachelagring läge inställt **ReadWrite** och som disken skapas från en [plattformsavbildning](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Skapa nya virtuella datorer från befintliga hanterade diskar

Om du vill skapa virtuella datorer från befintliga diskar, ta bort imageReference och osProfile-element och definiera diskinställningarna:

```
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

Om du vill skapa en virtuell dator från en hanterad avbildning ändra imageReference elementet och definiera diskinställningarna:

```
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

### <a name="attach-data-disks"></a>Koppla datadiskar

Du kan du lägga till datadiskar till de virtuella datorerna. Den [antalet diskar](sizes.md) beror på storleken på operativsystemdisken som du använder. Med storleken på de virtuella datorerna inställd på Standard_DS1_v2, är det maximala antalet datadiskar som kan läggas till i de två. I det här exemplet är en hanterad datadisk läggs till varje virtuell dator:

```
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

Även om [tillägg](extensions-features.md) är en separat resurs, de är beroende av virtuella datorer. Tillägg kan läggas till som en underordnad resurs för den virtuella datorn eller som en separat resurs. I exempel visas den [Diagnostiktillägget](extensions-diagnostics-template.md) som läggs till de virtuella datorerna:

```
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

Tillägget resursen använder storageName variabeln och diagnostiska variabler för att ange värden. Om du vill ändra de data som samlas in av det här tillägget kan du lägga till fler prestandaräknare wadperfcounters variabeln. Du kan också välja att placera diagnostics-data i ett annat lagringskonto än där VM-diskarna lagras.

Det finns många tillägg som du kan installera på en virtuell dator, men de mest användbara är antagligen den [tillägget för anpassat skript](extensions-customscript.md). I det här exemplet körs ett PowerShell.skript som heter start.ps1 på varje virtuell dator när den startas första gången:

```
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

Skriptet start.ps1 kan utföra flera konfigurationsåtgärder. Till exempel är inte de datadiskar som läggs till de virtuella datorerna i det här exemplet initierad. Du kan använda ett anpassat skript för att initiera dem. Om du har flera startåtgärder för att göra, kan du använda filen start.ps1 för att anropa andra PowerShell-skript i Azure storage. I exemplet används PowerShell, men du kan använda valfri metod för skript som är tillgänglig på det operativsystem som du använder.

Du kan se status för de installerade tilläggen från inställningarna för tillägg i portalen:

![Hämta Tilläggsstatus för](./media/template-description/virtual-machines-show-extensions.png)

Du kan också få tilläggsinformation med hjälp av den **Get-AzVMExtension** PowerShell-kommando i **vm-tillägget get** Azure CLI-kommando eller **hämta tilläggsinformation**REST-API.

## <a name="deployments"></a>Distributioner

När du distribuerar en mall, spårar Azure de resurser som du distribuerat som en grupp och tilldelar automatiskt ett namn på den här distribuerade gruppen. Namnet på distributionen är samma som namnet på mallen.

Om du är nyfiken på status för resurser i distributionen kan du visa resursgruppen i Azure portal:

![Hämta information om distribution](./media/template-description/virtual-machines-deployment-info.png)
    
Det är inte ett problem att använda samma mall för att skapa resurser eller för att uppdatera befintliga resurser. När du distribuerar mallar med hjälp av kommandon har du möjlighet att säga som [läge](../../resource-group-template-deploy.md) du vill använda. Läget kan vara inställd på antingen **Slutför** eller **stegvis**. Standardvärdet är att göra inkrementella uppdateringar. Var försiktig när du använder den **Slutför** läge eftersom av misstag kan du ta bort resurser. När du har angett läget **Slutför**, Resource Manager tar bort alla resurser i resursgruppen som inte är i mallen.

## <a name="next-steps"></a>Nästa steg

- Skapa en egen mall med hjälp av [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).
- Distribuera mallen som du skapat med [skapa en Windows-dator med en Resource Manager-mall](ps-template.md).
- Lär dig hur du hanterar de virtuella datorerna som du skapade genom att granska [skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- JSON-syntax och egenskaper för resurstyper i mallar finns i [mallreferensen för Azure Resource Manager](/azure/templates/).

---
title: Virtuella datorer i en Azure Resource Manager-mall | Microsoft Azure
description: "Läs mer om hur den virtuella datorresursen har definierats i en Azure Resource Manager-mall."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: davidmu
ms.openlocfilehash: 9c0039987ec28601c9338d2b94633c38c31e01f8
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuella datorer i en Azure Resource Manager-mall

Den här artikeln beskriver aspekter av en Azure Resource Manager-mall som gäller för virtuella datorer. Den här artikeln beskriver inte en fullständig mall för att skapa en virtuell dator; för att du behöver resursdefinitionerna för storage-konton, nätverksgränssnitt, offentliga IP-adresser och virtuella nätverk. Mer information om hur dessa resurser kan definieras tillsammans finns i [genomgång av Resource Manager-mall](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Det finns många [mallar i galleriet](https://azure.microsoft.com/documentation/templates/?term=VM) som innehåller den Virtuella datorresursen. Här beskrivs inte alla element som kan ingå i en mall.

Det här exemplet illustrerar en typisk resursavsnitt i en mall för att skapa ett angivet antal virtuella datorer:

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
>Det här exemplet bygger på ett lagringskonto som skapades tidigare. Du kan skapa lagringskontot genom att distribuera den från mallen. Exemplet använder också ett nätverksgränssnitt och dess beroende resurser som skulle definieras i mallen. Dessa resurser visas inte i exemplet.
>
>

## <a name="api-version"></a>API-Version

Du måste ange en version av API för att använda när du distribuerar resurser med hjälp av en mall. Exemplet visar den virtuella datorresursen med den här apiVersion-element:

```
"apiVersion": "2016-04-30-preview",
```

Versionen av API: N som du anger i mallen påverkar vilka egenskaper som du kan definiera i mallen. I allmänhet bör du välja den senaste API-versionen när du skapar mallar. Du kan bestämma om du vill fortsätta med en tidigare API-version eller uppdatera mallen för den senaste versionen dra nytta av nya funktioner för befintliga mallar.

Använd dessa möjligheter för att hämta de senaste API-versionerna:

- REST API - [lista över alla resursproviders](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)
- PowerShell - [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider)
- Azure CLI 2.0 - [az provider show](https://docs.microsoft.com/cli/azure/provider#az_provider_show)

## <a name="parameters-and-variables"></a>Parametrar och variabler

[Parametrarna](../../resource-group-authoring-templates.md) gör det lättare för dig att ange värden för mallen när den körs. Det här avsnittet parametrar används i exemplet:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

När du distribuerar mallen exempel kan ange du värden för namn och lösenord för administratörskontot för varje virtuell dator och antalet virtuella datorer för att skapa. Du har möjlighet att ange parametervärden i en separat fil som hanteras med hjälp av mallen eller tillhandahåller värden när du uppmanas.

[Variabler](../../resource-group-authoring-templates.md) gör det lättare för dig att ange värden i mallen som används flera gånger i den eller som kan ändras med tiden. Det här avsnittet för variabler som används i exemplet:

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

När du distribuerar mallen exempel används variabelvärden för namn och ID: t för det tidigare skapade lagringskontot. Variabler används också för att ange inställningar för diagnostik tillägget. Använd den [bästa praxis för att skapa mallar för Azure Resource Manager](../../resource-manager-template-best-practices.md) som hjälper dig att bestämma hur du vill att strukturera parametrar och variabler i mallen.

## <a name="resource-loops"></a>Resursen slingor

När du behöver mer än en virtuell dator för programmet, kan du använda en kopia-element i en mall. Det här valfria elementet loop genom att skapa antal virtuella datorer som du angav som en parameter:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Observera också i exempel loopindexet används när du anger en del av värden för resursen. Om du har angett ett instansantal på tre är till exempel namnen på operativsystemet diskar myOSDisk1, myOSDisk2 och myOSDisk3:

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

Tänk på att skapa en loop för en resurs i mallen kan kräva att använda loop när du skapar eller få åtkomst till andra resurser. Flera virtuella datorer kan inte använda samma nätverksgränssnitt, så om din mall loop genom att skapa tre virtuella datorer måste den också gå igenom skapa tre nätverksgränssnitt. När du tilldelar en virtuell dator ett nätverksgränssnitt används loopindexet för att identifiera den:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Beroenden

De flesta resurser är beroende av andra resurser ska fungera korrekt. Virtuella datorer måste vara associerad med ett virtuellt nätverk och gör att den behöver ett nätverksgränssnitt. Den [dependsOn](../../resource-group-define-dependencies.md) element som används för att kontrollera att nätverkskortet är redo att användas innan de virtuella datorerna har skapats:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Hanteraren för filserverresurser distribuerar parallellt alla resurser som inte är beroende av en annan resurs som ska distribueras. Var försiktig när du ställer in beroenden eftersom du kan oavsiktligt göra distributionen genom att ange onödiga beroenden. Beroenden kan kedja genom flera resurser. Till exempel beror nätverksgränssnittet på den offentliga IP-adressen och nätverksresurser på virtuella datorer.

Hur vet du om det krävs ett beroende? Titta på de värden som du anger i mallen. Om ett element i den virtuella resource definition pekar på en annan resurs som distribueras i samma mall måste ett beroende. Till exempel definierar den virtuella datorn exempel en nätverksprofil:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Nätverksgränssnittet måste finnas för att ange den här egenskapen. Därför måste ett beroende. Du måste ange ett beroende när en resurs (en underordnad) definieras inom en annan resurs (överordnad). Till exempel är diagnostikinställningar och tillägg för anpassat skript definierade som underordnade resurser till den virtuella datorn. De kan inte skapas förrän den virtuella datorn finns. Därför markeras båda resurserna som är beroende av den virtuella datorn.

## <a name="profiles"></a>Profiler

Flera profil element används när du definierar en virtuell datorresurs. Vissa är obligatoriska och vissa är valfria. Till exempel hardwareProfile, osProfile, storageProfile och networkProfile-element krävs, men diagnosticsProfile är valfritt. De här profilerna definiera inställningar som:
   
- [storlek](sizes.md)
- [namnet](/architecture/best-practices/naming-conventions) och autentiseringsuppgifter
- disk och [inställningar för operativsystem](cli-ps-findimage.md)
- [nätverksgränssnitt](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- Startdiagnostikinställningar

## <a name="disks-and-images"></a>Diskar och bilder
   
I Azure, vhd-filer kan representera [diskar eller bilder](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). När operativsystemet i en vhd-fil är specialanpassat ska vara en specifik VM, kallas det en disk. När operativsystemet i en vhd-fil är generaliserad som används för att skapa många virtuella datorer, kallas det en bild.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Skapa nya virtuella datorer och nya diskar från en plattformsavbildning

När du skapar en virtuell dator, måste du bestämma vilket operativsystem du använder. Elementet imageReference används för att definiera operativsystemet på en ny virtuell dator. Exemplet visar en definition för ett Windows Server-operativsystem:

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

Konfigurationsinställningar för operativsystemets disk tilldelas med elementet osDisk. I exempel definierar en ny hanterade disk med cachelagring inställd på **ReadWrite** och att disken skapas från en [plattformsavbildning](cli-ps-findimage.md):

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

Om du vill skapa en virtuell dator från en hanterad avbildning ändra elementet imageReference och definiera diskinställningarna:

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

### <a name="attach-data-disks"></a>Bifoga datadiskar

Du kan lägga till datadiskar till de virtuella datorerna. Den [antal diskar](sizes.md) beror på storleken på disken för operativsystemet som du använder. Med storleken på de virtuella datorerna som angetts till Standard_DS1_v2 är maximala antalet datadiskar som kan läggas till dem två. I exemplet är läggs en hanterad datadisk till varje virtuell dator:

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

Även om [tillägg](extensions-features.md) är en separat resurs, de är beroende av virtuella datorer. Tillägg kan läggas till som en underordnad resurs för den virtuella datorn eller som en separat resurs. Exempel visar den [diagnostik tillägget](extensions-diagnostics-template.md) läggs till i de virtuella datorerna:

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

Tillägget resursen använder variabeln storageName och variablerna diagnostik för att ge värden. Om du vill ändra de data som samlas in med det här tillägget kan du lägga till flera prestandaräknare wadperfcounters variabeln. Du kan också välja att placera diagnostikdata i ett annat lagringskonto än där VM-diskarna lagras.

Det finns många tillägg som du kan installera på en virtuell dator, men den mest användbara är antagligen det [tillägget för anpassat skript](extensions-customscript.md). I det här exemplet körs ett PowerShell.skript som heter start.ps1 på varje virtuell dator när den startas:

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

Skriptet start.ps1 kan utföra många konfigurationsåtgärder. Till exempel har datadiskar som läggs till de virtuella datorerna i exemplet inte initierats; Du kan använda ett anpassat skript till initiering. Om du har flera Start uppgifter om du vill kan använda du filen start.ps1 för att anropa andra PowerShell-skript i Azure-lagring. I exemplet används PowerShell, men du kan använda valfri metod för skript som är tillgänglig på det operativsystem som du använder.

Du kan se status för de installerade tilläggen från tillägg-inställningarna i portalen:

![Hämta Åtgärdsstatus för tillägg](./media/template-description/virtual-machines-show-extensions.png)

Du kan också få tillägget information med hjälp av den **Get-AzureRmVMExtension** PowerShell-kommando på **vm-tillägget get** Azure CLI 2.0 kommando eller **hämta information om tillägg** REST-API.

## <a name="deployments"></a>Distributioner

När du distribuerar en mall spårar resurser som du distribuerat som en grupp och tilldelar automatiskt ett namn för den här distribuerad grupp i Azure. Namnet på distributionen är samma som namnet på mallen.

Om du är nyfiken på status för resurser i distributionen kan använda du resursgrupp-bladet i Azure-portalen:

![Hämta information om distribution](./media/template-description/virtual-machines-deployment-info.png)
    
Det är inte ett problem ska använda samma mall att skapa resurser eller uppdaterar befintliga resurser. När du distribuera mallar med hjälp av kommandon har möjlighet att säga som [läge](../../resource-group-template-deploy.md) du vill använda. Läget kan vara inställd på antingen **Slutför** eller **stegvis**. Standardvärdet är att göra inkrementella uppdateringar. Var försiktig när du använder den **Slutför** läge eftersom av misstag kan du ta bort resurser. När du anger läget till **Slutför**, Resource Manager tar du bort alla resurser i resursgruppen som inte ingår i mallen.

## <a name="next-steps"></a>Nästa steg

- Skapa en egen mall med hjälp av [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).
- Distribuera mallen som du skapat med [skapa en Windows-dator med en Resource Manager-mall](ps-template.md).
- Lär dig hur du hanterar de virtuella datorerna som du skapade genom att granska [skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

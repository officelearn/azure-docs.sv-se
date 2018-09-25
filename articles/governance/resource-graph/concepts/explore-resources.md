---
title: Utforska Azure-resurser med Resource Graph
description: Lär dig att använda Resource Graph-frågespråk för att utforska dina resurser och Upptäck hur de är anslutna.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: fa31abae794fe9842d6e4e2d704971b3a4783555
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979000"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Utforska Azure-resurser med Resource Graph

Azure Resource Graph ger möjlighet att utforska och identifiera dina Azure-resurser, snabbt och skalenligt. Utformad för snabba svar, är det ett bra sätt att lära dig om din miljö och även om de egenskaper som utgör din Azure-resurser.

## <a name="explore-virtual-machines"></a>Utforska virtuella datorer

En gemensam resurs i Azure är en virtuell dator. Virtuella datorer har ett stort antal egenskaper som kan efterfrågas som en resurstyp. Varje egenskap innehåller ett alternativ för filtrering eller hitta exakt den resurs som du letar efter.

### <a name="virtual-machine-discovery"></a>Identifiering av virtuell dator

Låt oss börja med en enkel fråga för att hämta en enda virtuell dator från vår miljö och titta på egenskaperna som returneras.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

JSON-resultaten är strukturerade liknar följande exempel:

```json
[
  {
    "aliases": {
      "Microsoft.Compute/imageId": null,
      "Microsoft.Compute/imageOffer": "WindowsServer",
      "Microsoft.Compute/imagePublisher": "MicrosoftWindowsServer",
      "Microsoft.Compute/imageSku": "2016-Datacenter",
      "Microsoft.Compute/imageVersion": "latest",
      "Microsoft.Compute/licenseType": null,
      "Microsoft.Compute/virtualMachines/availabilitySet.id": null,
      "Microsoft.Compute/virtualMachines/diagnosticsProfile.bootDiagnostics": null,
      "Microsoft.Compute/virtualMachines/diagnosticsProfile.bootDiagnostics.enabled": null,
      "Microsoft.Compute/virtualMachines/diagnosticsProfile.bootDiagnostics.storageUri": null,
      "Microsoft.Compute/virtualMachines/imageOffer": "WindowsServer",
      "Microsoft.Compute/virtualMachines/imagePublisher": "MicrosoftWindowsServer",
      "Microsoft.Compute/virtualMachines/imageSku": "2016-Datacenter",
      "Microsoft.Compute/virtualMachines/imageVersion": "latest",
      "Microsoft.Compute/virtualMachines/networkInterfaces[*].id": [
        "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535"
      ],
      "Microsoft.Compute/virtualMachines/osDisk.Uri": null,
      "Microsoft.Compute/virtualMachines/osProfile.adminPassword": null,
      "Microsoft.Compute/virtualMachines/osProfile.adminUsername": "localAdmin",
      "Microsoft.Compute/virtualMachines/osProfile.linuxConfiguration": null,
      "Microsoft.Compute/virtualMachines/osProfile.linuxConfiguration.disablePasswordAuthentication": null,
      "Microsoft.Compute/virtualMachines/osProfile.windowsConfiguration": {
        "enableAutomaticUpdates": true,
        "provisionVMAgent": true
      },
      "Microsoft.Compute/virtualMachines/osProfile.windowsConfiguration.enableAutomaticUpdates": true,
      "Microsoft.Compute/virtualMachines/osProfile.windowsConfiguration.provisionVMAgent": true,
      "Microsoft.Compute/virtualMachines/sku.name": "Standard_B2s",
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].caching": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].createOption": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].diskSizeGB": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].image.uri": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].lun": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].managedDisk.id": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].managedDisk.storageAccountType": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].name": [],
      "Microsoft.Compute/virtualMachines/storageProfile.dataDisks[*].vhd.uri": [],
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.caching": "ReadWrite",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.createOption": "FromImage",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.encryptionSettings": null,
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.encryptionSettings.enabled": null,
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType": "Premium_LRS",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.osType": "Windows",
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.vhd": null,
      "Microsoft.Compute/virtualMachines/storageProfile.osDisk.vhd.uri": null
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

Den första uppsättningen med egenskaper under **alias** erbjuder ett antal relaterade egenskapsvärden. Om du vill ha mer information om alias och identifiera vilka alias som är tillgängliga, se [Azure Policy-definitionsstruktur - alias](../../policy/concepts/definition-structure.md#aliases). Alias används främst av Azure Policy för att hantera efterlevnad av organisation regler och styrning.

Berätta mer om den virtuella datorresursen, allt från SKU: N, OS, diskar, taggar, de andra egenskaperna och resursgrupp och prenumeration är medlem i.

### <a name="virtual-machines-by-location"></a>Virtuella datorer efter plats

Vi tar vad vi lärt oss om resursen för virtuella datorer kan använda den **plats** egenskapen du räknar alla virtuella datorer efter plats. Om du vill uppdatera frågan vi ta bort gränsen och sammanfatta antalet värden för platsen.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

JSON-resultaten är strukturerade liknar följande exempel:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Vi kan nu se hur många virtuella datorer har vi i varje Azure-region.

### <a name="virtual-machines-by-sku"></a>Virtuella datorer av SKU: N

Vi går tillbaka till den ursprungliga virtuella datorns egenskaper, försök att hitta alla virtuella datorer som har en SKU-storlek på **Standard_B2s**. Titta på JSON returneras, ser vi att den är lagrad i **properties.hardwareprofile.vmsize**. Vi uppdaterar fråga för att hitta alla virtuella datorer som matchar den här storleken och returnera bara namnet på den virtuella datorn och region.

```Query
where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuella datorer är anslutna till premium-hanterade diskar

Om vi vill hämta information om premium-hanterade diskar som är kopplade till dessa **Standard_B2s** virtuella datorer, kan vi Expandera fråga för att ge oss resurs-id för dessa hanterade diskar.

```Query
where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Ett annat sätt att hämta SKU: N skulle ha varit med hjälp av den **alias** egenskapen **Microsoft.Compute/virtualMachines/sku.name**.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Resultatet är en lista över disk-ID: n

### <a name="managed-disk-discovery"></a>Identifiering av hanterad disk

Vi tar den första posten från den föregående frågan kommer att utforska de egenskaper som finns på den hantera disken som har kopplats till den första virtuella datorn. Uppdaterade frågan använder disk-id och ändra typen.

Exempel utdata från den föregående frågan till exempel:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
````

```Query
where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Innan du kör frågan, hur vi visste den **typ** nu bör vara **Microsoft.Compute/disks**?
Om du tittar på det fullständiga ID: T visas **/providers/Microsoft.Compute/disks/** som en del av strängen. Den här strängen fragment ger en ledtråd om vilken typ som eftersöks. En alternativ metod är att ta bort gränsen efter typ och i stället bara söka efter ID-fältet. Eftersom det ID: T är unik, bara en post skulle returneras och **typ** egenskap på den innehåller den information.

> [!NOTE]
> I det här exemplet ska fungera måste du ersätta ID-fältet med ett resultat från din egen miljö.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

JSON-resultaten är strukturerade liknar följande exempel:

```json
[
  {
    "aliases": {
      "Microsoft.Compute/disks/sku.name": "Premium_LRS",
      "Microsoft.Compute/imageId": null,
      "Microsoft.Compute/imageOffer": null,
      "Microsoft.Compute/imagePublisher": null,
      "Microsoft.Compute/imageSku": null,
      "Microsoft.Compute/imageVersion": null
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Utforska virtuella datorer för att hitta offentliga IP-adresser

Den här Azure CLI flerstegstest uppsättning frågor först söker efter och lagrar alla nätverket nätverksgränssnitt (NIC)-resurser som är anslutna till virtuella datorer, använder listan över nätverkskort att hitta varje IP-adressresurs som är en offentlig IP-adress och lagra dessa värden och slutligen en lista över faktiska offentliga IP-adresser.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nic' variable
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

När vi har den `nics.txt` fil, använder vi den i nästa fråga och få nätverket gränssnittet resurser information där det finns en offentlig IP-adress som är kopplade till nätverkskortet.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

Senaste, Använd listan över offentliga IP-adressresurser lagras i `ips.txt` skaffa faktiska offentliga IP-adress från dem och visa.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [frågespråket](query-language.md)
- Se språket som användes i [Starter frågor](../samples/starter.md)
- Se avancerade använder i [avancerade frågor](../samples/advanced.md)
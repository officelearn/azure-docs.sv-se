---
title: Utforska dina Azure-resurser
description: Lär dig att använda frågespråket Resource Graph för att utforska dina resurser och ta reda på hur de är anslutna.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 0c191915b8c558d80ffef554ef758a35157e035c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76156989"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Utforska dina Azure-resurser med resursgrafer

Azure Resource Graph ger möjlighet att utforska och upptäcka dina Azure-resurser snabbt och i stor skala. Det är ett bra sätt att lära sig om din miljö och även om de egenskaper som utgör dina Azure-resurser.

## <a name="explore-virtual-machines"></a>Utforska virtuella datorer

En gemensam resurs i Azure är en virtuell dator. Som en resurstyp har virtuella datorer många egenskaper som kan efterfrågas. Varje egenskap är ett alternativ för att filtrera eller hitta exakt den resurs du letar efter.

### <a name="virtual-machine-discovery"></a>Identifiering av virtuella datorer

Låt oss börja med en enkel fråga för att få en enda virtuell dator från vår miljö och titta på de egenskaper som returneras.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Cmdlet `Search-AzGraph` för Azure PowerShell returnerar ett **PSCustomObject** som standard. Om du vill att utdata ska se likadan `ConvertTo-Json` ut som det som returneras av Azure CLI används cmdleten. Standardvärdet för **Djup** är _2_. Om du ställer in den på _100_ ska alla returnerade nivåer konverteras.

JSON-resultaten är strukturerade på samma sätt som i följande exempel:

```json
[
  {
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

Egenskaperna berättar ytterligare information om själva resursen för den virtuella datorn, allt från SKU, OS, diskar, taggar och resursgruppen och prenumerationen som den är medlem i.

### <a name="virtual-machines-by-location"></a>Virtuella datorer efter plats

Med vad vi lärt oss om resursen för **location** virtuella datorer, låt oss använda platsegenskapen för att räkna alla virtuella datorer efter plats. Om du vill uppdatera frågan tar vi bort gränsen och summerar antalet platsvärden.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

JSON-resultaten är strukturerade på samma sätt som i följande exempel:

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

Vi kan nu se hur många virtuella datorer vi har i varje Azure-region.

### <a name="virtual-machines-by-sku"></a>Virtuella datorer av SKU

Gå tillbaka till den ursprungliga virtuella datorn egenskaper, låt oss försöka hitta alla virtuella datorer som har en SKU storlek **Standard_B2s**. Om man tittar på JSON tillbaka, ser vi att det lagras i **properties.hardwareprofile.vmsize**. Vi uppdaterar frågan för att hitta alla virtuella datorer som matchar den här storleken och returnerar bara namnet på den virtuella datorn och regionen.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuella datorer som är anslutna till premiumhanterade diskar

Om vi ville få information om premiumhanterade diskar som är kopplade till dessa **Standard_B2s** virtuella datorer, kan vi utöka frågan för att ge oss resurs-ID för dessa hanterade diskar.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Ett annat sätt att hämta SKU skulle ha varit genom att använda **alias** egenskapen **Microsoft.Compute/virtualMachines/sku.name**. Se exemplen [Visa alias](../samples/starter.md#show-aliases) och Visa [olika aliasvärden.](../samples/starter.md#distinct-alias-values)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Resultatet är en lista över disk-ID: er.

### <a name="managed-disk-discovery"></a>Identifiering av hanterade diskar

Med den första posten från föregående fråga utforskar vi de egenskaper som finns på den hanterade disken som var kopplad till den första virtuella datorn. Den uppdaterade frågan använder disk-ID:et och ändrar typen.

Exempel på utdata från föregående fråga till exempel:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Innan du kör frågan, hur visste vi **att typen** nu ska vara **Microsoft.Compute/disks?**
Om du tittar på det fullständiga ID:t visas **/providers/Microsoft.Compute/disks/** som en del av strängen. Detta strängfragment ger dig en ledtråd om vilken typ du ska söka efter. En alternativ metod skulle vara att ta bort gränsen efter typ och i stället bara söka efter ID-fältet. Eftersom ID:et är unikt returneras bara en post och **egenskapen type** på den innehåller den detaljen.

> [!NOTE]
> Om du vill arbeta i det här exemplet måste du ersätta ID-fältet med ett resultat från din egen miljö.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

JSON-resultaten är strukturerade på samma sätt som i följande exempel:

```json
[
  {
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

Den här uppsättningen frågor hittar och lagrar först alla nätverksgränssnitt (NIC) resurser som är anslutna till virtuella datorer. Sedan använder frågorna listan över nätverkskort för att hitta varje IP-adressresurs som är en offentlig IP-adress och lagra dessa värden. Slutligen innehåller frågorna en lista över de offentliga IP-adresserna.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Använd filen (Azure CLI) eller variabeln (Azure PowerShell) i nästa fråga för att hämta information om relaterade nätverksresurser där det finns en offentlig IP-adress kopplad till nätverkskortet.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Använd senast listan över offentliga IP-adressresurser som lagras i filen (Azure CLI) eller variabeln (Azure PowerShell) för att hämta den faktiska offentliga IP-adressen från det relaterade objektet och visningen.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Mer om du vill se hur du `join` utför dessa steg i en enda fråga med operatören finns i [virtuella listadatorer med nätverksgränssnittet och det offentliga IP-exemplet.](../samples/advanced.md#join-vmpip)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [frågespråket](query-language.md).
- Se språket som används i [Starter-frågor](../samples/starter.md).
- Se avancerade användningsområden i [avancerade frågor](../samples/advanced.md).

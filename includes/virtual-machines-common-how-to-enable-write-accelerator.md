---
title: ta med fil
description: ta med fil
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 6/8/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: b2733bed4418fdfcaefb20c04683cb6a229134e9
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53594285"
---
# <a name="enable-write-accelerator"></a>Aktiverar du Write Accelerator

Skriva Accelerator är en disk-funktion för M-serien virtuella datorer (VM på Premium Storage med Azure Managed Disks) exklusivt. Som namnet tillstånd, är syftet med funktionen för att förbättra i/o-svarstiden för skrivningar Azure Premium Storage. Skriva Accelerator är idealiska där log filuppdateringar krävs för att spara till disk på ett mycket effektivt sätt för moderna databaser.

Skriva Accelerator är allmänt tillgängliga för virtuella datorer i M-serien i det offentliga molnet.

## <a name="planning-for-using-write-accelerator"></a>Planering för att använda Write Accelerator

Skriva Accelerator bör användas för de volymer som innehåller transaktionsloggen eller göra om loggarna för ett DBMS. Du bör inte använda Write Accelerator för datavolymer av en DBMS som funktionen har optimerats för att användas mot loggdiskar.

Skriva Accelerator fungerar bara tillsammans med [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Aktivera Write Accelerator för operativsystemdisken för den virtuella datorn startas om den virtuella datorn.
>
> Så här aktiverar du Write Accelerator till en befintlig Azure-disk som inte är en del av en volym kompilering från flera diskar med Windows disk eller volym chefer, Windows med lagringsutrymmen, Windows Scale-out filserver (SOFS), Linux LVM eller MDADM arbetsbelastningen åtkomst till Azure-disken måste stängas av. Databasprogram med hjälp av Azure-disken måste stängas av.
>
> Om du vill aktivera eller inaktivera Write Accelerator för en befintlig volym som har skapats från flera Azure Premium Storage-diskar och stripe-volymer med hjälp av Windows-disk eller volym chefer lagringsutrymmen för Windows, Windows skalbar filserver (SOFS), Linux LVM eller MDADM, alla diskar som att skapa volymen måste aktiveras eller inaktiveras för Write Accelerator i separata steg. **Innan du aktiverar eller inaktiverar Write Accelerator i en sådan konfiguration, stänga av den virtuella Azure-datorn**.

Aktivera Write Accelerator för OS-diskar får inte vara nödvändiga för SAP-relaterade VM-konfigurationer.

### <a name="restrictions-when-using-write-accelerator"></a>Begränsningar när du använder Write Accelerator

Dessa begränsningar gäller när du använder Write Accelerator för en Azure-disk/VHD:

- Cachelagring för Premium disk måste anges till ”ingen” eller ”skrivskyddad”. Alla andra lägen för cachelagring stöds inte.
- Ögonblicksbilder stöds inte för närvarande för Write Accelerator-aktiverade diskar. Under säkerhetskopieringen är inkluderas tjänsten Azure Backup Write Accelerator-aktiverade diskar som är anslutna till den virtuella datorn.
- Endast mindre i/o-storlekar (< = 32 KiB) tar snabbare sökvägen. I arbetsbelastningen situationer där data får bulk lästs in, eller om transaktionen log buffertar av olika DBMS är ifyllt i större utsträckning innan komma beständiga lagringen, risken är att i/o som skrivs till disk inte tar snabbare sökvägen.

Det finns gränser för Azure Premium Storage virtuella hårddiskar per virtuell dator som stöds av Write Accelerator. De aktuella gränserna är:

| SKU FÖR VIRTUELL DATOR | Antalet diskar som Write Accelerator | Write Accelerator Disk IOPS per virtuell dator |
| --- | --- | --- |
| M128ms 128s | 16 | 8000 |
| M64ms, M64ls, M64s | 8 | 4000 |
| M32ms, M32ls, M32ts, M32s | 4 | 2000 |
| M16ms M16s | 2 | 1000 |
| M8ms M8s | 1 | 500 |

IOPS-gränserna är per virtuell dator och *inte* per disk. Alla diskar för Write Accelerator delar samma IOPS-gränsen per virtuell dator.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Aktivera Write Accelerator på en specifik disk

Följande avsnitt beskriver hur Write Accelerator kan aktiveras i Azure Premium Storage virtuella hårddiskar.

### <a name="prerequisites"></a>Förutsättningar

Följande krav gäller för användningen av Write Accelerator vid denna tidpunkt:

- De diskar som du vill använda Azure Write Accelerator mot måste vara [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/) på Premium Storage.
- Du måste använda en virtuell dator i M-serien

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Aktivera Azure Write Accelerator med Azure PowerShell

Azure Power Shell-modul från version 5.5.0 innefattar ändringar i de relevanta cmdletar för att aktivera eller inaktivera Write Accelerator för specifika Azure Premium Storage-diskar.
För att aktivera eller distribuera diskar som stöds av Write Accelerator, har följande Power Shell-kommandon ändrats och utökas för att acceptera en parameter för Write Accelerator.

En ny switchparameter **- WriteAccelerator** har lagts till i följande cmdletar:

- [Set-AzureRmVMOsDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-6.0.0)
- [Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Add-AzureRmVMDataDisk?view=azurermps-6.0.0)
- [Set-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Set-AzureRmVMDataDisk?view=azurermps-6.0.0)
- [Add-AzureRmVmssDataDisk](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Add-AzureRmVmssDataDisk?view=azurermps-6.0.0)

Ger inte parametern anger egenskapen till false och distribuerar diskar som har inte stöd av Write Accelerator.

En ny switchparameter **OsDiskWriteAccelerator -** har lagts till i följande cmdletar:

- [Set-AzureRmVmssStorageProfile](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile?view=azurermps-6.0.0)

Inte att ange parametern anger egenskapen till false som standard returnerar diskar som inte utnyttjar Write Accelerator.

En ny valfritt booleskt (icke-nullbar) parameter, **OsDiskWriteAccelerator -** har lagts till i följande cmdletar:

- [Update-AzureRmVM](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Update-AzureRmVM?view=azurermps-6.0.0)
- [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Update-AzureRmVmss?view=azurermps-6.0.0)

Ange $true eller $false att styra stöd för Azure Write Accelerator med diskar.

Exempel på kommandon kan se ut:

```PowerShell
New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false
```

Två huvudscenarier kan skriptas som visas i följande avsnitt.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Att lägga till en ny disk som stöds av Write Accelerator med hjälp av PowerShell

Du kan använda det här skriptet för att lägga till en ny disk till den virtuella datorn. Disken som skapats med det här skriptet använder Write Accelerator.

Ersätt `myVM`, `myWAVMs`, `log001`, storleken på disken och LunID på disken med värden som är lämpliga för din specifika distribution.

```PowerShell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Att aktivera Write Accelerator på en befintlig Azure-disk med hjälp av PowerShell

Du kan använda det här skriptet för att aktivera Write Accelerator på en befintlig disk. Ersätt `myVM`, `myWAVMs`, och `test-log001` med värden som är lämpliga för din specifika distribution. Skriptet lägger till Write Accelerator till en befintlig disk där värdet för **$newstatus** är inställd på '$true'. Med hjälp av värdet '$false' inaktiverar Write Accelerator på en angiven disk.

```PowerShell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Kör skriptet ovan ska koppla från disken som angetts, aktivera Write Accelerator mot disken och sedan koppla disken igen

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Aktivera Write Accelerator med Azure portal

Du kan aktivera Write Accelerator via portalen där du anger din diskcachelagringstypen inställningar:

![Write Accelerator på Azure portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Aktivera Write Accelerator med Azure CLI

Du kan använda den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) så här aktiverar du Write Accelerator.

Om du vill aktivera Write Accelerator på en befintlig disk, Använd [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), du kan använda följande exempel om du ersätter den diskName och VMName ResourceGroup med dina egna värden: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Att ansluta en disk med Write Accelerator aktiverat Använd [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), du kan använda följande exempel om du ersätta dem med dina egna värden: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Om du vill inaktivera Write Accelerator använda [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), ange egenskaperna till false: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Aktivera Write Accelerator med hjälp av Rest API: er

Du måste installera Azure armclient om du vill distribuera via Azure Rest-API.

### <a name="install-armclient"></a>Installera armclient

Du måste installera den via Chocolatey för att köra armclient. Du kan installera det via cmd.exe eller powershell. Använd utökade rättigheter för dessa kommandon (”Kör som administratör”).

Med hjälp av cmd.exe, kör du följande kommando: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Med Power Shell, kör du följande kommando: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Nu kan du installera armclient med hjälp av följande kommando i cmd.exe eller PowerShell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Hämta din nuvarande konfiguration av virtuell dator

Om du vill ändra attribut för diskkonfigurationen måste du först hämta den aktuella konfigurationen i en JSON-fil. Du kan hämta den aktuella konfigurationen genom att köra följande kommando: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Ersätt villkoren i <> <>med dina data, inklusive filnamnet JSON-filen ska ha.

Utdata bör se ut som:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Därefter uppdaterar JSON-filen och för att aktivera Write Accelerator på disken som kallas ”log1”. Detta kan åstadkommas genom att lägga till det här attributet i JSON-filen efter cache-post på disken.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Sedan uppdatera den befintliga distributionen med det här kommandot: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Utdata bör se ut som i exemplet nedan. Du kan se att Write Accelerator-aktiverade för en disk.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

När du har gjort den här ändringen, ska enheten stödjas av Write Accelerator.

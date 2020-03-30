---
title: ta med fil
description: ta med fil
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 11/27/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 456d550659c04b2272c048fcd64fe73b1a11522a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74566369"
---
Write Accelerator är en diskfunktion för virtuella datorer i M-serien (VMs) på Premium Storage med Azure Managed Disks exklusivt. Som namnet anger är syftet med funktionerna att förbättra I/O-svarstiden för skrivningar mot Azure Premium Storage. Write Accelerator är idealiskt där loggfilsuppdateringar krävs för att spara till disken på ett mycket högpresterande sätt för moderna databaser.

Write Accelerator är allmänt tillgängligt för virtuella datorer i M-serien i det offentliga molnet.

## <a name="planning-for-using-write-accelerator"></a>Planera för att använda Write Accelerator

Skrivaccelerator ska användas för de volymer som innehåller transaktionsloggen eller gör om loggar för en DBMS. Det rekommenderas inte att använda Write Accelerator för datavolymerna för en DBMS eftersom funktionen har optimerats för att användas mot loggdiskar.

Write Accelerator fungerar bara tillsammans med [Azure-hanterade diskar](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Om du aktiverar Write Accelerator för den virtuella datorns operativsystemdisk startas den virtuella datorn om.
>
> Om du vill aktivera Write Accelerator till en befintlig Azure-disk som INTE ingår i en volymversion av flera diskar med Windows disk- eller volymhanterare, Windows Storage Spaces, Windows Scale-out file server (SOFS), Linux LVM eller MDADM, arbetsbelastningen som ansluter till Azure-disken måste stängas av. Databasprogram som använder Azure-disken MÅSTE stängas av.
>
> Om du vill aktivera eller inaktivera Write Accelerator för en befintlig volym som är byggd av flera Azure Premium Storage-diskar och randiga med Windows disk- eller volymhanterare, Windows Storage Spaces, Windows Scale-out file server (SOFS), Linux LVM eller MDADM, alla diskar som bygger volymen måste vara aktiverade eller inaktiverade för Write Accelerator i separata steg. **Stäng av Den virtuella datorn för Azure.**

Aktivera Write Accelerator för OS-diskar bör inte vara nödvändigt för SAP-relaterade VM-konfigurationer.

### <a name="restrictions-when-using-write-accelerator"></a>Begränsningar när skrivningsacceleratorer används

När du använder Write Accelerator för en Azure-disk/VIRTUELLD gäller följande begränsningar:

- Cachelagring av Premium-disken måste vara inställd på "Ingen" eller Skrivskyddad. Alla andra cachelagringslägen stöds inte.
- Ögonblicksbild stöds för närvarande inte för Skriv acceleratoraktiverade diskar. Under säkerhetskopieringen utesluter Azure Backup-tjänsten automatiskt Write Accelerator-aktiverade diskar som är anslutna till den virtuella datorn.
- Endast mindre I/O-storlekar (<=512 KiB) tar den accelererade vägen. I arbetsbelastningssituationer där data får massinlästa eller där transaktionsloggbuffertarna för de olika DBMS fylls i i större utsträckning innan de sparas till lagringen, är chansen stor att I/O som skrivs till disken inte tar den accelererade sökvägen.

Det finns gränser för Azure Premium Storage VHDs per virtuell dator som kan stödjas av Write Accelerator. De nuvarande gränserna är:

| VM-SKU | Antal skrivacceleratordiskar | Skriv acceleratordisk IOPS per virtuell dator |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20000 |
| M208ms_v2, M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

IOPS-gränserna är per virtuell dator och *inte* per disk. Alla Skriv accelerator diskar delar samma IOPS gräns per virtuell dator.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Aktivera skrivningsacceleratorer på en specifik disk

De följande avsnitten beskriver hur Write Accelerator kan aktiveras på Azure Premium Storage VHDs.

### <a name="prerequisites"></a>Krav

Följande förutsättningar gäller för användningen av Skrivaccelerator vid denna tidpunkt:

- Diskarna som du vill använda Azure Write Accelerator mot måste vara [Azure-hanterade diskar](https://azure.microsoft.com/services/managed-disks/) på Premium Storage.
- Du måste använda en VM-serie i M-serien

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Aktivera Azure Write Accelerator med Azure PowerShell

Azure Power Shell-modulen från version 5.5.0 innehåller ändringar i relevanta cmdlets för att aktivera eller inaktivera Write Accelerator för specifika Azure Premium Storage-diskar.
För att aktivera eller distribuera diskar som stöds av Write Accelerator ändrades följande Power Shell-kommandon och utökades för att acceptera en parameter för Write Accelerator.

En ny switchparameter, **-WriteAccelerator** har lagts till i följande cmdlets:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Tillägg-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Tillägg AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Om du inte anger parametern anges egenskapen till false och distribuerar diskar som inte har något stöd av Write Accelerator.

En ny växelparameter, **-OsDiskWriteAccelerator** har lagts till i följande cmdlets:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Om du inte anger parametern anges egenskapen till false som standard och diskar som inte utnyttjar Write Accelerator returneras.

En ny boolesk parameter (som inte kan nullable) (som inte kan null-er) **- OsDiskWriteAccelerator** har lagts till i följande cmdlets:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Ange antingen $true eller $false för att styra stödet för Azure Write Accelerator med diskarna.

Exempel på kommandon kan se ut som:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Två huvudscenarier kan skriptas som visas i följande avsnitt.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Lägga till en ny disk som stöds av Write Accelerator med PowerShell

Du kan använda det här skriptet för att lägga till en ny disk i den virtuella datorn. Disken som skapas med det här skriptet använder Write Accelerator.

Ersätt `myVM` `myWAVMs`, `log001`, , storleken på disken och LunID på disken med värden som är lämpliga för din specifika distribution.

```powershell
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
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Aktivera Write Accelerator på en befintlig Azure-disk med PowerShell

Du kan använda det här skriptet för att aktivera Skrivaccelerator på en befintlig disk. Ersätt `myVM` `myWAVMs`, `test-log001` och med värden som är lämpliga för din specifika distribution. Skriptet lägger skrivaccelera på en befintlig disk där värdet för **$newstatus** är inställt på "$true". Om du använder värdet "$false" inaktiveras Skriv accelerator på en viss disk.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Om du kör skriptet ovan tas disken bort, write accelerator aktiveras mot disken och disken kopplas sedan igen igen

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Aktivera Azure Write Accelerator med Azure-portalen

Du kan aktivera Write Accelerator via portalen där du anger diskcacheinställningarna:

![Skriv accelerator på Azure-portalen](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Aktivera Azure Write Accelerator med Azure CLI

Du kan använda [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) för att aktivera Write Accelerator.

Om du vill aktivera Write Accelerator på en befintlig disk kan du använda [az vm-uppdateringen](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)och du kan använda följande exempel om du ersätter diskName, VMName och ResourceGroup med dina egna värden:`az vm update -g group1 -n vm1 -write-accelerator 1=true`

Om du vill koppla en disk med Write Accelerator aktiverat använder [az vm-disken ansluta,](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach)kan du använda följande exempel om du ersätter i dina egna värden:`az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Om du vill inaktivera Write Accelerator använder du [uppdatering av az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)och anger egenskaperna till false:`az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Aktivera skrivaccelerator med rest-API:er

Om du vill distribuera via Azure Rest API måste du installera Azure-armkliienten.

### <a name="install-armclient"></a>Installera armklient

För att köra armclient, måste du installera det genom Chocolatey. Du kan installera den via cmd.exe eller powershell. Använd förhöjda rättigheter för dessa kommandon ("Kör som administratör").

Kör följande kommando med cmd.exe:`@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Kör följande kommando med Power Shell:`Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Nu kan du installera armklienten med hjälp av följande kommando i antingen cmd.exe eller PowerShell`choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Hämta din nuvarande VM-konfiguration

Om du vill ändra attributen för diskkonfigurationen måste du först hämta den aktuella konfigurationen i en JSON-fil. Du kan hämta den aktuella konfigurationen genom att köra följande kommando:`armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Ersätt termerna i "<<   >>" med dina data, inklusive filnamnet som JSON-filen ska ha.

Utdata kan se ut så här:

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

Uppdatera sedan JSON-filen och aktivera Write Accelerator på disken som kallas log1. Detta kan åstadkommas genom att lägga till det här attributet i JSON-filen efter diskens cachepost.

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

Uppdatera sedan den befintliga distributionen med det här kommandot:`armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Utdata ska se ut som den nedan. Du kan se att Write Accelerator aktiverat för en disk.

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

När du har gjort den här ändringen bör enheten stödjas av Write Accelerator.

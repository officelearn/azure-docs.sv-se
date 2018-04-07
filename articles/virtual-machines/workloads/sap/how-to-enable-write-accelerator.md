---
title: Azure skriva snabbtangenten för SAP distributioner | Microsoft Docs
description: Handboken för SAP HANA-system som distribueras på virtuella Azure-datorer.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0cb9b4003faa2ccdd07ccc78c2095472690f0e7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>Azure skriva snabbtangenten för SAP-distributioner
Azure skriva Accelerator är en funktion som hämtar lyfts för virtuella datorer M-serien exklusivt. Azure skriva Accelerator är inte tillgänglig i alla andra VM-serien i Azure, förutom M-serien. Som namnet tillstånd, är syftet med funktionen för att förbättra i/o-fördröjningen för skrivningar mot Azure Premium-lagring. 

>[!NOTE]
> Nu Azure skriva Accelerator är tillgänglig som förhandsversion och kräver vitt-lista över Azure prenumerations-ID

Azure skriva Accelerator-funktionen är tillgänglig för distribution av M-serien som förhandsversion i:

- Västra US2
- Västeuropa
- Sydostasien

## <a name="planning-for-using-azure-write-accelerator"></a>Planering för att använda Azure skriva Accelerator
Azure skriva Accelerator ska användas för volymer som innehåller transaktionsloggen eller gör om-loggarna för ett DBMS. Det rekommenderas inte att använda Azure skriva snabbtangenten för datavolymer i en DBMS. Orsak till den här begränsningen är att Azure skriva Accelerator kräver Azure Premium Storage VHD-diskarna monteras utan ytterligare skrivskyddade cachelagring som är tillgängligt för Premium-lagring. Större fördelar med den här typen av cachelagring kan iakttas med traditionella databaser. Eftersom skriva Accelerator påverkar endast skrivåtgärder aktiviteter och inte påskynda läsningar, är stöds designen för SAP använda skriva Accelerator mot transaktionsloggen eller göra om loggen enheter av SAP stöds databaser. 

Azure skriva Accelerator fungerar bara tillsammans med [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Därför måste du planera på lämpligt sätt. 

>[!NOTE]
> Eftersom funktionerna i Azure skriva Accelerator fortfarande förhandsversion, ingen produktion scenariot distribution stöds på funktionen som ännu.

Det finns begränsningar för Azure Premium Storage virtuella hårddiskar per virtuell dator som stöds av Azure skriva Accelerator. Aktuella begränsningar är:

- 16 virtuella hårddiskar för en M128xx VM
- 8 virtuella hårddiskar för en M64xx VM

> [!IMPORTANT]
> Om du vill aktivera eller inaktivera Azure skriva snabbtangenten för en befintlig volym som har skapats utanför flera Azure Premium Storage diskar och stripe-volymer med hjälp av Windows-disken eller volymen chefer lagringsutrymmen för Windows, Windows skalbar filserver (SOFS), Linux LVM eller MDADM alla diskar skapa volymen måste vara aktiverat eller inaktiverat för skriva Accelerator i separata steg. **Innan du aktiverar eller inaktiverar skriva Accelerator i en sådan konfiguration, stänga av Virtuella Azure**. 


> [!IMPORTANT]
> Aktivera Azure skriva Accelerator till en befintlig Azure disk som inte ingår i en volym build utanför flera diskar med disk i Windows eller volymhanterare lagringsutrymmen för Windows, Windows skalbar filserver (SOFS), Linux LVM eller MDADM, arbetsbelastning åtkomst till den Azure-disken måste stängas av. Databasprogram med hjälp av Azure-disken måste stängas av.

> [!IMPORTANT]
> Aktivera skriva snabbtangenten för Azure VM operativsystemdisken för den virtuella datorn startas om den virtuella datorn. 

Aktivera Azure skriva snabbtangenten för operativsystemet diskar inte får vara nödvändiga för SAP relaterade VM-konfigurationer

### <a name="restrictions-when-using-azure-write-accelerator"></a>Begränsningar när du använder Azure skriva Accelerator
Dessa begränsningar gäller när du använder Azure skriva snabbtangenten för ett Azure disk/VHD:

- Premium-diskcachelagring måste anges till 'Ingen' eller 'Read Only'. Cachelagring lägen stöds inte.
- Ögonblicksbilder på disken skriva Accelerator aktiverad stöds inte ännu. Den blockerar Azure Backup-tjänsten möjligheten att utföra en programkonsekvent ögonblicksbild av alla diskar på den virtuella datorn.
- Endast mindre i/o-storlekar tar snabbare sökvägen. I arbetsbelastningen situationer där data blir bulk läsas in eller där transaction log buffertar för olika DBMS fylls i större utsträckning innan du hämtar beständig lagring, risken är som i/o som skrivs till disk inte tar snabbare sökvägen.


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Aktivera skriva Accelerator på en specifik disk
De följande avsnitten beskrivs hur Azure skriva Accelerator kan aktiveras på Azure Premium Storage virtuella hårddiskar.

Vid denna tidpunkt, är aktivera skriva Accelerator via Azure Rest-API och Power Shell de enda. Andra metoder för upp till stöd i Azure portal följer under nästa några veckor.

### <a name="prerequisites"></a>Förutsättningar
Följande krav gäller för användning av Azure skriva Accelerator vid denna tidpunkt:

- Ditt prenumerations-ID som användes för att distribuera virtuell dator och lagring för den virtuella datorn måste vara tomt i listan. Kontakta din Microsoft CSA GBB, eller Kontoansvariga att hämta ditt prenumerations-ID tomt angiven. 
- De diskar du vill använda Azure skriva Accelerator mot måste vara [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/).

### <a name="enabling-through-power-shell"></a>Aktivera via PowerShell
Azure Power Shell-modul från version 5.5.0 innefattar ändringar i den relevanta cmdletar för att aktivera eller inaktivera Azure skriva snabbtangenten för specifika Azure Premium Storage-diskar.
För att aktivera eller distribuera diskar som stöds av skriva Accelerator fick följande Power Shell-kommandon ändras och om du accepterar en parameter för skriva Accelerator extended.

En ny växel parameter ”WriteAccelerator” har lagts till med följande cmdlets: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Ger inte parametern anger egenskapen till false och distribuerar diskar som har inte stöd för genom att skriva Accelerator i Azure.

En ny växel parameter ”OsDiskWriteAccelerator” har lagts till med följande cmdlets: 

- Set-AzureRmVmssStorageProfile

Inte ger anger egenskapen till false och levererar diskar som inte utnyttjar Azure skriva Accelerator.

En ny valfri boolesk (icke-nullbar) parameter, ”OsDiskWriteAccelerator” har lagts till med följande cmdlets: 

- Update-AzureRmVM
- Update-AzureRmVmss

Ange antingen $true eller $false att styra support för Azure skriva Accelerator med diskarna.

Exempel på kommandon kan se ut så:

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Två huvudscenarier kan skriptas som visas i följande avsnitt.

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>Lägga till nya disken som stöds av Azure skriva Accelerator
Du kan använda det här skriptet för att lägga till en ny disk till den virtuella datorn. Disken som skapats med det här skriptet kommer att använda Azure skriva Accelerator.

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
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
Du måste anpassa namn på virtuell dator, disk och resursgrupp, storleken på disken och LunID för din distribution.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Om du aktiverar Azure skriva Accelerator på en befintlig Azure disk
Du kan använda det här skriptet för att utföra åtgärden om du behöver aktivera skriva Accelerator på en befintlig disk:

```

#Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Du måste anpassa namnen på VM, disk och resursgruppen. Skriptet ovan lägger till skriva Accelerator till en befintlig disk är värdet för $newstatus har angetts till '$true'. Med hjälp av värdet '$false' inaktiverar skriva Accelerator på en angiven disk.

> [!Note]
> Skriptet ovan ska koppla bort disk som har angetts, aktivera skriva Accelerator mot disken och ansluta disken igen




### <a name="enabling-through-rest-apis"></a>Aktivera via Rest API: er
För att kunna distribuera via Azure Rest-API, måste du installera Azure armclient

#### <a name="install-armclient"></a>Installera armclient

Om du vill köra armclient som du behöver installera via Chocolatey. Du kan installera det via cmd.exe eller powershell. Använd utökade behörigheter för dessa kommandon (”Kör som administratör”).

Använda cmd.exe kör följande kommando:

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

Med Power Shell som du behöver använda:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Nu kan du installera armclient med kommandot nedan i cmd.exe eller Power Shell

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Hämta din aktuella VM-konfiguration
För att kunna ändra attributen för diskkonfigurationen, måste du först hämta den aktuella konfigurationen i JSON-fil. Du kan hämta den aktuella konfigurationen genom att köra följande kommando:

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Ersätt villkoren i <> <>med dina data, inklusive namnet på JSON-filen ska ha.

Utdata kan se ut så:

```
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

Nästa steg är att uppdatera JSON-fil och aktivera skriva Accelerator på disken kallas 'log1'. Det här steget kan utföras genom att lägga till attributet i JSON-filen efter cacheposten på disken. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Uppdatera den befintliga distributionen med det här kommandot:

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

Resultatet bör se ut som i exemplet nedan. Du kan se att det finns skriva Accelerator som aktiverats för en disk.

```
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
          **"writeAcceleratorEnabled": true,**
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

Enheten måste ha stöd av skriva Accelerator från punkten på Ändra.

 
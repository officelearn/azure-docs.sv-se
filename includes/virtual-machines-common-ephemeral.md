---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805877"
---
Tillfälliga operativsystemdiskar skapas på lagringen för lokala virtuella datorer (VM) och inte har sparats till fjärranslutna Azure Storage. Tillfälliga operativsystemdiskar fungerar bra för tillståndslösa arbetsbelastningar, där program är toleranta för enskilda VM-fel, men är mer påverkas av tidpunkten för distribution av VM eller avbildningen på flera Virtuella datorinstanser. Med tillfälliga OS-disk får du lägre läsning och skrivning till OS-disken och snabbare återställa VM-avbildning. 
 
De viktigaste funktionerna i differentierande diskar är: 
- Perfekt för tillståndslösa program.
- De kan användas med både Marketplace och anpassade avbildningar.
- Möjligheten att snabbt återställa eller återställa avbildningen av de virtuella datorerna och skala inställd det ursprungliga tillståndet för start instanser.  
- Kortare svarstider, liknar en tillfällig disk. 
- Tillfälliga operativsystemdiskar är kostnadsfria, du betalar inga lagringskostnaden för OS-disken.
- De är tillgängliga i alla Azure-regioner. 
- Differentierande diskar som stöds av [delad bildgalleriet](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Viktiga skillnader mellan beständiga och tillfälliga OS-diskar:

|                             | Permanent OS-Disk                          | Differentierande OS-disk                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Storleksgräns för OS-disk      | 2 TiB                                                                                        | Cache-storlek för den Virtuella datorstorleken eller 2TiB, beroende på vilket som är mindre. För den **cachestorlek i GiB**, se [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), och [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| VM-storlekar som stöds          | Alla                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Stöd för diskar-typ           | Hanterade och ohanterade OS-disk                                                                | Hanterad operativsystemsdisk                                                               |
| Regionsstöd              | Alla regioner                                                                                  | Alla regioner                              |
| Datapersistens            | OS-diskdata som skrivs till OS-disken lagras i Azure Storage                                  | Data som skrivs till OS-disken lagras till enhetens lokala lagring för virtuell dator och har sparats inte till Azure Storage. |
| Frigörandet av tillstånd      | Virtuella datorer och skalningsuppsättningsinstanser kan frigörandet av och startas om från frigörandet av tillstånd | Virtuella datorer och skalningsuppsättningsinstanser får inte vara frigörandet av                                  |
| Stöd för specialiserad OS-diskar | Ja                                                                                          | Nej                                                                                 |
| OS-diskstorlek              | Under skapandet av VM och virtuella datorn är frigörandet av som stöds                                | Stöds endast skapa en virtuell dator                                                  |
| Ändra storlek till en ny VM-storlek   | OS-diskdata bevaras                                                                    | Data på OS-disken har tagits bort, OS är nytt etablerade                                      |

## <a name="size-requirements"></a>Storlekskraven

Du kan distribuera virtuella datorer och instans avbildningar upp till storleken på VM-cacheminne. Till exempel är Standard Windows Server-avbildningar från marketplace håller 127 GiB, vilket innebär att du behöver en VM-storlek som har ett cacheminne som är större än 127 GiB. I det här fallet den [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) har en cachestorlek på 86 GiB som inte är tillräckligt stor. Standard_DS2_v2 har en cachestorlek på 172 GiB som är tillräckligt stor. I det här fallet är Standard_DS3_v2 den minsta storleken i DSv2-serien som du kan använda den här avbildningen. Grundläggande Linux-avbildningar i Marketplace och Windows Server-avbildningar som med `[smallsize]` brukar vara cirka 30 GiB och kan använda de flesta av de tillgängliga storlekarna.

Differentierande diskar kräver också att VM-storleken stöder premiumlagring. Storlekarna som vanligtvis (men inte alltid) har en `s` i namn, t.ex. DSv2 och EsV3. Mer information finns i [Azure VM-storlekar](../articles/virtual-machines/linux/sizes.md) för information om vilka storlekar stöd för Premium storage.

## <a name="powershell"></a>PowerShell

Om du vill använda en differentierande disk för en PowerShell-VM-distribution, använda [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) VM-konfiguration. Ange den `-DiffDiskSetting` till `Local` och `-Caching` till `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

För distributioner skalningsuppsättning, använder den [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet i din konfiguration. Ange den `-DiffDiskSetting` till `Local` och `-Caching` till `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Om du vill använda en differentierande disk för en CLI VM-distribution, ange den `--ephemeral-os-disk` parameter i [az vm skapa](/cli/azure/vm#az-vm-create) till `true` och `--os-disk-caching` parameter `ReadOnly`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

För skalningsuppsättningar använda samma `--ephemeral-os-disk true` parametern för [az vmss create](/cli/azure/vmss#az-vmss-create) och ange den `--os-disk-caching` parameter `ReadOnly`.

## <a name="portal"></a>Portalen   

I Azure-portalen kan du välja att använda differentierande diskar när du distribuerar en virtuell dator genom att öppna den **Avancerat** delen av den **diskar** fliken. För **använda differentierande diskar** Välj **Ja**.

![Skärmbild som visar knappen för att du väljer att använda differentierande diskar](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Om alternativet för att använda en differentierande disk är nedtonat kanske du har valt en VM-storlek som inte har en cachestorlek som är större än OS-avbildning eller som inte stöder Premium storage. Gå tillbaka till den **grunderna** sidan och välja en annan VM-storlek.

Du kan också skapa skalningsuppsättningar med tillfälliga OS-diskar med hjälp av portalen. Se bara till att välja en VM-storlek med en tillräckligt stor cachestorlek och sedan i **använda differentierande diskar** Välj **Ja**.

![Skärmbild som visar knappen för att du väljer att använda differentierande diskar för din skalningsuppsättning](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Scale Sets malldistribution  
Processen för att skapa en skalningsuppsättning som använder differentierande diskar är att lägga till den `diffDiskSettings` egenskap enligt den `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` resurstyp i mallen. Principen för cachelagring måste också anges till `ReadOnly` för den differentierande disken. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Mall för distribution av virtuella datorer 
Du kan distribuera en virtuell dator med differentierande diskar med hjälp av en mall. Processen för att skapa en virtuell dator som använder differentierande OS-diskar är att lägga till den `diffDiskSettings` egenskapen till Microsoft.Compute/virtualMachines resurstypen i mallen. Principen för cachelagring måste också anges till `ReadOnly` för den differentierande disken. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Återställ avbildning av en virtuell dator med hjälp av REST
För närvarande är det enda sättet att återställa avbildningen av en virtuell datorinstans med differentierande diskar med hjälp av REST API. För skalningsuppsättningar är avbildningen redan tillgänglig via Powershell, CLI och portalen.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Vad är storleken på den lokala OS-diskar?**

S: Vi stöder plattform och anpassade avbildningar upp till den virtuella dator cachestorleken, där alla Läs/skrivningar till OS-disken ska vara lokal på samma nod som den virtuella datorn. 

**F: Kan den differentierande disken ändras?**

S: Nej, när den differentierande disken har etablerats OS-disken kan inte ändras. 

**F: Kan jag koppla hanterade diskar till en differentierande virtuell dator?**

S: Ja, du kan koppla en hanterad datadisk till en virtuell dator som använder differentierande diskar. 

**F: Alla storlekar som stöds för tillfälliga operativsystemdiskar?**

S: Nej, alla storlekar för virtuella datorer i Premium Storage kan stöds (DS, ES, FS, GS och M) utom B-serien, N-serien och H-serien storlekar.  
 
**F: Kan den differentierande disken tillämpas på befintliga virtuella datorer och skalningsuppsättningar?**

S: Nej, tillfälliga OS-disk kan bara användas under VM och skala skapas. 

**F: Kan du blanda tillfälliga och normal OS-diskar i en skalningsuppsättning?**

S: Nej, du kan inte ha en blandning av tillfälliga och permanenta OS diskinstanser inom samma skalningsuppsättningen. 

**F: Kan den differentierande disken skapas med hjälp av Powershell eller CLI?**

S: Ja, du kan skapa virtuella datorer med differentierande diskar med hjälp av REST, mallar, PowerShell och CLI.

**F: Vilka funktioner stöds inte med differentierande diskar?**

S: Differentierande diskar stöder inte:
- Skapa VM-avbildningar
- Ögonblicksbilder 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- OS-disken växling 

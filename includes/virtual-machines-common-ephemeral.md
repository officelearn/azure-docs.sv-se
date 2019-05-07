---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d7737f73ee4eb9ae9dc8c4845020b7543a5b3495
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159172"
---
Tillfälliga operativsystemdiskar skapas i den lokala lagringen för virtuell dator (VM) och inte sparats till fjärranslutna Azure Storage. Tillfälliga operativsystemdiskar fungerar bra för tillståndslösa arbetsbelastningar där program är toleranta för enskilda VM-fel, men är mer orolig för den tid det tar för storskaliga distributioner eller tid att återställa avbildningen av de enskilda Virtuella datorinstanserna. Det är också lämpliga för program som distribueras med den klassiska distributionsmodellen för att flytta till Resource Manager-distributionsmodellen. Du skulle se lägre läsning och skrivning till OS-disken och snabbare återställa VM-avbildning med tillfälliga OS-disken. Dessutom kan tillfälliga OS-disken är kostnadsfria, du betalar inga lagringskostnaden för OS-disken. 
 
De viktigaste funktionerna i differentierande diskar är: 
- De kan användas med både Marketplace-avbildningar och anpassade avbildningar.
- Du kan distribuera VM-avbildningar upp till storleken på VM-cacheminne.
- Möjlighet att snabbt återställa eller återställa avbildningen av de virtuella datorerna till det ursprungliga tillståndet för start.  
- Lägre körning svarstid liknar en tillfällig disk. 
- Utan kostnad för OS-disken. 
 
 
Viktiga skillnader mellan beständiga och tillfälliga OS-diskar:

|                             | Permanent OS-Disk                          | Differentierande OS-disk                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Storleksgräns för OS-disk      | 2 TiB                                                                                        | Cache-storlek för den Virtuella datorstorleken eller 2TiB, beroende på vilket som är mindre - [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), och [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| VM-storlekar som stöds          | Alla                                                                                          | DSv1, DSv2, DSv3, Esv2, Fs, FsV2, GS, M                                               |
| Stöd för diskar-typ           | Hanterade och ohanterade OS-disk                                                                | Hanterad operativsystemsdisk                                                               |
| Regionsstöd              | Alla regioner                                                                                  | Alla regioner                              |
| Datapersistens            | OS-diskdata som skrivs till OS-disken lagras i Azure Storage                                  | Data som skrivs till OS-disken lagras till enhetens lokala lagring för virtuell dator och har sparats inte till Azure Storage. |
| Frigörandet av tillstånd      | Virtuella datorer och skalningsuppsättningsinstanser kan frigörandet av och startas om från frigörandet av tillstånd | Virtuella datorer och skalningsuppsättningsinstanser får inte vara frigörandet av                                  |
| Stöd för specialiserad OS-diskar | Ja                                                                                          | Nej                                                                                 |
| OS-diskstorlek              | Under skapandet av VM och virtuella datorn är frigörandet av som stöds                                | Stöds endast skapa en virtuell dator                                                  |
| Ändra storlek till en ny VM-storlek   | OS-diskdata bevaras                                                                    | Data på OS-disken har tagits bort, OS är nytt etablerade                                      |

## <a name="register-for-the-preview"></a>Registrera dig för förhandsversionen


Registrera sig för förhandsversionen av tillfälliga OS-diskar med hjälp av den senaste versionen av Azure CLI eller Azure PowerShell.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzRmProviderFeature –FeatureName LocalDiffDiskPreview
```

Så här kontrollerar du om du är registrerad för förhandsversionen:

```azurepowershell-interactive
Get-AzRmProviderFeature –FeatureName LocalDiffDiskPreview
```

### <a name="cli"></a>CLI

```azurecli-interactive
az provider register --namespace Microsoft.Compute
az feature register --namespace Microsoft.Compute --name LocalDiffDiskPreview
```

Så här kontrollerar du om du är registrerad för förhandsversionen:
 
```azurecli-interactive
az provider show –namespace ‘Microsoft.Compute’
```


## <a name="scale-set-deployment"></a>Scale Sets distribution  
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

## <a name="vm-deployment"></a>Distribution av virtuella datorer 
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

S: I förhandsvisningen stöder vi plattform och/eller avbildningar upp till den virtuella dator cachestorleken, där alla Läs/skrivningar till OS-disken ska vara lokal på samma nod som den virtuella datorn. 

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

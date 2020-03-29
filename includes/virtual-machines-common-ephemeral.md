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
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155454"
---
Efemära OS-diskar skapas på den lokala virtuella datorn (VM) lagring och inte sparas i fjärrEn Azure Storage. Efemära OS-diskar fungerar bra för tillståndslösa arbetsbelastningar, där program är toleranta mot enskilda vm-fel, men påverkas mer av vm-distributionstid eller ominställa de enskilda VM-instanserna. Med Ephemeral OS-disk får du lägre läs-/skrivfördröjning till OS-disken och snabbare omimage för virtuella datorer. 
 
De viktigaste funktionerna i efemära diskar är: 
- Perfekt för tillståndslösa applikationer.
- De kan användas med både Marketplace och anpassade bilder.
- Möjlighet att snabbt återställa eller reimage virtuella datorer och skala uppsättning instanser till det ursprungliga starttillståndet.  
- Lägre svarstid, liknande en tillfällig disk. 
- Efemära OS-diskar är gratis, du ådrar dig ingen lagringskostnad för OS-disk.
- De är tillgängliga i alla Azure-regioner. 
- Ephemeral OS Disk stöds av [Shared Image Gallery](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Viktiga skillnader mellan beständiga och efemära OS-diskar:

|                             | Beständig OS-disk                          | Differentierande OS-disk                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Storleksgräns för OS-disk      | 2 TiB                                                                                        | Cachestorlek för vm-storlek eller 2TiB, beroende på vilket som är mindre. För **cachestorleken i GiB**finns i [DS,](../articles/virtual-machines/linux/sizes-general.md) [ES,](../articles/virtual-machines/linux/sizes-memory.md) [M,](../articles/virtual-machines/linux/sizes-memory.md) [FS](../articles/virtual-machines/linux/sizes-compute.md)och [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| VM-storlekar stöds          | Alla                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Stöd för disktyp           | Hanterad och ohanterad OS-disk                                                                | Endast hanterad OS-disk                                                               |
| Stöd för regionen              | Alla regioner                                                                                  | Alla regioner                              |
| Data persistens            | OS-diskdata som skrivits till OS-disk lagras i Azure Storage                                  | Data som skrivs till OS-disk lagras i den lokala VM-lagringen och sparas inte i Azure Storage. |
| Stop-deallocated tillstånd      | Virtuella datorer och skalningsuppsättningsinstanser kan stoppas och startas om från det mellandelade tillståndet | Virtuella datorer och skalningsuppsättningsinstanser kan inte stoppas                                  |
| Stöd för specialiserad disk | Ja                                                                                          | Inga                                                                                 |
| Operativsystemets storleksstorlek              | Stöds under skapande av virtuella datorer och efter att virtuell dator har stop-deallocated                                | Stöds endast vid skapande av virtuella datorer                                                  |
| Ändra storlek på en ny vm-storlek   | OS-diskdata bevaras                                                                    | Data på OS-disken tas bort, OS återupprättas                                      |

## <a name="size-requirements"></a>Krav på storlek

Du kan distribuera vm- och instansavbildningar upp till storleken på den virtuella datorns cache. Standard windows server-avbildningar från marknadsplatsen är till exempel ca 127 GiB, vilket innebär att du behöver en vm-storlek som har en cache som är större än 127 GiB. I det här fallet har [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) en cachestorlek på 86 GiB, vilket inte är tillräckligt stort. Den Standard_DS3_v2 har en cache storlek 172 GiB, vilket är tillräckligt stor. I det här fallet är Standard_DS3_v2 den minsta storleken i DSv2-serien som du kan använda med den här bilden. Grundläggande Linux-avbildningar på Marketplace och Windows `[smallsize]` Server-avbildningar som betecknas med tenderar att vara runt 30 GiB och kan använda de flesta av de tillgängliga VM-storlekarna.

Efemära diskar kräver också att vm-storleken stöder Premium-lagring. Storlekarna vanligt (men inte alltid) har en `s` i det känt, något liknande DSv2 och EsV3. Mer information finns i [Azure VM-storlekar](../articles/virtual-machines/linux/sizes.md) för information om vilka storlekar som stöder Premium-lagring.

## <a name="powershell"></a>PowerShell

Om du vill använda en efemär disk för en PowerShell VM-distribution använder du [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) i vm-konfigurationen. Ställ `-DiffDiskSetting` in `Local` `-Caching` till `ReadOnly`och till .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

För skalningsuppsättningsdistributioner använder du cmdleten [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) i konfigurationen. Ställ `-DiffDiskSetting` in `Local` `-Caching` till `ReadOnly`och till .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Om du vill använda en efemär disk `--ephemeral-os-disk` för en CLI VM-distribution anger du parametern i [az vm create](/cli/azure/vm#az-vm-create) to `true` and the `--os-disk-caching` parameter to `ReadOnly`.

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

För skalningsuppsättningar `--ephemeral-os-disk true` använder du samma parameter för [az-vmss-create](/cli/azure/vmss#az-vmss-create) och ställer in parametern `--os-disk-caching` på `ReadOnly`.

## <a name="portal"></a>Portalen   

I Azure-portalen kan du välja att använda tillfälliga diskar när du distribuerar en virtuell dator genom att öppna avsnittet **Avancerat** på fliken **Diskar.** För **Använd efemära OS-disk** väljer du **Ja**.

![Skärmbild som visar alternativknappen för att välja att använda en efemär OS-disk](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Om alternativet för att använda en efemär disk är nedtonat kan du ha valt en vm-storlek som inte har en cachestorlek som är större än os-avbildningen eller som inte stöder Premium-lagring. Gå tillbaka till sidan **Grunderna** och prova att välja en annan vm-storlek.

Du kan också skapa skalningsuppsättningar med efemära OS-diskar med hjälp av portalen. Se bara till att du väljer en VM-storlek med tillräckligt stor cachestorlek och sedan i **Använd efemära OS-disken** välj **Ja**.

![Skärmbild som visar alternativknappen för att välja att använda en efemär OS-disk för din skalningsuppsättning](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Skalningsuppsättningsmalldistribution  
Processen för att skapa en skalningsuppsättning som använder en `diffDiskSettings` efemär OS-disk är att lägga till egenskapen `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` i resurstypen i mallen. Cachelagringsprincipen måste också ställas in `ReadOnly` på för den efemära OS-disken. 


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

## <a name="vm-template-deployment"></a>Distribution av VM-mall 
Du kan distribuera en virtuell dator med en efemär OS-disk med hjälp av en mall. Processen för att skapa en virtuell dator som använder efemära OS-diskar är att lägga till egenskapen `diffDiskSettings` i microsoft.Compute/virtualMachines-resurstypen i mallen. Cachelagringsprincipen måste också ställas in `ReadOnly` på för den efemära OS-disken. 

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


## <a name="reimage-a-vm-using-rest"></a>Reimage en virtuell dator med REST
Du kan använda en virtuell dator-instans med efemära OS-disk med REST API enligt beskrivningen nedan och via Azure Portal genom att gå till översiktsfönstret för den virtuella datorn. För skalningsuppsättningar är omimering redan tillgänglig via Powershell, CLI och portalen.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Vad är storleken på de lokala OS-diskarna?**

S: Vi stöder plattform och anpassade avbildningar, upp till den virtuella datorns cachestorlek, där alla läsning/skrivningar till OS-disken kommer att vara lokala på samma nod som den virtuella datorn. 

**F: Kan den efemära OS-disken storleksas?**

S: Nej, när den efemära OS-disken har etablerats kan OS-disken inte storleksas om. 

**F: Kan jag koppla en hanterad disk till en tillfällig virtuell dator?**

S: Ja, du kan koppla en hanterad datadisk till en virtuell dator som använder en efemär OS-disk. 

**F: Stöds alla VM-storlekar för efemära OS-diskar?**

S: Nej, alla VM-storlekar för Premium Storage stöds (DS, ES, FS, GS och M) förutom storlekarna I B-serien, N-serien och H-serien.  
 
**F: Kan den tillfälliga OS-disken tillämpas på befintliga virtuella datorer och skalningsuppsättningar?**

S: Nej, efemära OS-disk kan endast användas under vm och skalningsuppsättning skapande. 

**F: Kan du blanda efemära och normala OS-diskar i en skalningsuppsättning?**

S: Nej, du kan inte ha en blandning av efemära och beständiga OS-diskinstanser inom samma skalningsuppsättning. 

**F: Kan den efemära OS-disken skapas med Powershell eller CLI?**

S: Ja, du kan skapa virtuella datorer med Ephemeral OS Disk med REST, Mallar, PowerShell och CLI.

**F: Vilka funktioner stöds inte med efemära OS-disk?**

S: Efemära diskar stöder inte:
- Ta bilder av virtuella datorer
- Ögonblicksbilder 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Diskbyte för operativsystem 

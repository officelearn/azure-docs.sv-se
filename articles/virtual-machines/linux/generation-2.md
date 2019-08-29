---
title: Azure-stöd för virtuella datorer i generation 2 (för hands version) | Microsoft Docs
description: Översikt över Azure-stöd för virtuella datorer i generation 2
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 9d94c4be90b408da7635f47567aa8f713f14de86
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083193"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Stöd för virtuella datorer i generation 2 (för hands version) på Azure

> [!IMPORTANT]
> Azure-stöd för virtuella datorer i generation 2 är för närvarande en för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna. 

Stöd för virtuella datorer i generation 2 (VM) är nu tillgängligt i för hands versionen i Azure. Du kan inte ändra en virtuell dators generation när du har skapat den, så Läs igenom övervägandena på den här sidan innan du väljer en generation. 

Virtuella datorer i generation 2 stöder viktiga funktioner som inte stöds i virtuella datorer i generation 1. Dessa funktioner omfattar ökat minne, Intel Software Guard-tillägg (Intel SGX) och virtualiserat beständigt minne (vPMEM). Virtuella datorer i generation 2 har också vissa funktioner som inte stöds i Azure ännu. Mer information finns i avsnittet [funktioner och funktioner](#features-and-capabilities) .

Virtuella datorer i generation 2 använder den nya UEFI-baserade start arkitekturen i stället för den BIOS-baserade arkitekturen som används av virtuella datorer i generation 1. Jämfört med virtuella datorer i generation 1 kan virtuella datorer i generation 2 ha förbättrade start-och installations tider. En översikt över virtuella datorer i generation 2 och några av skillnaderna mellan generation 1 och generation 2 finns i [ska jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Generation 2 VM-storlekar

Virtuella datorer i generation 1 stöds av alla VM-storlekar i Azure. Azure erbjuder nu för hands version 2-stöd för följande valda VM-serien:

* [B-serien](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [Dsv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) och [Dsv3-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [Ls-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) och [Lsv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)
* [NCv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv2-series) och [NCv3-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv3-series)
* [ND-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nd-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Generation 2 VM-avbildningar på Azure Marketplace

Virtuella datorer i generation 2 stöder följande Marketplace-avbildningar:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Lokalt eller Virtuella Azure-datorer i generation 2

Azure stöder för närvarande inte några av de funktioner som lokala Hyper-V stöder för virtuella datorer i generation 2.

| Generation 2-funktion                | Lokal Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Säker start                         | :heavy_check_mark:  | :x:   |
| Avskärmad virtuell dator                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Virtualiseringsbaserad säkerhet (VBS) | :heavy_check_mark:  | :x:   |
| VHDX-format                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funktioner och funktioner

### <a name="generation-1-vs-generation-2-features"></a>Generation 1 vs. generation 2-funktioner

| Funktion | Generation 1 | Generation 2 |
|---------|--------------|--------------|
| Start             | PCAT                      | UEFI                               |
| Disk styrenheter | IDE                       | SCSI                               |
| VM-storlekar         | Alla VM-storlekar | Endast virtuella datorer som stöder Premium Storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Funktioner för generation 1 vs. generation 2

| Funktion | Generation 1 | Generation 2 |
|------------|--------------|--------------|
| OS-disk > 2 TB                    | :x:                        | :heavy_check_mark: |
| Anpassad disk/avbildning/växla OS         | :heavy_check_mark:         | :heavy_check_mark: |
| Stöd för skalnings uppsättning för virtuell dator | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/säkerhets kopiering                        | :heavy_check_mark:         | :x:                |
| Delat avbildningsgalleri              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Skapar en virtuell dator i generation 2

### <a name="marketplace-image"></a>Marketplace-avbildning

I Azure Portal eller Azure CLI kan du skapa virtuella datorer i generation 2 från en Marketplace-avbildning som stöder UEFI-start.

`windowsserver-gen2preview` Erbjudandet innehåller endast Windows generation 2-avbildningar. Den här förpackningen förhindrar förvirring mellan generation 1 och generation 2-avbildningar. Om du vill skapa en virtuell dator i generation 2 väljer du avbildningar från det här erbjudandet och följer standard processen för att skapa den virtuella datorn.

Marketplace erbjuder för närvarande följande Windows generation 2-avbildningar:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

I avsnittet [funktioner och funktioner](#features-and-capabilities) finns en aktuell lista över Marketplace-avbildningar som stöds.

### <a name="managed-image-or-managed-disk"></a>Hanterad avbildning eller hanterad disk

Du kan skapa en virtuell dator i generation 2 från en hanterad avbildning eller en hanterad disk på samma sätt som du skapar en virtuell dator i generation 1.

### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Du kan också skapa virtuella datorer i generation 2 med hjälp av skalnings uppsättningar för virtuella datorer. I Azure CLI använder du Azure Scale set för att skapa virtuella datorer i generation 2.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Är generation 2 virtuella datorer tillgängliga i alla Azure-regioner?**  
    Ja. Men alla [VM-storlekar i generation 2](#generation-2-vm-sizes) är inte tillgängliga i varje region. Tillgängligheten för den virtuella datorn i generation 2 beror på tillgängligheten för VM-storleken.

* **Finns det en pris skillnad mellan virtuella datorer i generation 1 och generation 2?**  
    Nej.

* **Hur gör jag för att du öka storleken på operativ system disken?**  
  OS-diskar som är större än 2 TB är nya för virtuella datorer i generation 2. Som standard är OS-diskar mindre än 2 TB för virtuella datorer i generation 2. Du kan öka disk storleken upp till ett rekommenderat maximum på 4 TB. Använd Azure CLI eller Azure Portal för att öka storleken på operativ system disken. Information om hur du expanderar diskar program mässigt finns i [ändra storlek på en disk](expand-disks.md).

  För att öka storleken på operativ system disken från Azure Portal:

  1. Gå till sidan Egenskaper för virtuell dator i Azure Portal.
  1. Välj **stopp** -knappen om du vill stänga av och frigöra den virtuella datorn.
  1. I avsnittet **diskar** väljer du den OS-disk som du vill öka.
  1. I avsnittet **diskar** väljer du **konfiguration**och uppdaterar **storleken** till det värde som du vill använda.
  1. Gå tillbaka till sidan Egenskaper för virtuell dator och **Starta** den virtuella datorn.

  Du kan se en varning för OS-diskar som är större än 2 TB. Varningen gäller inte för virtuella datorer i generation 2. OS-disk storlekar som är större än 4 TB *rekommenderas dock inte.*

* **Stöder virtuella datorer i generation 2 stöd för accelererat nätverk?**  
    Ja. Mer information finns i [skapa en virtuell dator med accelererat nätverk](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Stöds VHDX i generation 2?**  
    Nej, generation 2-VM: ar stöder bara VHD.

* **Har virtuella datorer i generation 2 stöd för Azure Ultra Disklagring?**  
    Ja.

* **Kan jag migrera en virtuell dator från generation 1 till generation 2?**  
    Nej, du kan inte ändra genereringen av en virtuell dator när du har skapat den. Om du behöver växla mellan VM-generationer skapar du en ny virtuell dator med en annan generation.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [virtuella datorer i generation 2 i Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

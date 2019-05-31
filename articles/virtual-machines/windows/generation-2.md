---
title: Generation 2 virtuella datorer (förhandsversion) på Azure | Microsoft Docs
description: Översikt över Azure generering 2 virtuella datorer
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 1dcc0d3a652ccbf365a18ce734a54dc78515b1a7
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388355"
---
# <a name="generation-2-vms-preview-on-azure"></a>Generation 2 virtuella datorer (förhandsversion) på Azure

> [!IMPORTANT]
> Virtuella datorer i generation 2 finns för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Stöd för generering 2 virtuella maskiner (VM) är nu tillgängligt som offentlig förhandsversion på Azure. Du kan inte ändra en virtuell dator generation när du har skapat den. Därför rekommenderar vi att du läser överväganden [här](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) samt information om den här sidan innan du väljer en generation.

Generation 2 virtuella datorer support viktiga funktioner som inte stöds på virtuella datorer i generation 1, till exempel: ökat minne, Intel® programvara Guard tillägg (SGX) och virtuella beständigt minne (vPMEM). Virtuella datorer i generation 2 har också vissa funktioner som ännu inte stöds på Azure. Mer information finns i den [funktioner](#features-and-capabilities) avsnittet.

Virtuella datorer i generation 2 använda de nya arkitekturen i stället för UEFI-baserade Start i BIOS-baserad arkitektur som används av virtuella datorer i generation 1. Jämfört med virtuella datorer i generation 1, kan virtuella datorer i generation 2 har förbättrat start- och gånger. En översikt över virtuella datorer i generation 2 och några av de viktigaste skillnaderna mellan generation 1 och generation 2 finns i [bör jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Generation 2 VM-storlekar

Virtuella datorer i generation 1 stöds av alla VM-storlekar i Azure. Azure erbjuder nu Generation 2 stöd för följande valda VM-serierna i offentlig förhandsversion:

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) och [Dsv3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#gs-series)
* [Ls-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) och [Lsv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Generation 2 VM-avbildningar på Azure Marketplace

Virtuella datorer i generation 2 stöd för följande Azure Marketplace-avbildningar:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Jämfört med lokala virtuella datorer i Azure generation 2

Azure stöder för närvarande inte några av de funktioner som en lokal Hyper-V har stöd för virtuella datorer i Generation 2.

| Generation 2-funktionen                | En lokal Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Secure Boot                         | :heavy_check_mark:  | :x:   |
| Avskärmad virtuell dator                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Virtualiseringsbaserad säkerhet (VBS) | :heavy_check_mark:  | :x:   |
| VHDX-format                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funktioner och egenskaper

### <a name="generation-1-vs-generation-2-features"></a>Generation 1 eller generation 2-funktioner

| Funktion | Generation 1 | Generation 2 |
|---------|--------------|--------------|
| Start             | PCAT                      | UEFI                               |
| Styrenheter | IDE                       | SCSI                               |
| VM-storlekar         | Tillgängligt på alla VM-storlekar | Premium-lagring stöds endast virtuella datorer |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generation 1 eller generation 2-funktioner

| Funktion | Generation 1 | Generation 2 |
|------------|--------------|--------------|
| OS disk > 2 TB                    | :x:                        | :heavy_check_mark: |
| Den anpassade avbildningen-Disk/växling OS         | :heavy_check_mark:         | :heavy_check_mark: |
| Stöd för VM-skalningsuppsättningen | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/säkerhetskopiering                        | :heavy_check_mark:         | :x:                |
| Delat bildgalleri              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Skapa en generation 2 VM

### <a name="marketplace-image"></a>Marketplace-avbildning

Virtuella datorer i generation 2 kan skapas från en marketplace-avbildning (som har stöd för UEFI-startmetod) via Azure portal eller Azure CLI.

Den `windowsserver-gen2preview` erbjudandet innehåller Windows generation 2 avbildningar. På så sätt undviker förvirring för generation 1 eller generation 2-avbildningar. Om du vill skapa generation 2 virtuella datorer, Välj **avbildningar** från detta erbjuder och följer standard VM-skapandeprocessen.

För närvarande följande Windows-generation 2 bilder har publicerats på Azure Marketplace:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Se avsnittet funktioner för en lista i stöds marketplace-avbildningar som vi kommer att fortsätta att lägga till ytterligare bilder som har stöd för Generation 2.

### <a name="managed-image-or-managed-disk"></a>Hanterad avbildning eller en hanterad disk

Generation 2 virtuella datorer kan skapas från hanterad avbildning eller en hanterad disk på samma sätt som du skapar en generation 1 VM.

### <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för virtuella datorer

Generation 2 virtuella datorer kan även skapas med hjälp av VM-skalningsuppsättningar. Du kan skapa generation 2 virtuella datorer med hjälp av Azure VM-skalningsuppsättningar via Azure CLI.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Är generation 2 virtuella datorer är tillgängliga i alla Azure-regioner?**  
    Ja. men inte alla [generation 2 VM-storlekar](#generation-2-vm-sizes) är tillgängliga i varje region. Tillgängligheten för generation 2 virtuella datorer är beroende av tillgängligheten för VM-storleken.

* **Finns det en prisskillnader mellan generation 1 och generation 2 virtuella datorer?**  
    Det finns ingen skillnad i kostnaden för generation 1 och virtuella datorer i generation 2.

* **Hur ökar jag OS-diskstorlekar på?**  
  OS-diskar som är större än 2 TB är nya för generering 2 virtuella datorer. De flesta OS-diskar är mindre än 2 TB för virtuella datorer i generation 2 som standard, men diskens storlek kan ökas till högst rekommenderade 4 TB. Du kan öka OS-diskstorlekar på via Azure CLI eller Azure-portalen. Läs mer på expanderande diskar programmässigt [ändra storlek på en disk](expand-os-disk.md).

  Så här ökar OS-diskstorlekar på Azure Portal:

  * Gå till egenskapssidan för virtuell dator på Azure portal.

  * Stänga av och frigör den virtuella datorn med hjälp av den **stoppa** knappen på Azure portal.

  * I den **diskar** väljer du den OS-disk som du vill öka.

  * Välj **Configuration** i den **diskar** avsnittet och uppdatera den **storlek** till önskat värde.

  * Gå tillbaka till egenskapssidan för virtuell dator och **starta** den virtuella datorn.
  
  Du kan se en varning för OS-diskar som är större än 2 TB. Varningen gäller inte för virtuella datorer i generation 2; OS-diskstorlekar större än 4 TB är dock **rekommenderas inte.**

* **Stöder virtuella datorer i generation 2 Accelererat nätverk?**  
    Ja, support för virtuella datorer av generation 2 [Accelerated Networking](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Stöds .vhdx för generation 2?**  
    Nej, endast VHD stöds på virtuella datorer i generation 2.

* **Stöder virtuella datorer i generation 2 Ultra Solid tillstånd-hårddiskar (SSD)?**  
    Ja, virtuella datorer i generation 2 stöder Ultra SSD.

* **Kan jag migrera från generation 1 till virtuella datorer i generation 2?**  
    Nej, du kan inte ändra generering av en virtuell dator när du har skapat den. Om du vill växla mellan VM generationer som du behöver skapa en ny virtuell dator av en annan generation.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [generering 2 virtuella maskiner i Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Lär dig hur du [Förbered en virtuell Hårddisk](prepare-for-upload-vhd-image.md) för överföring från en lokal plats till Azure.

---
title: Azure-support för virtuella datorer i generation 2 (förhandsversion) | Microsoft Docs
description: Översikt över Azure-support för virtuella datorer i generation 2
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 7abc1b7cd3be775af03aeaf558f631060cc6f81c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722723"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Stöd för generering 2 virtuella datorer (förhandsversion) på Azure

> [!IMPORTANT]
> Azure-support för virtuella datorer i generation 2 förhandsvisas just nu.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Stöd för generering 2 virtuella maskiner (VM) är nu tillgänlig i förhandsgranskningsversion i Azure. Du kan inte ändra en virtuell dator generation när du har skapat den, så gå igenom överväganden på den här sidan innan du väljer en generation. 

Virtuella datorer i generation 2 stöder viktiga funktioner som inte stöds i virtuella datorer i generation 1. Dessa funktioner omfattar ökad minne, Intel programtillägg Guard (Intel SGX) och virtualiserade beständigt minne (vPMEM). Virtuella datorer i generation 2 har också vissa funktioner som ännu inte stöds i Azure. Mer information finns i den [funktioner](#features-and-capabilities) avsnittet.

Virtuella datorer i generation 2 använder den nya arkitekturen i UEFI-baserade Start i stället för att BIOS-baserad arkitektur som används av generation 1 virtuella datorer. Jämfört med virtuella datorer i generation 1, kan virtuella datorer i generation 2 har förbättrat start- och gånger. En översikt över virtuella datorer i generation 2 och några av skillnaderna mellan generation 1 och generation 2 finns i [bör jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Generation 2 VM-storlekar

Virtuella datorer i generation 1 stöds av alla VM-storlekar i Azure. Förhandsversion av generation 2 stöd för följande valda VM-serie erbjuder nu i Azure:

* [Dsv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) och [Dsv3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Ls-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) och [Lsv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Generation 2 VM-avbildningar på Azure Marketplace

Virtuella datorer i generation 2 stöder följande Marketplace-avbildningar:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Jämfört med lokala Virtuella datorer i Azure generation 2

Azure stöder för närvarande inte några av de funktioner som en lokal Hyper-V har stöd för virtuella datorer i generation 2.

| Generation 2-funktionen                | En lokal Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Säker Start                         | :heavy_check_mark:  | :x:   |
| Avskärmad virtuell dator                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Virtualiseringsbaserad säkerhet (VBS) | :heavy_check_mark:  | :x:   |
| VHDX-format                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funktioner och egenskaper

### <a name="generation-1-vs-generation-2-features"></a>Generation 1 och generation 2-funktioner

| Funktion | Generation 1 | Generation 2 |
|---------|--------------|--------------|
| Start             | PCAT                      | UEFI                               |
| Styrenheter | IDE                       | SCSI                               |
| VM-storlekar         | Alla VM-storlekar | Endast virtuella datorer som stöd för premium storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generation 1 och generation 2-funktioner

| Funktion | Generation 1 | Generation 2 |
|------------|--------------|--------------|
| OS disk > 2 TB                    | :x:                        | :heavy_check_mark: |
| Anpassad disk/bild/växling OS         | :heavy_check_mark:         | :heavy_check_mark: |
| Stöd för VM-skalningsuppsättningen | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/säkerhetskopiering                        | :heavy_check_mark:         | :x:                |
| Delade bildgalleri              | :heavy_check_mark:         | :x:                |
| Azure-diskkryptering             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Skapa en generation 2 VM

### <a name="marketplace-image"></a>Marketplace-avbildning

I Azure-portalen eller Azure CLI kan skapa du generation 2 virtuella datorer från en Marketplace-avbildning som har stöd för UEFI-Startmetod.

Den `windowsserver-gen2preview` erbjudandet innehåller Windows generation 2 avbildningar. Den här paketering undviks förvirring mellan generation 1 och generation 2-avbildningar. Skapa en generation 2 virtuella dator väljer **avbildningar** från detta erbjuder och Följ standardprocessen för att skapa den virtuella datorn.

Marketplace erbjuder för närvarande följande Windows-generation 2 avbildningar:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Se den [funktioner](#features-and-capabilities) avsnittet för att en aktuell lista av Marketplace-avbildningar som stöds.

### <a name="managed-image-or-managed-disk"></a>Hanterad avbildning eller en hanterad disk

Du kan skapa en generation 2 VM från en hanterad avbildning eller en hanterad disk på samma sätt som du skapar en generation 1 VM.

### <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för virtuella datorer

Du kan också skapa generation 2 virtuella datorer med hjälp av VM-skalningsuppsättningar. Använd Azure scale anger skapa generation 2 virtuella datorer i Azure-CLI.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Är generation 2 virtuella datorer är tillgängliga i alla Azure-regioner?**  
    Ja. Men inte alla [generation 2 VM-storlekar](#generation-2-vm-sizes) är tillgängliga i varje region. Tillgängligheten för generation 2 VM beror på tillgängligheten för VM-storleken.

* **Finns det en prisskillnader mellan generation 1 och generation 2 virtuella datorer?**  
   Nej.

* **Hur ökar jag OS-diskstorlekar på?**  
  OS-diskar som är större än 2 TB är nya för generering 2 virtuella datorer. OS-diskar är mindre än 2 TB för virtuella datorer i generation 2 som standard. Du kan öka diskstorleken rekommenderade upp till 4 TB. Använda Azure CLI eller Azure-portalen för att öka OS-disken. Information om hur du expanderar diskar programmässigt finns i [ändra storlek på en disk](expand-os-disk.md).

  Att öka OS-disken från Azure portal:

  1. Gå till egenskapssidan för virtuell dator i Azure-portalen.
  1. Om du vill stänga av och frigör den virtuella datorn, Välj den **stoppa** knappen.
  1. I den **diskar** väljer du den OS-disken som du vill öka.
  1. I den **diskar** väljer **Configuration**, och uppdatera den **storlek** till det värde du vill.
  1. Gå tillbaka till egenskapssidan för virtuell dator och **starta** den virtuella datorn.
  
  Du kan se en varning för OS-diskar som är större än 2 TB. Varningen gäller inte för virtuella datorer i generation 2. OS-diskstorlekar större än 4 TB är dock *rekommenderas inte.*

* **Göra generation 2 stöd för virtuella datorer accelererat nätverk?**  
    Ja. Mer information finns i [skapa en virtuell dator med accelererat nätverk](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Stöds VHDX på generation 2?**  
    Nej, virtuella datorer i generation 2 stöder bara virtuella Hårddisken.

* **Stöder virtuella datorer i generation 2 Azure Ultra Disk Storage?**  
    Ja.

* **Kan jag migrera en virtuell dator från generation 1 till generation 2?**  
    Nej, du kan inte ändra generering av en virtuell dator när du har skapat. Om du vill växla mellan VM generationer kan du skapa en ny virtuell dator av en annan generation.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [generering 2 virtuella maskiner i Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Lär dig hur du [Förbered en virtuell Hårddisk](prepare-for-upload-vhd-image.md) att ladda upp från lokala system till Azure.

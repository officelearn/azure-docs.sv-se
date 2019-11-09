---
title: Azure-stöd för virtuella datorer i generation 2 | Microsoft Docs
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
ms.date: 11/04/2019
ms.author: lahugh
ms.openlocfilehash: b5cc4a511d6347d28a1737932c1b4b5b768d3fc4
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891560"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Stöd för virtuella datorer i generation 2 i Azure

Stöd för virtuella datorer i generation 2 (VM) är nu tillgängligt på Azure. Du kan inte ändra en virtuell dators generation när du har skapat den, så Läs igenom övervägandena på den här sidan innan du väljer en generation.

Virtuella datorer i generation 2 stöder viktiga funktioner som inte stöds i virtuella datorer i generation 1. Dessa funktioner omfattar ökat minne, Intel Software Guard-tillägg (Intel SGX) och virtualiserat beständigt minne (vPMEM). Generation 2 virtuella datorer som körs lokalt, har vissa funktioner som inte stöds i Azure än. Mer information finns i avsnittet [funktioner och funktioner](#features-and-capabilities) .

Virtuella datorer i generation 2 använder den nya UEFI-baserade start arkitekturen i stället för den BIOS-baserade arkitekturen som används av virtuella datorer i generation 1. Jämfört med virtuella datorer i generation 1 kan virtuella datorer i generation 2 ha förbättrade start-och installations tider. En översikt över virtuella datorer i generation 2 och några av skillnaderna mellan generation 1 och generation 2 finns i [ska jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Generation 2 VM-storlekar

Virtuella datorer i generation 1 stöds av alla VM-storlekar i Azure. Azure erbjuder nu generation 2-stöd för följande valda VM-serien:

* [B-serien](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [DC-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dc-series)
* [Dsv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) och [Dsv3-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [HB-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hb-series)
* [HC-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hc-series)
* [Ls-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) och [Lsv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)
* [NCv2-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv2-series) och [NCv3-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv3-series)
* [ND-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nd-series)
* [NVv3-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

> [!NOTE]
> Användningen av virtuella datorer i generation 2 för virtuella datorer i Mv2-serien är allmänt tillgänglig eftersom Mv2-serien fungerar med generation 2 VM-avbildningar exklusivt. Generation 1 VM-avbildningar stöds inte på virtuella datorer i Mv2-serien. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Generation 2 VM-avbildningar på Azure Marketplace

Virtuella datorer i generation 2 stöder följande Marketplace-avbildningar:

* Windows Server 2019 Data Center
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19,04, 19,10 
* RHEL 8,0

## <a name="on-premises-vs-azure-generation-2-vms"></a>Lokala eller Azure generation 2-datorer

Azure stöder för närvarande inte några av de funktioner som lokala Hyper-V stöder för virtuella datorer i generation 2.

| Generation 2-funktion                | Lokal Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Säker start                         | :heavy_check_mark:  | röntgen   |
| Avskärmad virtuell dator                         | :heavy_check_mark:  | röntgen   |
| vTPM                                | :heavy_check_mark:  | röntgen   |
| Virtualiseringsbaserad säkerhet (VBS) | :heavy_check_mark:  | röntgen   |
| VHDX-format                         | :heavy_check_mark:  | röntgen   |

## <a name="features-and-capabilities"></a>Funktioner och funktioner

### <a name="generation-1-vs-generation-2-features"></a>Generation 1 vs. generation 2-funktioner

| Funktion | Generation 1 | Generation 2 |
|---------|--------------|--------------|
| Start             | PCAT         | UEFI |
| Disk styrenheter | IDE          | SCSI |
| VM-storlekar         | Alla VM-storlekar | Endast virtuella datorer som stöder Premium Storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Funktioner för generation 1 vs. generation 2

| Funktion | Generation 1 | Generation 2 |
|------------|--------------|--------------|
| OS-disk > 2 TB                    | röntgen                | :heavy_check_mark: |
| Anpassad disk/avbildning/växla OS         | :heavy_check_mark: | :heavy_check_mark: |
| Stöd för skalnings uppsättning för virtuell dator | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | röntgen                |
| Säkerhets kopiering/återställning                    | :heavy_check_mark: | :heavy_check_mark: |
| Galleri för delad avbildning              | :heavy_check_mark: | :heavy_check_mark: |
| Azure Disk Encryption             | :heavy_check_mark: | röntgen                |

## <a name="creating-a-generation-2-vm"></a>Skapar en virtuell dator i generation 2

### <a name="marketplace-image"></a>Marketplace-avbildning

I Azure Portal eller Azure CLI kan du skapa virtuella datorer i generation 2 från en Marketplace-avbildning som stöder UEFI-start.

#### <a name="azure-portal"></a>Azure Portal

Generation 2-avbildningar för Windows och SLES ingår i samma server erbjudande som gen1-avbildningarna. Vad det innebär från ett flödes perspektiv är att du väljer erbjudandet och SKU: n från portalen för din virtuella dator. Om SKU: n stöder både generation 1 och generation 2-avbildningar kan du välja att skapa en virtuell dator i generation 2 från fliken *Avancerat* i flödet för att skapa virtuella datorer.

För närvarande stöder följande SKU: er både generation 1 och generation 2-avbildningar:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

När du väljer en Windows Server-SKU som erbjudande finns det ett alternativ för att skapa antingen en **gen 1** (BIOS) eller **gen 2** (UEFI) virtuell dator i fliken **Avancerat** . Om du väljer **gen 2**, se till att den virtuella dator storleken som valts i fliken **grundläggande** [stöds för virtuella datorer i generation 2](#generation-2-vm-sizes).

![Välj gen 1 eller gen 2 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Du kan också använda PowerShell för att skapa en virtuell dator genom att referera till SKU för generation 1 eller generation 2.

Använd till exempel följande PowerShell-cmdlet för att hämta en lista över SKU: er i `WindowsServer` erbjudandet.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Om du skapar en virtuell dator med Windows Server 2012 som operativ system väljer du antingen generation 1 (BIOS) eller generation 2 (UEFI) VM SKU, som ser ut så här:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

I avsnittet [funktioner och funktioner](#features-and-capabilities) finns en aktuell lista över Marketplace-avbildningar som stöds.

### <a name="managed-image-or-managed-disk"></a>Hanterad avbildning eller hanterad disk

Du kan skapa en virtuell dator i generation 2 från en hanterad avbildning eller en hanterad disk på samma sätt som du skapar en virtuell dator i generation 1.

### <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för virtuella datorer

Du kan också skapa virtuella datorer i generation 2 med hjälp av skalnings uppsättningar för virtuella datorer. I Azure CLI använder du Azure Scale set för att skapa virtuella datorer i generation 2.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Är generation 2 virtuella datorer tillgängliga i alla Azure-regioner?**  
    Ja. Men alla [VM-storlekar i generation 2](#generation-2-vm-sizes) är inte tillgängliga i varje region. Tillgängligheten för den virtuella datorn i generation 2 beror på tillgängligheten för VM-storleken.

* **Finns det en pris skillnad mellan virtuella datorer i generation 1 och generation 2?**  
    Nej.

* **Jag har en. VHD-fil från min lokala generation 2 VM. Kan jag använda den. VHD-filen för att skapa en generation 2 VM i Azure?**
  Ja, du kan ta din generations 2. VHD-fil till Azure och använda den för att skapa en virtuell dator i generation 2. Använd följande steg för att göra det:
    1. Överför. VHD till ett lagrings konto i samma region där du vill skapa den virtuella datorn.
    1. Skapa en hanterad disk från VHD-filen. Ställ in Hyper-V generations egenskapen på v2. Följande PowerShell-kommandon ställer in Hyper-V generations-egenskapen när du skapar en hanterad disk.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. När disken är tillgänglig skapar du en virtuell dator genom att koppla den här disken. Den virtuella datorn som skapas är en virtuell dator i generation 2.
    När generation 2 VM skapas kan du välja att generalisera avbildningen av den virtuella datorn. Genom att generalisera avbildningen kan du använda den för att skapa flera virtuella datorer.

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

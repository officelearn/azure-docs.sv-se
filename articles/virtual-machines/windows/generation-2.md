---
title: Azure-stöd för generation 2 virtuella datorer
description: Översikt över Azure-stöd för virtuella generation 2-datorer
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: 53806a1a627c7ae6bc3470aa387be0982df914fa
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082266"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Stöd för generering 2 virtuella datorer på Azure

Stöd för virtuella datorer för generation 2 är nu tillgängligt på Azure. Du kan inte ändra en virtuell dators generering när du har skapat den, så läs övervägandena på den här sidan innan du väljer en generation.

Generation 2 virtuella datorer stöder viktiga funktioner som inte stöds i virtuella generationer från generation 1. Dessa funktioner inkluderar ökat minne, Intel Software Guard Extensions (Intel SGX) och virtualiserat beständigt minne (vPMEM). Generation 2 virtuella datorer som körs lokalt har vissa funktioner som inte stöds i Azure ännu. Mer information finns i avsnittet [Funktioner och funktioner.](#features-and-capabilities)

Generation 2 virtuella datorer använder den nya UEFI-baserade startarkitekturen i stället för den BIOS-baserade arkitekturen som används av generation 1 virtuella datorer. Jämfört med generation 1 virtuella datorer kan generation 2 virtuella datorer ha förbättrat start- och installationstiderna. En översikt över virtuella datorer för generation 2 och några av skillnaderna mellan generation 1 och generation 2 finns i [Ska jag skapa en virtuell dator för generation 1 eller 2 i Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Generation 2 VM-storlekar

Virtuella datorer med generation 1 stöds av alla vm-storlekar i Azure (förutom virtuella datorer i Mv2-serien). Azure erbjuder nu generation 2-stöd för följande valda VM-serie:

* [B-serien](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [DCsv2-serien](../dcv2-series.md)
* [DSv2-serien](../dv2-dsv2-series.md) och [Dsv3-serien](../dv3-dsv3-series.md)
* [Dasv4-serien](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series)
* [Esv3-serien](../ev3-esv3-series.md)
* [Easv4-serien](https://docs.microsoft.com/azure/virtual-machines/eav4-easv4-series)
* [Fsv2-serien](../fsv2-series.md)
* [GS-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB-serien](../hb-series.md)
* [HC-serien](../hc-series.md)
* [Ls-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) och [Lsv2-serien](../lsv2-series.md)
* [Mv2-serien](../mv2-series.md)
* [NCv2-serien](../ncv2-series.md) och [NCv3-serien](../ncv3-series.md)
* [ND-serien](../nd-series.md)
* [NVv3-serien](../nvv3-series.md)

> [!NOTE]
> Användningen av generation 2 VM-avbildningar för virtuella datorer i Mv2-serien är allmänt tillgänglig eftersom Mv2-serien fungerar med generation 2 VM-avbildningar exklusivt. Generation 1 VM-avbildningar stöds inte på virtuella datorer i Mv2-serien. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Generation 2 VM-avbildningar på Azure Marketplace

Generation 2 virtuella datorer stöder följande Marketplace-avbildningar:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Företag
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16,04, 18,04, 19,04, 19,10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>Lokala virtuella datorer jämfört med Azure generation 2

Azure stöder för närvarande inte några av de funktioner som lokala Hyper-V stöder för virtuella generationer 2.

| Generation 2-funktion                | Lokal Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Säker start                         | :heavy_check_mark:  | :x:   |
| Avskärmad virtuell dator                         | :heavy_check_mark:  | :x:   |
| vTPM (VTPM)                                | :heavy_check_mark:  | :x:   |
| Virtualiseringsbaserad säkerhet (VBS) | :heavy_check_mark:  | :x:   |
| VHDX-format                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funktioner

### <a name="generation-1-vs-generation-2-features"></a>Generation 1 jämfört med generation 2-funktioner

| Funktion | Generation 1 | Generation 2 |
|---------|--------------|--------------|
| Boot             | PCAT (PÅ)                      | Uefi                               |
| Diskstyrenheter | IDE                       | SCSI                               |
| VM-storlekar         | Alla VM-storlekar | Endast virtuella datorer som stöder premiumlagring |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generation 1 jämfört med generation 2-funktioner

| Funktion | Generation 1 | Generation 2 |
|------------|--------------|--------------|
| OS-disk > 2 TB                    | :x:                | :heavy_check_mark: |
| Anpassat operativsystem för disk/bild/byt         | :heavy_check_mark: | :heavy_check_mark: |
| Stöd för skalningsuppsättning för virtuell dator | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Säkerhetskopiera/återställa                    | :heavy_check_mark: | :heavy_check_mark: |
| Galleri för delad bild              | :heavy_check_mark: | :heavy_check_mark: |
| Kryptering av Azure-disk             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Skapa en generation 2 VM

### <a name="marketplace-image"></a>Marketplace-bild

I Azure-portalen eller Azure CLI kan du skapa generation 2 virtuella datorer från en Marketplace-avbildning som stöder UEFI-start.

#### <a name="azure-portal"></a>Azure Portal

Nedan följer stegen för att skapa en generation 2 (Gen2) VM i Azure-portalen.

1. Logga in på Azure Portal på https://portal.azure.com.
1. Välj **Skapa en resurs**.
1. Klicka på **Visa alla** från Azure Marketplace till vänster.
1. Välj en bild som stöder Gen2.
1. Klicka på **Skapa**.
1. Välj alternativet **Gen 2** under avsnittet **Generation virtuell dator** under fliken **Avancerat.**
1. Gå till **Storlek** under **Information om förekomst**på fliken **Grunderna** och öppna bladet Välj **en vm-storlek.**
1. Välj en [generation 2-vm som stöds](#generation-2-vm-sizes).
1. Gå igenom [Azure-portalens skapandeflöde](quick-create-portal.md) för att slutföra skapandet av den virtuella datorn.

![Välj Gen 1 eller Gen 2 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Du kan också använda PowerShell för att skapa en virtuell dator genom att direkt referera till generation 1 eller generation 2 SKU.

Använd till exempel följande PowerShell-cmdlet för att få `WindowsServer` en lista över SKU:erna i erbjudandet.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Du kan också använda Azure CLI för att se alla tillgängliga generation 2-avbildningar som visas i **Publisher**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Om du skapar en virtuell dator med Windows Server 2012 som operativsystem väljer du antingen generation 1 (BIOS) eller generation 2 (UEFI) VM SKU, som ser ut så här:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Se avsnittet [Funktioner och funktioner](#features-and-capabilities) för en aktuell lista över Marketplace-avbildningar som stöds.

### <a name="managed-image-or-managed-disk"></a>Hanterad avbildning eller hanterad disk

Du kan skapa en generation 2-virtuell dator från en hanterad avbildning eller hanterad disk på samma sätt som du skulle skapa en generation 1 virtuell dator.

### <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för virtuella datorer

Du kan också skapa generering 2 virtuella datorer med hjälp av skaluppsättningar för virtuella datorer. I Azure CLI använder du Azure-skalningsuppsättningar för att skapa virtuella generationer 2.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Är generation 2 virtuella datorer tillgängliga i alla Azure-regioner?**  
    Ja. Men inte alla [generation 2 VM storlekar](#generation-2-vm-sizes) är tillgängliga i varje region. Tillgängligheten för generation 2-virtuella datorer beror på tillgängligheten för vm-storleken.

* **Finns det en prisskillnad mellan generation 1 och generation 2 virtuella datorer?**  
   Nej.

* **Jag har en VHD-fil från min lokala generation 2 VM. Kan jag använda den VHD-filen för att skapa en generation 2-vm i Azure?**
  Ja, du kan ta med din generation 2 VHD-fil till Azure och använda den för att skapa en generation 2 virtuell dator. Gör så här:
    1. Ladda upp .vhd till ett lagringskonto i samma region där du vill skapa den virtuella datorn.
    1. Skapa en hanterad disk från VHD-filen. Ange egenskapen Hyper-V Generation till V2. Följande PowerShell-kommandon anger hyper-V-genereringsegenskap när du skapar hanterad disk.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. När disken är tillgänglig skapar du en virtuell dator genom att ansluta den här disken. Den skapade virtuella datorn kommer att vara en generation 2 VM.
    När generation 2-vm skapas kan du också generalisera avbildningen av den här virtuella datorn. Genom att generalisera avbildningen kan du använda den för att skapa flera virtuella datorer.

* **Hur ökar jag os-diskstorleken?**  
  OS-diskar som är större än 2 TB är nya för generation 2 virtuella datorer. Som standard är OS-diskar mindre än 2 TB för virtuella datorer för generation 2. Du kan öka diskstorleken upp till ett rekommenderat maximum av 4 TB. Använd Azure CLI eller Azure-portalen för att öka os-diskstorleken. Information om hur du expanderar diskar programmässigt finns i [Ändra storlek på en disk](expand-os-disk.md).

  Så här ökar du diskstorleken från Azure-portalen:

  1. Gå till egenskapssidan för den virtuella datorn i Azure-portalen.
  1. Om du vill stänga av och frigöra den virtuella datorn väljer du **stoppknappen.**
  1. Välj den OS-disk som du vill öka i avsnittet **Diskar.**
  1. I avsnittet **Diskar** väljer du **Konfiguration**och uppdaterar **storleken** till önskat värde.
  1. Gå tillbaka till egenskapssidan för den virtuella datorn och **starta** den virtuella datorn.
  
  Du kan se en varning för OS-diskar som är större än 2 TB. Varningen gäller inte för virtuella datorer för generation 2. Os-diskstorlekar som är större än 4 TB rekommenderas dock *inte.*

* **Stöder generation 2 virtuella datorer accelererade nätverk?**  
    Ja. Mer information finns i [Skapa en virtuell dator med accelererade nätverk](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Stöds VHDX på generation 2?**  
    Nej, generation 2 virtuella datorer stöder endast VHD.

* **Har generation 2 virtuella datorer stöd för Azure Ultra Disk Storage?**  
    Ja.

* **Kan jag migrera en virtuell dator från generation 1 till generation 2?**  
    Nej, du kan inte ändra genereringen av en virtuell dator när du har skapat den. Om du behöver växla mellan vm-generationer skapar du en ny virtuell dator för en annan generation.

* **Varför är inte min vm-storlek aktiverad i storleksväljaren när jag försöker skapa en Gen2-vm?**

    Detta kan lösas genom att göra följande:

    1. Kontrollera att den **virtuella generationens** egenskap är inställd **på Gen 2** på fliken **Avancerat.**
    1. Kontrollera att du söker efter en [vm-storlek som stöder gen2 virtuella datorer](#generation-2-vm-sizes).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [virtuella generation 2-datorer i Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Lär dig hur du [förbereder en virtuell hårddisk](prepare-for-upload-vhd-image.md) för att ladda upp från lokala system till Azure.

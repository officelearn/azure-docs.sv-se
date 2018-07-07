---
title: Använda en Windows felsöknings-VM med Azure PowerShell | Microsoft Docs
description: Lär dig att felsöka problem med Windows-VM i Azure genom att ansluta operativsystemdisken till en virtuell dator med Azure PowerShell för återställning
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 1e87704e7d8cf3c7cc21e537d36f95a97265061b
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903524"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator med Azure PowerShell för återställning
Om din Windows virtuell dator (VM) i Azure påträffar ett fel vid start- eller disk, kan du behöva utföra felsökningssteg direkt på den virtuella hårddisken. Ett vanligt exempel är en misslyckad programuppdatering som förhindrar den virtuella datorn från att kunna starta. Den här artikeln beskriver hur du använder Azure PowerShell för att ansluta den virtuella hårddisken till en annan virtuell Windows-dator att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.


## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Ta bort den virtuella datorn uppstår några problem, som de virtuella hårddiskarna.
2. Anslut och montera den virtuella hårddisken till en annan virtuell Windows-dator för felsökning.
3. Anslut till den virtuella felsökningsdatorn. Redigera filer eller köra några verktyg för att åtgärda problem på den ursprungliga virtuella hårddisken.
4. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
5. Skapa en virtuell dator med den ursprungliga virtuella hårddisken.

För den virtuella datorn som använder hanterade diskar, se [felsöka en hanterad Disk i virtuell dator genom att koppla en ny OS-disk](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).


Se till att du har [den senaste Azure PowerShell](/powershell/azure/overview) installerat och loggat in till din prenumeration:

```powershell
Connect-AzureRmAccount
```

I följande exempel, ersätter du parameternamn med dina egna värden. Parametern exempelnamnen inkluderar `myResourceGroup`, `mystorageaccount`, och `myVM`.


## <a name="determine-boot-issues"></a>Fastställa startproblem
Du kan visa en skärmbild av den virtuella datorn i Azure för att felsöka startproblem. Den här skärmbilden kan hjälpa dig att identifiera varför det inte går att starta en virtuell dator. I följande exempel hämtas skärmbilden från Windows-VM med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Granska skärmbilden för att avgöra varför den virtuella datorn kan inte starta. Observera några felmeddelanden eller felkoder som tillhandahålls.


## <a name="view-existing-virtual-hard-disk-details"></a>Se information om en befintlig virtuell hårddisk
Innan du kan koppla den virtuella hårddisken till en annan virtuell dator, måste du identifiera namnet på den virtuella hårddisken (VHD).

I följande exempel hämtar information för den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Leta efter `Vhd URI` inom den `StorageProfile` avsnittet från utdata från föregående kommando. Följande trunkeras exempel utdata visas den `Vhd URI` mot slutet av kodblocket:

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Ta bort befintlig virtuell dator
Virtuella hårddiskar och virtuella datorer är två separata resurser i Azure. En virtuell hårddisk är där själva operativsystemet, program och konfigurationer lagras. Virtuellt datorn är bara metadata som definierar storleken eller platsen och refererar till resurser, till exempel en virtuell hårddisk eller virtuella nätverkskort (NIC). Varje virtuell hårddisk har ett lån när ansluten till en virtuell dator. Datadiskar kan anslutas och kopplas från när den virtuella datorn körs, men operativsystemdisken kan inte kopplas från om inte den virtuella datorresursen tagits bort. Lånet fortsätter att associera operativsystemdisken med en virtuell dator, även om den virtuella datorn är i stoppat eller frigjort läge.

Det första steget att återställa den virtuella datorn är att ta bort den Virtuella datorresursen. När du tar bort den virtuella datorn hamnar de virtuella hårddiskarna på ditt lagringskonto. När den virtuella datorn har tagits bort, kan du koppla den virtuella hårddisken till en annan virtuell dator för att felsöka och lösa problemen.

I följande exempel tar bort den virtuella datorn med namnet `myVM` från resursgruppen med namnet `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Vänta tills den virtuella datorn har tagits bort innan du kopplar den virtuella hårddisken till en annan virtuell dator. Lånet på den virtuella hårddisken som associeras med den virtuella datorn måste frigöras innan du kan koppla den virtuella hårddisken till en annan virtuell dator.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Bifoga befintlig virtuell hårddisk till en annan virtuell dator
De efterföljande stegen använder du en annan virtuell dator för felsökning. Du kan koppla den befintliga virtuella hårddisken till den här virtuella datorn för felsökning Bläddra och redigera dess innehåll. Den här processen kan du korrigera eventuella konfigurationsfel eller granska ytterligare program eller systemloggfilerna, till exempel. Välj eller skapa en annan virtuell dator för felsökning.

När du ansluter den befintliga virtuella hårddisken, anger du Webbadressen till den disk som hämtades i föregående `Get-AzureRmVM` kommando. Följande exempel ansluter en befintlig virtuell hårddisk för felsökning VM med namnet `myVMRecovery` i resursgruppen med namnet `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Lägger till en disk måste du ange storleken på disken. Som vi kopplar en befintlig disk den `-DiskSizeInGB` har angetts som `$null`. Det här värdet garanterar att datadisken är korrekt ansluten, och utan att behöva avgör SANT storleken på data-disk.


## <a name="mount-the-attached-data-disk"></a>Montera ansluten datadisk

1. RDP till den felsökning virtuella datorn med rätt autentiseringsuppgifter. I följande exempel hämtar RDP-anslutning-filen för den virtuella datorn med namnet `myVMRecovery` i resursgruppen med namnet `myResourceGroup`, och laddar ned den till `C:\Users\ops\Documents`”

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Datadisken har identifierats och anslutna automatiskt. Visa en lista över anslutna volymer för att fastställa enhetsbeteckningen på följande sätt:

    ```powershell
    Get-Disk
    ```

    Följande Exempelutdata visar den virtuella hårddisken är ansluten till en disk **2**. (Du kan också använda `Get-Volume` att visa enhetsbeteckningen):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Åtgärda problem på den ursprungliga virtuella hårddisken
Med den befintliga virtuella hårddisken monteras, kan du nu utföra underhålls- och felsökningssteg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Demontera och koppla från den ursprungliga virtuella hårddisken
När din fel har åtgärdats du demontera och koppla från den befintliga virtuella hårddisken från Virtuellt felsökningsdatorn. Du kan inte använda den virtuella hårddisken med andra virtuella datorer tills frigörs lånet som kopplar den virtuella hårddisken till Virtuellt felsökningsdatorn.

1. Demontera datadisken på den Virtuella återställningsdatorn i RDP-session från. Du behöver numret på den disk från den tidigare `Get-Disk` cmdlet. Använd sedan `Set-Disk` att ställa in disken som offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Bekräfta disken nu är inställd som offline med hjälp av `Get-Disk` igen. Följande Exempelutdata visar disken nu är inställd som offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Avsluta RDP-session. Ta bort den virtuella hårddisken från Virtuellt felsökningsdatorn från Azure PowerShell-session.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Skapa virtuell dator från den ursprungliga hårddisken
Du kan skapa en virtuell dator från den ursprungliga virtuella hårddisken med [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Faktiska JSON-mallen finns på följande länk:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

Mallen distribuerar en virtuell dator i ett befintligt virtuellt nätverk med hjälp av VHD-URL: en från det tidigare kommandot. I följande exempel distribuerar mallen till resursgruppen med namnet `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Svara på frågorna för mallen som namn på virtuell dator, OS-typ och storlek. Den `osDiskVhdUri` är samma som används som tidigare när bifoga den befintliga virtuella hårddisken till Virtuellt felsökningsdatorn.


## <a name="re-enable-boot-diagnostics"></a>Återaktivera startdiagnostiken

När du skapar den virtuella datorn från den befintliga virtuella hårddisken kan startdiagnostik inte automatiskt att aktivera. I följande exempel aktiveras diagnostiktillägget på den virtuella datorn med namnet `myVMDeployed` i resursgruppen med namnet `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Felsöka en hanterad Disk i virtuell dator genom att koppla en ny OS-disk
1. Stoppa den berörda Managed Disk Windows virtuella datorn.
2. [Skapa en hanterad disk ögonblicksbild](snapshot-copy-managed-disk.md) av OS-disken för den hanterade disken i virtuella datorn.
3. [Skapa en hanterad disk från ögonblicksbilden](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Koppla hanterad disk som en datadisk på den virtuella datorn](attach-disk-ps.md).
5. [Ändra datadisken från steg 4 till OS-disken](os-disk-swap.md).

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du läsa [felsöka RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problem med att komma åt program som körs på den virtuella datorn finns [felsöka problem med programanslutningar på en virtuell Windows-dator](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om hur du använder Resource Manager finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

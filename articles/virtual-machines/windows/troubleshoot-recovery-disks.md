---
title: Använda en Windows-felsökning av virtuell dator med Azure PowerShell | Microsoft Docs
description: Lär dig hur du felsöker problem med Windows VM i Azure genom att ansluta OS-disken till återställning av en virtuell dator med hjälp av Azure PowerShell
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
ms.openlocfilehash: 408429d0f8697b8b807e386dbcf2eade29938249
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271699"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Felsöka en virtuell Windows-dator genom att koppla OS-disken till återställning av en virtuell dator med hjälp av Azure PowerShell
Om din Windows-dator (VM) i Azure påträffar ett fel vid start- eller disk, kan du behöva utför felsökning på den virtuella hårddisken sig själv. Ett vanligt exempel är en uppdatering för det program som förhindrar den virtuella datorn ska starta. Den här artikeln beskriver hur du använder Azure PowerShell för att ansluta den virtuella hårddisken till en annan Windows virtuell dator att åtgärda eventuella fel och sedan skapa den ursprungliga virtuella datorn igen.


## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Ta bort den virtuella datorn får problem, hålla de virtuella hårddiskarna.
2. Anslut och montera den virtuella hårddisken till en annan Windows virtuell dator i felsökningssyfte.
3. Anslut till den virtuella felsökningsdatorn. Redigera filer eller köra några verktyg för att åtgärda problem på den ursprungliga virtuella hårddisken.
4. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
5. Skapa en virtuell dator med hjälp av den ursprungliga virtuella hårddisken.

För den virtuella datorn som använder hanterade diskar, se [felsöka en hanteras Disk i virtuell dator genom att koppla en ny OS-disk](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).


Se till att du har [senaste Azure PowerShell](/powershell/azure/overview) installerad och loggat in till din prenumeration:

```powershell
Connect-AzureRmAccount
```

Ersätt parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar `myResourceGroup`, `mystorageaccount`, och `myVM`.


## <a name="determine-boot-issues"></a>Fastställa startproblem
Du kan visa en skärmbild av den virtuella datorn i Azure för att felsöka startproblem. Den här skärmbilden kan hjälpa dig att identifiera varför en virtuell dator kan inte starta. I följande exempel hämtas skärmbilden från Windows-Virtuella datorn `myVM` i resursgrupp med namnet `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Granska skärmbilden för att avgöra varför den virtuella datorn inte kan starta. Observera eventuella felmeddelanden eller felkoderna som tillhandahålls.


## <a name="view-existing-virtual-hard-disk-details"></a>Visa information om befintlig virtuell hårddisk
Innan du kan koppla den virtuella hårddisken till en annan virtuell dator, måste du identifiera namnet på den virtuella hårddisken (VHD).

I följande exempel hämtar information för den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Leta efter `Vhd URI` inom den `StorageProfile` avsnitt från utdata i kommandot ovan. Följande trunkeras exempel utdata visar den `Vhd URI` mot slutet av kodblocket:

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


## <a name="delete-existing-vm"></a>Ta bort en befintlig virtuell dator
Virtuella hårddiskar och virtuella datorer är två separata resurser i Azure. En virtuell hårddisk är där själva operativsystemet, program och konfigurationer lagras. Virtuellt datorn är enbart metadata som definierar storlek eller plats, och refererar till resurser, till exempel en virtuell hårddisk eller virtuella nätverksgränssnittskortet (NIC). Varje virtuell hårddisk har tilldelats när ansluten till en virtuell dator. Datadiskar kan anslutas och kopplas från när den virtuella datorn körs, men operativsystemdisken kan inte kopplas från om inte den virtuella datorresursen tagits bort. Lånet fortsätter att koppla OS-disken till en virtuell dator även om den virtuella datorn är i tillståndet stoppad och frigjord.

Det första steget att återställa den virtuella datorn är att ta bort den Virtuella datorresursen sig själv. När du tar bort den virtuella datorn hamnar de virtuella hårddiskarna på ditt lagringskonto. När den virtuella datorn har tagits bort, kopplar du den virtuella hårddisken till en annan virtuell dator för att felsöka och lösa problemen.

I följande exempel tar bort den virtuella datorn med namnet `myVM` från resursgruppen med namnet `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Vänta tills den virtuella datorn har tagits bort innan du kopplar den virtuella hårddisken till en annan virtuell dator. Lånet på den virtuella hårddisken som associeras med den virtuella datorn måste släppas innan du kan koppla den virtuella hårddisken till en annan virtuell dator.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Koppla en befintlig virtuell hårddisk till en annan virtuell dator
För nästa steg använder du en annan virtuell dator i felsökningssyfte. Du bifoga en befintlig virtuell hårddisk för den här felsökningsinformationen VM att bläddra och redigera dess innehåll. Den här processen kan du korrigera eventuella fel i programkonfigurationen eller granska ytterligare program eller system loggfiler, till exempel. Välj eller skapa en annan virtuell dator ska användas för felsökning.

När du ansluter en befintlig virtuell hårddisk, ange URL till den disk som hämtades i föregående `Get-AzureRmVM` kommando. I följande exempel bifogar en befintlig virtuell hårddisk till den Virtuella datorn med namnet felsökning `myVMRecovery` i resursgrupp med namnet `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Lägga till en disk måste du ange storleken på disken. Som vi bifoga en befintlig disk i `-DiskSizeInGB` har angetts som `$null`. Det här värdet garanterar datadisken är korrekt ansluten, och utan att behöva bestämma true storleken på datadisk.


## <a name="mount-the-attached-data-disk"></a>Bifogade data disken

1. RDP för din felsökning VM med rätt autentiseringsuppgifter. I följande exempel laddar ned RDP-anslutningsfil för den virtuella datorn med namnet `myVMRecovery` i resursgrupp med namnet `myResourceGroup`, och hämtar den `C:\Users\ops\Documents`”

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Datadisken upptäckt och kopplade automatiskt. Visa en lista över anslutna volymer att fastställa enhetsbeteckningen på följande sätt:

    ```powershell
    Get-Disk
    ```

    Följande exempel visas den virtuella hårddisken ansluten en disk **2**. (Du kan också använda `Get-Volume` att visa enhetsbeteckningen):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Åtgärda problemen på den ursprungliga virtuella hårddisken
Med den befintliga virtuella hårddisken monteras, kan du nu utföra eventuella underhåll och felsökning efter behov. När du har åtgärdat problemen fortsätter du med följande steg.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Demontera och koppla från den ursprungliga virtuella hårddisken
När din felen är löst kan du demontera och koppla från den befintliga virtuella hårddisken från den virtuella datorn med felsökning. Du kan inte använda din virtuella hårddisk med andra Virtuella förrän lånet bifoga den virtuella hårddisken till Virtuellt datorn felsökning släpps.

1. Demontera datadisken på den Virtuella återställningsdatorn i RDP-session från. Du behöver numret på den disk från den tidigare `Get-Disk` cmdlet. Använd sedan `Set-Disk` att ange disken som offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Bekräfta disken nu anges som offline med `Get-Disk` igen. Följande visas exempel disken nu anges som offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Avsluta RDP-session. Ta bort den virtuella hårddisken från den felsökning virtuella datorn från Azure PowerShell-sessionen.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Skapa virtuell dator från den ursprungliga hårddisken
Så här skapar du en virtuell dator från den ursprungliga virtuella hårddisken [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Den faktiska JSON-mallen finns på följande länk:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

Mallen distribuerar en virtuell dator i ett befintligt virtuellt nätverk med hjälp av VHD-Webbadressen från tidigare kommandot. I följande exempel distribuerar mallen till resursgruppen med namnet `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Besvara anvisningarna för mallen som namn på virtuell dator, OS-typen och VM-storlek. Den `osDiskVhdUri` är samma som tidigare använt när kopplar befintlig virtuell hårddisk till Virtuellt datorn felsökning.


## <a name="re-enable-boot-diagnostics"></a>Återaktivera startdiagnostikinställningar

När du skapar den virtuella datorn från den befintliga virtuella hårddisken får startdiagnostikinställningar inte automatiskt aktiveras. I följande exempel aktiveras diagnostiska tillägget på den virtuella datorn med namnet `myVMDeployed` i resursgrupp med namnet `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Felsöka en hanteras Disk i virtuell dator genom att koppla en ny OS-disk
1. Stoppa den berörda hanteras Disk Windows VM.
2. [Skapa en ögonblicksbild för hanterade diskar](snapshot-copy-managed-disk.md) av OS-disken på VM: hanterade disken.
3. [Skapa en hanterad disk från ögonblicksbilden](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Koppla den hantera disken som en datadisk för den virtuella datorn](attach-disk-ps.md).
5. [Ändra datadisken från steg 4 till OS-disken](os-disk-swap.md).

## <a name="next-steps"></a>Nästa steg
Om du har problem med anslutningen till den virtuella datorn finns [felsökning av RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Problem med att komma åt program som körs på den virtuella datorn finns [felsökning av problem med nätverksanslutningen på en Windows-VM](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om hur du använder Resource Manager finns [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

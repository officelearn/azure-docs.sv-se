---
title: VM Start har fastnat på ”Getting Windows redo. Stäng inte av datorn ”i Azure | Microsoft Docs
description: Introducera stegen för att felsöka problemet där VM Start har fastnat på ”Getting Windows redo. Stäng inte av datorn”.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: a291b7640662da7ea0c205c2027fe83d6243850e
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980371"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM Start har fastnat på ”Getting Windows redo. Stäng inte av datorn ”i Azure

Den här artikeln hjälper dig att lösa problemet när den virtuella datorn (VM) har fastnat på den ”Windows förbereda. Stäng inte av datorn ”steg under starten.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="symptoms"></a>Symtom

När du använder **Startdiagnostik** Skärmbild av en virtuell dator får operativsystemet inte fullständigt startas. Den virtuella datorn visar meddelandet ”Getting Windows redo. Stäng inte av datorn”.

![Exempel på meddelande för Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exempel på meddelande](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Orsak

Det här problemet uppstår vanligen när servern gör den slutliga omstarten när konfigurationen ändrades. Konfigurationsändringen kan initieras av Windows-uppdateringar eller av ändringar på roller /-funktionen på servern. För Windows Update, om storleken på uppdateringarna som har stor eller måste operativsystemet mer tid för att konfigurera om ändringarna.

## <a name="back-up-the-os-disk"></a>Säkerhetskopiera OS-disken

Säkerhetskopiera OS-disken innan du försöker åtgärda problemet.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>För virtuella datorer med en krypterad disk, du måste låsa upp diskarna först

Följ stegen nedan för att avgöra om den virtuella datorn är en krypterad virtuell dator.

1. Öppna din virtuella dator på Azure portal och bläddra sedan till diskarna.

2. Titta på den **kryptering** kolumnen för att se om kryptering har aktiverats.

Låsa upp den krypterade disken om OS-disken är krypterad. Följ dessa steg för att låsa upp disken.

1. Skapa ett Recovery virtuell dator som finns i samma resursgrupp, Storage-konto och plats som den berörda virtuella datorn.

2. Ta bort den berörda virtuella datorn och hålla disken i Azure-portalen.

3. Kör PowerShell som administratör.

4. Kör följande cmdlet för att hämta det hemliga namnet.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. När du har det hemliga namnet, kör du följande kommandon i PowerShell.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Konvertera det Base64-kodad värdet till byte och skriva utdata till en fil. 

    > [!Note]
    > Om du använder USB låsa upp alternativet, BEK filnamnet måste matcha det ursprungliga BEK GUID. Skapa en mapp på enhet C med namnet ”BEK” innan du följer dessa steg.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. När filen BEK har skapats på din dator kan du kopiera filen till återställningen VM som du har låst OS-disken är ansluten till. Kör följande kommandon med hjälp av BEK filens plats.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Valfritt**: I vissa situationer kan det vara nödvändigt att dekryptera disken med hjälp av det här kommandot.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Föregående kommando förutsätter att kryptera disken finns på bokstaven F.

8. Om du behöver samla in loggar, går du till sökvägen **enhet BOKSTAV: \Windows\System32\winevt\Logs**.

9. Koppla bort enheten från dator för återställning.

### <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Om du vill skapa en ögonblicksbild, följer du stegen i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Samla in en minnesdump för OS

Följ stegen i den [samla in os dump](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) avsnitt för att samla in en OS-dump när den virtuella datorn har fastnat på konfiguration.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

När du har samlat in dumpfilen Kontakta [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att analysera rotorsaken.


## <a name="rebuild-the-vm-by-using-powershell"></a>Återskapa den virtuella datorn med hjälp av PowerShell

När du samlar in minnesdumpen, följer du stegen nedan för att återskapa den virtuella datorn.

**För icke-hanterade diskar**

```PowerShell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**För hanterade diskar**

```PowerShell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```

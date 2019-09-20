---
title: Starten av den virtuella datorn har fastnat i "komma igång med Windows. Stäng inte av datorn i Azure | Microsoft Docs
description: Introducera stegen för att felsöka problemet där VM-start fastnar på "komma igång med Windows. Stäng inte av datorn”.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: d0a946ede154561aaa49d335b7b91fdae72c51d3
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155557"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Starten av den virtuella datorn har fastnat i "komma igång med Windows. Stäng inte av datorn i Azure

Den här artikeln hjälper dig att lösa problemet när den virtuella datorn (VM) har fastnat i "komma igång med Windows. Stäng inte av datorn "-steget under starten.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Symtom

När du använder **startdiagnostik** för att hämta skärm bilden för en virtuell dator startar inte operativ systemet helt. Den virtuella datorn visar meddelandet "komma igång med Windows. Stäng inte av datorn”.

![Meddelande exempel för Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Meddelande exempel](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Orsak

Det här problemet uppstår vanligt vis när servern utför den slutliga omstarten när konfigurationen har ändrats. Konfigurations ändringen kan initieras av Windows-uppdateringar eller av ändringarna på serverns roller/funktion. För Windows Update, om Uppdateringens storlek var stor, behöver operativ systemet mer tid för att omkonfigurera ändringarna.

## <a name="back-up-the-os-disk"></a>Säkerhetskopiera OS-disken

Säkerhetskopiera OS-disken innan du försöker åtgärda problemet.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>För virtuella datorer med en krypterad disk måste du låsa upp diskarna först

Följ dessa steg för att avgöra om den virtuella datorn är en krypterad virtuell dator.

1. Öppna den virtuella datorn på Azure Portal och bläddra sedan till diskarna.

2. Titta i kolumnen **kryptering** för att se om kryptering är aktiverat.

Om operativ system disken är krypterad kan du låsa upp den krypterade disken. Följ dessa steg om du vill låsa upp disken.

1. Skapa en virtuell dator för återställning som finns i samma resurs grupp, lagrings konto och plats som den berörda virtuella datorn.

2. Ta bort den virtuella datorn som påverkas i Azure Portal och behåll disken.

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

5. När du har det hemliga namnet kör du följande kommandon i PowerShell.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Konvertera det Base64-kodade värdet till byte och skriv utdata till en fil. 

    > [!Note]
    > Om du använder alternativet för USB-upplåsning måste fil namnet BEK matcha det ursprungliga BEK-GUID: t. Skapa en mapp på C-enheten med namnet "BEK" innan du följer de här stegen.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. När BEK-filen har skapats på datorn kopierar du filen till den virtuella återställnings datorn som du har den låsta OS-disken ansluten till. Kör följande kommandon med hjälp av fil platsen BEK.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Valfritt**: I vissa fall kan det vara nödvändigt att dekryptera disken med hjälp av det här kommandot.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Föregående kommando förutsätter att disken som ska krypteras är i bokstav F.

8. Om du behöver samla in loggar går du till sökvägen **enhets beteckning: \ Windows\System32\winevt\Logs**.

9. Koppla bort enheten från återställnings datorn.

### <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Följ stegen i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md)för att skapa en ögonblicks bild.

## <a name="collect-an-os-memory-dump"></a>Samla in en minnesdump för operativ system

Använd stegen i avsnittet [samla in OS-dump](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) för att samla in en OS-dump när den virtuella datorn fastnar i konfigurationen.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

När du har samlat in dumpfilen kontaktar du [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att analysera rotor saken.


## <a name="rebuild-the-vm-by-using-powershell"></a>Återskapa den virtuella datorn med hjälp av PowerShell

När du har samlat in minnesdumpen följer du de här stegen för att återskapa den virtuella datorn.

**För icke-hanterade diskar**

```powershell
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

**För Managed disks**

```powershell
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

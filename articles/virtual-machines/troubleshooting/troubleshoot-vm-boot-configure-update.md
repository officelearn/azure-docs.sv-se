---
title: VM Start har fastnat på ”Windows förbereda. Stäng inte av datorn ”. i Azure | Microsoft Docs
description: Introducera stegen för att felsöka problemet i vilken VM Start har fastnat på ”Windows förbereda. Stäng inte av datorn ”.
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
ms.openlocfilehash: e0ad7a8b083a72a9d3c630fa53601aa8fb6ad601
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48862807"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM Start har fastnat på ”Windows förbereda. Stäng inte av datorn ”. i Azure

Den här artikeln hjälper dig att lösa problemet när dina virtuella datorer (VM) har fastnat på den ”Windows förbereda. Stäng inte av datorn ”. steg under starten.

## <a name="symptoms"></a>Symtom

När du använder **Startdiagnostik** för att få Skärmbild av en virtuell dator kan du hitta operativsystemet inte fullständigt startas. Dessutom kan den virtuella datorn visas en **”förbereda för Windows. Stäng inte av datorn ”.** meddelande.

![Exempel på meddelande](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exempel på meddelande](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Orsak

Det här problemet uppstår vanligen när servern gör den slutliga omstarten när konfigurationen ändrades. Konfigurationsändringen kunde initieras av Windows-uppdateringar eller av ändringar på roller /-funktionen på servern. För Windows Update, om storleken på uppdateringarna som har stor eller behöver operativsystemet mer tid för att konfigurera om ändringarna.

## <a name="back-up-the-os-disk"></a>Säkerhetskopiera OS-disken

Säkerhetskopiera OS-disken innan du försöker åtgärda problemet:

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>För virtuella datorer med en krypterad disk, du måste låsa upp diskarna först

Kontrollera om den virtuella datorn är en krypterad virtuell dator. Det gör du genom att följa dessa steg:

1. Öppna den virtuella datorn på portalen och bläddra sedan till diskarna.

2. Du ser en kolumn som anropar ”kryptering”, som talar om kryptering är aktiverat.

Låsa upp den krypterade disken om OS-disken är krypterad. Det gör du genom att följa dessa steg:

1. Skapa ett Recovery virtuell dator som finns i samma resursgrupp, Storage-konto och plats som den berörda virtuella datorn.

2. Ta bort den berörda virtuella datorn och hålla disken i Azure-portalen.

3. Kör PowerShell som administratör.

4. Kör följande cmdlet för att hämta det hemliga namnet.

    ```Powershell
    Login-AzureRmAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. När du har hemligt namn, kör du följande kommandon i PowerShell:

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Konvertera Base64-kodad värde till byte och skriva utdata till en fil. 

    > [!Note]
    > BEK filnamnet måste matcha ursprungligt BEK GUID om du använder USB låsa upp alternativet. Du måste också att skapa en mapp på enhet C med namnet ”BEK” följande steg ska fungera.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. När filen BEK har skapats på din dator kan du kopiera filen till återställningen VM som du har låst OS-disken är ansluten till. Kör följande med BEK Filplats:

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Valfritt** i vissa situationer kan vara nödvändigt även dekryptera disken med det här kommandot.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Detta har tagit med att disken för att kryptera på bokstaven F:.

8. Om du vill samla in loggar du kan navigera till sökvägen **enhet BOKSTAV: \Windows\System32\winevt\Logs**.

9. Koppla bort enheten från dator för återställning.

### <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Om du vill skapa en ögonblicksbild, följer du stegen i [ögonblicksbild av en disk](..\windows\snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Samla in en minnesdump för OS

Följ stegen i den [samla in os dump](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) avsnitt för att samla in en OS-dump när den virtuella datorn har fastnat på konfiguration.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

När du har samlat in dumpfilen Kontakta [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) till analys av grundorsaken.


## <a name="rebuild-the-vm-using-powershell"></a>Återskapa den virtuella datorn med hjälp av PowerShell

När du har samlat in minnesdumpen, Använd följande steg för att återskapa den virtuella datorn.

**För icke-hanterade diskar**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID “SubscriptionID” | Select-AzureRmSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzureRmVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzureRmNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzureRmVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzureRmVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**För hanterade diskar**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

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

#This can be found by selecting the Managed Disks you wish you use in the Azure Portal if the format below doesn't match
$osDiskResouceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availabilty Set
#$avSet = Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResouceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResouceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additnal Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```

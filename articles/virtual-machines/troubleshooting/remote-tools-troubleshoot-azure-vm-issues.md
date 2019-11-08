---
title: Använd fjärrverktyg för att felsöka problem med virtuella Azure-datorer | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: fab1e0b6f3b01446baed974b4be9b7295af4f837
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749715"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Använd fjärrverktyg för att felsöka problem med Azure VM

När du felsöker problem på en virtuell Azure-dator (VM) kan du ansluta till den virtuella datorn med hjälp av de fjärrverktyg som beskrivs i den här artikeln i stället för att använda Remote Desktop Protocol (RDP).

## <a name="serial-console"></a>Seriekonsol

Använd den [virtuella datorns serie konsol](serial-console-windows.md) för att köra kommandon på den virtuella Azure-datorn.

## <a name="remote-cmd"></a>Fjärr-CMD

Ladda ned [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Anslut till den virtuella datorn genom att köra följande kommando:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* Kommandot måste köras på en dator som är i samma VNET.
>* DIP eller värdnamn kan användas för att ersätta \<dator >.
>* Parametern-s ser till att kommandot anropas med hjälp av system kontot (administratörs behörighet).
>* PsExec använder TCP-portarna 135 och 445. De två portarna måste därför vara öppna i brand väggen.

## <a name="run-commands"></a>Kör kommandon

Se [kör PowerShell-skript i din virtuella Windows-dator med kommandot kör](../windows/run-command.md) för mer information om hur du använder funktionen kör kommandon för att köra skript på den virtuella datorn.

## <a name="customer-script-extension"></a>Kundens skript tillägg

Du kan använda funktionen för anpassat skript tillägg för att köra ett anpassat skript på den virtuella mål datorn. Om du vill använda den här funktionen måste följande villkor vara uppfyllda:

* Den virtuella datorn är ansluten.

* Azure-agenten är installerad och fungerar som förväntat på den virtuella datorn.

* Tillägget installerades inte tidigare på den virtuella datorn.
 
  Tillägget kommer bara att mata in skriptet första gången det används. Om du använder den här funktionen senare kommer tillägget att identifiera att det redan har använts och att det inte överför det nya skriptet.

Du måste ladda upp ditt skript till ett lagrings konto och generera en egen behållare. Kör sedan följande skript i Azure PowerShell på en dator som har anslutning till den virtuella datorn.

### <a name="for-v1-vms"></a>För virtuella v1-datorer

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>För v2-VM

 

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Fjärr-PowerShell

>[!Note]
>TCP-port 5986 (HTTPS) måste vara öppen så att du kan använda det här alternativet.
>
>För virtuella ARM-datorer måste du öppna port 5986 på nätverks säkerhets gruppen (NSG). Mer information finns i säkerhets grupper. 
>
>För RDFE-VM: ar måste du ha en slut punkt som har en privat port (5986) och en offentlig port. Sedan måste du också öppna den offentliga porten på NSG.

### <a name="set-up-the-client-computer"></a>Konfigurera klient datorn

Om du vill använda PowerShell för att fjärrans luta till den virtuella datorn måste du först konfigurera klient datorn så att anslutningen tillåts. Det gör du genom att lägga till den virtuella datorn i PowerShell-listan över betrodda värdar genom att köra följande kommando, efter vad som är tillämpligt.

Så här lägger du till en virtuell dator i listan över betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Så här lägger du till flera virtuella datorer i listan över betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Så här lägger du till alla datorer i listan över betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Aktivera RemotePS på den virtuella datorn

För klassiska virtuella datorer använder du anpassat skript tillägg för att köra följande skript:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

För virtuella ARM-datorer använder du kör-kommandon från portalen för att köra EnableRemotePS-skriptet:

![Kör kommando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Anslut till VM:en

Kör följande kommando, beroende på klient datorns plats:

* Utanför VNET eller distribution

  * För en klassisk virtuell dator kör du följande kommando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * För en ARM-VM måste du först lägga till ett DNS-namn till den offentliga IP-adressen. Detaljerade anvisningar finns i [skapa ett fullständigt kvalificerat domän namn i Azure Portal för en virtuell Windows-dator](../windows/portal-create-fqdn.md). Kör sedan följande kommando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* I VNET eller distribution kör du följande kommando:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Om du ställer in flaggan SkipCaCheck kringgås kravet på att importera ett certifikat till den virtuella datorn när du startar sessionen.

Du kan också använda cmdleten Invoke-Command för att köra ett skript på den virtuella datorn från en fjärrdator:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Fjär register

>[!Note]
>TCP-port 135 eller 445 måste vara öppen för att det här alternativet ska kunna användas.
>
>För virtuella ARM-datorer måste du öppna port 5986 på NSG. Mer information finns i säkerhets grupper. 
>
>För RDFE-VM: ar måste du ha en slut punkt som har en privat port 5986 och en offentlig port. Du måste också öppna den offentliga porten på NSG.

1. Öppna Registereditorn (regedit. exe) från en annan virtuell dator i samma VNET.

2. Välj **fil** >**Anslut nätverks registret**.

   ![Fjärralternativ](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Leta upp den virtuella mål datorn efter **värdnamn** eller **dynamisk IP** (föredra) genom att ange den i rutan "Ange det objekt namn som ska väljas".

   ![Fjärralternativ](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Ange autentiseringsuppgifterna för den virtuella mål datorn.

5. Gör nödvändiga register ändringar.

## <a name="remote-services-console"></a>Konsolen för fjärrtjänster

>[!Note]
>TCP-portarna 135 eller 445 måste vara öppna för att det här alternativet ska kunna användas.
>
>För virtuella ARM-datorer måste du öppna port 5986 på NSG. Mer information finns i säkerhets grupper. 
>
>För RDFE-VM: ar måste du ha en slut punkt som har en privat port 5986 och en offentlig port. Sedan måste du också öppna den offentliga porten på NSG.

1. Öppna en instans av **Services. msc**från en annan virtuell dator i samma VNet.

2. Högerklicka på **tjänster (lokalt)** .

3. Välj **Anslut till en annan dator**.

   ![Fjärrtjänst](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Ange den dynamiska IP-adressen för den virtuella mål datorn.

   ![Ingångs-DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Gör nödvändiga ändringar i tjänsterna.

## <a name="next-steps"></a>Nästa steg

[Retur-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Anpassat skript tillägg för Windows med hjälp av den klassiska distributions modellen](../extensions/custom-script-classic.md)

PsExec är en del av [PSTools-sviten](https://download.sysinternals.com/files/PSTools.zip).

Mer information om PSTools Suite finns i [PSTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).



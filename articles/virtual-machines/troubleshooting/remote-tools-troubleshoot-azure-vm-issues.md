---
title: Använd verktyg för felsökning av problem med virtuella Azure-datorer | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: dc27ea0552c6135d01256586b1746219caac17f1
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58539878"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Använd Administrationsverktyg för att felsöka problem med virtuella Azure-datorer

När du felsöker problem på en Azure-dator (VM), kan du ansluta till den virtuella datorn genom att använda remote tools som beskrivs i den här artikeln istället för att använda Remote Desktop Protocol (RDP).

## <a name="serial-console"></a>Seriekonsol

Använd [Virtual Machine Serial Console](serial-console-windows.md) att köra kommandon på den fjärranslutna Azure-VM.

## <a name="remote-cmd"></a>Remote CMD

Ladda ned [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Anslut till den virtuella datorn genom att köra följande kommando:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* Kommandot måste köras på en dator som är i samma virtuella nätverk.
>* DIP eller värdnamnet kan användas för att ersätta \<dator >.
>* Parametern -s ser till att kommandot har anropats med hjälp av System-kontot (administratörsbehörighet).
>* PsExec använder TCP-portarna 135 och 445. Två portar måste därför vara öppen i brandväggen.

## <a name="run-commands"></a>Kör kommandon

Se [Kör PowerShell-skript i din virtuella Windows-dator med kommandot Kör](../windows/run-command.md) för mer information om hur du använder funktionen kör kommandon för att köra skript på den virtuella datorn.

## <a name="customer-script-extension"></a>Kundskriptstillägget

Du kan använda funktionen för tillägget för anpassat skript för att köra ett anpassat skript på den Virtuella måldatorn. Följande villkor måste vara uppfyllda för att använda den här funktionen:

* Den virtuella datorn är ansluten till.

* Azure-agenten är installerad och fungerar som förväntat på den virtuella datorn.

* Tillägget har inte installerats på den virtuella datorn.
 
  Tillägget mata in skriptet bara första gången den används. Om du använder den här funktionen senare tillägget identifierar att den har redan använts och kommer inte att ladda upp det nya skriptet.

Du måste ladda upp skriptet till ett lagringskonto och generera en egen behållare. Kör sedan följande skript i Azure PowerShell på en dator som är ansluten till den virtuella datorn.

### <a name="for-v1-vms"></a>För virtuella datorer i V1

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

### <a name="for-v2-vms"></a>För V2-datorer

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

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
>TCP Port 5986 (HTTPS) måste vara öppen så att du kan använda det här alternativet.
>
>För ARM-datorer måste du öppna port 5986 på nätverkssäkerhetsgrupp (NSG). Mer information finns i säkerhetsgrupper. 
>
>RDFE virtuella datorer, måste du ha en slutpunkt som har en privat port (5986) och en offentlig port. Sedan har du också öppna den offentliga riktar sig mot-porten på NSG: N.

### <a name="set-up-the-client-computer"></a>Konfigurera klientdatorn

För att använda PowerShell för att fjärransluta till den virtuella datorn måste först du konfigurera datorn så att anslutningen. Gör detta genom att lägga till den virtuella datorn i listan över betrodda värdar PowerShell genom att köra följande kommando, efter behov.

Att lägga till en virtuell dator i listan över betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Att lägga till flera virtuella datorer i listan över betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Att lägga till alla datorer i listan över betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Aktivera RemotePS på den virtuella datorn

För klassiska virtuella datorer, att använda tillägget för anpassat skript för att köra följande skript:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

För ARM-datorer, använder du kör kommandon från portalen för att köra skriptet EnableRemotePS:

![Kör kommando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Anslut till VM:en

Kör följande kommando, beroende på klienten datorns placering:

* Utanför det virtuella nätverket eller distribution

  * För en klassisk virtuell dator, kör du följande kommando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * För en virtuell dator i ARM, först lägga till ett DNS-namn till den offentliga IP-adressen. Detaljerade anvisningar finns i [skapa ett fullständigt kvalificerat domännamn i Azure portal för en virtuell Windows-dator](../windows/portal-create-fqdn.md). Kör sedan följande kommando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* I virtuella nätverk eller distributionen, kör du följande kommando:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Flagga som anger SkipCaCheck kringgår kravet på att importera ett certifikat till den virtuella datorn när du startar sessionen.

Du kan också använda cmdleten Invoke-Command för att köra ett skript på den virtuella datorn via en fjärranslutning:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Remote Registry

>[!Note]
>TCP-port 135 eller 445 måste vara öppna för att kunna använda det här alternativet.
>
>Du måste öppna port 5986 på NSG: N för ARM-datorer. Mer information finns i säkerhetsgrupper. 
>
>Du måste ha en slutpunkt som har en privat port 5986 och en offentlig port för RDFE-datorer. Du måste också öppna den offentliga porten på NSG: N.

1. Öppna Registereditorn (regedit.exe) från en annan virtuell dator på samma virtuella nätverk.

2. Välj **filen** >**ansluta register**.

   ![Alternativ för fjärråtkomst](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Leta upp den Virtuella måldatorn av **värdnamn** eller **dynamisk IP** (bättre) genom att skriva in den i rutan ”Ange objektnamn att välja”.

   ![Alternativ för fjärråtkomst](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Ange autentiseringsuppgifterna för den Virtuella måldatorn.

5. Gör alla nödvändiga ändringar.

## <a name="remote-services-console"></a>Fjärrtjänster konsolen

>[!Note]
>TCP-portar 135 eller 445 måste vara öppna för att kunna använda det här alternativet.
>
>Du måste öppna port 5986 på NSG: N för ARM-datorer. Mer information finns i säkerhetsgrupper. 
>
>Du måste ha en slutpunkt som har en privat port 5986 och en offentlig port för RDFE-datorer. Sedan har du också öppna den offentliga porten på NSG: N.

1. Öppna en instans av en annan virtuell dator på samma virtuella nätverk, **Services.msc**.

2. Högerklicka på **tjänster (lokala)**.

3. Välj **Anslut till en annan dator**.

   ![Fjärrtjänst](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Ange dynamiska IP-Adressen för den Virtuella måldatorn.

   ![Indata DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Gör nödvändiga ändringar till tjänsterna.

## <a name="next-steps"></a>Nästa steg

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Anpassade skript-tillägget för Windows med hjälp av den klassiska distributionsmodellen](../extensions/custom-script-classic.md)

PsExec är en del av den [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip).

Läs mer om våra PSTools [PSTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).



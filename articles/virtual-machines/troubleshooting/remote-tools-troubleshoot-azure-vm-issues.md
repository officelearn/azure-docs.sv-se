---
title: Använd fjärrverktyg för att felsöka Problem med Azure VM | Microsoft-dokument
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
ms.openlocfilehash: b86b1a2d8a49554cc3df99e0a32a2c0ccaacb560
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920016"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Använda fjärrverktyg för att felsöka Problem med Azure VM

När du felsöker problem på en virtuell Azure-dator (VM) kan du ansluta till den virtuella datorn med hjälp av fjärrverktygen som beskrivs i den här artikeln i stället för att använda RDP (Remote Desktop Protocol).

## <a name="serial-console"></a>Seriekonsol

Använd en [seriell konsol för Virtuella Azure-datorer för](serial-console-windows.md) att köra kommandon på fjärrdass.Virtual Vm.

## <a name="remote-cmd"></a>Fjärr-CMD

Ladda ner [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Anslut till den virtuella datorn genom att köra följande kommando:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Kommandot måste köras på en dator som finns i samma virtuella nätverk.
>* DIP eller HostName kan \<användas för att ersätta dator>.
>* Parametern -s ser till att kommandot anropas med hjälp av Systemkonto (administratörsbehörighet).
>* PsExec använder TCP-portar 135 och 445. Därför måste de två portarna vara öppna på brandväggen.

## <a name="run-command"></a>Kör kommandot 

Mer information om hur du använder kommandofunktionen kör för att köra skript på den virtuella datorn finns [i Kör PowerShell-skript i windows-datorn med kommandot kör](../windows/run-command.md).

## <a name="custom-script-extension"></a>Anpassat skripttillägg

Du kan använda funktionen Anpassat skripttillägg för att köra ett anpassat skript på måldatorn. För att kunna använda den här funktionen måste följande villkor vara uppfyllda:

* Den virtuella datorn har anslutning.
* Azure Virtual Machine Agent är installerad och fungerar som förväntat på den virtuella datorn.
* Tillägget har inte tidigare installerats på den virtuella datorn.
 
  Tillägget injicerar skriptet bara första gången det används. Om du använder den här funktionen senare känner tillägget igen att det redan har använts och inte överför det nya skriptet.

Ladda upp skriptet till ett lagringskonto och generera en egen behållare. Kör sedan följande skript i Azure PowerShell på en dator som har anslutning till den virtuella datorn.

### <a name="for-classic-deployment-model-vms"></a>För virtuella datorer för klassiska distributionsmodeller

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>För virtuella azure resource manager-datorer

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Fjärr-PowerShell

>[!NOTE]
>TCP Port 5986 (HTTPS) måste vara öppen så att du kan använda det här alternativet.
>
>För virtuella Azure Resource Manager-datorer måste du öppna port 5986 i nätverkssäkerhetsgruppen (NSG). Mer information finns i Säkerhetsgrupper. 
>
>För virtuella datorer för fjärrskrivbordsfäder måste du ha en slutpunkt som har en privat port (5986) och en offentlig port. Sedan måste du också öppna den offentliga porten på NSG.

### <a name="set-up-the-client-computer"></a>Konfigurera klientdatorn

Om du vill använda PowerShell för att fjärransluta till den virtuella datorn måste du först konfigurera klientdatorn så att anslutningen tillåts. Det gör du genom att lägga till den virtuella datorn i listan betrodda betrodda värdar i PowerShell genom att köra följande kommando, beroende på vad som är lämpligt.

Så här lägger du till en virtuell dator i listan betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Så här lägger du till flera virtuella datorer i listan betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Så här lägger du till alla datorer i listan betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Aktivera RemotePS på den virtuella datorn

För virtuella datorer som skapats med den klassiska distributionsmodellen använder du det anpassade skripttillägget för att köra följande skript:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

För virtuella Azure Resource Manager-datorer använder du kör kommandon från portalen för att köra EnableRemotePS-skriptet:

![Kör kommandot ](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Anslut till VM:en

Kör följande kommando baserat på klientdatorns plats:

* Utanför det virtuella nätverket eller distributionen

  * För en virtuell dator som skapats med den klassiska distributionsmodellen kör du följande kommando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * För en virtuell azure Resource Manager-dator lägger du först till ett DNS-namn i den offentliga IP-adressen. Detaljerade steg finns [i Skapa ett fullständigt kvalificerat domännamn i Azure-portalen för en Windows VM](../windows/portal-create-fqdn.md). Kör sedan följande kommando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Kör följande kommando i det virtuella nätverket eller distributionen:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Om du ställer in flaggan SkipCaCheck kringgås kravet på att importera ett certifikat till den virtuella datorn när du startar sessionen.

Du kan också använda cmdleten Invoke-Command för att fjärrstyra ett skript på den virtuella datorn.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Fjärregister

>[!NOTE]
>TCP-port 135 eller 445 måste vara öppen för att kunna använda det här alternativet.
>
>För virtuella Azure Resource Manager-datorer måste du öppna port 5986 på NSG. Mer information finns i Säkerhetsgrupper. 
>
>För virtuella datorer för fjärrskrivbordsfäder måste du ha en slutpunkt som har en privat port 5986 och en offentlig port. Du måste också öppna den offentliga porten på NSG.

1. Från en annan virtuell dator i samma virtuella nätverk öppnar du registerredigeraren (regedit.exe).

2. Välj Register över**filanslutningsnätverk** **File** > .

   ![Registerredigerare](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Leta reda på måldatorn efter **värdnamn** eller **dynamisk IP** (att föredra) genom att ange den i rutan **Ange objektnamnet som ska markeras.**

   ![Ange den objektnamn som ska markeras](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Ange autentiseringsuppgifterna för måldatorn.

5. Gör nödvändiga registerändringar.

## <a name="remote-services-console"></a>Konsolen Fjärrtjänster

>[!NOTE]
>TCP-portar 135 eller 445 måste vara öppna för att kunna använda det här alternativet.
>
>För virtuella Azure Resource Manager-datorer måste du öppna port 5986 på NSG. Mer information finns i Säkerhetsgrupper. 
>
>För virtuella datorer för fjärrskrivbordsfäder måste du ha en slutpunkt som har en privat port 5986 och en offentlig port. Du måste också öppna den offentliga porten på NSG.

1. Öppna en instans av **Services.msc**från en annan virtuell dator i samma virtuella nätverk.

2. Högerklicka på **Tjänster (Lokal)**.

3. Välj **Anslut till en annan dator**.

   ![Fjärrtjänst](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Ange den dynamiska IP-adressen för måldatorn.

   ![Dynamisk IP-för input](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Gör nödvändiga ändringar i tjänsterna.

## <a name="next-steps"></a>Nästa steg

- Mer information om cmdleten Enter-PSSession finns i [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- Mer information om det anpassade skripttillägget för Windows med den klassiska distributionsmodellen finns i [Anpassat skripttillägg för Windows](../extensions/custom-script-classic.md).
- PsExec är en del av [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip).
- Mer information om PSTools Suite finns i [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).



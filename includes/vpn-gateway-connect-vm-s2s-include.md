---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5acdd54bcf7e253bc21dc3f99207fc1b2bd1ff59
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
Du kan ansluta till en virtuell dator som är distribuerad till ditt VNet genom att skapa en anslutning till fjärrskrivbord till den virtuella datorn. Det bästa sättet att först kontrollera att du kan ansluta till den virtuella datorn är att ansluta via dess privata IP-adress snarare än datornamnet. På så sätt kan du testa om du kan ansluta, men inte om namnmatchningen är korrekt konfigurerad.

1. Leta upp den privata IP-adressen. Du hittar den privata IP-adressen för en virtuell dator på flera olika sätt. Nedan visar vi stegen för Azure-portalen och PowerShell.

  - Azure Portal – Leta upp din första virtuella dator på Azure Portal. Visa egenskaperna för den virtuella datorn. Den privata IP-adressen är angiven.

  - PowerShell – Använd exemplet för att visa en lista över virtuella datorer och privata IP-adresser från dina resursgrupper. Du behöver inte ändra exemplet innan du använder det.

    ```azurepowershell-interactive
    $VMs = Get-AzureRmVM
    $Nics = Get-AzureRmNetworkInterface | Where VirtualMachine -ne $null

    foreach($Nic in $Nics)
    {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
    }
    ```

2. Kontrollera att du är ansluten till ditt VNet med VPN-anslutning.
3. Öppna **fjärrskrivbordsanslutningen** genom att skriva "RDP" eller "Anslutning till fjärrskrivbord" i sökrutan i Aktivitetsfältet och välj sedan Anslutning till fjärrskrivbord. Du kan även öppna Anslutning till fjärrskrivbord med hjälp av kommandot ”mstsc” i PowerShell. 
4. I fjärrskrivbordsanslutningen anger du den virtuella datorns privata IP-adress. Du kan klicka på "Visa alternativ" för att justera inställningar och sedan ansluta.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Felsöka en RDP-anslutning till en virtuell dator

Om du har problem med att ansluta till en virtuell dator via VPN-anslutningen kan du kontrollera följande:

- Kontrollera att VPN-anslutningen har genomförts.
- Kontrollera att du ansluter till den virtuella datorns privata IP-adress.
- Om du kan ansluta till den virtuella datorn med hjälp av den privata IP-adressen, men inte med namnet på datorn, kontrollerar du att du har konfigurerat DNS korrekt. Mer information om hur namnmatchningen fungerar för virtuella datorer finns i [Namnmatchning för virtuella datorer](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Mer information finns i [Felsöka fjärrskrivbordsanslutningar till en virtuell dator](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).
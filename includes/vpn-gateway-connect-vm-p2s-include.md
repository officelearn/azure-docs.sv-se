---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35814de74fa03f9969cdd48882a5f672cfe306a1
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418091"
---
Du kan ansluta till en virtuell dator som är distribuerad till ditt VNet genom att skapa en anslutning till fjärrskrivbord till den virtuella datorn. Det bästa sättet att först kontrollera att du kan ansluta till den virtuella datorn är att ansluta via dess privata IP-adress snarare än datornamnet. På så sätt kan du testa om du kan ansluta, men inte om namnmatchningen är korrekt konfigurerad.

1. Leta upp den privata IP-adressen. Du kan hitta privata IP-adressen för en virtuell dator genom att antingen granska egenskaperna för den virtuella datorn i Azure Portal eller med hjälp av PowerShell.

   - Azure Portal – Leta upp din första virtuella dator på Azure Portal. Visa egenskaperna för den virtuella datorn. Den privata IP-adressen är angiven.

   - PowerShell – Använd exemplet för att visa en lista över virtuella datorer och privata IP-adresser från dina resursgrupper. Du behöver inte ändra exemplet innan du använder det.

     ```powershell
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. Kontrollera att du är ansluten till ditt VNet med en punkt-till-plats-anslutning via VPN.
3. Öppna **fjärrskrivbordsanslutningen** genom att skriva "RDP" eller "Anslutning till fjärrskrivbord" i sökrutan i Aktivitetsfältet och välj sedan Anslutning till fjärrskrivbord. Du kan även öppna Anslutning till fjärrskrivbord med hjälp av kommandot ”mstsc” i PowerShell. 
4. I fjärrskrivbordsanslutningen anger du den virtuella datorns privata IP-adress. Du kan klicka på "Visa alternativ" för att justera inställningar och sedan ansluta.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Felsöka en RDP-anslutning till en virtuell dator

Om du har problem med att ansluta till en virtuell dator via VPN-anslutningen kan du kontrollera följande:

- Kontrollera att VPN-anslutningen har genomförts.
- Kontrollera att du ansluter till den virtuella datorns privata IP-adress.
- Använd ”ipconfig” för att kontrollera vilken IPv4-adress som har tilldelats till Ethernet-adaptern på den dator som du ansluter från. Om IP-adressen ligger inom adressintervallet för det virtuella nätverk som du ansluter till eller inom adressintervallet för din VPNClientAddressPool, kallas detta för ett överlappande adressutrymme. När ditt adressutrymme överlappar på det här sättet når inte nätverkstrafiken Azure, utan stannar i det lokala nätverket.
- Om du kan ansluta till den virtuella datorn med hjälp av den privata IP-adressen, men inte med namnet på datorn, kontrollerar du att du har konfigurerat DNS korrekt. Mer information om hur namnmatchningen fungerar för virtuella datorer finns i [Namnmatchning för virtuella datorer](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Kontrollera att paketet för VPN-klientkonfiguration har skapats efter att IP-adresser för DNS-server har angetts för VNet. Om du uppdaterade IP-adresserna för DNS-servern skapar och installerar du ett nytt paket för VPN-klientkonfiguration.
- Mer information finns i [Felsöka fjärrskrivbordsanslutningar till en virtuell dator](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).
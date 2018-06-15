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
ms.openlocfilehash: e49cfe786272b34675ca377808e2961e61a757e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197592"
---
Du kan ansluta till en virtuell dator som är distribuerad till ditt VNet genom att skapa en anslutning till fjärrskrivbord till den virtuella datorn. Det bästa sättet att först kontrollera att du kan ansluta till den virtuella datorn är att ansluta via dess privata IP-adress snarare än datornamnet. På så sätt kan du testa om du kan ansluta, men inte om namnmatchningen är korrekt konfigurerad. 

1. Leta upp den privata IP-adressen för den virtuella datorn. Du kan hitta privata IP-adressen för en virtuell dator genom att antingen granska egenskaperna för den virtuella datorn i Azure Portal eller med hjälp av PowerShell.
2. Kontrollera att du är ansluten till ditt VNet med en punkt-till-plats-anslutning via VPN. 
3. Öppna fjärrskrivbordsanslutningen genom att skriva "RDP" eller "Remote Desktop Connection" (fjärrskrivbordsanslutning) i sökrutan i aktivitetsfältet och välj sedan fjärrskrivbordsanslutning. Du kan även öppna Anslutning till fjärrskrivbord med hjälp av kommandot ”mstsc” i PowerShell. 
3. I fjärrskrivbordsanslutningen anger du den virtuella datorns privata IP-adress. Du kan klicka på "Visa alternativ" för att justera inställningar och sedan ansluta.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Felsöka en RDP-anslutning till en virtuell dator

Om du har problem med att ansluta till en virtuell dator via VPN-anslutningen finns det några saker som du kan kontrollera. Mer felsökningsinformation finns i [Felsöka fjärrskrivbordsanslutningar till en virtuell dator](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).

- Kontrollera att VPN-anslutningen har genomförts.
- Kontrollera att du ansluter till den virtuella datorns privata IP-adress.
- Använd ”ipconfig” för att kontrollera vilken IPv4-adress som har tilldelats till Ethernet-adaptern på den dator som du ansluter från. Om IP-adressen ligger inom adressintervallet för det virtuella nätverk som du ansluter till eller inom adressintervallet för din VPNClientAddressPool, kallas detta för ett överlappande adressutrymme. När ditt adressutrymme överlappar på det här sättet når inte nätverkstrafiken Azure, utan stannar i det lokala nätverket.
- Om du kan ansluta till den virtuella datorn med hjälp av den privata IP-adressen, men inte med namnet på datorn, kontrollerar du att du har konfigurerat DNS korrekt. Mer information om hur namnmatchningen fungerar för virtuella datorer finns i [Namnmatchning för virtuella datorer](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Kontrollera att paketet för VPN-klientkonfiguration har skapats efter att IP-adresser för DNS-server har angetts för VNet. Om du uppdaterade IP-adresserna för DNS-servern skapar och installerar du ett nytt paket för VPN-klientkonfiguration.
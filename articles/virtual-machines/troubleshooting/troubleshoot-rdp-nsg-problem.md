---
title: Det går inte att ansluta till virtuella Azure-datorer eftersom RDP-porten inte är aktiverad i NSG | Microsoft-dokument
description: Lär dig hur du felsöker ett problem där RDP misslyckas på grund av NSG-konfigurationen i Azure-portalen | Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 6c877690cf27edd73f1d828b8a1dda6f4f34e780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918180"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Det går inte att fjärransluta till en virtuell dator eftersom RDP-porten inte är aktiverad i NSG

I den här artikeln beskrivs hur du löser ett problem där du inte kan ansluta till en virtuell Azure Windows-dator (VM) eftersom RDP-porten (Remote Desktop Protocol) inte är aktiverad i nätverkssäkerhetsgruppen (NSG).


## <a name="symptom"></a>Symptom

Du kan inte göra en RDP-anslutning till en virtuell dator i Azure eftersom RDP-porten inte öppnas i nätverkssäkerhetsgruppen.

## <a name="solution"></a>Lösning 

När du skapar en ny virtuell dator blockeras all trafik från Internet som standard. 

Så här aktiverar du RDP-porten i en NSG:00:
1. Logga in [på Azure-portalen](https://portal.azure.com).
2. I **Virtuella datorer**väljer du den virtuella datorn som har problemet. 
3. Välj **Nätverk**i **Inställningar**. 
4. Kontrollera om porten för RDP är korrekt inställd i regler för **inkommande port.** Följande är ett exempel på konfigurationen: 

    **Prioritet**: 300 </br>
    **Namn**: Port_3389 </br>
    **Hamn(Destination)**: 3389 </br>
    **Protokoll**: TCP </br>
    **Källa**: Alla </br>
    **Destinationer:** Alla </br>
    **Åtgärd**: Tillåt </br>

Om du anger källans IP-adress tillåter den här inställningen endast trafik från en viss IP-adress eller ett visst intervall med IP-adresser att ansluta till den virtuella datorn. Kontrollera att datorn du använder för att starta RDP-sessionen ligger inom intervallet.

Mer information om NSG finns i [nätverkssäkerhetsgruppen](../../virtual-network/security-overview.md).

> [!NOTE]
> RDP-port 3389 är exponerad för Internet. Därför rekommenderar vi att du använder den här porten endast för rekommenderad testning. För produktionsmiljöer rekommenderar vi att du använder en VPN- eller privat anslutning.

## <a name="next-steps"></a>Nästa steg

Om RDP-porten redan är aktiverad i NSG läser [du Felsöka ett generalfel för fjärrskrivbordsprogrammen i Azure VM](./troubleshoot-rdp-general-error.md).




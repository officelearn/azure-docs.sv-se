---
title: Det går inte att ansluta till virtuella Azure-datorer eftersom RDP-porten inte är aktive rad i NSG | Microsoft Docs
description: Lär dig hur du felsöker ett problem där RDP Miss lyckas på grund av NSG-konfigurationen i Azure Portal | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77918180"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Det går inte att fjärrans luta till en virtuell dator eftersom RDP-porten inte är aktive rad i NSG

Den här artikeln förklarar hur du löser ett problem som du inte kan ansluta till en virtuell Azure Windows-dator på, eftersom den Remote Desktop Protocol (RDP) inte är aktive rad i nätverks säkerhets gruppen (NSG).


## <a name="symptom"></a>Symptom

Det går inte att upprätta en RDP-anslutning till en virtuell dator i Azure eftersom RDP-porten inte är öppen i nätverks säkerhets gruppen.

## <a name="solution"></a>Lösning 

När du skapar en ny virtuell dator blockeras all trafik från Internet som standard. 

Följ dessa steg om du vill aktivera RDP-porten i en NSG:
1. Logga in på [Azure Portal](https://portal.azure.com).
2. I **Virtual Machines**väljer du den virtuella dator som har problemet. 
3. I **Inställningar**väljer du **nätverk**. 
4. I **regler för inkommande port**kontrollerar du om porten för RDP är korrekt inställd. Följande är ett exempel på konfigurationen: 

    **Prioritet**: 300 </br>
    **Namn**: Port_3389 </br>
    **Port (mål)**: 3389 </br>
    **Protokoll**: TCP </br>
    **Källa**: alla </br>
    **Destinationer**: alla </br>
    **Åtgärd**: Tillåt </br>

Om du anger käll-IP-adressen tillåter den här inställningen trafik endast från en speciell IP-adress eller ett intervall med IP-adresser för att ansluta till den virtuella datorn. Kontrol lera att datorn som du använder för att starta RDP-sessionen ligger inom intervallet.

Mer information om NSG: er finns i [nätverks säkerhets grupp](../../virtual-network/security-overview.md).

> [!NOTE]
> RDP-port 3389 exponeras för Internet. Vi rekommenderar därför att du bara använder den här porten för testning. För produktions miljöer rekommenderar vi att du använder en VPN-eller privat anslutning.

## <a name="next-steps"></a>Nästa steg

Om RDP-porten redan är aktive rad i NSG, se [Felsöka ett RDP-allmänt fel i virtuell Azure-dator](./troubleshoot-rdp-general-error.md).




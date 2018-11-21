---
title: Det går inte att ansluta till Azure Virtual Machines eftersom RDP-porten inte är aktiverat i NSG | Microsoft Docs
description: Lär dig att felsöka ett problem där RDP misslyckas på grund av NSG-konfiguration i Azure-portalen | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 89af30533e10df0968b60039d7ea15886e89bc25
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52267100"
---
#  <a name="cannot-rdp-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Kan inte använda RDP till en virtuell dator eftersom RDP-porten inte är aktiverat i NSG

Den här artikeln visar hur du löser problem som du inte kan ansluta till Azure Windows Virtual Machines (VM) eftersom porten inte är aktiverad i nätverkssäkerhetsgruppen.


> [!NOTE] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver Resource Manager-distributionsmodellen, som vi rekommenderar att du använder för nya distributioner i stället för den klassiska distributionsmodellen. 

## <a name="symptom"></a>Symtom

Du kan inte göra en RDP Remote Desktop Protocol ()-anslutning till en virtuell dator i Azure på grund av RDP-porten inte öppnas i nätverkssäkerhetsgruppen.

## <a name="solution"></a>Lösning 

När du skapar en ny virtuell dator, blockeras all trafik från Internet som standard. 

Följ dessa steg om du vill aktivera RDP-porten i nätverkssäkerhetsgrupp:
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I **virtuella datorer**, den virtuella datorn som har problem. 
3. I **inställningar**väljer **nätverk**. 
4. I **regler för inkommande portar**, kontrollera om porten för RDP är korrekt. Följande är exempel på konfigurationen. 

    **Prioritet**: 300 </br>
    **Port**: 3389 </br>
    **Namn på**: Port_3389 </br>
    **Port**: 3389 </br>
    **Protokollet**: TCP </br>
    **Källan**: alla </br>
    **Mål**: alla </br>
    **Åtgärden**: Tillåt </br>

I anger du IP-källadressen, den här inställningen tillåter trafik enbart från en specifik IP- eller intervall av IP-adresser kan ansluta till den virtuella datorn. Kontrollera att den dator som du använder för att initiera RDP-session är i intervallet.

Läs mer om nätverkssäkerhetsgruppen [nätverkssäkerhetsgrupp](../../virtual-network/security-overview.md).

> [!NOTE]
> RDP-porten 3389 visas på Internet. Det här rekommenderas endast för testning. För produktionsmiljöer rekommenderar vi en VPN eller privat anslutning.

## <a name="next-steps"></a>Nästa steg

Om RDP-porten är redan aktiverad i nätverkssäkerhetsgruppen, se [felsöka en RDP-Allmänt fel i Azure VM](./troubleshoot-rdp-general-error.md).




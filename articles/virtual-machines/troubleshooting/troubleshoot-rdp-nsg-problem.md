---
title: Det går inte att ansluta till virtuella Azure-datorer eftersom RDP-porten inte är aktiverat i NSG | Microsoft Docs
description: Lär dig att felsöka ett problem där RDP misslyckas på grund av NSG-konfiguration i Azure portal | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: c32612c411f275220f549eea79276fa5a7232fd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318943"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Det går inte att fjärransluta till en virtuell dator eftersom RDP-porten inte är aktiverat i NSG

Den här artikeln förklarar hur du löser problem som du inte kan ansluta till en Azure Windows-dator (VM) eftersom Remote Desktop Protocol (RDP)-porten inte är aktiverad i nätverkssäkerhetsgruppen (NSG).


> [!NOTE] 
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Vi rekommenderar att du använder Resource Manager-distributionsmodellen i stället för den klassiska distributionsmodellen för nya distributioner. 

## <a name="symptom"></a>Symtom

Du kan inte göra en RDP-anslutning till en virtuell dator i Azure eftersom RDP-porten inte öppnas i nätverkssäkerhetsgruppen.

## <a name="solution"></a>Lösning 

När du skapar en ny virtuell dator, blockeras all trafik från Internet som standard. 

Följ dessa steg om du vill aktivera RDP-porten i en NSG:
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I **virtuella datorer**, Välj den virtuella datorn där problemet. 
3. I **inställningar**väljer **nätverk**. 
4. I **regler för inkommande portar**, kontrollera om porten för RDP har angetts korrekt. Följande är ett exempel på konfigurationen: 

    **Prioritet**: 300 </br>
    **Port**: 3389 </br>
    **Namn**: Port_3389 </br>
    **Port**: 3389 </br>
    **Protokoll**: TCP </br>
    **Källa**: Alla </br>
    **Mål**: Alla </br>
    **Åtgärd**: Tillåt </br>

Om du anger källans IP-adress, kan den här inställningen endast trafik från en specifik IP-adress eller IP-adressintervall för att ansluta till den virtuella datorn. Kontrollera att datorn du använder för att starta RDP-session är inom intervallet.

Mer information om Nätverkssäkerhetsgrupper finns i [nätverkssäkerhetsgrupp](../../virtual-network/security-overview.md).

> [!NOTE]
> RDP-porten 3389 visas på Internet. Därför rekommenderar vi att du använder den här porten endast för rekommenderas för testning. För produktionsmiljöer rekommenderar vi att du använder en VPN eller privat anslutning.

## <a name="next-steps"></a>Nästa steg

Om RDP-porten är redan aktiverad i NSG, se [felsöka en RDP-Allmänt fel i Azure VM](./troubleshoot-rdp-general-error.md).




---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b7bbf1fb8ffc1f57a53682d3baf288077b144572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664693"
---
### <a name="virtual-networks"></a>Virtuella nätverk

|  |  |
|---------|---------|
| [Tillåtna SKU:er för programgateway](../articles/azure-policy/scripts/allowed-app-gate-sku.md) | Kräver att programgatewayer använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Ingen nätverkspeering till ER-nätverk](../articles/azure-policy/scripts/no-peering-er-net.md) | Förbjuder att en nätverkspeering associeras till ett nätverk i en angiven resursgrupp. Använd för att förhindra anslutning med centralt hanterad nätverksinfrastruktur. Du kan ange namnet på resursgruppen att förhindra association. |
| [Ingen användardefinierad routningstabell](../articles/azure-policy/scripts/no-user-def-route-table.md)  |Förbjuder att virtuella nätverk distribueras med en användardefinierad routningstabell. |
| [NSG X i varje undernät](../articles/azure-policy/scripts/nsg-on-subnet.md) | Kräver att en viss nätverkssäkerhetsgrupp används med varje virtuella undernät. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [Använda godkända undernät för VM-nätverksgränssnitt](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt undernät. Du anger ID för det godkända undernätet. |
| [Använda godkända vNet för VM-nätverksgränssnitt](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt virtuellt nätverk. Du anger ID för det godkända virtuella nätverket. |
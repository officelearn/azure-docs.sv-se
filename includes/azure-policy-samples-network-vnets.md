---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: df9c291136fc6a48effb08cd59eeb66486eb641c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003970"
---
### <a name="virtual-networks"></a>Virtuella nätverk

|  |  |
|---------|---------|
| [Tillåtna SKU:er för programgateway](../articles/governance/policy/samples/allowed-app-gate-sku.md) | Kräver att programgatewayer använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Ingen nätverkspeering till ER-nätverk](../articles/governance/policy/samples/no-peering-er-net.md) | Förbjuder att en nätverkspeering associeras till ett nätverk i en angiven resursgrupp. Använd för att förhindra anslutning med centralt hanterad nätverksinfrastruktur. Du kan ange namnet på resursgruppen att förhindra association. |
| [Ingen användardefinierad routningstabell](../articles/governance/policy/samples/no-user-def-route-table.md)  |Förbjuder att virtuella nätverk distribueras med en användardefinierad routningstabell. |
| [NSG X i varje undernät](../articles/governance/policy/samples/nsg-on-subnet.md) | Kräver att en viss nätverkssäkerhetsgrupp används med varje virtuella undernät. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [Använda godkända undernät för VM-nätverksgränssnitt](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt undernät. Du anger ID för det godkända undernätet. |
| [Använda godkända vNet för VM-nätverksgränssnitt](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt virtuellt nätverk. Du anger ID för det godkända virtuella nätverket. |
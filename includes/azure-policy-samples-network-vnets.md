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
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318308"
---
### <a name="virtual-networks"></a>Virtuella nätverk

|  |  |
|---------|---------|
| [Tillåtna SKU:er för programgateway](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | Kräver att programgatewayer använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Tillåtna vNet-gateway SKU:er](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Kräver att virtuella nätverksgatewayer använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Tillåtna SKU:er för lastbalanserare](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | Kräver att lastbalanserare för virtuella nätverk använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Ingen nätverkspeering till Express Route-nätverk](../articles/governance/policy/samples/no-peering-express-route-network.md) | Förbjuder att en nätverkspeering associeras till ett nätverk i en angiven resursgrupp. Använd för att förhindra anslutning med centralt hanterad nätverksinfrastruktur. Du kan ange namnet på resursgruppen att förhindra association. |
| [Ingen användardefinierad routningstabell](../articles/governance/policy/samples/no-user-defined-route-table.md)  | Förbjuder att virtuella nätverk distribueras med en användardefinierad routningstabell. |
| [NSG X i varje undernät](../articles/governance/policy/samples/nsg-on-subnet.md) | Kräver att en viss nätverkssäkerhetsgrupp används med varje virtuella undernät. Du anger ID för nätverkssäkerhetsgruppen som ska användas. |
| [Använda godkända undernät för VM-nätverksgränssnitt](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt undernät. Du anger ID för det godkända undernätet. |
| [Använda godkända vNet för VM-nätverksgränssnitt](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Kräver att nätverksgränssnitt använder ett godkänt virtuellt nätverk. Du anger ID för det godkända virtuella nätverket. |
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
ms.openlocfilehash: eadff6f61bd9e5979ae835230de53562ff8542d1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004007"
---
### <a name="virtual-machines"></a>Virtuella datorer

|  |  |
|---------|---------|
| [Tillåt anpassad VM-avbildning från en resursgrupp](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Kräver att anpassade avbildningar kommer från en godkänd resursgrupp. Du anger namnet på den godkända resursgruppen. |
| [Tillåtna SKU:er för lagringskonton och virtuella datorer](../articles/governance/policy/samples/allowed-skus-storage.md) | Kräver att lagringskonton och virtuella datorer använder godkända SKU:er. Använder inbyggda principer till att säkerställa godkända SKU:er. Du anger en matris med godkända SKU:er för virtuella datorer och en matris med godkända SKU:er för lagringskonton. |
| [Godkända VM-avbildningar](../articles/governance/policy/samples/allowed-custom-images.md) | Kräver att endast godkända anpassade avbildningar distribueras i din miljö. Du anger en matris med godkända avbildnings-ID. |
| [Granska om tillägg inte finns](../articles/governance/policy/samples/audit-ext-not-exist.md) | Granskar om ett tillägg inte distribueras med en virtuell dator. Du anger tilläggsutgivaren och typ för att kontrollera om den har distribuerats. |
| [Otillåtna VM-tillägg](../articles/governance/policy/samples/not-allowed-vm-ext.md) | Förbjuder användning av angivna tillägg. Du anger en matris som innehåller de otillåtna tilläggstyperna. |
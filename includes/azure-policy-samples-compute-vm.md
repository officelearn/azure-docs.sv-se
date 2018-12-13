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
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318290"
---
### <a name="virtual-machines"></a>Virtuella datorer

|  |  |
|---------|---------|
| [Tillåt anpassad VM-avbildning från en resursgrupp](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Kräver att anpassade avbildningar kommer från en godkänd resursgrupp. Du anger namnet på den godkända resursgruppen. |
| [Tillåtna SKU:er för lagringskonton och virtuella datorer](../articles/governance/policy/samples/allowed-skus-storage.md) | Kräver att lagringskonton och virtuella datorer använder godkända SKU:er. Använder inbyggda principer till att säkerställa godkända SKU:er. Du anger en matris med godkända SKU:er för virtuella datorer och en matris med godkända SKU:er för lagringskonton. |
| [Godkända VM-avbildningar](../articles/governance/policy/samples/allowed-custom-images.md) | Kräver att endast godkända anpassade avbildningar distribueras i din miljö. Du anger en matris med godkända avbildnings-ID. |
| [Granska om tillägg inte finns](../articles/governance/policy/samples/audit-extension-not-exist.md) | Granskar om ett tillägg inte distribueras med en virtuell dator. Du anger tilläggsutgivaren och typ för att kontrollera om den har distribuerats. |
| [Otillåtna VM-tillägg](../articles/governance/policy/samples/not-allowed-vm-extension.md) | Förbjuder användning av angivna tillägg. Du anger en matris som innehåller de otillåtna tilläggstyperna. |
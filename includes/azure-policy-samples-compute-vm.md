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
ms.openlocfilehash: eedce1ef3a4e7d56cfaf3142a72e370c96f9f575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664650"
---
### <a name="virtual-machines"></a>Virtuella datorer

|  |  |
|---------|---------|
| [Tillåt anpassad VM-avbildning från en resursgrupp](../articles/azure-policy/scripts/allow-custom-vm-image.md) |  Kräver att anpassade avbildningar kommer från en godkänd resursgrupp. Du anger namnet på den godkända resursgruppen. |
| [Tillåtna SKU:er för lagringskonton och virtuella datorer](../articles/azure-policy/scripts/allowed-skus-storage.md) | Kräver att lagringskonton och virtuella datorer använder godkända SKU:er. Använder inbyggda principer till att säkerställa godkända SKU:er. Du anger en matris med godkända SKU:er för virtuella datorer och en matris med godkända SKU:er för lagringskonton. |
| [Godkända VM-avbildningar](../articles/azure-policy/scripts/allowed-custom-images.md) | Kräver att endast godkända anpassade avbildningar distribueras i din miljö. Du anger en matris med godkända avbildnings-ID. |
| [Granska om tillägg inte finns](../articles/azure-policy/scripts/audit-ext-not-exist.md) | Granskar om ett tillägg inte distribueras med en virtuell dator. Du anger tilläggsutgivaren och typ för att kontrollera om den har distribuerats. |
| [Otillåtna VM-tillägg](../articles/azure-policy/scripts/not-allowed-vm-ext.md) | Förbjuder användning av angivna tillägg. Du anger en matris som innehåller de otillåtna tilläggstyperna. |
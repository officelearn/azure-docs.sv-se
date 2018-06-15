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
ms.openlocfilehash: 3009948c72b0cad6ba603a3ccbe35db8d93fe2c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664733"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [Tillåtna SKU:er för lagringskonton och virtuella datorer](../articles/azure-policy/scripts/allowed-skus-storage.md) | Kräver att lagringskonton och virtuella datorer använder godkända SKU:er. Använder inbyggda principer till att säkerställa godkända SKU:er. Du anger en matris med godkända SKU:er för virtuella datorer och en matris med godkända SKU:er för lagringskonton. |
| [Tillåtna SKU:er för lagringskonto](../articles/azure-policy/scripts/allowed-stor-acct-skus.md) | Kräver att lagringskonton använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Neka lågfrekvent åtkomstnivå för lagringskonton](../articles/azure-policy/scripts/deny-cool-access-tiering.md) | Förbjuder användning av lågfrekvent åtkomstnivå för Blob Storage-konton.  |
| [Garantera endast https-trafik för lagringskonto](../articles/azure-policy/scripts/ensure-https-stor-acct.md) | Kräver att lagringskonton använder HTTPS-trafik.  |
| [Garantera kryptering för lagringsfil](../articles/azure-policy/scripts/ensure-store-file-enc.md) | Kräver att filkryptering är aktiverat för lagringskonton.  |
| [Kräv kryptering för lagringskonto](../articles/azure-policy/scripts/req-store-acct-enc.md) | Kräver att lagringskontot använder blobkryptering.  |
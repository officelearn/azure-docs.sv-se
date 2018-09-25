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
ms.openlocfilehash: b967558828c42331b3702fe90ce842fd1c0032bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003957"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [Tillåtna SKU:er för lagringskonton och virtuella datorer](../articles/governance/policy/samples/allowed-skus-storage.md) | Kräver att lagringskonton och virtuella datorer använder godkända SKU:er. Använder inbyggda principer till att säkerställa godkända SKU:er. Du anger en matris med godkända SKU:er för virtuella datorer och en matris med godkända SKU:er för lagringskonton. |
| [Tillåtna SKU:er för lagringskonto](../articles/governance/policy/samples/allowed-stor-acct-skus.md) | Kräver att lagringskonton använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Neka lågfrekvent åtkomstnivå för lagringskonton](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Förbjuder användning av lågfrekvent åtkomstnivå för Blob Storage-konton.  |
| [Garantera endast https-trafik för lagringskonto](../articles/governance/policy/samples/ensure-https-stor-acct.md) | Kräver att lagringskonton använder HTTPS-trafik.  |
| [Garantera kryptering för lagringsfil](../articles/governance/policy/samples/ensure-store-file-enc.md) | Kräver att filkryptering är aktiverat för lagringskonton.  |
| [Kräv kryptering för lagringskonto](../articles/governance/policy/samples/req-store-acct-enc.md) | Kräver att lagringskontot använder blobkryptering.  |
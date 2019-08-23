---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/21/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 42e965b188db2b84579ab322fbe19781000dff7e
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894077"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [Tillåtna SKU:er för lagringskonton och virtuella datorer](../articles/governance/policy/samples/allowed-skus-storage.md) | Kräver att lagringskonton och virtuella datorer använder godkända SKU:er. Använder inbyggda principer till att säkerställa godkända SKU:er. Du anger en matris med godkända SKU:er för virtuella datorer och en matris med godkända SKU:er för lagringskonton. |
| [Tillåtna SKU:er för lagringskonto](../articles/governance/policy/samples/allowed-storage-account-skus.md) | Kräver att lagringskonton använder en godkänd SKU. Du anger en matris med godkända SKU:er. |
| [Neka lågfrekvent åtkomstnivå för lagringskonton](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Förbjuder användning av lågfrekvent åtkomstnivå för Blob Storage-konton.  |
| [Garantera endast https-trafik för lagringskonto](../articles/governance/policy/samples/ensure-https-storage-account.md) | Kräver att lagringskonton använder HTTPS-trafik.  |
| [Garantera kryptering för lagringsfil](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | Kräver att filkryptering är aktiverat för lagringskonton.  |
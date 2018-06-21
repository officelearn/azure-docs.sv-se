---
title: Azure API management princip exempel - generera en signatur för delad åtkomst | Microsoft Docs
description: Azure API management princip exemplet - visas hur du generera en signatur för delad åtkomst med uttryck och vidarebefordrar begäran till Azure-lagring med omarbetning-uri-principer...
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: c8a4d25211a0030c013628e69865406bb6e8899e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286296"
---
# <a name="generate-shared-access-signature"></a>Generera en signatur för delad åtkomst

Den här artikeln visar ett Azure API management princip exempel som visar hur du skapar [signatur för delad åtkomst](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) med uttryck och vidarebefordrar begäran till Azure-lagring med omarbetning-uri-principer. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


---
title: "Azure API management princip exempel - generera en signatur för delad åtkomst | Microsoft Docs"
description: "Azure API management princip exemplet - visas hur du generera en signatur för delad åtkomst med uttryck och vidarebefordrar begäran till Azure-lagring med omarbetning-uri-principer..."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e1f17f9f4e17a3eebb55e4ec1905aec19a2165a5
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="generate-shared-access-signature"></a>Generera en signatur för delad åtkomst

Den här artikeln visar ett Azure API management princip exempel som visar hur du skapar [signatur för delad åtkomst](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1) med uttryck och vidarebefordrar begäran till Azure-lagring med omarbetning-uri-principer. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


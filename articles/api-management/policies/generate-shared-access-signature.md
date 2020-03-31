---
title: Exempel på API-hanteringsprincip – generera signatur för delad åtkomst
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du genererar signature för delad åtkomst med uttryck och vidarebefordrar begäran till Azure-lagring med omskrivning-uri-princip..
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 0f003bc268af6b7f8bd6b046ae84734dbefeac28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442464"
---
# <a name="generate-shared-access-signature"></a>Generera signatur för delad åtkomst

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du genererar [signaturer](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) för delad åtkomst med uttryck och vidarebefordrar begäran till Azure-lagring med omskrivning-uri-principen. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


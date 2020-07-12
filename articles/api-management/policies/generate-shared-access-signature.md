---
title: Exempel på API Management-princip – generera signatur för delad åtkomst
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy – visar hur du genererar signaturen för delad åtkomst med hjälp av uttryck och vidarebefordrar begäran till Azure Storage med Rewrite-URI-princip.
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
ms.openlocfilehash: 62a6e8c9fdec3b30bd193e9887d7e0cb7926e73e
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243009"
---
# <a name="generate-shared-access-signature"></a>Generera signatur för delad åtkomst

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du genererar [signaturen för delad åtkomst](../../storage/common/storage-sas-overview.md) med hjälp av uttryck och vidarebefordrar begäran till Azure Storage med en Rewrite-URI-princip. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Transformeringsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)

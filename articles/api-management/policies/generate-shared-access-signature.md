---
title: Azure API Management policy-exempel – generera signatur för delad åtkomst | Microsoft Docs
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
ms.openlocfilehash: 45e29673e264f64c976cce664eaf749636400c7f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067738"
---
# <a name="generate-shared-access-signature"></a>Generera signatur för delad åtkomst

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du genererar signaturen för [delad åtkomst](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) med hjälp av uttryck och vidarebefordrar begäran till Azure Storage med en Rewrite-URI-princip. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


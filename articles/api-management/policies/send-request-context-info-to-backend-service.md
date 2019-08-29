---
title: Hanterings princip exempel för Azure API – skicka förfrågnings Sammanhangs information till Server dels tjänsten | Microsoft Docs
description: Exempel på Azure API Management policy-visar hur du skickar kontext information för begäran till backend-tjänsten.
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
ms.openlocfilehash: cc770ff700155f8ab32bbbd6737c9dad7bc6e664
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067509"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Skicka kontext information för begäran till backend-tjänsten

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du skickar kontext information för begäran till backend-tjänsten. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


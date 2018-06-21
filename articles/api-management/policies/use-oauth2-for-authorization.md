---
title: Azure API management princip exempel - Använd OAuth2 för auktorisering mellan gateway och en serverdel | Microsoft Docs
description: Azure API management princip exempel - visar hur du använder OAuth2 för auktorisering mellan gateway och en serverdel. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen.
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
ms.openlocfilehash: d064e918d514b9be1b9fa2dbf30c10edf5167908
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287828"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Använda OAuth2 för auktorisering mellan gateway och en serverdel

Den här artikeln visar ett Azure API management princip exempel som visar hur du använder OAuth2 för auktorisering mellan gateway och en serverdel. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen. 

Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

Följande skript använder egenskaper som visas i {{egenskapen}}. Mer information om egenskaper och hur de används i API Management-principer, se [detta](../api-management-howto-properties.md) avsnittet.
 
## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


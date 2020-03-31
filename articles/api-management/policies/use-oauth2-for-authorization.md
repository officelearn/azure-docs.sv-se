---
title: Exempel på Azure API-hanteringsprincip - Använd OAuth2 för auktorisering mellan gateway och servering
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du använder OAuth2 för auktorisering mellan gatewayen och en serverning. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen.
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
ms.openlocfilehash: 09d51759c07e7dacc25d5b5ffce9698831c37a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442358"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Använd OAuth2 för auktorisering mellan gatewayen och en bakåtsträvande

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du använder OAuth2 för auktorisering mellan gatewayen och en serverd. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen. 

Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

I följande skript används egenskaper som visas i {{egenskap}}. Mer information om egenskaper och hur du använder dem i API Management-principer finns i [det här](../api-management-howto-properties.md) avsnittet.
 
## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


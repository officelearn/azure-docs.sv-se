---
title: Azure API management princip exempel – Använd OAuth2 för auktorisering mellan gatewayen och en serverdel | Microsoft Docs
description: Azure API management princip-exempel – visar hur du använder OAuth2 för auktorisering mellan gatewayen och en serverdel. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen.
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
ms.openlocfilehash: 519233cb9e77bf48f67d869a54af771c17c7827e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874582"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Använd OAuth2 för auktorisering mellan gatewayen och en serverdel

Den här artikeln visar ett urval av Azure API management-princip som visar hur du använder OAuth2 för auktorisering mellan gatewayen och en serverdel. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen. 

Om du vill ange eller redigera en princip för kod, följer du stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns i [princip exempel](../policy-samples.md).

Följande skript använder egenskaperna som visas i {{egenskapen}}. Mer information om egenskaper och hur de används i API Management-principer, se [detta](../api-management-howto-properties.md) avsnittet.
 
## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Nästa steg

Mer information om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


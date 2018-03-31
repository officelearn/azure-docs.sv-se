---
title: Azure API management princip exempel - Använd OAuth2 för auktorisering mellan gateway och en serverdel | Microsoft Docs
description: Azure API management princip exempel - visar hur du använder OAuth2 för auktorisering mellan gateway och en serverdel. Den visar hur du hämtar en åtkomst-token från AAD och vidarebefordra dem till serverdelen.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: fc896656a4725475fc78cadb5bab54a27cfc02a2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Använda OAuth2 för auktorisering mellan gateway och en serverdel

Den här artikeln visar ett Azure API management princip exempel som visar hur du använder OAuth2 för auktorisering mellan gateway och en serverdel. Den visar hur du hämtar en åtkomst-token från AAD och vidarebefordra dem till serverdelen. 

Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

Följande skript använder egenskaper som visas i {{egenskapen}}. Mer information om egenskaper och hur de används i API Management-principer, se [detta](../api-management-howto-properties.md) avsnittet.
 
## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


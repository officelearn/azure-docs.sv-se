---
title: "Azure API management princip exempel - Använd OAuth2 för auktorisering mellan gateway och en serverdel | Microsoft Docs"
description: "Azure API management princip exempel - visar hur du använder OAuth2 för auktorisering mellan gateway och en serverdel. Den visar hur du hämtar en åtkomst-token från AAD och vidarebefordra dem till serverdelen."
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
ms.openlocfilehash: e0aeec66f23033f916c782c8a895e725b0735b62
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Använda OAuth2 för auktorisering mellan gateway och en serverdel

Den här artikeln visar ett Azure API management princip exempel som visar hur du använder OAuth2 för auktorisering mellan gateway och en serverdel. Den visar hur du hämtar en åtkomst-token från AAD och vidarebefordra dem till serverdelen. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


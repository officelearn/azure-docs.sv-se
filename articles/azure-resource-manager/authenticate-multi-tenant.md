---
title: Autentisera över klienter – Azure Resource Manager
description: Beskriver hur Azure Resource Manager hanterar begäranden om autentisering över klienter.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 6554c05f40f580a6d7ae086e1d09834298f86621
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550775"
---
# <a name="authenticate-requests-across-tenants"></a>Autentisera begäranden över klienter

När du skapar ett program med flera innehavare, kan du behöva hantera autentiseringsbegäranden för resurser som finns i olika klienter. Ett vanligt scenario är när en virtuell dator i en klient måste ansluta ett virtuellt nätverk i en annan klient. Azure Resource Manager tillhandahåller ett huvudvärde för att lagra extra token för att autentisera begäranden till olika klienter.

## <a name="header-values-for-authentication"></a>Huvudvärden för autentisering

Begäran har följande värden i huvudet autentisering:

| Huvudnamn | Beskrivning | Exempelvärde |
| ----------- | ----------- | ------------ |
| Auktorisering | Primära token | Ägar &lt;primära-token&gt; |
| x-ms-authorization-auxiliary | Extra token | Ägar &lt;assistent token1&gt;; Detta EncryptedBearer &lt;assistent token2&gt;; Ägar &lt;assistent token3&gt; |

Extra huvudet kan innehålla upp till tre extra token. 

Hämta autentiseringen åtkomsttoken för andra klienter i koden för din app för flera klienter, och lagra dem i de extra rubrikerna. Alla token måste komma från samma användare eller program. Användarens eller programmets måste har bjudits in som en gäst till att andra klienter.

## <a name="processing-the-request"></a>Bearbetning av begäran

När din app skickar en begäran till Resource Manager, kör begäran under identiteten från det primära token. Det primära token måste vara giltig och läggs. Den här token måste vara från en klient som kan hantera prenumerationen.

När begäran refererar till en resurs från en annan klient, kontrollerar Resource Manager de extra token för att avgöra om begäran kan bearbetas. Alla extra token i rubriken måste vara giltig och läggs. Om alla token har upphört att gälla, returnerar Resource Manager 401 svarskod. Svaret innehåller klient-ID och klient-ID från den token som är inte giltig. Om det extra huvudet innehåller en giltig token för klienten, bearbetas mellan klientbegäran.

## <a name="next-steps"></a>Nästa steg
* Läs om hur du skickar begäranden om autentisering med Azure Resource Manager API: er i [autentisering använda Resource Manager API att få åtkomst till prenumerationer](resource-manager-api-authentication.md).
* Mer information om token finns i [åtkomsttoken för Azure Active Directory](/azure/active-directory/develop/access-tokens).

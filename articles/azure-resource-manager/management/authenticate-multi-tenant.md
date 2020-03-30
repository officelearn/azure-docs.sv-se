---
title: Autentisera mellan klienter
description: Beskriver hur Azure Resource Manager hanterar autentiseringsbegäranden mellan klienter.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478830"
---
# <a name="authenticate-requests-across-tenants"></a>Autentisera begäranden mellan klienter

När du skapar ett program med flera innehavare kan du behöva hantera autentiseringsbegäranden för resurser som finns i olika klienter. Ett vanligt scenario är när en virtuell dator i en klient måste ansluta till ett virtuellt nätverk i en annan klientorganisation. Azure Resource Manager tillhandahåller ett huvudvärde för lagring av hjälptokenetter för att autentisera begäranden till olika klienter.

## <a name="header-values-for-authentication"></a>Rubrikvärden för autentisering

Begäran har följande autentiseringshuvudvärden:

| Huvudnamn | Beskrivning | Exempelvärde |
| ----------- | ----------- | ------------ |
| Auktorisering | Primär token | Bärare &lt;primär-token&gt; |
| x-ms-tillstånd-hjälpare | Hjälptokens | Bärare &lt;hjälp-token1&gt;, EncryptedBearer &lt;extra-token2&gt; &lt;, Bärare extra-token3&gt; |

Hjälphuvudet rymmer upp till tre hjälptoken. 

I koden för din app med flera innehavare hämtar du autentiseringstoken för andra klienter och lagrar dem i hjälphuvudena. Alla token måste komma från samma användare eller program. Användaren eller programmet måste ha bjudits in som gäst till de andra klienterna.

## <a name="processing-the-request"></a>Behandling av begäran

När appen skickar en begäran till Resource Manager körs begäran under identiteten från den primära token. Den primära token måste vara giltig och oexpirerad. Den här token måste komma från en klient som kan hantera prenumerationen.

När begäran refererar till en resurs från en annan klient kontrollerar Resource Manager hjälptoken för att avgöra om begäran kan bearbetas. Alla hjälptoken i huvudet måste vara giltiga och oexpirerade. Om någon token har upphört att gälla returnerar Resource Manager en 401-svarskod. Svaret innehåller klient-ID och klient-ID från token som inte är giltig. Om hjälphuvudet innehåller en giltig token för klienten bearbetas begäran om korsklienter.

## <a name="next-steps"></a>Nästa steg

* Mer information om autentiseringsbegäranden finns [i Autentiseringsflöden och programscenarier](../../active-directory/develop/authentication-flows-app-scenarios.md).
* Mer information om token finns i [Azure Active Directory access tokens](../../active-directory/develop/access-tokens.md).

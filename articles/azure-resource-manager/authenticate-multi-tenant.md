---
title: Autentisera mellan klienter – Azure Resource Manager
description: Beskriver hur Azure Resource Manager hanterar autentiseringsbegäranden över klienter.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 625a17156eaf199af0d51151c6fd37769b8f7b4a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848758"
---
# <a name="authenticate-requests-across-tenants"></a>Autentisera begär anden över klienter

När du skapar ett program med flera klienter kan du behöva hantera autentiseringsbegäranden för resurser som finns i olika klienter. Ett vanligt scenario är när en virtuell dator i en klient måste anslutas till ett virtuellt nätverk i en annan klient. Azure Resource Manager innehåller ett huvud värde för lagring av hjälp-token för att autentisera begär anden till olika klienter.

## <a name="header-values-for-authentication"></a>Huvud värden för autentisering

Begäran har följande värden för Authentication-huvud:

| Huvudnamn | Beskrivning | Exempelvärde |
| ----------- | ----------- | ------------ |
| Authorization | Primär token | &lt;Primär token för Bearer&gt; |
| x-ms-authorization-auxiliary | Extra token | &gt; &lt;&gt;Bearer-token1, EncryptedBearer-tillägg – token2, Bearer &lt;-token3 &lt;&gt; |

Tilläggs huvudet kan innehålla upp till tre hjälp-token. 

I koden för din app för flera klienter hämtar du autentiseringstoken för andra klienter och lagrar dem i hjälp rubrikerna. Alla tokens måste vara från samma användare eller program. Användaren eller programmet måste ha bjudits in som gäst till andra klienter.

## <a name="processing-the-request"></a>Bearbetar begäran

När din app skickar en begäran till Resource Manager, körs begäran under identiteten från den primära-token. Den primära token måste vara giltig och ha upphört att gälla. Denna token måste vara från en klient som kan hantera prenumerationen.

När begäran refererar till en resurs från en annan klient kontrollerar Resource Manager de extra token för att avgöra om begäran kan bearbetas. Alla extra token i huvudet måste vara giltiga och har inte gått ut. Om någon token har upphört att gälla returnerar Resource Manager en 401-svarskod. Svaret innehåller klient-ID och klient-ID från den token som inte är giltig. Om tilläggs huvudet innehåller en giltig token för klienten, bearbetas begäran om flera innehavare.

## <a name="next-steps"></a>Nästa steg
* Information om hur du skickar autentiseringsbegäranden med Azure Resource Manager-API: er finns i [använda Resource Manager Authentication API för att få åtkomst till prenumerationer](resource-manager-api-authentication.md).
* Mer information om tokens finns [Azure Active Directory åtkomsttoken](/azure/active-directory/develop/access-tokens).

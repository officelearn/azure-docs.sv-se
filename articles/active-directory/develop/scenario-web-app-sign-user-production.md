---
title: Flytta webbapp som loggar in användare till produktion - Microsoft identity platform | Azure
description: Läs om hur du skapar en webbapp som loggar in användare (flytta till produktion)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9c5fd444c55a20441325088912a07eb051219b84
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881476"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Webbapp som signerar användare: Flytta till produktion

Nu när du vet hur du får en token för att anropa webb-API: er, lära dig hur du flyttar den till produktion.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

### <a name="same-site"></a>Samma plats

Se till att du förstår möjliga problem med nya versioner av webbläsaren Chrome

> [!div class="nextstepaction"]
> [Så här hanterar du ändringar av SameSite-cookies i webbläsaren Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Scenario för att anropa webb-API:er

När webbappen har loggat ut i användare kan den anropa webb-API:er för de inloggade användarnas räkning. Att anropa webb-API:er från webbappen är föremål för följande scenario:

> [!div class="nextstepaction"]
> [Webbapp som anropar webb-API:er](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Djupdykning: ASP.NET Core webbapp handledning

Läs mer om andra sätt att logga in användare med den här ASP.NET Core-självstudien: 

> [!div class="nextstepaction"]
> [Aktivera dina webbappar för att logga in användare och anropa API:er med Microsofts identitetsplattform för utvecklare](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Den här progressiva självstudien har produktionsklar kod för en webbapp, inklusive hur du lägger till inloggning med konton i:

- Din organisation
- Flera organisationer
- Arbets- eller skolkonton eller personliga Microsoft-konton
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nationella moln

## <a name="sample-code-java-web-app"></a>Exempelkod: Java-webbapp

Läs mer om Java-webbappen från det här exemplet på GitHub: 

> [!div class="nextstepaction"]
> [Ett Java-webbprogram som loggar in användare med Microsofts identitetsplattform och anropar Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

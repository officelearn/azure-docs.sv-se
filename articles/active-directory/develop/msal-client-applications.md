---
title: Klientprogram (Microsoft Authentication Library) | Azure
description: Läs mer om offentlig klient och konfidentiell klient program i Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430823"
---
# <a name="public-client-and-confidential-client-applications"></a>Offentlig klient och konfidentiella klientprogram
Microsoft Authentication Library (MSAL) definierar två typer av klienter: offentliga klienter och konfidentiella klienter. Två klienttyper särskiljs från varandra med sina möjlighet att autentisera på ett säkert sätt med auktoriseringsservern och upprätthålla sekretessen för sina klientautentiseringsuppgifter. Däremot Azure AD-Autentiseringsbiblioteket (ADAL) använder något som kallas *autentiseringskontext* (vilket är en anslutning till Azure AD).

- **Konfidentiellt klientprogram** är appar som körs på servrar (web apps, webb-API-appar, eller även service/daemon-appar). De betraktas svårt att åtkomst och därför kan hålla en programhemlighet. Konfidentiella klienter kan innehålla configuration-time-hemligheter. Varje instans av klienten har en distinkt konfiguration (inklusive klient-ID och klienthemlighet). Dessa värden är svårt för användarna att extrahera. En webbapp är den vanligaste konfidentiell klienten. Klient-ID är tillgängliga via webbläsaren, men hemligheten skickas endast i en kanal för säkerhetskopiering och exponeras aldrig direkt.

    Konfidentiell klient apps: <BR>
    ![Webbapp](media/msal-client-applications/web-app.png) ![webb-API](media/msal-client-applications/web-api.png) ![Daemon/tjänst](media/msal-client-applications/daemon-service.png)

- **Offentliga klientprogram** är appar som körs på enheter eller stationära datorer eller i en webbläsare. De är inte betrodd för att hålla programhemligheter, på ett säkert sätt så att de bara åtkomst webb-API: er för användarens räkning. (De stöder endast offentlig klient flöden.) Offentliga klienter kan inte innehålla configuration-time-hemligheter, så att de inte behöver klienten hemligheter.

    Offentlig klient apps: <BR>
    ![Skrivbordsappen](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![mobilapp](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> I MSAL.js finns det inga uppdelning av offentliga och konfidentiella klientappar.  MSAL.js representerar klientappar som användaren agent-baserade appar, offentliga klienter där klientkoden körs i en användaragent som en webbläsare. Dessa klienter lagra inte hemligheter eftersom webbläsaren kontexten är öppet tillgänglig.

## <a name="comparing-the-client-types"></a>Jämföra klienttyper
Här följer några likheterna och skillnaderna mellan offentlig klient och konfidentiell klient apps:

- Båda typerna av app upprätthåller en token användarcachen och hämta en token tyst (när detta token används redan i token-cache). Konfidentiell klient apps har också en app-tokencache för token som är för själva appen.
- Båda typerna av app kan hantera användarkonton och hämta ett konto från token användarcachen, skaffa ett konto från dess identifierare, eller ta bort ett konto.
- Offentlig klient apps har fyra sätt att hämta en token (fyra autentiseringsflöden). Konfidentiell klient apps har tre sätt att hämta en token (och ett sätt att beräkna URL för identitetsprovidern tillåta slutpunkt). Mer information finns i [hämta token](msal-acquire-cache-tokens.md).

Om du har använt ADAL, märker du, till skillnad från ADALS autentiseringskontext i MSAL klient-ID (även kallat den *program-ID* eller *app-ID*) skickas en gång vid konstruktion av programmet. Det behöver inte skickas igen när appen hämtar en token. Det här gäller för både offentliga och konfidentiella klientappar. Konstruktorerna i konfidentiell klient apps skickas också klientautentiseringsuppgifter: hemligheten som de delar med identitetsprovidern.

## <a name="next-steps"></a>Nästa steg
Läs mer om:
- [Klienten programmets konfigurationsalternativ](msal-client-application-configuration.md)
- [Instansiera klientprogram med hjälp av MSAL.NET](msal-net-initializing-client-applications.md)
- [Instansiera klientprogram med hjälp av MSAL.js](msal-js-initializing-client-applications.md)

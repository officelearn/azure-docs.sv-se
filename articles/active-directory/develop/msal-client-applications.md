---
title: Klientprogram (Microsoft Authentication Library) | Azure
description: Läs mer om offentlig klient och konfidentiell klient program i Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077147"
---
# <a name="public-client-and-confidential-client-applications"></a>Offentlig klient och konfidentiella klientprogram
Microsoft Authentication Library (MSAL) definierar två typer av klienter: offentliga klienter och konfidentiella klienter. Två klienttyper särskiljs från varandra med sina möjlighet att autentisera på ett säkert sätt med auktoriseringsservern och upprätthålla sekretessen för sina klientautentiseringsuppgifter.  Azure AD Authentication Library (ADAL) har däremot begreppet autentiseringskontext (vilket är en anslutning till Azure AD).

- **Konfidentiellt klientprogram** är program som körs på servrar (Web Apps, webb-API eller även service/daemon program). De anses vara svårt att åtkomst och kan därför att hålla en programhemlighet. Konfidentiella klienter kan lagra konfiguration tid hemligheter. Varje instans av klienten har en distinkt konfiguration (inklusive clientId och hemlighet). Dessa värden är svårt för användarna att extrahera. En webbapp är den vanligaste konfidentiell klienten. Klient-ID är tillgängliga via webbläsaren, men hemligheten skickas endast i en kanal för säkerhetskopiering och exponeras aldrig direkt.

    Konfidentiell klient apps: <BR>
    ![Webbapp](media/msal-client-applications/web-app.png) ![webb-API](media/msal-client-applications/web-api.png) ![Daemon/tjänst](media/msal-client-applications/daemon-service.png)

- **Offentliga klientprogram** är program som körs på enheter eller stationära datorer eller i en webbläsare. De är inte betrodd för att på ett säkert sätt hålla programhemligheter och därför bara åtkomst till webb-API: er åt användaren (de endast stöd för offentlig klient flöden). Offentliga klienter kan inte att hålla configuration tid hemligheter, och därför har inga klienthemlighet.

    Offentliga klientprogram: <BR>
    ![Skrivbordsappen](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![mobilapp](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> I MSAL.js finns det inga uppdelning av offentliga och konfidentiella klientappar.  MSAL.js representerar klientappar som användar-agent-baserade appar, en offentlig klient där klientkoden körs i en användaragent till exempel en webbläsare.  Dessa klienter lagrar inte hemligheter, eftersom webbläsaren kontexten är öppet tillgängliga.

## <a name="comparing-the-client-types"></a>Jämföra klienttyper
Det finns vissa gemensamma utformning gör och skillnaderna mellan offentlig klient och konfidentiell klient program:

- Båda typerna av program upprätthåller en token användarcachen och hämta en token tyst (i fall där detta token används redan i token-cache). Konfidentiellt klientprogram innehåller också en app-tokencache för token som är för själva appen.
- Både kan hantera användarkonton och hämta konton från token användarcachen, hämta ett konto från dess identifierare eller ta bort ett konto.
- Offentliga klientprogram innehåller fyra sätt för att skaffa en token (fyra autentiseringsflöden), medan konfidentiell klientprogram innehåller tre (och en metod för att beräkna URL för identitetsprovidern tillåta slutpunkt). Mer information finns i scenarier och hämta token.

Om du använde ADAL tidigare kan kanske du märker att sätt som strider mot ADALS autentisering kontexten i MSAL klient-ID (som även kallas program-ID eller app-ID) skickas en gång vid konstruktion av programmet, och inte längre måste du upprepa när du hämtar en token. Detta gäller både för offentliga och konfidentiella klientprogram. Konstruktorerna i konfidentiell klientprogram skickas också klientautentiseringsuppgifter: hemligheten som de delar med identitetsprovidern.

## <a name="next-steps"></a>Nästa steg
Läs mer om:
- [Klienten programmets konfigurationsalternativ](msal-client-application-configuration.md)
- [Instansiera klientprogram med hjälp av MSAL.NET](msal-net-initializing-client-applications.md).
- [Instansiera klientprogram som använder MSAL.js](msal-js-initializing-client-applications.md).

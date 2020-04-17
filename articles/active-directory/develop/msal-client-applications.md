---
title: Offentliga och konfidentiella klientappar (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om offentliga klient- och konfidentiella klientprogram i Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9c3292a31e5f750c16933acf94509e0ad226080a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534320"
---
# <a name="public-client-and-confidential-client-applications"></a>Offentliga klient- och konfidentiella klientprogram
Microsoft Authentication Library (MSAL) definierar två typer av klienter: offentliga klienter och konfidentiella klienter. De två klienttyperna kännetecknas av sin förmåga att autentisera säkert med auktoriseringsservern och upprätthålla sekretessen för sina klientuppgifter. Azure AD Authentication Library (ADAL) använder däremot den som kallas *autentiseringskontext* (som är en anslutning till Azure AD).

- **Konfidentiella klientprogram** är appar som körs på servrar (webbappar, webb-API-appar eller till och med tjänst-/daemonappar). De anses svåra att komma åt, och av den anledningen kan hålla ett program hemligt. Konfidentiella klienter kan hålla konfigurationstidshemligheter. Varje instans av klienten har en distinkt konfiguration (inklusive klient-ID och klienthemlighet). Dessa värden är svåra för slutanvändare att extrahera. En webbapp är den vanligaste konfidentiella klienten. Klient-ID:n exponeras via webbläsaren, men hemligheten skickas bara i den bakre kanalen och exponeras aldrig direkt.

    Konfidentiella klientappar: <BR>
    ![Webbapp](media/msal-client-applications/web-app.png) ![Webb-API](media/msal-client-applications/web-api.png) ![Daemon/tjänst](media/msal-client-applications/daemon-service.png)

- **Offentliga klientprogram** är appar som körs på enheter eller stationära datorer eller i en webbläsare. De är inte betrodda att säkert hålla programhemligheter, så de bara tillgång till webb-API: er på uppdrag av användaren. (De stöder endast offentliga kundflöden.) Offentliga klienter kan inte hålla konfigurationstidshemligheter, så de har inte klienthemligheter.

    Offentliga klientappar: <BR>
    ![](media/msal-client-applications/desktop-app.png) ![Webbläsarappen Browserless API](media/msal-client-applications/browserless-app.png) ![Mobilapp](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> I MSAL.js finns det ingen separation av offentliga och konfidentiella klientappar.  MSAL.js representerar klientappar som användaragentbaserade appar, offentliga klienter där klientkoden körs i en användaragent som en webbläsare. Dessa klienter lagrar inte hemligheter eftersom webbläsarkontexten är öppet tillgänglig.

## <a name="comparing-the-client-types"></a>Jämföra klienttyper
Här är några likheter och skillnader mellan offentliga klient- och konfidentiella klientappar:

- Båda typerna av app upprätthålla en användare token cache och kan förvärva en token tyst (när token är redan i token cache). Konfidentiella klientappar har också en apptokencache för token som är för själva appen.
- Båda typerna av app hantera användarkonton och kan hämta ett konto från användartokencache, hämta ett konto från dess identifierare eller ta bort ett konto.
- Offentliga klientappar har fyra sätt att hämta en token (fyra autentiseringsflöden). Konfidentiella klientappar har tre sätt att skaffa en token (och ett sätt att beräkna URL:en för identitetsprovidern auktorisera slutpunkten). Mer information finns i [Hämta token](msal-acquire-cache-tokens.md).

Om du har använt ADAL kanske du märker att klient-ID (även kallat *program-ID* eller *app-ID)* skickas en gång vid konstruktionen av programmet, till skillnad från ADAL:s autentiseringskontext. Det behöver inte skickas igen när appen skaffar en token. Detta gäller både för offentliga och konfidentiella klientappar. Konstruktorer av konfidentiella klientappar skickas också klientautentiseringsuppgifter: hemligheten de delar med identitetsleverantören.

## <a name="next-steps"></a>Nästa steg
Läs om:
- [Konfigurationsalternativ för klientprogram](msal-client-application-configuration.md)
- [Instansiera klientprogram med hjälp av MSAL.NET](msal-net-initializing-client-applications.md)
- [Instansiera klientprogram med MSAL.js](msal-js-initializing-client-applications.md)

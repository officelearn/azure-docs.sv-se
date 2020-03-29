---
title: 'Skapa ett webb-API som anropar webb-API: er - Microsoft identity platform | Azure'
description: Lär dig hur du skapar ett webb-API som anropar underordnade webb-API:er (översikt).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701747"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenario: Ett webb-API som anropar webb-API:er

Läs om vad du behöver veta för att skapa ett webb-API som anropar webb-API:er.

## <a name="prerequisites"></a>Krav

Det här scenariot, där ett skyddat webb-API anropar webb-API: er, bygger ovanpå scenariot "Skydda ett webb-API". Mer information om det här grundläggande scenariot finns i [Scenario: Skyddat webb-API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Översikt

- En webb-, skrivbords-, mobil- eller ensidig programklient (som inte finns representerad i det medföljande diagrammet) anropar ett skyddat webb-API och tillhandahåller en JSON Web Token -bärare i http-huvudet för Auktorisering.
- Det skyddade webb-API:et validerar token och använder `AcquireTokenOnBehalfOf` MSAL-metoden (Microsoft Authentication Library) för att begära en annan token från Azure Active Directory (Azure AD) så att det skyddade webb-API:et kan anropa ett andra webb-API, eller nedströms webb-API, för användarens räkning.
- Det skyddade webb-API:et kan också anropa `AcquireTokenSilent`senare för att begära token för andra underordnade API:er för samma användare. `AcquireTokenSilent`uppdaterar token när det behövs.

![Diagram över ett webb-API som anropar ett webb-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Detaljerna

Appregistreringsdelen som är relaterad till API-behörigheter är klassisk. Appkonfigurationen innebär att använda OAuth 2.0 On-Behalf-Of-flow för att byta JWT-innehavartoken mot en token för ett nedströms-API. Den här token läggs till i tokencachen, där den är tillgänglig i webb-API:ets styrenheter, och den kan sedan hämta en token tyst för att anropa underordnade API:er.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-web-api-call-api-app-registration.md)

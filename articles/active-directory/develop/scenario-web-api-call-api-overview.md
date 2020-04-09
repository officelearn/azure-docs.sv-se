---
title: 'Skapa ett webb-API som anropar webb-API: er - Microsoft identity platform | Azure'
description: Lär dig hur du skapar ett webb-API som anropar underordnade webb-API:er (översikt).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 88a0177755fbd913bdaaf0ecf3e12c62dee294c1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885080"
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

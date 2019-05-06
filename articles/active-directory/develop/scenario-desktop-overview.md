---
title: 'Skrivbordsappen att anrop webb-API: er (översikt) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en skrivbordsapp att anrop webb-API: er (översikt)'
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44d31011ca70bbebaf994b5fb80a45eee8dbde40
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076952"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: Skrivbordsappen att anrop webb-API: er

Lär dig allt du behöver skapa en skrivbordsapp som anropar webb-API: er

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Komma igång

Om du inte redan gjort kan du skapa ditt första program genom att följa Snabbstart för .NET-skrivbord eller UWP-snabbstarten:

> [!div class="nextstepaction"]
> [Snabbstart: Hämta en token och anropa Microsoft Graph API från en Windows-skrivbordsapp](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Snabbstart: Hämta en token och anropa Microsoft Graph API från en UWP-app](./quickstart-v2-uwp.md)

## <a name="overview"></a>Översikt

Du skriver ett skrivbordsprogram och du vill logga in användare i ditt program och anropa webb-API: er som Microsoft Graph, andra Microsoft-APIs eller dina egna webb-API. Du har flera möjligheter:

- Om din skrivbordsprogram stöder grafiska kontroller, till exempel om det är en Windows.Form eller en WPF-program, kan du använda interaktiva tokenförvärv.
- Det är också möjligt för program som körs på datorer som är anslutna till en Windows-domän för värd för Windows-program, eller AAD ansluten för att hämta en token tyst genom att använda integrerad Windows-autentisering.
- Slutligen, och även om det inte rekommenderas kan du använda användarnamn/lösenord i offentliga klientprogram. Det krävs fortfarande i vissa fall (till exempel DevOps), men tänk på att använda det medför begränsningar i ditt program. Exempelvis kan logga den inte in användare som behöver utföra multifaktorautentisering (villkorlig åtkomst). Ditt program dra inte också nytta av enkel inloggning (SSO).

  Det är också mot principerna för modern autentisering och finns bara för äldre orsaker.

  ![Skrivbordsprogram](media/scenarios/desktop-app.svg)

- Om du skriver en bärbar kommandoradsverktyget - förmodligen ett .NET Core-program som körs på Linux eller Mac – du kommer inte att kunna använda varken den interaktiv autentiseringen (även .NET Core inte tillhandahåller en [webbläsare](https://aka.ms/msal-net-uses-web-browser)), eller integrerad Windows-autentisering. Det är i så fall det bästa alternativet att använda kodflöde för enheten. Det här flödet används också för program utan en webbläsare, till exempel iOT-program

  ![Browserless program](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Närmare information

Program har ett antal särdrag, vilket beror huvudsakligen på om ditt program använder interaktiv autentisering eller inte.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skrivbordsappen - appregistrering](scenario-desktop-app-registration.md)

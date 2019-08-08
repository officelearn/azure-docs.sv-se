---
title: 'Webb-API som anropar underordnade webb-API: er (översikt) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar ett webb-API som anropar underordnade webb-API: er (översikt).'
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef9fc121b16d81eed932d1ab55ca38d2a2f1057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852505"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scenario: Webb-API som anropar webb-API:er

Lär dig allt du behöver för att bygga ett webb-API som anropar webb-API: er.

## <a name="prerequisites"></a>Förutsättningar

Det här scenariot, skyddat webb-API som anropar webb-API: er, bygger ovanpå "skydda ett webb-API"-scenario. Mer information om det här grundläggande scenariot finns i [Protected Web API-scenario](scenario-protected-web-api-overview.md) först.

## <a name="overview"></a>Översikt

- En klient (webb-, skriv bords-, mobil-eller Enkels Ides program) – visas inte i diagrammet nedan – anropar ett skyddat webb-API och tillhandahåller en JWT Bearer-token i HTTP-huvudet "Authorization".
- Det skyddade webb-API: et verifierar token och `AcquireTokenOnBehalfOf` använder MSAL-metoden för att begära (från Azure AD) en annan token, så att den kan anropa en andra webb-API (med namnet "underordnat webb-API) för användarens räkning.
- Det skyddade webb-API: et använder denna token för att anropa ett underordnat API. Det kan också anropas `AcquireTokenSilent`senare för att begära token för andra underordnade API: er (men fortfarande för samma användares räkning). `AcquireTokenSilent`uppdaterar token vid behov.

![Webb-API som anropar ett webb-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Information

Den del av app-registreringen som är relaterad till API-behörigheterna är klassisk. Program konfigurationen innebär att du använder OAuth 2,0 på uppdrag av Flow för att byta JWT Bearer-token mot en token för ett underordnat API. Den här token läggs till i token cache, där den är tillgänglig i webb-API: s kontrollanter och kan hämta en token tyst för att anropa underordnade API: er.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-web-api-call-api-app-registration.md)

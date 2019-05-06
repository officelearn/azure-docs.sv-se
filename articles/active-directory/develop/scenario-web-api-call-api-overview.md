---
title: 'Webb-API som anropar underordnade webb-API: er (översikt) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar ett webb-API som anrop underordnat webb API: er (översikt).'
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
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075407"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scenario: Webb-API som anropar webb-API: er

Lär dig allt du behöver för att skapa ett webb-API som anropar webb-API: er.

## <a name="prerequisites"></a>Nödvändiga komponenter

Det här scenariot skyddat webb-API att anrop webb-API: er, bygger på scenariot ”skydda ett webb-API”. Mer information om det här grundläggande scenariot finns [skyddade webb-API - Scenario](scenario-protected-web-api-overview.md) första.

## <a name="overview"></a>Översikt

- En klient (webb, skrivbord, mobilt eller enkelsidigt program) - inte visas i diagrammet nedan – ett skyddat webb-API-anrop och tillhandahåller en JWT-ägartoken i dess ”Authorization” Http-huvud.
- Skyddade webb-API: verifierar token och använder MSAL `AcquireTokenOnBehalfOf` metod för att begära (från Azure AD) en annan token så att den kan själva, anropa en andra webb-API (med namnet underordnat webb-API) användarens räkning.
- Skyddade webb-API använder sin token för att anropa en underordnad API. Det kan också anropa `AcquireTokenSilent`senare att begära token för andra underordnade API: er (men fortfarande för samma användare). `AcquireTokenSilent` uppdaterar token när det behövs.

![Webb-API som anropar ett webb-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Närmare information

En del av appregistrering som rör API-behörigheter är klassisk. Programkonfigurationen innebär att du använder flöde för OAuth 2.0 on-behalf-of för att utbyta JWT-ägartoken mot en token för en underordnad API. Denna token har lagts till tokens cacheminne, där den är tillgänglig i webb-API: er domänkontrollanter och hämta en token tyst för att anropa API: er för underordnade.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-web-api-call-api-app-registration.md)

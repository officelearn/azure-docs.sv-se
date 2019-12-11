---
title: Registrera program med en sida – Microsoft Identity Platform | Azure
description: Lär dig hur du skapar ett program med en enda sida (app Registration)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03115db0ad286c7a5c24590906d8e3715e43bac7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962448"
---
# <a name="single-page-application-app-registration"></a>Program med en sida: app-registrering

På den här sidan förklaras registrerings information för appar för ett enda sid program (SPA).

Följ stegen för att [Registrera ett nytt program med Microsoft Identity Platform](quickstart-register-app.md)och välj de konton som stöds för programmet. SPA-scenariot har stöd för autentisering med konton i din organisation eller i alla organisationer och personliga Microsoft-konton.

Läs sedan de specifika aspekter av program registrering som gäller för program på en enda sida.

## <a name="register-a-redirect-uri"></a>Registrera en omdirigerings-URI

Det implicita flödet skickar token i en omdirigering till ett program med en enda sida som körs i en webbläsare. Det är därför viktigt att registrera en omdirigerings-URI där ditt program kan ta emot tokens. Se till att omdirigerings-URI: n matchar exakt URI för ditt program.

I [Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908)går du till ditt registrerade program. På sidan **autentisering** i programmet väljer du **webb** plattformen. Ange värdet för omdirigerings-URI för programmet i fältet **omdirigerings-URI** .

## <a name="enable-the-implicit-flow"></a>Aktivera det implicita flödet

På sidan samma **autentisering** , under **Avancerade inställningar**, måste du även aktivera **implicit beviljande**. Om ditt program bara loggar in användare och hämtar ID-token, räcker det att markera kryss rutan **ID-token** .

Om ditt program också behöver hämta åtkomsttoken för att anropa API: er, måste du även markera kryss rutan **åtkomsttoken** . Mer information finns i [ID-tokens](./id-tokens.md) och [åtkomsttoken](./access-tokens.md).

## <a name="api-permissions"></a>API-behörigheter

Program med en enda sida kan anropa API: er åt den inloggade användaren. De måste begära delegerade behörigheter. Mer information finns i [lägga till behörigheter för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-spa-app-configuration.md)

---
title: Ensidesapplikation (appregistrering) - Microsoft identity-plattformen
description: Lär dig hur du skapar ett enkelsidigt program (appregistrering)
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
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074837"
---
# <a name="single-page-application---app-registration"></a>Ensidesprogram - appregistrering

Den här sidan förklaras informationen för app-registrering för för en ensidesapplikation (SPA).

Följ stegen för att [registrera ett nytt program med Microsoft identity-plattformen](quickstart-register-app.md), och välj kontona som stöds för ditt program. SPA-scenariot har stöd för autentisering med konton i din organisation eller en organisation och personliga Microsoft-konton.

Lär dig sedan specifika aspekter av programregistrering som gäller enkelsidigt program.

## <a name="register-a-redirect-uri"></a>Registrera en omdirigerings-URI

Implicit flöde skickar token i en omdirigering till ensidesprogram som körs i en webbläsare. Därför är det ett viktigt krav att registrera en omdirigerings-URI där ditt program kan ta emot token. Kontrollera att omdirigerings-URI: N matchar exakt med URI: N för ditt program.

I den [Azure-portalen](https://go.microsoft.com/fwlink/?linkid=2083908), navigera till ditt registrerade program på den **autentisering** för programmet, Välj den **Web** plattform och ange värdet för den omdirigerings-URI för ditt program i den **omdirigerings-URI** fält.

## <a name="enable-the-implicit-flow"></a>Aktivera det implicita flödet

På samma **autentisering** sidan under **avancerade inställningar**, måste du aktivera den **Implicit beviljande**. Om ditt program fungerar endast inloggning av användare och hämta ID-token, räcker det att aktivera **ID-token** kryssrutan.

Om ditt program behöver också hämta åtkomsttoken för att anropa API: er, se till att aktivera den **åtkomsttoken** samt kryssrutan. Mer information finns i [ID-token](./id-tokens.md) och [åtkomsttoken](./access-tokens.md).

## <a name="api-permissions"></a>API-behörigheter

Enkelsidigt program kan anropa API: er åt den inloggade användaren. De måste begära delegerade behörigheter. Mer information finns i [Lägg till behörigheter för att få åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-spa-app-configuration.md)

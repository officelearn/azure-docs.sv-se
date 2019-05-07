---
title: Skyddade webb-API - appregistrering | Azure
description: Lär dig hur du skapar ett skyddat webb-API och den information du behöver registrera appen.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: a1d8c8fcf84cd008957fcdb7cd14c4a07d9f3643
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074912"
---
# <a name="protected-web-api---app-registration"></a>Skyddade webb-API - appregistrering

Den här artikeln förklarar app registrering dig ett skyddat webb-API.

Gå till [Snabbstart: Registrera ett program med Microsoft identity-plattformen](quickstart-register-app.md) vanliga anvisningar för hur du registrera programmet.

## <a name="accepted-token-version"></a>Godkänd token version

Microsoft identity-plattformen endpoint kan utfärda två typer av token: token v1.0 och v2.0-token. Du kan läsa mer om dessa token i [åtkomsttoken](access-tokens.md). Godkänd token version beror på den **stöds kontotyper** du angav när du skapade ditt program:

- Om värdet för **stöds kontotyper** är **konton alla organisationskatalog och personliga Microsoft-konton (t.ex. Skype, Xbox, Outlook.com)**, godkänd token version kommer att v2.0.
- Annars blir godkänd token version v2.0.

När du har skapat programmet kan ändra du godkänd token version genom att följa dessa steg:

1. Välj din app i Azure-portalen och välj sedan den **Manifest** för din app.
2. I manifestet, söker du efter **”accessTokenAcceptedVersion”**, och se dess värde är **2**. Den här egenskapen kan Azure AD som vet att webb-API accepterar v2.0-token. Om det är **null**, godkänd token version kommer att v1.0.
3. Välj **Spara**.

> [!NOTE]
> Det är upp till webb-API för att bestämma vilka token version (v1.0 eller v2.0) som accepteras. När klienter begär en token för ditt webb-API med hjälp av Microsoft identity-plattformen v2.0-slutpunkten kan får de en token som anger vilken version accepteras av webb-API.

## <a name="no-redirect-uri"></a>Inga omdirigerings-URI

Webb-API: er behöver inte registrera en omdirigerings-URI eftersom ingen användare är inloggad interaktivt.

## <a name="expose-an-api"></a>Exponera ett API

En annan inställning som är specifika för web API: er är exponerad API och exponerade omfång.

### <a name="resource-uri-and-scopes"></a>Resurs-URI och scope

Scope är vanligtvis formulärets `resourceURI/scopeName`. För Microsoft Graph scope har genvägar som `User.Read`, men den här strängen är bara en genväg för `https://graph.microsoft.com/user.read`.

Under registreringen måste du definiera följande parametrar:

- En resurs-URI - som standard på programregistreringsportalen rekommenderar att du kan använda `api://{clientId}`. Det här resurs-URI är unikt, men det är inte mänskliga läsbar. Du kan ändra den, men se till att det blir unikt.
- Ett eller flera omfång

Scope visas också på godkännandeskärmen som presenteras för slutanvändare som använder programmet. Därför måste du ange motsvarande strängar som beskriver vilka:

- Som visas av slutanvändaren
- Enligt klientadministratör som kan bevilja administratörens godkännande

### <a name="how-to-expose-the-api"></a>Hur du kan exponera API: et

1. Välj den **exponerar ett API** avsnittet i program-registreringen och:
   1. Välj **Lägg till omfång**.
   1. Acceptera den föreslagna program-ID-URI (api :// {clientId}) genom att välja **spara och fortsätt**.
   1. Ange följande parametrar:
      - För **scopenamn**, använda `access_as_user`.
      - För **vem som kan godkänna**, se till att den **administratörer och användare** alternativet är markerat.
      - I **visningsnamn för administratörsmedgivande**, typen `Access TodoListService as a user`.
      - I **beskrivning av administratörsmedgivande**, typen `Accesses the TodoListService Web API as a user`.
      - I **visningsnamn för användarmedgivande**, typen `Access TodoListService as a user`.
      - I **beskrivning av användarmedgivande**, typen `Accesses the TodoListService Web API as a user`.
      - Behåll **tillstånd** inställd **aktiverad**.
      - Välj **Lägg till omfattning**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-protected-web-api-app-configuration.md)

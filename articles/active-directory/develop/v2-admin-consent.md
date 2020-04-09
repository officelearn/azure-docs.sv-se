---
title: Godkännandeprotokoll för Microsoft identity platform admin consent
description: En beskrivning av auktoriseringen i slutpunkten för Microsofts identitetsplattform, inklusive scope, behörigheter och medgivande.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c9f633e0d205adaf5cefb2e3c036ce7f48253651
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886389"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Administratörsmedgivande på Microsofts identitetsplattform

Vissa behörigheter kräver medgivande från en administratör innan de kan beviljas inom en klient.  Du kan också använda slutpunkten för administratörsgodkännande för att bevilja behörigheter till en hel klient.  

## <a name="recommended-sign-the-user-into-your-app"></a>Rekommenderas: Logga in användaren i appen

När du skapar ett program som använder slutpunkten för administratörsmedgivande behöver appen vanligtvis en sida eller vy där administratören kan godkänna appens behörigheter. Den här sidan kan vara en del av appens registreringsflöde, en del av appens inställningar, eller så kan det vara ett dedikerat "connect"-flöde. I många fall är det vettigt att appen visar den här "connect"-vyn först när en användare har loggat in med ett Microsoft-konto för arbete eller skola.

När du signerar användaren i din app kan du identifiera den organisation som administratören tillhör innan du ber dem att godkänna de behörigheter som krävs. Även om det inte är absolut nödvändigt kan det hjälpa dig att skapa en mer intuitiv upplevelse för dina organisationsanvändare. Om du vill logga in användaren följer du våra [självstudier för Microsoft Identity Platform Protocol](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en katalogadministratör

När du är redo att begära behörigheter från organisationens administratör kan du omdirigera användaren till *slutpunkten för administratör*för Microsoft-identitetsplattform.

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| Parameter     | Villkor     | Beskrivning                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Krävs | Den katalogklient som du vill begära behörighet från. Kan tillhandahållas i GUID eller eget namnformat `organizations` ELLER allmänt refereras med som ses i exemplet. Använd inte "vanligt", eftersom personliga konton inte kan ge administratörsgodkännande utom i samband med en klient. Använd klient-ID när det är möjligt för att säkerställa bästa kompatibilitet med personliga konton som hanterar klienter. |
| `client_id` | Krävs | **Program-ID (klient)** som [Azure-portalen – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) erfarenhet som tilldelats din app. |
| `redirect_uri` | Krävs |Omdirigerings-URI:n där du vill att svaret ska skickas för att appen ska hanteras. Den måste exakt matcha en av de omdirigerings-URI:er som du registrerade i appregistreringsportalen. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng av vilket innehåll du vill. Använd tillståndet för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |
|`scope`        | Krävs      | Definierar den uppsättning behörigheter som begärs av programmet. Detta kan vara antingen statiska (med /.default) eller dynamiska scope.  Detta kan inkludera OIDC-scope `profile` `email`(`openid`, , ). | 


Nu kräver Azure AD att en klientadministratör loggar in för att slutföra begäran. Administratören uppmanas att godkänna alla behörigheter som `scope` du har begärt i parametern.  Om du har använt`/.default`ett statiskt ( ) värde fungerar det som slutpunkten v1.0 admin consent och begär medgivande för alla scope som finns i de behörigheter som krävs för appen.

### <a name="successful-response"></a>Lyckat svar

Om administratören godkänner behörigheterna för din app ser det lyckade svaret ut så här:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parameter         | Beskrivning                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| Katalogklienten som gav ditt program de behörigheter som begärs, i GUID-format.|
| `state`           | Ett värde som ingår i begäran som också kommer att returneras i tokensvaret. Det kan vara en sträng av vilket innehåll du vill. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på.|
| `scope`          | Den uppsättning behörigheter som har beviljats åtkomst till för programmet.|
| `admin_consent`   | Kommer att `True`ställas in på .|

### <a name="error-response"></a>Felsvar

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Lägga till de parametrar som ses i ett lyckat svar, felparametrar ses som nedan.

| Parameter          | Beskrivning                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel.|
| `error_description`| Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett fel.|
| `tenant`| Katalogklienten som gav ditt program de behörigheter som begärs, i GUID-format.|
| `state`           | Ett värde som ingår i begäran som också kommer att returneras i tokensvaret. Det kan vara en sträng av vilket innehåll du vill. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på.|
| `admin_consent`   | Kommer att `True` ställas in för att ange att det här svaret inträffade på ett administratörssamtyckeflöde.|

## <a name="next-steps"></a>Nästa steg
- Se [hur du konverterar en app till att vara flera innehavare](howto-convert-app-to-be-multi-tenant.md)
- Läs om hur [medgivande stöds på OAuth 2.0-protokolllagret under tilldelningskodsbidragsflödet](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Lär dig [hur ett program med flera innehavare kan använda medgivanderamverket](active-directory-devhowto-multi-tenant-overview.md) för att implementera "användare" och "admin"-medgivande, vilket stöder mer avancerade programmönster på flera nivåer.
- Förstå [azure AD-programmedgivanderupplevelser](application-consent-experience.md)

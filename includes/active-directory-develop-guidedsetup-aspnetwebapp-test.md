---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 4c4870dc0f5a423288e6cb561b985501414e8525
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203665"
---
## <a name="test-your-code"></a>Testa koden

Om du vill testa ditt program i Visual Studio trycker du på **F5** köra projektet. I webbläsaren öppnas http://<span></span>localhost: {portplatsen} och du ser den **logga in med Microsoft** knappen. Välj knappen för att börja logga in.

När du är redo att köra testet kan använda ett Microsoft Azure Active Directory (Azure AD)-konto (arbets- eller skolkonto konto) eller ett personligt microsoftkonto (<span>live.</span> COM eller <span>outlook.</span> com) för att logga in.

![Logga in med Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Logga in på ditt Microsoft-konto](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Visa program resultat

När du har loggat in omdirigeras användaren till startsidan för din webbplats. På sidan är HTTPS-URL som anges i registreringsinformationen program i portalen för registrering av Microsoft-program. Startsidan innehåller ett välkomstmeddelande *”Hello \<användare >”,* en länk för att logga ut och en länk för att visa användarens anspråk. Länken för användarens anspråk bläddrar till den *anspråk* styrenhet som du skapade tidigare.

### <a name="browse-to-see-the-users-claims"></a>Bläddra för att se användarens anspråk

Välj länken för att bläddra till den domänkontrollant vy som är endast tillgängligt för autentiserade användare för att se användarens anspråk.

#### <a name="view-the-claims-results"></a>Visa resultatet anspråk

När du bläddrar du till vyn controller, bör du se en tabell som innehåller de grundläggande egenskaperna för användaren:

|Egenskap  |Värde |Beskrivning |
|---|---|---|
|**Namn** |Användarens fullständiga namn | Användaren förnamn och efternamn.
|**Användarnamn** |Användaren<span>@domain.com</span> | Det användarnamn som används för att identifiera användaren.
|**Ämne** |Subjekt |En sträng som unikt identifierar användaren på webben.|
|**Klient-ID** |Guid | En **guid** som unikt representerar användarens Azure AD-organisation.|

Dessutom bör du se en tabell med alla anspråk som finns i autentiseringsbegäran. Mer information finns i den [lista över anspråk som finns i en Azure AD-ID-Token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testa åtkomst till en metod som har attributet auktorisera (valfritt)

Att testa åtkomst som en anonym användare till en domänkontrollant skyddas med den `Authorize` attributet, Följ dessa steg:

1. Klicka på länken logga ut användaren och slutföra utloggningen.
2. I webbläsaren, skriver du http://<span></span>localhost: {port} / anspråk som ska få åtkomst till din domänkontrollant som är skyddat med den `Authorize` attribut.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Förväntat resultat när du har åtkomst till en skyddad domänkontrollant

Du uppmanas att autentisera sig för att använda skyddade controller-vyn.

## <a name="advanced-options"></a>Avancerade alternativ

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Skydda hela webbplatsen
Att skydda hela webbplatsen på den **Global.asax** Lägg till den `AuthorizeAttribute` attributet den `GlobalFilters` filtrera i den `Application_Start` metoden:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Begränsa vem som kan logga in på ditt program

Som standard när du skapar programmet som skapats av den här guiden programmet accepterar inloggningar av personliga konton (inklusive outlook.com, live.com och andra) samt arbete och skolkonton från alla företag eller organisation som har integrerats med Azure Active Directory. Det här är en rekommenderade alternativet för SaaS-program.

Om du vill begränsa användarnas inloggning åtkomst för ditt program, finns flera alternativ:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Alternativ 1: Begränsa inloggningen till ditt program till en enda organisations Active Directory-instans (en enda innehavare)

Det här alternativet är ett vanligt scenario för *LOB-program*: Om du vill att programmet ska godkänna inloggningar från konton som tillhör en viss Azure Active Directory-instans (inklusive *gästkonton* instansens) gör du följande:

1. I den **web.config** filen, ändrar du värdet för den `Tenant` parametern från `Common` till klientnamnet för organisationen, till exempel `contoso.onmicrosoft.com`.
2. I din [OWIN-startklass](#configure-the-authentication-pipeline), ange den `ValidateIssuer` argumentet för `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Alternativ 2: Begränsa åtkomsten till ditt program till användare i en specifik lista av organisationer

Du kan begränsa inloggning åtkomsten till endast användarkonton som tillhör en Azure AD-organisation som finns i listan över tillåtna organisationer:
1. I din [OWIN-startklass](#configure-the-authentication-pipeline), ange den `ValidateIssuer` argumentet för `true`.
2. Ange värdet för den `ValidIssuers` parameter i listan över tillåtna organisationer.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Alternativ 3: Använda en anpassad metod för att verifiera utfärdare

Du kan implementera en anpassad metod för att verifiera utfärdare med hjälp av den **IssuerValidator** parametern. Mer information om hur du använder den här parametern Läs mer om den [TokenValidationParameters klass](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) på MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
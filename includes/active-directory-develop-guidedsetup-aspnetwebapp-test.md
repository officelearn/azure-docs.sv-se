---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7e9518f8a90faa0566b96d58992b01e4b0a642f4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202774"
---
## <a name="test-your-code"></a>Testa din kod

Om du vill testa ditt program i Visual Studio trycker du på **F5** köra projektet. I webbläsaren öppnas http://<span></span>localhost: {port} plats och du ser den **logga in med Microsoft** knappen. Klicka på knappen för att starta processen för inloggning.

När du är redo att köra testet kan använda ett Microsoft Azure Active Directory (Azure AD)-konto (arbets- eller skolkonto konto) eller ett personligt microsoftkonto (<span>live.</span> COM eller <span>outlook.</span> com) för att logga in.

![Logga in med Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Logga in på ditt Microsoft-konto](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Visa program resultat
När du har loggat in omdirigeras användaren till startsidan för din webbplats. Sidan är HTTPS-URL som anges i programmet registreringsinformation i portalen för registrering av Microsoft-program. Sidan innehåller ett välkomstmeddelande *”Hello \<användare >”,* en länk till logga ut och en länk för att visa användarens anspråk. Länken för användarens anspråk bläddrar till den *anspråk* domänkontrollant som du skapade tidigare.

### <a name="browse-to-see-the-users-claims"></a>Bläddra till användarens anspråk
Välj länken för att bläddra till vyn domänkontrollant som är tillgängligt endast för autentiserade användare för att se användarens anspråk.

#### <a name="view-the-claims-results"></a>Testresultaten anspråk
När du bläddrar till vyn domänkontrollanten bör du se en tabell som innehåller de grundläggande egenskaperna för användaren:

|Egenskap |Värde |Beskrivning |
|---|---|---|
|**Namn** |Användarens fullständiga namn | Användaren förnamn och efternamn.
|**Användarnamn** |Användaren<span>@domain.com</span> | Användarnamnet som används för att identifiera användaren.
|**Ämne** |Ämne |En sträng som unikt identifierar användaren på Internet.|
|**Klient-ID** |GUID | En **guid** som unikt representerar användarens Azure AD-organisation.|

Dessutom bör du se en tabell med alla anspråk i autentiseringsbegäran. Mer information finns i [listan med anspråk som finns i en Azure AD-ID-Token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testa åtkomst till en metod som har attributet auktorisera (valfritt)
Att testa åtkomst som en anonym användare till en domänkontrollant skyddas med den `Authorize` attribut, gör du följande:
1. Välj länken för att logga ut användaren och slutföra utloggning.
2. Skriv http:// i din webbläsare<span></span>localhost: {port} / utger sig för att komma åt den domänkontrollant som är skyddat med den `Authorize` attribut.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Förväntat resultat när du har åtkomst till en skyddad domänkontrollant
Du uppmanas att autentisera sig för att använda vyn skyddade domänkontrollant.

## <a name="advanced-options"></a>Avancerade alternativ

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Skydda hela webbplatsen
Att skydda hela webbplatsen i den **Global.asax** lägger du till den `AuthorizeAttribute` attribut till den `GlobalFilters` filter i den `Application_Start` metoden:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Begränsa vem som kan logga in på ditt program
Som standard när du skapar program som skapats av den här guiden tillämpningsprogrammet accepterar logga moduler av personliga konton (inklusive outlook.com och live.com) som fungerar och skolkonton från alla företag eller organisation som har integrerat med Azure Active Directory. Detta är en rekommenderade alternativet för SaaS-program.

Om du vill begränsa användarnas inloggning åtkomst för ditt program, finns flera alternativ:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Alternativ 1: Begränsa användare från Active Directory-instans i en enda organisation att logga in på ditt program (single-klient)

Det här alternativet är ett vanligt scenario för *LOB-program*: Om du vill att programmet ska acceptera inloggningar från konton som tillhör en viss Azure Active Directory-instans (inklusive *gästkonton*på instansen) gör du följande:

1. I den **web.config** fil, ändrar du värdet för den `Tenant` parametern från `Common` till innehavarens namn för organisationen som `contoso.onmicrosoft.com`.
2. I din [OWIN-startklass](#configure-the-authentication-pipeline), ange den `ValidateIssuer` argument för `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Alternativ 2: Begränsa åtkomsten till ditt program till användare i en viss lista av organisationer

Du kan begränsa inloggning åtkomsten till endast användarkonton som finns i en Azure AD-organisation som finns i listan över tillåtna organisationer:
1. I din [OWIN-startklass](#configure-the-authentication-pipeline), ange den `ValidateIssuer` argument för `true`.
2. Ange värdet för den `ValidIssuers` parameter i listan över tillåtna organisationer.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Alternativ 3: Använda en anpassad metod för att verifiera utfärdare
Du kan implementera en anpassad metod för att verifiera certifikatutfärdare med hjälp av den **IssuerValidator** parameter. Mer information om hur du använder den här parametern Läs mer om den [TokenValidationParameters klassen](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) på MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

---
title: "Azure AD v2 ASP.NET Web Server komma igång - testa | Microsoft Docs"
description: "Implementera Microsoft logga In på en ASP.NET-lösning med ett traditionellt webbläsarbaserade program med OpenID Connect standard"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: bfa2563a6a58370d9a611440017441a751b46244
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>Testa din kod

Tryck på `F5` köra projektet i Visual Studio. Webbläsaren öppnas och be dig *http://localhost: {port}* där du ser den *logga in med Microsoft* knappen. Gå vidare och genom att klicka på Logga in.

När du är redo att testa kan du använda ett arbets- eller skolkonto (Azure Active Directory) eller ett konto för personal (live.com, outlook.com) för att logga in. 

![Logga in med Microsoft webbläsarfönster](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![Logga in med Microsoft webbläsarfönster](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Förväntat resultat
Efter inloggning omdirigeras användaren till startsidan för webbplatsen som är HTTPS-URL som anges i programmet registreringsinformation i portalen för registrering av Microsoft-program. Den här sidan visas nu *Hello {User}* och en länk till utloggning och en länk till finns användaranspråk – som är en länk till auktorisera controller skapade tidigare.

### <a name="see-users-claims"></a>Se användarens anspråk
Välj länken för att se användarens anspråk. Detta leder dig till domänkontrollanten och visa som endast är tillgängliga för användare som autentiseras.

#### <a name="expected-results"></a>Förväntat resultat
 Du bör se en tabell som innehåller de grundläggande egenskaperna för den inloggade användaren:

| Egenskap | Värde | Beskrivning|
|---|---|---|
| Namn | {Fullständig användarnamn} | Användaren förnamn och efternamn
|Användarnamn | <span>user@domain.com</span>| Användarnamnet som används för att identifiera den inloggade användaren
| Ämne| {Ämne}|En sträng för att identifiera användaren loggar in på Internet|
| Klient-ID:t| {Guid}| En *guid* som unikt representerar användarens Azure Active Directory-organisation.|

Dessutom visas en tabell, inklusive alla användaranspråk som ingår i autentiseringsbegäran. En lista över alla anspråk i en Id-Token och förklaring finns [artikel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "lista av anspråk i Token Id").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Testa åtkomst till en metod som har en *[auktorisera]* attribut (valfritt)
I det här steget ska du testa åtkomst till autentiserad domänkontrollant som en anonym användare:<br/>
Välj en länk till utloggning användaren och slutföra utloggning.<br/>
Skriv nu i din webbläsare http://localhost: {port} / autentiserad åtkomst till din domänkontrollant som är skyddat med den `[Authorize]` attribut

#### <a name="expected-results"></a>Förväntat resultat
Du får uppmaningen att kräva att du autentisera för att visa.

## <a name="additional-information"></a>Ytterligare information

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Skydda hela webbplatsen
För att skydda hela webbplatsen, lägger du till den `AuthorizeAttribute` till `GlobalFilters` i `Global.asax` `Application_Start` metod:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **Hur du begränsa användare från en enda organisation att logga in på ditt program**

> Som standard personliga konton (inklusive outlook.com och live.com) samt fungerar och skolkonton från företaget eller organisationen som har integrerat med Azure Active Directory kan logga in på ditt program. 

> Om du vill att programmet ska acceptera inloggningar från en enda organisation i Azure Active Directory, ersätter den `Tenant` parameter i *web.config* från `Common` till innehavarens namn för organisationen – exempel, *contoso.onmicrosoft.com*. Sedan kan ändra den `ValidateIssuer` argument i din *OWIN-startklass* till `true`.

> Om du vill tillåta att användare från en lista med specifika organisationer, ange `ValidateIssuer` till true och använda den `ValidIssuers` parametern för att ange en lista över organisationer.

> Ett annat alternativ är att implementera en anpassad metod för att verifiera de utfärdare som använder IssuerValidator parameter. Mer information om `TokenValidationParameters`, se [detta](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN-artikel") MSDN-artikel.

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]
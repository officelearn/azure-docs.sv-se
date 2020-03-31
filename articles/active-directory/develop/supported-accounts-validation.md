---
title: Valideringsskillnader med kontotyper som stöds – Microsoft identity platform | Azure
description: Lär dig mer om valideringsskillnader för olika egenskaper för olika kontotyper som stöds när du registrerar din app med Microsofts identitetsplattform.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128862"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Valideringsskillnader med kontotyper som stöds (signInAudience)

När du registrerar ett program hos Microsofts identitetsplattform för utvecklare uppmanas du att välja vilka kontotyper ditt program stöder. I programobjektet och manifestet `signInAudience`är den här egenskapen .

Alternativen omfattar följande:

- *AzureADMyOrg*: Endast konton i organisationskatalogen där appen är registrerad (en klient)
- *AzureADMultipleOrgs*: Konton i alla organisationskataloger (flera innehavare)
- *AzureADandPersonalMicrosoftAccount*: Konton i alla organisationskataloger (flera innehavare) och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)

För registrerade program kan du hitta värdet för kontotyper som stöds i avsnittet **Autentisering** i ett program. Du kan också hitta `signInAudience` den under egenskapen i **Manifestet**.

Det värde som du väljer för den här egenskapen har konsekvenser för andra appobjektegenskaper. Om du ändrar den här egenskapen kan du därför behöva ändra andra egenskaper först.

Se följande tabell för valideringsskillnader för olika egenskaper för olika kontotyper som stöds.

| Egenskap | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` och `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Program-ID URI (`identifierURIs`)  | Måste vara unik i hyresgästen <br><br> urn:// system stöds <br><br> Jokertecken stöds inte <br><br> Frågesträngar och fragment stöds <br><br> Maximal längd på 255 tecken <br><br> Ingen gräns* för antalet identifierare  | Måste vara globalt unik <br><br> urn:// system stöds <br><br> Jokertecken stöds inte <br><br> Frågesträngar och fragment stöds <br><br> Maximal längd på 255 tecken <br><br> Ingen gräns* för antalet identifierare | Måste vara globalt unik <br><br> urn:// system stöds inte <br><br> Jokertecken, fragment och frågesträngar stöds inte <br><br> Maximal längd på 120 tecken <br><br> Högst 50 identifierare |
| Certifikat (`keyCredentials`) | Symmetrisk signeringsnyckel | Symmetrisk signeringsnyckel | Kryptering och asymmetrisk signeringsnyckel | 
| Klienthemligheter (`passwordCredentials`) | Ingen gräns* | Ingen gräns* | Om liveSDK är aktiverat: Högst 2 klienthemligheter | 
| Omdirigera URI:er (`replyURLs`) | Mer information finns i begränsningar och begränsningar för [url för omdirigering av URI/svar.](reply-url.md) | | | 
| API-behörigheter`requiredResourceAccess`( ) | Ingen gräns* | Ingen gräns* | Maximalt 30 behörigheter per tillåten resurs (t.ex. | 
| Scope som definieras av`oauth2Permissions`detta API ( ) | Maximal scopenamnslängd på 120 tecken <br><br> Ingen gräns* för antalet definierade scope | Maximal scopenamnslängd på 120 tecken <br><br> Ingen gräns* för antalet definierade scope |  Maximal scopenamnslängd på 40 tecken <br><br> Maximalt 100 definierade scope | 
| Auktoriserade klientprogram`preautorizedApplications`( ) | Ingen gräns* | Ingen gräns* | Totalt högst 500 <br><br> Maximalt 100 klientappar har definierats <br><br> Maximalt 30 scope som definierats per klient | 
| appRoles | Stöds <br> Ingen gräns* | Stöds <br> Ingen gräns* | Stöds inte | 
| Utloggnings-URL | http://localhostär tillåtet <br><br> Maximal längd på 255 tecken | http://localhostär tillåtet <br><br> Maximal längd på 255 tecken | <br><br> https://localhostär tillåtet, http://localhost misslyckas för MSA <br><br> Maximal längd på 255 tecken <br><br> HTTP-schema är inte tillåtet <br><br> Jokertecken stöds inte | 

*Det finns en global gräns på cirka 1 000 objekt i alla samlingsegenskaper på appobjektet

## <a name="next-steps"></a>Nästa steg

- Läs mer om [registrering av program](app-objects-and-service-principals.md)
- Läs mer om [programmanifestet](reference-app-manifest.md)

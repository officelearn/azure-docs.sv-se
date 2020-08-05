---
title: Använd attribut för Azure AD schema-tillägg i anspråk
titleSuffix: Microsoft identity platform
description: Beskriver hur du använder attribut för katalog schema tillägg för att skicka användar data till program i token-anspråk.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: cd21ef8d697570afb2109bb56d552284c03fd9a2
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552789"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>Använda attribut för katalog schema tillägg i anspråk

Med tillägg för katalog schema är ett sätt att lagra ytterligare data i Azure Active Directory på användar objekt och andra katalog objekt, till exempel grupper, klient information, tjänstens huvud namn.  Det går bara att använda tilläggs-attribut för användar objekt för att sända anspråk till program. Den här artikeln beskriver hur du använder attribut för katalog schema tillägg för att skicka användar data till program i token-anspråk.

> [!NOTE]
> Microsoft Graph ger två andra utöknings bara metoder för att anpassa diagram objekt. Dessa kallas Microsoft Graph öppna tillägg och Microsoft Graph schema tillägg. Mer information finns i [Microsoft Graph-dokumentationen](/graph/extensibility-overview) . Data som lagras på Microsoft Graph objekt som använder dessa funktioner är inte tillgängliga som källor för anspråk i tokens.

Attribut för katalog schemats tillägg är alltid kopplade till ett program i klienten och refereras till av programmets *applicationId* i sitt namn.

Identifieraren för ett katalog schemas tilläggs attribut är av typen *Extension_xxxxxxxxx_AttributeName*.  Där *xxxxxxxxx* är *applicationId* för det program som tillägget definierats för.

## <a name="registering-and-using-directory-schema-extensions"></a>Registrera och använda katalog schema tillägg
Attribut för katalog schema utökning kan registreras och fyllas i på något av följande sätt:

- Genom att konfigurera AD Connect för att skapa dem och synkronisera data med dem från lokalt AD. Se [Azure AD Connect Sync Directory Extensions](/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions).
- Använd Microsoft Graph för att registrera, ange värden för och läsa från tillägg för katalog schema tillägg [| Graph API koncept](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions) och/eller PowerShell + [Hantera tilläggs-attribut med PowerShell-cmdletar för AzureAD](/powershell/azure/active-directory/using-extension-attributes-sample?view=azureadps-2.0).

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>Sändning av anspråk med data från tillägg för katalog schema som skapats med AD Connect
Attribut för katalog schema tillägg som skapas och synkroniseras med AD Connect är alltid kopplade till det program-ID som används av AD Connect. De kan användas som källa för anspråk både genom att konfigurera dem som anspråk i konfigurationen för **företags program** i användar gränssnittet för SAML-program som registrerats med galleriet eller program konfigurations miljö som inte är galleri i **företags program**, och via en princip för anspråks mappning för program som registrerats via program registrerings upplevelsen.  När ett katalog tillägg som har skapats via AD Connect finns i katalogen visas det i konfigurations gränssnittet för SAML SSO-anspråk.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Genererade anspråk med data från tillägg för katalog schema som skapats för ett program med Graph eller PowerShell
Om ett tillägg för katalog schema är registrerat för ett program med hjälp av Microsoft Graph eller PowerShell (via ett programs inledande installations-eller etablerings steg för instans) kan samma program konfigureras i Azure Active Directory för att ta emot data i attributet från ett användar objekt i ett anspråk när användaren loggar in.  Programmet kan konfigureras för att ta emot data i katalog schema tillägg som är registrerade på samma program med [valfria anspråk](active-directory-optional-claims.md#configuring-directory-extension-optional-claims).  Dessa kan anges i applikations manifestet.  Detta gör att ett program med flera klienter kan registrera tillägg för katalog schema tillägg för eget bruk. När programmet är etablerad i en klient blir de associerade tilläggen för katalog scheman tillgängliga för användare i den klient organisationen och förbrukas.  När den har kon figurer ATS i klient organisationen och det godkännande som beviljats, kan den användas för att lagra och hämta data via graf och för att mappa till anspråk i tokens som Microsoft Identity Platform avger för program.

Attribut för katalog schema utökning kan registreras och fyllas i för alla program.

Om ett program behöver skicka anspråk med data från ett tillägg som registrerats i ett annat program, måste en [anspråks mappnings princip](active-directory-claims-mapping.md) användas för att mappa attributet Extension till anspråket.  Ett vanligt mönster för att hantera tillägg för katalog schema är att skapa ett program som är specifikt för registrerings punkten för alla schema tillägg som du behöver.  Det behöver inte vara ett verkligt program och den här metoden innebär att alla tillägg har samma program-ID i sitt namn.

Här är till exempel en princip för att mappa anspråk för att generera ett enda anspråk från ett katalog schemas tilläggs attribut i en OAuth/OIDC-token:

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            }, 
        ]
    }
}
```

Där *xxxxxxx* är det program-ID som tillägget registrerades med.

> [!TIP]
> Fall konsekvens är viktigt när du anger attribut för katalog tillägg för objekt.  Namn på tilläggsfiler är inte Skift läges känsliga när de konfigureras, men de är Skift läges känsliga när de läses från katalogen av token-tjänsten.  Om ett tilläggs attribut anges för ett användar objekt med namnet "LegacyId" och på ett annat användar objekt med namnet "LegacyID", och om attributet mappas till ett anspråk med namnet "LegacyId", hämtas data och anspråket som ingår i token för den första användaren, men inte den andra.
>
> Parametern "ID" i anspråks schemat som används för inbyggda katalogattribut är "ExtensionID" för katalog tilläggets attribut.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [lägger till anpassade eller ytterligare anspråk till token för SAML 2,0 och JSON Web tokens (JWT)](active-directory-optional-claims.md). 
- Lär dig hur du [anpassar anspråk som skickas i token för en angiven App](active-directory-claims-mapping.md).
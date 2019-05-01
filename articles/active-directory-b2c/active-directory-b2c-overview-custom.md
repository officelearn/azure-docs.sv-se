---
title: Anpassade principer för Azure Active Directory B2C | Microsoft Docs
description: Läs mer om Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ddc77cbcf004ddbd1738e598d5073a11f13e2c5a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695426"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Anpassade principer är konfigurationsfiler som definierar beteendet för din Azure Active Directory (Azure AD) B2C-klient. Användarflöden är fördefinierade i Azure AD B2C-portalen för de vanligaste uppgifterna identitet. Anpassade principer kan redigeras helt genom att utföra många olika aktiviteter.

## <a name="comparing-user-flows-and-custom-policies"></a>Jämföra användarflöden och anpassade principer

| | Användarflöden | Anpassade principer |
|-|-------------------|-----------------|
| Målanvändare | Alla utvecklare av företagsprogram med eller utan kunskaper om identitet. | Experter, systemintegrerare, konsulter och interna identitet team. De är nöjd med OpenIDConnect flöden och förstå Identitetsproviders och Anspråksbaserad autentisering. |
| Konfigurationsmetoden | Azure-portalen med ett användarvänligt användargränssnitt (UI). | Redigera XML-filer direkt och sedan ladda upp till Azure-portalen. |
| Anpassning av Användargränssnittet | Fullständig anpassningar inklusive HTML, CSS och JavaScript.<br><br>Stöd för flera språk med anpassade strängar. | Samma |
| Attributanpassning | Standardentiteter och anpassade attribut. | Samma |
| Hantering av token och sessionen | Anpassade token och flera sessionsalternativ för. | Samma |
| Identitetsprovidrar | Fördefinierade provider för lokal eller sociala och de flesta OIDC-identitetsleverantörer, till exempel federation med Azure Active Directory-klienter. | Standardbaserad OIDC, OAUTH och SAML.  Autentisering är också möjligt med hjälp av integrering med REST API: er. |
| Identity-uppgifter | Registrera dig eller logga in med lokal eller många konton i sociala medier.<br><br>Återställning av lösenord.<br><br>Profilredigering.<br><br>Multi-Factor Authentication.<br><br>Anpassa token och sessioner.<br><br>Åtkomst-token flöden. | Utföra samma åtgärder som användarflöden med hjälp av anpassade identitetsprovidrar eller använda anpassade omfattningar.<br><br>Etablera ett användarkonto på ett annat system vid tidpunkten för registrering.<br><br>Skicka ett välkomstmeddelande med din egen e-post-leverantör.<br><br>Använd en användararkivet utanför Azure AD B2C.<br><br>Validera användaren tillhandahållit information med en betrodd dator med hjälp av ett API. |

## <a name="policy-files"></a>Principfiler

Dessa tre typer av principfiler används:

- **Grundläggande filen** -innehåller de flesta av definitionerna. Vi rekommenderar att du gör ett minsta antal ändringar till den här filen för att hjälpa till med felsökning och långsiktig underhåll av dina principer.
- **Tilläggsfilen** -innehåller unika konfigurationsändringar för din klient.
- **Förlitande part (RP) filen** -enskild uppgift designmiljöer-fil som anropas direkt av programmet eller tjänsten (kallas även för en förlitande part). Varje unik aktivitet kräver sin egen RP och beroende på anpassningen krav, antalet vara ”totala programmens x Totalt antal objekt”.

Användarflöden i Azure AD B2C följer tre-file-mönster som beskrivs ovan, men utvecklaren kan endast se RP-filen medan Azure-portalen gör ändringar i bakgrunden i tilläggsfilen.

## <a name="custom-policy-core-concepts"></a>Anpassad princip för grundläggande begrepp

Identitets- och hantering av Kundidentitet tjänsten kund i Azure innehåller:

- En användarkatalog som är tillgänglig genom att använda Microsoft Graph och som innehåller användardata för både lokala konton och externa konton.
- Åtkomst till den **Identitetsramverk** som samordnar förtroende mellan användare och entiteter och skickar anspråk mellan dem att slutföra en hanteringsaktivitet identitet eller åtkomst. 
- En säkerhetstokentjänst (STS) som utfärdar-ID token, uppdatera token, och åtkomst-token (och motsvarande SAML intyg) och för att kontrollera dem för att skydda resurser.

Azure AD B2C samverkar med identitetsleverantörer, användare, andra system och med lokala användarkatalogen i följd att uppnå en identity-uppgift. Till exempel logga in en användare, registrera en ny användare eller återställa ett lösenord. Den Identitetsramverk och en princip (kallas även en användarresa eller en förtroendeprincipen framework) upprättar flerparti förtroende och definierar explicit aktörer, åtgärder, protokoll och sekvens med steg för att slutföra.

Den Identitetsramverk är en fullständigt konfigurerbar, principdriven, molnbaserad Azure-plattformen som samordnar förtroende mellan entiteter i standardprotokollet format som OpenIDConnect, OAuth, SAML, WSFed och några inte är standard format, till exempel REST API-baserade system mellan system anspråk utbyten. Ramverket skapar användarvänligt, white etikett upplevelser som har stöd för HTML och CSS.

En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar schemat för anspråk, anspråksomvandlingar, innehållsdefinitioner, Anspråksproviders, tekniska profiler och användaren resa orchestration-steg, bland annat. En anpassad princip är tillgänglig som en eller flera XML-filer som körs av Identitetsramverk när anropas av en förlitande part. Utvecklare som konfigurerar anpassade principer måste definiera betrodda relationer i noggrann detaljer som ska innehålla Metadataslutpunkter, exakt anspråk exchange-definitioner och konfigurera hemligheter, nycklar och certifikat som krävs av varje identitetsprovider.

### <a name="inheritance-model"></a>Arvsmodell

När ett program anropar principfilen som RP, Identitetsramverk i Azure AD B2C lägger till alla element från grundläggande fil från tilläggsfilen och sedan från RP principfil sätta ihop den aktuella principen gäller.  Delar av samma typ och namn i RP-filen åsidosätter de i tillägg och tillägg åsidosättningar base.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md)

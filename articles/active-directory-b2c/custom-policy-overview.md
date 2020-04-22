---
title: Anpassade principer för Azure Active Directory B2C | Microsoft-dokument
description: Lär dig mer om anpassade Azure Active Directory B2C-principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f18f44208b97ab5bc8d9cd9ff01d604c62deb963
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678154"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Anpassade principer är konfigurationsfiler som definierar beteendet för din Azure Active Directory B2C-klientorganisation (Azure AD B2C). Användarflöden är fördefinierade i Azure AD B2C-portalen för de vanligaste identitetsuppgifterna. Anpassade principer kan redigeras helt av en identitetsutvecklare för att slutföra många olika uppgifter.

## <a name="comparing-user-flows-and-custom-policies"></a>Jämföra användarflöden och anpassade principer

| | Användarflöden | Anpassade principer |
|-|-------------------|-----------------|
| Rikta in sig på användare | Alla programutvecklare med eller utan identitetsexpertis. | Identitetsproffs, systemintegratörer, konsulter och interna identitetsteam. De är bekväma med OpenID Connect-flöden och förstår identitetsleverantörer och anspråksbaserad autentisering. |
| Konfigurationsmetod | Azure-portal med ett användarvänligt användargränssnitt (UI). | Direkt redigering av XML-filer och sedan ladda upp till Azure-portalen. |
| Anpassning av användargränssnitt | Fullständig anpassning av användargränssnittet, inklusive HTML, CSS och JavaScript.<br><br>Stöd för flera språk med anpassade strängar. | Samma |
| Anpassning av attribut | Standard- och anpassade attribut. | Samma |
| Hantering av token och sessions | Anpassade token- och flera sessionsalternativ. | Samma |
| Identitetsprovidrar | Fördefinierade lokala eller sociala provider och de flesta OIDC-identitetsleverantörer, till exempel federation med Azure Active Directory-klienter. | Standardbaserade OIDC, OAUTH och SAML.  Autentisering är också möjligt genom att använda integrering med REST API:er. |
| Identitetsuppgifter | Registrera dig eller logga in med lokala eller många sociala konton.<br><br>Återställning av lösenord för självbetjäning.<br><br>Profilredigering.<br><br>Multifaktorautentisering.<br><br>Anpassa token och sessioner.<br><br>Få tillgång till tokenflöden. | Slutför samma uppgifter som användarflöden med hjälp av anpassade identitetsleverantörer eller använd anpassade scope.<br><br>Etablera ett användarkonto i ett annat system vid registreringstillfället.<br><br>Skicka ett välkomstmeddelande via din egen e-postleverantör.<br><br>Använd ett användararkiv utanför Azure AD B2C.<br><br>Validera användarinformation med ett betrott system med hjälp av ett API. |

## <a name="policy-files"></a>Principfiler

Dessa tre typer av principfiler används:

- **Basfil** - innehåller de flesta definitioner. Vi rekommenderar att du gör ett minsta antal ändringar i den här filen för att hjälpa till med felsökning och långsiktigt underhåll av dina principer.
- **Tilläggsfil** - innehåller de unika konfigurationsändringarna för din klient.
- **RP-fil (Relying Party)** - Den enda uppgiftsfokuserade filen som anropas direkt av programmet eller tjänsten (även, känd som en förlitande part). Varje unik uppgift kräver sin egen RP och beroende på varumärkeskrav kan antalet vara "totalt antal program x totalt antal användningsfall".

Användarflöden i Azure AD B2C följer filmönstret som visas ovan, men utvecklaren ser bara RP-filen, medan Azure-portalen gör ändringar i bakgrunden till tilläggsfilen.

Även om det finns tre typer av principfiler är du inte begränsad till endast tre filer. Du kan ha flera filer av varje filtyp. Om du till exempel inte vill göra ändringar i filen Tillägg kan du skapa en Extensions2-fil för att utöka filen Tillägg ytterligare.

## <a name="custom-policy-core-concepts"></a>Anpassade principgrundkoncept

CIAM-tjänsten (Customer Identity and Access Management) i Azure innehåller:

- En användarkatalog som är tillgänglig med hjälp av Microsoft Graph och som innehåller användardata för både lokala konton och federerade konton.
- Åtkomst till **Identity Experience Framework** som dirigerar förtroende mellan användare och entiteter och skickar anspråk mellan dem för att slutföra en identitets- eller åtkomsthanteringsuppgift.
- En säkerhetstokentjänst (STS) som utfärdar ID-token, uppdaterar token och åtkomsttoken (och motsvarande SAML-påståenden) och validerar dem för att skydda resurser.

Azure AD B2C interagerar med identitetsleverantörer, användare, andra system och med den lokala användarkatalogen i följd för att uppnå en identitetsuppgift. Logga till exempel in en användare, registrera en ny användare eller återställa ett lösenord. Identity Experience Framework och en princip (kallas även en användarresa eller en förtroenderamsprincip) skapar förtroende för flera parter och definierar uttryckligen aktörerna, åtgärderna, protokollen och sekvensen av steg som ska slutföras.

Identity Experience Framework är en helt konfigurerbar, principdriven, molnbaserad Azure-plattform som dirigerar förtroende mellan entiteter i standardprotokollformat som OpenID Connect, OAuth, SAML och några icke-standardbaserade, till exempel REST API-baserade system-till-system-anspråksutbyten. Ramverket skapar användarvänliga, vitmärkta upplevelser som stöder HTML och CSS.

En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar anspråksschema, anspråksomvandlingar, innehållsdefinitioner, anspråksleverantörer, tekniska profiler och steg för användarfärdorkestrering, bland andra element. En anpassad princip är tillgänglig som en eller flera XML-filer som körs av Identity Experience Framework när den anropas av en förlitande part. Utvecklare som konfigurerar anpassade principer måste definiera de betrodda relationerna i detalj för att inkludera metadataslutpunkter, exakta definitioner för anspråksutbyte och konfigurera hemligheter, nycklar och certifikat efter behov av varje identitetsprovider.

### <a name="inheritance-model"></a>Arvsmodell

När ett program anropar RP-principfilen lägger Identity Experience Framework i Azure AD B2C till alla element från basfilen, från tilläggsfilen och sedan från RP-principfilen för att montera den aktuella principen som gäller.  Element av samma typ och namn i RP-filen åsidosätter dem i tilläggen och tillägg åsidosätter basen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med anpassade principer](custom-policy-get-started.md)

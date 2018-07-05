---
title: Anpassade principer för Azure Active Directory B2C | Microsoft Docs
description: Läs mer om Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0724227da425eb2faeee9ac4ae8449782e62a241
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445998"
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: Anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Vad är anpassade principer?

Anpassade principer är konfigurationsfiler som definierar beteendet för din Azure AD B2C-klient. Medan **inbyggda principer** är fördefinierade i Azure AD B2C-portalen för de vanligaste uppgifterna för identitet, anpassade principer kan redigeras helt genom att utföra ett nästan obegränsat antal uppgifter. Läs vidare för att avgöra om anpassade principer är rätt för dig och ditt identity-scenario.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Jämföra inbyggda och anpassade principer

| | Inbyggda principer | Anpassade principer |
|-|-------------------|-----------------|
|Målanvändare | Alla apputvecklare med eller utan kunskaper om identitet | Experter: systemintegrerare, konsulter och interna identitet team. De är nöjd med OpenIDConnect flöden och förstå Identitetsproviders och Anspråksbaserad autentisering |
|Konfigurationsmetoden | Azure-portalen med ett användarvänligt gränssnitt | Redigera XML-filer direkt och sedan ladda upp till Azure-portalen |
|Anpassning av Användargränssnittet | Fullständig anpassning av Användargränssnittet, inklusive HTML, CSS och javascript-stöd (kräver anpassad domän)<br><br>Stöd för flera språk med anpassade strängar | Samma |
| Attributanpassning | Standardentiteter och anpassade attribut | Samma |
|Hantering av token och sessionen | Anpassade token och flera sessionsalternativ | Samma |
|Identitetsproviders| **Idag**: fördefinierade lokal, social provider<br><br>**Framtiden**: standardbaserad OIDC SAML, OAuth | **Idag**: standardbaserad OIDC OAUTH SAML<br><br>**Framtiden**: WsFed |
|Identitet uppgifter (exempel) | Registrera dig eller logga in med lokal och många konton i sociala medier<br><br>Återställning av lösenord för självbetjäning<br><br>Profilredigering<br><br>Multi-Factor Auth-scenarier<br><br>Anpassa token och sessioner<br><br>Åtkomsttoken flöden | Slutföra uppgifterna som inbyggda principer med hjälp av anpassade identitetsprovidrar eller använda anpassade omfattningar<br><br>Etablera användare i ett annat system vid tidpunkten för registrering<br><br>Skicka ett välkomstmeddelande med din egen e-post-leverantör<br><br>Använda en användararkivet utanför B2C<br><br>Validera användaren tillhandahållit information med en betrodd dator via API: et |

## <a name="policy-files"></a>Principfiler

En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. Definiera XML-element: schemat för anspråk, anspråk transformationer, innehållsdefinitioner, anspråk providers-tekniska profiler och användaren resa orchestration-steg, bland annat.

Vi rekommenderar användning av tre typer av principfiler:

- **En grundläggande fil**, som innehåller de flesta av definitionerna och för som Azure tillhandahåller ett fullständigt exempel.  Vi rekommenderar att du gör ett minsta antal ändringar till den här filen för att hjälpa till med felsökning och långsiktig underhåll av dina principer
- **en fil med tillägg** som innehåller de unika konfigurationsändringarna för din klient
- **en förlitande part (RP)-fil** som är den enda uppgift fokuserar-fil som anropas direkt av programmet eller tjänsten (även kallat förlitande part).  Läs artikeln om filen principdefinitioner för mer information.  Varje unik aktivitet kräver sin egen RP och beroende på anpassningen krav antalet vara ”totala programmens x Totalt antal objekt”.


Inbyggda principer i Azure AD B2C följer tre-file-mönster som beskrivs ovan, men utvecklaren kan endast se filen förlitande part (RP), medan portalen gör ändringar i bakgrunden i filen tillägg.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Grundläggande begrepp som du bör känna till när du använder anpassade principer

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azures identitets- och hantering av Kundidentitet kundservice. Tjänsten omfattar:

1. En katalog för användarnamn i form av en särskilda ändamål Azure Active Directory tillgängliga via Microsoft Graph och som innehåller användardata för både lokala konton och externa konton 
2. Åtkomst till den **Identitetsramverk** som samordnar förtroende mellan användare och entiteter och skickar anspråk mellan dem att slutföra en aktivitet för identitet/access management 
3. En säkerhetstokentjänst (STS) utfärda ID token, uppdatera token, och åtkomst-token (och motsvarande SAML intyg) och godkänna dem för att skydda resurser.

Azure AD B2C interagerar med identitetsleverantörer, användare, andra system och med lokala användarkatalogen i följd att uppnå en identity-uppgift (till exempel logga in en användare kan registrera en ny användare, återställa ett lösenord). Den underliggande plattformen som upprättar flerparti förtroende och kör de här stegen kallas den Identitetsramverk och en princip (kallas även en användarresa eller en förtroendeprincipen framework) explicit definierar aktörer, åtgärder, protokoll, och sekvens med steg för att slutföra.

### <a name="identity-experience-framework"></a>Identity Experience Framework

En fullständigt konfigurerbar, principdriven, molnbaserad Azure-plattformen som samordnar förtroende mellan entiteter (brett Anspråksproviders) i standardprotokollet formaterar som OpenIDConnect, OAuth, SAML, WSFed och några inte är standard format (till exempel REST API-based--system till anspråk utbyten). I2E skapar användarvänligt, white etikett upplevelser som har stöd för HTML, CSS och javascript.  Idag är det Identitetsramverk endast tillgängliga i samband med Azure AD B2C-tjänsten och prioriterad för uppgifter som rör CIAM.

### <a name="built-in-policies"></a>Inbyggda principer

Fördefinierade konfiguration filer som dirigerar beteendet för Azure AD B2C för att utföra de vanligaste används identitet uppgifter (det vill säga användarregistrering, inloggning, återställning av lösenord) och interagera med parter vars relation också är fördefinierade i Azure AD B2C ( till exempel Facebook identitetsprovidern, LinkedIn, Account, Google-konton).  I framtiden, kan inbyggda principer också ge för anpassning av identitetsleverantörer som vanligtvis ingår i enterprise-området Azure Active Directory Premium, Active Directory/AD FS, t.ex Salesforce-ID-providern.


### <a name="custom-policies"></a>Anpassade principer

Konfigurationsfiler som definierar beteendet för Identitetsramverk i din Azure AD B2C-klient. En anpassad princip är tillgänglig som en eller flera XML-filer (se principfiler definitioner) som utförs av Identitetsramverk när anropas av en förlitande part (till exempel ett program). Anpassade principer kan redigeras direkt genom att utföra ett nästan obegränsat antal uppgifter. Utvecklare som konfigurerar anpassade principer måste definiera betrodda relationer i noggrann detaljer som ska innehålla Metadataslutpunkter, exakt anspråk exchange-definitioner och konfigurera hemligheter, nycklar och certifikat som krävs av varje identitetsprovider.

## <a name="policy-file-definitions-for-identity-experience-framework-trust-frameworks"></a>Filen Principdefinitioner för ramverk för Identitetsupplevelse förtroende

### <a name="policy-files"></a>Principfiler

En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. Definiera XML-element: schemat för anspråk, anspråk transformationer, innehållsdefinitioner, anspråk providers-tekniska profiler och användaren resa orchestration-steg, bland annat.  Vi rekommenderar användning av tre typer av principfiler:

- **En grundläggande fil** innehåller de flesta av definitionerna och för som Azure tillhandahåller ett fullständigt exempel.  Vi rekommenderar att du gör ett minsta antal ändringar till den här filen för att hjälpa till med felsökning och långsiktig underhåll av dina principer
- **en fil med tillägg** som innehåller de unika konfigurationsändringarna för din klient
- **en förlitande part (RP)-fil** är den enda uppgift fokuserar fil som anropas direkt av programmet eller tjänsten (även kallat förlitande part).  Läs artikeln om filen principdefinitioner för mer information.  Varje unik aktivitet kräver sin egen RP och beroende på anpassningen krav antalet vara ”totala programmens x Totalt antal objekt”.

![Principen filtyper](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Typ av principfil | Exempel-filnamn | Rekommenderade användning | Ärver från |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Innehåller grundläggande anspråk schemat, anspråksomvandlingar, Anspråksproviders och användaren utbildning som konfigurerats av Microsoft<br><br>Göra minimala ändringar i den här filen | Ingen |
| Tillägget (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Konsolidera dina ändringar till bas-filen här<br><br>Ändrade Anspråksproviders<br><br>Ändrade användaren resor<br><br>Dina egna anpassade schemadefinitioner | GRUNDLÄGGANDE fil |
| Förlitande part (RP) | B2C_1A_sign_up_sign_in.xml| Ändra token form och sessionen inställningar| Extensions(ext) fil |

### <a name="inheritance-model"></a>Arvsmodell

När ett program anropar principfilen som RP, Identitetsramverk i B2C lägger till alla element från BASE och tillägg, och slutligen från RP principfil sätta ihop den aktuella principen gäller.  Delar av samma typ och namn i RP-filen åsidosätter de i tillägg och tillägg åsidosättningar BASE.

**Inbyggda principer** i Azure AD B2C följer mönstret 3-fil som beskrivs ovan, men utvecklaren kan endast se filen förlitande part (RP), medan portalen gör ändringar i bakgrunden i filen EXTenstions.  Alla Azure AD B2C delar en basprincipen-filen som kontrolleras av Azure B2C-teamet och uppdateras ofta.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md)

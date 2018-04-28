---
title: 'Azure Active Directory B2C: Anpassade principer | Microsoft Docs'
description: Ett ämne på Azure Active Directory B2C anpassade principer
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.openlocfilehash: 22d34ac4128da1d1a9f20619aec2aaccc2425a21
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: Anpassade policys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Vad är anpassade principer?

Anpassade principer är konfigurationsfiler som definierar beteendet för din Azure AD B2C-klient. Medan **inbyggda principer** är fördefinierade i Azure AD B2C-portal för de vanligaste uppgifterna identitet anpassade principer kan redigeras fullständigt med en identity-utvecklare att slutföra en nära obegränsat antal uppgifter. Läs vidare för att avgöra om anpassade principer som är rätt för dig och din identitet scenario.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Jämföra inbyggda och anpassade principer

| | Inbyggda principer | Anpassade principer |
|-|-------------------|-----------------|
|Målgruppsanvändare | Alla utvecklare av program med eller utan kunskaper identitet | Identity-tekniker: systemintegrerare, konsulter och interna identitet team. De är nöjd med OpenIDConnect flöden och förstå identitetsleverantörer och Anspråksbaserad autentisering |
|Metod för principkonfiguration | Azure-portalen med ett användarvänligt gränssnitt | Redigera XML-filer och sedan ladda upp till Azure-portalen |
|Anpassning av Användargränssnittet | Fullständig anpassning av Användargränssnittet, inklusive HTML, CSS och javascript-stöd (kräver anpassade domäner)<br><br>Stöd för flera språk med anpassade strängar | samma |
| Attributanpassning | Standard- och anpassade attribut | samma |
|Hantering av token och session | Anpassade token och flera olika sessionsalternativ för | samma |
|Identitetsproviders| **Idag**: fördefinierade lokal, sociala provider<br><br>**Framtida**: standardbaserad OIDC SAML OAuth | **Idag**: standardbaserad OIDC OAUTH SAML<br><br>**Framtida**: WsFed |
|Identitet uppgifter (exempel) | Registrera dig eller logga in med lokala och många sociala konton<br><br>Återställning av lösenord för självbetjäning<br><br>Redigera profil<br><br>Multi-Factor Authentication-scenarier<br><br>Anpassa token och sessioner<br><br>Åtkomsttoken flödar | Utföra samma åtgärder som inbyggda principer med hjälp av anpassade identitetsleverantörer eller använda anpassade omfattningar<br><br>Etablera användare i ett annat system vid tiden för registrering<br><br>Skicka ett välkomstmeddelande med din egen e-post-leverantör<br><br>Använd en användararkivet utanför B2C<br><br>Verifiera användarens angivna information med en betrodd dator via API |

## <a name="policy-files"></a>Principfiler

En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-element definiera: anspråk schemat anspråk omvandlingar, definitioner av innehåll, anspråk providers-tekniska profiler och Userjourney orchestration-steg, bland annat.

Vi rekommenderar användning av tre typer av principfiler:

- **En grundläggande fil**, som innehåller de flesta av definitionerna och som Azure tillhandahåller ett fullständigt exempel.  Vi rekommenderar att du gör ett minsta antal ändringar i den här filen som hjälp med felsökning och lång sikt dina principer
- **en fil i tillägg** som innehåller unika konfigurationsändringar för din klient
- **en förlitande part (RP)-fil** som är den enda uppgift fokuserar fil som anropas direkt av programmet eller tjänsten (även kallat förlitande part).  Läs artikeln på principdefinitioner för mer information.  Varje unik aktiviteten kräver sin egen RP och beroende på anpassningen krav antalet vara ”Totalt program x Totalt antal användningsområden”.


Inbyggda principer i Azure AD B2C följer mönstret för 3-fil som beskrivs ovan, men utvecklaren ser endast förlitande part (RP)-fil, medan portalen gör ändringar i bakgrunden i filen tillägg.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Grundläggande begrepp som du bör känna till när du använder anpassade principer

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azures identitets- och hantering (CIAM) kundtjänst. Tjänsten omfattar:

1. En användarkatalog i form av en särskild syfte Azure Active Directory tillgänglig via Microsoft Graph och som innehåller användardata för både lokala och konton för federerade 
2. Åtkomst till den **identitet upplevelse Framework** som samordnar förtroende mellan användare och enheter och skickar anspråk mellan dem för att slutföra en identity-/ access management-aktivitet 
3. En säkerhetstokentjänst (STS) utfärda id token, uppdatera token och åtkomst-token (och motsvarande SAML intyg) och godkänna dem för att skydda resurser.

Azure AD B2C samverkar med identitetsleverantörer, användare, andra system och lokala användarkatalogen i följd att uppnå en identity-aktivitet (till exempel en användare logga in registrera en ny användare, återställa ett lösenord). Den underliggande plattformen som upprättar flerparti förtroende och utför dessa steg anropas identitet upplevelse Framework och en princip (kallas även för en användare resa eller en förtroendeprincipen framework) definierar aktörer, åtgärder, protokoll, och sekvens med steg för att slutföra.

### <a name="identity-experience-framework"></a>Identity Experience Framework

Ett fullständigt konfigurerbara, principer, molnbaserad Azure-plattformen som samordnar förtroende mellan enheter (brett Anspråksproviders) i standardprotokoll format, till exempel OpenIDConnect, OAuth, SAML, WSFed och några standardmässiga format (till exempel REST API-based--system till anspråk utbyte). I2E skapar användarvänliga, vit etikett program som har stöd för HTML, CSS och javascript.  Idag är endast tillgänglig i samband med tjänsten Azure AD B2C och prioriterad för uppgifter som rör CIAM identitet upplevelse ramen.

### <a name="built-in-policies"></a>Inbyggda principer

Fördefinierade konfigurationsfiler som direkt beteendet för Azure AD B2C för att utföra de vanligaste används identitet uppgifter (d.v.s. användarregistrering, inloggning, återställning av lösenord) och interagera med betrodda parter vars relation också är fördefinierade i Azure AD B2C (för exempel Facebook identitetsleverantör, LinkedIn, Account, Google-konton).  I framtiden, kan inbyggda principerna också innehålla för anpassning av identitetsleverantörer som vanligtvis finns i enterprise-sfär som Azure Active Directory Premium, Active Directory/ADFS o.s.v. Salesforce-ID-providern.


### <a name="custom-policies"></a>Anpassade principer

Konfigurationsfiler som definierar beteendet för identitet upplevelse Framework i din Azure AD B2C-klient. En anpassad princip är tillgänglig som en eller flera XML-filer (se principfiler definitioner) som utförs av ramverket identitet upplevelse när den anropades av en förlitande part (till exempel ett program). Anpassade principer kan direkt redigeras av en identity-utvecklare att slutföra en nära obegränsat antal uppgifter. Utvecklare konfigurera anpassade principer måste definiera betrodda relationer i noggrann detaljer som ska innehålla Metadataslutpunkter, exakt anspråk exchange definitioner och konfigurera hemligheter, nycklar och certifikat som krävs av varje identitetsleverantör.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Princip för definitioner för identitet upplevelse Framework Trustframeworks

### <a name="policy-files"></a>Principfiler

En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-element definiera: anspråk schemat anspråk transformationer, definitioner av innehåll, anspråk providers-tekniska profiler och användaren resa orchestration-steg, bland annat.  Vi rekommenderar användning av tre typer av principfiler:

- **En grundläggande fil** innehåller de flesta av definitionerna och som Azure tillhandahåller ett fullständigt exempel.  Vi rekommenderar att du gör ett minsta antal ändringar i den här filen som hjälp med felsökning och lång sikt dina principer
- **en fil i tillägg** som innehåller unika konfigurationsändringar för din klient
- **en förlitande part (RP)-fil** är den enda fokuserad aktivitet fil som anropas direkt av programmet eller tjänsten (även kallat förlitande part).  Läs artikeln på principdefinitioner för mer information.  Varje unik aktiviteten kräver sin egen RP och beroende på anpassningen krav antalet vara ”Totalt program x Totalt antal användningsområden”.

![Princip för filtyper](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Typ av princip-fil | Exempel filnamn | Rekommenderad användning | Ärver från |
|---------------------|--------------------|-----------------|---------------|
| BAS |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Innehåller grundläggande anspråk schemat, anspråksomvandlingar, Anspråksproviders och användaren resor konfigureras av Microsoft<br><br>Göra minimala ändringar i filen | Ingen |
| Tillägget (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Konsolidera dina ändringar i grundläggande filen<br><br>Ändrade Anspråksproviders<br><br>Ändrade användaren resor<br><br>Anpassat schemadefinitionerna | BAS-fil |
| Förlitande part (RP) | B2C_1A_sign_up_sign_in.xml| Ändra token form och session här| Extensions(ext) fil |

### <a name="inheritance-model"></a>Arv modellen

När ett program anropar RP principfilen, identitet upplevelse ramverk i B2C lägger till alla element från basen och tillägg, och slutligen från principfilen RP att montera den aktuella principen gäller.  Element av samma typ och namn i RP-filen åsidosätter de i tillägg och tillägg åsidosätter BASE.

**Principer för inbyggda** i Azure AD B2C följer mönstret för 3-fil som beskrivs ovan, men utvecklaren ser endast förlitande part (RP)-fil, medan portalen gör ändringar i filen EXTenstions i bakgrunden.  Alla Azure AD B2C delar en grundläggande principfil som kontrolleras i Azure B2C-teamet och uppdateras ofta.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md)

---
title: Teknisk översikt och funktionsöversikt – Azure Active Directory B2C
description: En djupgående introduktion till funktioner och tekniker i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d6b33211f6f247d9f30c0f162b388085faabe6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332537"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Teknisk översikt och funktionsöversikt för Azure Active Directory B2C

En följeslagare till [Om Azure Active Directory B2C](overview.md), den här artikeln ger en mer djupgående introduktion till tjänsten. Här beskrivs de primära resurser som du arbetar med i tjänsten, dess funktioner och hur dessa gör att du kan ge dina kunder en helt anpassad identitetsupplevelse i dina program.

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C-klient

I Azure Active Directory B2C (Azure AD B2C) representerar en *klient* din organisation och är en katalog över användare. Varje Azure AD B2C-klientorganisation är separat och åtskild från andra Azure AD B2C-klientorganisationer. En Azure AD B2C-klient är annorlunda än en Azure Active Directory-klient, som du kanske redan har.

De primära resurserna som du arbetar med i en Azure AD B2C-klient är:

* **Katalog** - *Katalogen* är där Azure AD B2C lagrar användarnas autentiseringsuppgifter och profildata, samt dina programregistreringar.
* **Programregistreringar** – Du registrerar dina webb-, mobil- och inbyggda program med Azure AD B2C för att aktivera identitetshantering. Även alla API:er som du vill skydda med Azure AD B2C.
* **Användarflöden** och **anpassade principer** – De inbyggda (användarflödena) och helt anpassningsbara (anpassade principer) identitetsupplevelser för dina program.
  * Använd *användarflöden* för snabb konfiguration och aktivering av vanliga identitetsuppgifter som registrering, inloggning och profilredigering.
  * Använd *anpassade principer* för att aktivera användarupplevelser inte bara för vanliga identitetsuppgifter, utan också för att skapa stöd för komplexa identitetsarbetsflöden som är unika för din organisation, kunder, anställda, partner och medborgare.
* **Identitetsleverantörer** - Federationsinställningar för:
  * *Sociala* identitetsleverantörer som Facebook, LinkedIn eller Twitter som du vill stödja i dina program.
  * *Externa* identitetsleverantörer som stöder standardidentitetsprotokoll som OAuth 2.0, OpenID Connect med mera.
  * *Lokala* konton som gör det möjligt för användare att registrera sig och logga in med ett användarnamn (eller e-postadress eller annat ID) och lösenord.
* **Nycklar** - Lägg till och hantera krypteringsnycklar för signering och validering av token.

En Azure AD B2C-klient är den första resursen du behöver skapa för att komma igång med Azure AD B2C. Lär dig hur i [självstudiekurs: Skapa en Azure Active Directory B2C-klient](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Konton i Azure AD B2C

Azure AD B2C definierar flera typer av användarkonton. Azure Active Directory, Azure Active Directory B2B och Azure Active Directory B2C delar dessa kontotyper.

* **Arbetskonto** - Användare med arbetskonton kan hantera resurser i en klientorganisation och med en administratörsroll kan även hantera klienter. Användare med arbetskonton kan skapa nya konsumentkonton, återställa lösenord, blockera/avblockera konton och ange behörigheter eller tilldela ett konto till en säkerhetsgrupp.
* **Gästkonto** - Externa användare som du bjuder in till din klientorganisation som gäster. Ett typiskt scenario för att bjuda in en gästanvändare till din Azure AD B2C-klient är att dela administrationsansvar.
* **Konsumentkonto** - Konsumentkonton är de konton som skapas i din Azure AD B2C-katalog när användare slutför registreringsanvändarresan i ett program som du har registrerat i din klientorganisation.

![Sidan Azure AD B2C-användarhantering i Azure-portalen](media/technical-overview/portal-01-users.png)<br/>*Bild: Användarkatalog i en Azure AD B2C-klient i Azure-portalen*

### <a name="consumer-accounts"></a>Konsumentkonton

Med ett *konsumentkonto* kan användare logga in på de program som du har säkrat med Azure AD B2C. Användare med konsumentkonton kan dock inte komma åt Azure-resurser, till exempel Azure-portalen.

Ett konsumentkonto kan associeras med följande identitetstyper:

* **Lokal** identitet, med användarnamn och lösenord som lagras lokalt i Azure AD B2C-katalogen. Vi kallar ofta dessa identiteter för "lokala konton".
* **Sociala** identiteter eller företagsidentiteter, där användarens identitet hanteras av en federerad identitetsleverantör som Facebook, Microsoft, ADFS eller Salesforce. **enterprise**

En användare med ett konsumentkonto kan logga in med flera identiteter, till exempel användarnamn, e-post, medarbetar-ID, myndighets-ID och andra. Ett enda konto kan ha flera identiteter, både lokala och sociala.

![Identiteter för konsumentkonto](media/technical-overview/identities.png)<br/>*Bild: Ett enda konsumentkonto med flera identiteter i Azure AD B2C*

Med Azure AD B2C kan du hantera vanliga attribut för konsumentkontoprofiler som visningsnamn, efternamn, förnamn, ort och andra. Du kan också utöka Azure AD-schemat för att lagra ytterligare information om dina användare. Till exempel deras land eller hemvist, önskat språk och inställningar som om de vill prenumerera på ett nyhetsbrev eller aktivera multifaktorautentisering.

Läs mer om användarkontotyperna i Azure AD B2C i [Översikt över användarkonton i Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Externa identitetsprovidrar

Du kan konfigurera Azure AD B2C så att användare kan logga in på ditt program med autentiseringsuppgifter från externa sociala eller företagsidentitetsleverantörer (IdP). Azure AD B2C stöder externa identitetsleverantörer som Facebook, Microsoft-konto, Google, Twitter och alla identitetsleverantörer som stöder OAuth 1.0, OAuth 2.0, OpenID Connect, SAML eller WS-Federation-protokoll.

![Externa identitetsprovidrar](media/technical-overview/external-idps.png)

Med den externa identitetsproviderfederationen kan du erbjuda dina konsumenter möjligheten att logga in med sina befintliga sociala konton eller företagskonton, utan att behöva skapa ett nytt konto bara för ditt program.

På inloggnings- eller inloggningssidan presenterar Azure AD B2C en lista över externa identitetsleverantörer som användaren kan välja för inloggning. När de har valt en av de externa identitetsleverantörerna tas de (omdirigeras) till den valda leverantörens webbplats för att slutföra inloggningsprocessen. När användaren har loggat in returneras de tillbaka till Azure AD B2C för autentisering av kontot i ditt program.

![Exempel på mobil inloggning med ett socialt konto (Facebook)](media/technical-overview/external-idp.png)

Information om hur du lägger till identitetsleverantörer i Azure AD B2C finns i [Självstudiekurs: Lägg till identitetsleverantörer i dina program i Azure Active Directory B2C](tutorial-add-identity-providers.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Identitetsupplevelser: användarflöden eller anpassade principer

Det utökningsbara principramverket i Azure AD B2C är dess kärnstyrka. Principer beskriver användarnas identitetsupplevelser som registrering, inloggning och profilredigering.

I Azure AD B2C finns det två primära sökvägar som du kan ta för att tillhandahålla dessa identitetsupplevelser: användarflöden och anpassade principer.

* **Användarflöden** är fördefinierade, inbyggda, konfigurerbara principer som vi tillhandahåller så att du kan skapa anmälnings-, inloggnings- och principredigeringsupplevelser på några minuter.

* **Med anpassade principer** kan du skapa egna användarresor för komplexa identitetsupplevelsescenarier.

Både användarflöden och anpassade principer drivs av *Identity Experience Framework*, Azure AD B2C:s principorkestreringsmotor.

### <a name="user-flow"></a>Användarflöde

För att hjälpa dig att snabbt ställa in de vanligaste identitetsuppgifterna innehåller Azure-portalen flera fördefinierade och konfigurerbara principer som kallas *användarflöden*.

Du kan konfigurera inställningar för användarflöde som dessa för att styra identitetsupplevelser i dina program:

* Kontotyper som används för inloggning, till exempel sociala konton som en Facebook eller lokala konton som använder en e-postadress och ett lösenord för inloggning
* Attribut som ska samlas in från konsumenten, till exempel förnamn, postnummer eller bosättningsland
* Azure Multi-Factor Authentication (MFA)
* Anpassning av användargränssnittet
* Uppsättning anspråk i en token som ditt program tar emot när användaren har slutfört användarflödet
* Sessionshantering
* ... med mera.

De vanligaste identitetsscenarier för de flesta mobila, webb- och ensidiga program kan definieras och implementeras effektivt med användarflöden. Vi rekommenderar att du använder de inbyggda användarflödena om du inte har komplexa scenarier för användarfärder som kräver full flexibilitet i anpassade principer.

Läs mer om användarflöden i [användarflöden i Azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Anpassad princip

Anpassade principer låser upp åtkomsten till den fulla kraften i IEF-orchestration-motorn (Identity Experience Framework). Med anpassade principer kan du använda IEF för att skapa nästan alla autentiserings-, användarregistrerings- eller profilredigeringsupplevelser som du kan tänka dig.

Identity Experience Framework ger dig möjlighet att konstruera användarresor med valfri kombination av steg. Ett exempel:

* Federera med andra identitetsleverantörer
* Utmaningar för multifaktorautentisering från första och tredje part
* Samla in alla användarindata
* Integrera med externa system med REST API-kommunikation

Varje sådan användarfärd definieras av en princip och du kan skapa så många eller så få principer som du behöver för att aktivera den bästa användarupplevelsen för din organisation.

![Diagram som visar ett exempel på en komplex användarresa som aktiveras av IEF](media/technical-overview/custom-policy.png)

En anpassad princip definieras av flera XML-filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar anspråksschema, anspråksomvandlingar, innehållsdefinitioner, anspråksleverantörer, tekniska profiler, steg för användarfärdorkestrering och andra aspekter av identitetsupplevelsen.

Den kraftfulla flexibiliteten i anpassade principer är mest lämplig för när du behöver skapa komplexa identitetsscenarier. Utvecklare som konfigurerar anpassade principer måste definiera de betrodda relationerna i detalj för att inkludera metadataslutpunkter, exakta definitioner för anspråksutbyte och konfigurera hemligheter, nycklar och certifikat efter behov av varje identitetsprovider.

Läs mer om anpassade principer i [anpassade principer i Azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protokoll och token

Azure AD B2C stöder [OpenID Connect- och OAuth 2.0-protokollen](protocols-overview.md) för användarresor. I Azure AD B2C-implementeringen av OpenID Connect startar ditt program användarresan genom att utfärda autentiseringsbegäranden till Azure AD B2C.

Resultatet av en begäran till Azure AD B2C är en säkerhetstoken som t.ex. en [ID-token eller åtkomsttoken](tokens-overview.md). Denna säkerhetstoken definierar användarens identitet. Token tas emot från Azure AD B2C-slutpunkter som `/token` slutpunkten eller `/authorize` slutpunkten. Med dessa token kan du komma åt anspråk som kan användas för att validera en identitet och tillåta åtkomst till säkra resurser.

För externa identiteter stöder Azure AD B2C federation med alla OAuth 1.0, OAuth 2.0, OpenID Connect, SAML och WS-Fed-identitetsprovider.

![Diagram över OIDC-baserad klientapp som matas med ett SAML-baserat IdP](media/technical-overview/protocols.png)

Det föregående diagrammet visar hur Azure AD B2C kan kommunicera med olika protokoll inom samma autentiseringsflöde:

1. Det förlitande part-programmet initierar en auktoriseringsbegäran till Azure AD B2C med OpenID Connect.
1. När en användare av programmet väljer att logga in med en extern identitetsprovider som använder SAML-protokollet anropar Azure AD B2C SAML-protokollet för att kommunicera med den identitetsprovidern.
1. När användaren har slutfört inloggningsåtgärden med den externa identitetsprovidern returnerar Azure AD B2C sedan token till det förlitande part-programmet med OpenID Connect.

## <a name="application-integration"></a>Integrering av program

När en användare vill logga in på ditt program, oavsett om det är ett webb-, mobil-, skrivbords- eller ensidigt program (SPA), initierar programmet en auktoriseringsbegäran till en slutpunkt för användarflöde eller anpassad princip. Användarflödet eller den anpassade principen definierar och styr användarens upplevelse. När de slutför ett användarflöde, till exempel *registrerings- eller* inloggningsflödet, genererar Azure AD B2C en token och omdirigerar sedan användaren tillbaka till ditt program.

![Mobilapp med pilar som visar flödet mellan inloggningssidan för Azure AD B2C](media/technical-overview/app-integration.png)

Flera program kan använda samma användarflöde eller anpassade princip. Ett enda program kan använda flera användarflöden eller anpassade principer.

Om du till exempel vill logga in på ett program använder programmet *registrerings- eller* inloggningsanvändarflödet. När användaren har loggat in kanske de vill redigera sin profil, så programmet initierar en annan auktoriseringsbegäran, den här gången med hjälp av användarflödet för *profilredigering.*

## <a name="seamless-user-experiences"></a>Sömlösa användarupplevelser

I Azure AD B2C kan du skapa användarnas identitetsupplevelser så att sidorna de visas blandas sömlöst med utseendet och känslan hos ditt varumärke. Du får nästan full kontroll över HTML- och CSS-innehållet som presenteras för användarna när de går igenom programmets identitetsresor. Med den här flexibiliteten kan du upprätthålla varumärkes- och visuell konsekvens mellan ditt program och Azure AD B2C.

![Skärmbilder av registreringssida för varumärkesanpassade registreringar](media/technical-overview/seamless-ux.png)

Information om anpassning av användargränssnittet finns [i Om anpassning till användargränssnittet i Azure Active Directory B2C](customize-ui-overview.md).

## <a name="localization"></a>Lokalisering

Med språkanpassning i Azure AD B2C kan du anpassa olika språk som passar dina kunders behov. Microsoft tillhandahåller översättningar för 36 språk, men du kan också tillhandahålla egna översättningar för alla språk. Även om din upplevelse endast tillhandahålls för ett enda språk kan du anpassa all text på sidorna.

![Tre inloggningssidor med registreringsannonser som visar text i användargränssnittet på olika språk](media/technical-overview/localization.png)

Se hur lokalisering fungerar i [Språkanpassning i Azure Active Directory B2C](user-flow-language-customization.md).

## <a name="add-your-own-business-logic"></a>Lägg till din egen affärslogik

Om du väljer att använda anpassade principer kan du integrera med ett RESTful API i en användarresa för att lägga till din egen affärslogik i färden. Azure AD B2C kan till exempel utbyta data med en RESTful-tjänst till:

* Visa anpassade användarvänliga felmeddelanden.
* Verifiera användarindata för att förhindra att felaktiga data kvarstår i användarkatalogen. Du kan till exempel ändra de data som anges av användaren, till exempel att använda sitt förnamn om de har angett det i alla gemener.
* Berika användardata genom att integrera dem vidare med företagets affärsprogram.
* Med hjälp av RESTful-samtal kan du skicka push-meddelanden, uppdatera företagsdatabaser, köra en migreringsprocess för användare, hantera behörigheter, granska databaser med mera.

Bonusprogram är ett annat scenario som aktiveras av Azure AD B2C:s stöd för att anropa REST-API:er. Din RESTful-tjänst kan till exempel ta emot en användares e-postadress, fråga din kunddatabas och sedan returnera användarens förmånsnummer till Azure AD B2C. Returdata kan lagras i användarens katalogkonto i Azure AD B2C, sedan utvärderas ytterligare i efterföljande steg i principen eller inkluderas i åtkomsttoken.

![Business integration i en mobil applikation](media/technical-overview/lob-integration.png)

Du kan lägga till ett REST API-anrop när som helst i användarfärden som definieras av en anpassad princip. Du kan till exempel anropa ett REST API:

* Under inloggningen, strax innan Azure AD B2C validerar autentiseringsuppgifterna
* Omedelbart efter inloggning
* Innan Azure AD B2C skapar ett nytt konto i katalogen
* När Azure AD B2C har skapat ett nytt konto i katalogen
* Innan Azure AD B2C utfärdar en åtkomsttoken

Mer information om hur du använder anpassade principer för RESTful API-integrering i Azure AD B2C finns [i Integrera REST API-anspråksutbyten i din azure AD B2C-anpassade princip](custom-policy-rest-api-intro.md).

## <a name="protect-customer-identities"></a>Skydda kundidentiteter

Azure AD B2C uppfyller de säkerhets-, sekretess- och andra åtaganden som beskrivs i [Microsoft Azure Trust Center](https://www.microsoft.com/trustcenter/cloudservices/azure).

Sessioner modelleras som krypterade data, med dekrypteringsnyckeln som endast är känd för Azure AD B2C Security Token Service. En stark krypteringsalgoritm, AES-192, används. Alla kommunikationsvägar skyddas med TLS för sekretess och integritet. Vår security token service använder ett EV-certifikat (Extended Validation) för TLS. I allmänhet minskar security token-tjänsten XSS-attacker (cross-site scripting) genom att inte återge ej betrodda indata.

![Diagram över säkra data under transitering och i vila](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Tillgång till användardata

Azure AD B2C-klienter har många egenskaper med Azure Active Directory-klienter som används för anställda och partner. Delade aspekter omfattar mekanismer för att visa administrativa roller, tilldela roller och granska aktiviteter.

Du kan tilldela roller till kontroll som kan utföra vissa administrativa åtgärder i Azure AD B2C, inklusive:

* Skapa och hantera alla aspekter av användarflöden
* Skapa och hantera attributschemat som är tillgängligt för alla användarflöden
* Konfigurera identitetsleverantörer för användning i direkt federation
* Skapa och hantera principer för förtroenderamverk i Identity Experience Framework (anpassade principer)
* Hantera hemligheter för federation och kryptering i Identity Experience Framework (anpassade principer)

Mer information om Azure AD-roller, inklusive Azure AD B2C-administrationsrollstöd, finns [i Administratörsrollbehörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Azure AD B2C multifaktorautentisering (MFA) hjälper till att skydda åtkomsten till data och program samtidigt som enkelheten upprätthålls för användarna. Det ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering genom att erbjuda en rad lättanvända autentiseringsmetoder. Användarna kan eller inte kan ifrågasättas för MFA baserat på konfigurationsbeslut som du kan fatta som administratör.

Se hur du aktiverar MFA i användarflöden i [Aktivera multifaktorautentisering i Azure Active Directory B2C](custom-policy-multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Utelåsning av smarta konton

För att förhindra felförvaltade lösenordsgissningsförsök använder Azure AD B2C en sofistikerad strategi för att låsa konton baserat på IP-adressen för begäran, de angivna lösenorden och flera andra faktorer. Lockoutens varaktighet ökas automatiskt baserat på risk och antalet försök.

![Smart utelåsning av konto](media/technical-overview/smart-lockout1.png)

Mer information om hur du hanterar lösenordsskyddsinställningar finns i [Hantera hot mot resurser och data i Azure Active Directory B2C](threat-management.md).

### <a name="password-complexity"></a>Lösenordskomplexitet

När du registrerar dig eller återställs lösenord måste användarna ange ett lösenord som uppfyller komplexitetsreglerna. Som standard tillämpar Azure AD B2C en stark lösenordsprincip. Azure AD B2C innehåller också konfigurationsalternativ för att ange komplexitetskraven för de lösenord som dina kunder använder.

Du kan konfigurera krav på lösenordskomplexitet i både [användarflöden](user-flow-password-complexity.md) och [anpassade principer](custom-policy-password-complexity.md).

## <a name="auditing-and-logs"></a>Granskning och loggar

Azure AD B2C avger granskningsloggar som innehåller aktivitetsinformation om dess resurser, utfärdade token och administratörsåtkomst. Du kan använda dessa granskningsloggar för att förstå plattformsaktivitet och diagnostisera problem. Granskningsloggposter är tillgängliga strax efter att aktiviteten som genererade händelsen inträffar.

I en granskningslogg, som är tillgänglig för din Azure AD B2C-klient eller för en viss användare, hittar du information som:

* Aktiviteter som rör auktorisering av en användare att komma åt B2C-resurser (till exempel en administratör som har åtkomst till en lista över B2C-principer)
* Aktiviteter relaterade till katalogattribut som hämtas när en administratör loggar in med Azure-portalen
* Skapa, läsa, uppdatera och ta bort (CRUD) åtgärder på B2C-program
* CRUD-åtgärder på nycklar som lagras i en B2C-nyckelbehållare
* CRUD-åtgärder för B2C-resurser (t.ex. principer och identitetsleverantörer)
* Validering av användarautentiseringsuppgifter och tokenutfärdande

![Logg för enskild användargranskning som visas i Azure-portalen](media/technical-overview/audit-log.png)

Mer information om granskningsloggar finns i [Komma åt Granskningsloggar för Azure AD B2C](view-audit-logs.md).

### <a name="usage-insights"></a>Insikter om användning

Med Azure AD B2C kan du upptäcka när personer registrerar sig eller loggar in på din webbapp, var användarna finns och vilka webbläsare och operativsystem de använder. Genom att integrera Azure Application Insights i Azure AD B2C med hjälp av anpassade principer kan du få insikt i hur personer registrerar sig, loggar in, återställer sitt lösenord eller redigerar sin profil. Med sådan kunskap kan du fatta datadrivna beslut för dina kommande utvecklingscykler.

Läs mer om användningsanalys i [Spåra användarbeteende i Azure Active Directory B2C med hjälp av Application Insights](analytics-with-application-insights.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en djupare översikt över funktionerna och de tekniska aspekterna av Azure Active Directory B2C kan du komma igång med tjänsten genom att skapa en B2C-klient:

> [!div class="nextstepaction"]
> [Självstudiekurs: Skapa en Azure Active Directory B2C-klient >](tutorial-create-tenant.md)
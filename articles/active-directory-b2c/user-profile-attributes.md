---
title: Attribut för användarprofiler i Azure Active Directory B2C
description: Lär dig mer om attributen för användarresurstyp som stöds av användarprofilen för Azure AD B2C-katalogen. Ta reda på mer om inbyggda attribut, tillägg och hur attribut mappas till Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057296"
---
# <a name="user-profile-attributes"></a>Attribut för användarprofiler

Din Azure Active Directory (Azure AD) B2C-kataloganvändarprofil levereras med en inbyggd uppsättning attribut, till exempel förnamn, efternamn, ort, postnummer och telefonnummer. Du kan utöka användarprofilen med dina egna programdata utan att kräva ett externt datalager. De flesta attribut som kan användas med Azure AD B2C-användarprofiler stöds också av Microsoft Graph. I den här artikeln beskrivs attribut för Azure AD B2C-användarprofiler som stöds. De attribut som inte stöds av Microsoft Graph, samt Microsoft Graph-attribut som inte ska användas med Azure AD B2C, identifieras också.

> [!IMPORTANT]
> Du bör inte använda inbyggda attribut eller tilläggsattribut för att lagra känsliga personuppgifter, till exempel kontouppgifter, myndighetsidentifikationsnummer, kortinnehavarens data, finansiella kontodata, hälso- och sjukvårdsinformation eller känslig bakgrundsinformation.

Du kan också integrera med externa system. Du kan till exempel använda Azure AD B2C för autentisering, men delegera till en extern kundrelationshantering (CRM) eller kundlojalitetsdatabas som den auktoritära källan till kunddata. Mer information finns i [fjärrprofillösningen.](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)

I tabellen nedan visas de attribut för [användarresurstyp](https://docs.microsoft.com/graph/api/resources/user) som stöds av användarprofilen för Azure AD B2C-katalogen. Det ger följande information om varje attribut:

- Attributnamn som används av Azure AD B2C (följt av Microsoft Graph-namnet inom parentes, om annat)
- Attributdatatyp
- Beskrivning av attribut
- Om attributet är tillgängligt i Azure-portalen
- Om attributet kan användas i ett användarflöde
- Om attributet kan användas i en anpassad princip [Azure AD teknisk profil](active-directory-technical-profile.md) och i&gt;vilket avsnitt (InputClaims &lt;&gt;&lt;&gt;, &lt;OutputClaims eller PersistedClaims )

|Namn     |Typ     |Beskrivning|Azure Portal|Användarflöden|Anpassad princip|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Om användarkontot är aktiverat eller inaktiverat: **sant** om kontot är aktiverat, annars **falskt**.|Ja|Inga|Beständig, Utdata|
|ageGroup        |String|Användarens åldersgrupp. Möjliga värden: null, Odefinierad, Minderårig, Vuxen, NotAdult.|Ja|Inga|Beständig, Utdata|
|alternativeSecurityId ([Identiteter](manage-user-accounts-graph-api.md#identities-property))|String|En enda användaridentitet från den externa identitetsprovidern.|Inga|Inga|Ingång, beständig, utgång|
|alternativeSecurityIds ([Identiteter](manage-user-accounts-graph-api.md#identities-property))|alternativ säkerhetIdinsamling|En samling användaridentiteter från externa identitetsleverantörer.|Inga|Inga|Beständig, Utdata|
|city            |String|Den stad där användaren finns. Max längd 128.|Ja|Ja|Beständig, Utdata|
|samtyckeTillhandaladeForMinor|String|Om samtycke har lämnats för en minderårig. Tillåtna värden: null, beviljade, nekade eller ejRequired.|Ja|Inga|Beständig, Utdata|
|land         |String|Det land/den region där användaren befinner sig. Exempel: "USA" eller "Storbritannien". Max längd 128.|Ja|Ja|Beständig, Utdata|
|createdDateTime|DateTime|Det datum då användarobjektet skapades. Skrivskyddad.|Inga|Inga|Beständig, Utdata|
|creationType    |String|Om användarkontot skapades som ett lokalt konto för en Azure Active Directory B2C-klient är värdet LocalAccount eller nameCoexistence. Skrivskyddad.|Inga|Inga|Beständig, Utdata|
|datumFödelsedag     |Datum|Födelsedatum.|Inga|Inga|Beständig, Utdata|
|avdelning      |String|Namnet på den avdelning där användaren arbetar. Max längd 64.|Ja|Inga|Beständig, Utdata|
|displayName     |String|Användarens visningsnamn. Max längd 256.|Ja|Ja|Beständig, Utdata|
|faksimilTelefonAntal<sup>1</sup>|String|Telefonnumret till användarens affärsfaxmaskin.|Ja|Inga|Beständig, Utdata|
|förnamn       |String|Användarens förnamn (förnamn). Max längd 64.|Ja|Ja|Beständig, Utdata|
|jobbTitle        |String|Användarens befattning. Max längd 128.|Ja|Ja|Beständig, Utdata|
|immutableId     |String|En identifierare som vanligtvis används för användare som migreras från lokal Active Directory.|Inga|Inga|Beständig, Utdata|
|legalAgeGroupClassification|String|Juridisk åldersgrupp klassificering. Skrivskyddad och beräknad baserat på ageGroup och consentProvidedForMinor egenskaper. Tillåtna värden: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult och adult.|Ja|Inga|Beständig, Utdata|
|legalCountry<sup>1</sup>  |String|land för juridiska ändamål.|Inga|Inga|Beständig, Utdata|
|e-post            |String|SMTP-adressen för användaren, tillbob@contoso.comexempel " ". Skrivskyddad.|Inga|Inga|Beständig, Utdata|
|smeknamn för e-post    |String|Användarens e-postalias. Max längd 64.|Inga|Inga|Beständig, Utdata|
|mobil (mobiltelefon) |String|Användarens primära mobiltelefonnummer. Max längd 64.|Ja|Inga|Beständig, Utdata|
|netId (på)           |String|Net ID.|Inga|Inga|Beständig, Utdata|
|Objectid        |String|En globalt unik identifierare (GUID) som är den unika identifieraren för användaren. Exempel: 12345678-9abc-def0-1234-56789abcde. Skrivskyddad, Oföränderlig.|Skrivskyddad|Ja|Ingång, beständig, utgång|
|otherMails      |Strängsamling|En lista med ytterligare e-postadresser för användaren. Exempel: ["bob@contoso.com", "Robert@fabrikam.com"].|Ja (Alternativt e-postmeddelande)|Inga|Beständig, Utdata|
|password        |String|Lösenordet för det lokala kontot när användaren skapas.|Inga|Inga|Framhärdade|
|lösenordPolicies     |String|Principen för lösenordet. Det är en sträng som består av ett annat principnamn avgränsat med kommatecken. dvs "DisablePasswordExpiration, DisableStrongPassword".|Inga|Inga|Beständig, Utdata|
|physicalDeliveryOfficeName (officeLocation)|String|Kontorsplatsen på användarens verksamhetsställe. Max längd 128.|Ja|Inga|Beständig, Utdata|
|Postnummer      |String|Postnumret för användarens postadress. Postnumret är specifikt för användarens land/region. I USA innehåller det här attributet postnumret. Max längd 40.|Ja|Inga|Beständig, Utdata|
|preferredLanguage    |String|Det språk som användaren föredrar. Bör följa ISO 639-1 Code. Exempel: "en-US".|Inga|Inga|Beständig, Utdata|
|uppdateraTokensValidFrånDateTime|DateTime|Alla uppdateringstoken som utfärdas före den här tiden är ogiltiga och program får ett felmeddelande när du använder en ogiltig uppdateringstoken för att hämta en ny åtkomsttoken. Om detta inträffar måste programmet hämta en ny uppdateringstoken genom att göra en begäran till den auktorisera slutpunkten. Skrivskyddad.|Inga|Inga|Resultat|
|signInNames ([Identiteter](manage-user-accounts-graph-api.md#identities-property)) |String|Det unika inloggningsnamnet för den lokala kontoanvändaren av alla typer i katalogen. Använd detta för att hämta en användare med inloggningsvärde utan att ange den lokala kontotypen.|Inga|Inga|Indata|
|signInNames.userName ([Identiteter](manage-user-accounts-graph-api.md#identities-property)) |String|Det unika användarnamnet för den lokala kontoanvändaren i katalogen. Använd detta för att skapa eller hämta en användare med ett specifikt inloggningsanvändarnamn. Om du anger detta enbart i PersistedClaims under korrigeringsåtgärden tas andra typer av signInNames bort. Om du vill lägga till en ny typ av signInNames måste du också spara befintliga signInNames.|Inga|Inga|Ingång, beständig, utgång|
|signInNames.phoneNumber ([Identiteter](manage-user-accounts-graph-api.md#identities-property)) |String|Det unika telefonnumret för den lokala kontoanvändaren i katalogen. Använd detta för att skapa eller hämta en användare med ett specifikt inloggningstelefonnummer. Om du anger detta enbart i PersistedClaims under korrigeringsåtgärden tas andra typer av signInNames bort. Om du vill lägga till en ny typ av signInNames måste du också spara befintliga signInNames.|Inga|Inga|Ingång, beständig, utgång|
|signInNames.emailAddress ([Identiteter](manage-user-accounts-graph-api.md#identities-property))|String|Den unika e-postadressen för den lokala kontoanvändaren i katalogen. Använd detta för att skapa eller hämta en användare med en specifik e-postadress för inloggning. Om du anger detta enbart i PersistedClaims under korrigeringsåtgärden tas andra typer av signInNames bort. Om du vill lägga till en ny typ av signInNames måste du också spara befintliga signInNames.|Inga|Inga|Ingång, beständig, utgång|
|state           |String|Staten eller provinsen i användarens adress. Max längd 128.|Ja|Ja|Beständig, Utdata|
|streetAddress (streetAddress)   |String|Gatuadressen till användarens verksamhetsställe. Max längd 1024.|Ja|Ja|Beständig, Utdata|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|Användarens sekundära telefonnummer, som används för multifaktorautentisering.|Ja|Inga|Beständig, Utdata|
|strongAuthenticationEmailAddress<sup>1</sup>|String|SMTP-adressen för användaren. Exempel:bob@contoso.com" " Det här attributet används för inloggning med användarnamnspolicy för att lagra användarens e-postadress. E-postadressen som sedan används i ett flöde för återställning av lösenord.|Ja|Inga|Beständig, Utdata|
|starkAuthenticationPhoneNummer<sup>1</sup>|String|Användarens primära telefonnummer, som används för multifaktorautentisering.|Ja|Inga|Beständig, Utdata|
|surname         |String|Användarens efternamn (efternamn eller efternamn). Max längd 64.|Ja|Ja|Beständig, Utdata|
|telephoneNumber (första posten i businessPhones)|String|Det primära telefonnumret till användarens verksamhetsställe.|Ja|Inga|Beständig, Utdata|
|userPrincipalName    |String|Användarens huvudnamn (UPN). UPN är ett inloggningsnamn i Internet-stil för användaren baserat på Internet-standarden RFC 822. Domänen måste finnas i klientens samling av verifierade domäner. Den här egenskapen krävs när ett konto skapas. Oföränderliga.|Inga|Inga|Ingång, beständig, utgång|
|användningLokalisering   |String|Krävs för användare som kommer att tilldelas licenser på grund av lagstadgade krav för att kontrollera om det finns tjänster i länder. Kan inte nullable. Landskod med två bokstäver (ISO-standard 3166). Exempel: "US", "JP" och "GB".|Ja|Inga|Beständig, Utdata|
|userType (användare)        |String|Ett strängvärde som kan användas för att klassificera användartyper i katalogen. Värdet måste vara medlem. Skrivskyddad.|Skrivskyddad|Inga|Beständig, Utdata|
|userState (externUserState)<sup>2</sup>|String|För Azure AD B2B-konto anger du om inbjudan är väntande accepterad eller accepterad.|Inga|Inga|Beständig, Utdata|
|userStateChangedOn (externtUserStateChangeDateTime)<sup>2</sup>|DateTime|Visar tidsstämpeln för den senaste ändringen av egenskapen UserState.|Inga|Inga|Beständig, Utdata|
|<sup>1.</sup> Stöds inte av Microsoft Graph<br><sup>2.</sup> Bör inte användas med Azure AD B2C||||||


## <a name="extension-attributes"></a>Tilläggsattribut

Du behöver ofta skapa egna attribut, som i följande fall:

- Ett kundvänt program måste finnas kvar för ett attribut som **LoyaltyNumber**.
- En identitetsprovider har en unik användaridentifierare som **uniqueUserGUID** som måste sparas.
- En anpassad användarfärd måste finnas kvar för en användares tillstånd, till exempel **migrationStatus**.

Azure AD B2C utökar uppsättningen attribut som lagras på varje användarkonto. Tilläggsattribut [utökar schemat för](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) användarobjekten i katalogen. Tilläggsattributen kan bara registreras på ett programobjekt, även om de kan innehålla data för en användare. Tilläggets attribut är kopplat till programmet som kallas b2c-extensions-app. Ändra inte det här programmet eftersom det används av Azure AD B2C för lagring av användardata. Du hittar det här programmet under Azure Active Directory App registreringar.

> [!NOTE]
> - Upp till 100 tilläggsattribut kan skrivas till vilket användarkonto som helst.
> - Om programmet b2c-extensions-app tas bort tas dessa tilläggsattribut bort från alla användare tillsammans med alla data som de innehåller.
> - Om ett tilläggsattribut tas bort av programmet tas det bort från alla användarkonton och värdena tas bort.
> - Det underliggande namnet på tilläggets attribut genereras i formatet "Extension_" + Program-ID + "_" + Attributnamn. Om du till exempel skapar ett tilläggsattribut LoyaltyNumber och program-ID:t för b2c-tillägg-appen är 831374b3-bd50-41bf-aa54-263ec9e050fc, blir det underliggande tilläggets attributnamn: extension_831374b3bd5041bfaa54263ec9e050fc_ Lojalitetsantal. Du använder det underliggande namnet när du kör Graph API-frågor för att skapa eller uppdatera användarkonton.

Följande datatyper stöds när du definierar en egenskap i ett schematillägg:

|Egenskapstyp |Anmärkningar  |
|--------------|---------|
|Boolean    | Möjliga värden: **sant** eller **falskt**. |
|DateTime   | Måste anges i ISO 8601-format. Kommer att lagras i UTC.   |
|Integer    | 32-bitarsvärde.               |
|String     | Högst 256 tecken.     |

## <a name="next-steps"></a>Nästa steg
Läs mer om tilläggsattribut:
- [Schematillägg](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definiera anpassade attribut med användarflöde](user-flow-custom-attributes.md)
- [Definiera anpassade attribut med anpassad princip](custom-policy-custom-attributes.md)

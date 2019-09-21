---
title: Konfigurera grupp anspråk för program med Azure Active Directory | Microsoft Docs
description: Information om hur du konfigurerar grupp anspråk för användning med Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170485"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Konfigurera grupp anspråk för program med Azure Active Directory (offentlig för hands version)

Azure Active Directory kan ge en användare grupp medlemskaps information i tokens för användning i program.  Två huvud mönster stöds:

- Grupper som identifieras av sina Azure Active Directory objekt identifierare (OID)-attribut (allmänt tillgängliga)
- Grupper som identifieras av sAMAccountName eller GroupSID-attribut för Active Directory (AD) synkroniserade grupper och användare (offentlig för hands version)

> [!IMPORTANT]
> Det finns ett antal varningar att notera för den här för hands versions funktionen:
>
>- Stöd för användning av sAMAccountName-och säkerhets identifierare (SID) attribut som synkroniseras från lokalt är utformat för att kunna flytta befintliga program från AD FS och andra identitets leverantörer. Grupper som hanteras i Azure AD innehåller inte de attribut som krävs för att generera dessa anspråk.
>- I större organisationer kan antalet grupper som en användare är medlem i överskrida gränsen som Azure Active Directory kommer att läggas till i en token. 150 grupper för en SAML-token och 200 för ett JWT. Detta kan leda till oväntade resultat. Om det här är ett möjligt problem rekommenderar vi att du testar och om nödvändigt väntar tills vi lägger till förbättringar som gör att du kan begränsa anspråken till relevanta grupper för programmet.  
>- För ny program utveckling, eller i de fall där programmet kan konfigureras för det, och där stöd för kapslade grupper inte krävs, rekommenderar vi att autentisering i appar baseras på program roller i stället för grupper.  Detta begränsar den mängd information som måste gå till token, är säkrare och skiljer användar tilldelningen från App-konfigurationen.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Gruppera anspråk för program som migrerar från AD FS och andra identitets leverantörer

Många program som har kon figurer ATS för att autentiseras med AD FS förlitar sig på grupp medlemskaps information i form av Windows AD Group-attribut.   De här attributen är gruppen sAMAccountName, som kan vara kvalificerade efter domän namn eller Windows-gruppens säkerhets identifierare (GroupSID).  När programmet är federerad med AD FS, AD FS använder funktionen TokenGroups för att hämta grupp medlemskap för användaren.

För att matcha token som en app skulle ta emot från AD FS kan grupp-och roll anspråk genereras som innehåller domänens kvalificerade sAMAccountName i stället för gruppens Azure Active Directory objectID.

De format som stöds för grupp anspråk är:

- **Azure Active Directory grupp ObjectID** (Tillgängligt för alla grupper)
- **sAMAccountName** (Tillgängligt för grupper som synkroniserats från Active Directory)
- **NetbiosDomain\sAMAccountName** (Tillgängligt för grupper som synkroniserats från Active Directory)
- **DNSDomainName\sAMAccountName** (Tillgängligt för grupper som synkroniserats från Active Directory)
- **Säkerhets identifierare för lokal grupp** (Tillgängligt för grupper som synkroniserats från Active Directory)

> [!NOTE]
> SID-attribut för sAMAccountName och lokala grupper är bara tillgängliga för grupp objekt som har synkroniserats från Active Directory.   De är inte tillgängliga på grupper som skapats i Azure Active Directory eller Office365.   Program som kon figurer ATS i Azure Active Directory för att hämta synkroniserade lokala Gruppattribut får bara till synkroniserade grupper.

## <a name="options-for-applications-to-consume-group-information"></a>Alternativ för program att använda grupp information

Ett sätt för program att hämta grupp information är att anropa slut punkten för graf-grupper för att hämta grupp medlemskap för den autentiserade användaren. Det här anropet säkerställer att alla grupper som en användare är medlem i är tillgängliga även när det finns ett stort antal grupper som är inblandade och programmet måste räkna upp alla grupper som användaren är medlem i.  Grupp uppräkning är sedan oberoende av storleks begränsningarna för token.

Men om ett befintligt program redan förväntar sig att använda grupp information via anspråk i den token som den tar emot, kan Azure Active Directory konfigureras med ett antal olika anspråks alternativ som passar programmets behov.  Överväg följande alternativ:

- När du använder grupp medlemskap för auktorisering i program är det bättre att använda gruppen ObjectID, som är oföränderligt och unikt i Azure Active Directory och är tillgängligt för alla grupper.
- Om du använder den lokala gruppens sAMAccountName för auktorisering använder du kvalificerade domän namn.  Det finns mindre risk för situationer som uppstår i konflikt med namn. sAMAccountName på egen hand kan vara unikt inom en Active Directory domän, men om mer än en Active Directory domän är synkroniserad med en Azure Active Directory-klient finns det en möjlighet för fler än en grupp att ha samma namn.
- Överväg att använda [program roller](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) för att tillhandahålla ett lager av indirigering mellan grupp medlemskapet och programmet.   Programmet fattar sedan interna auktoriseringsbeslut baserat på roll clams i token.
- Om programmet har kon figurer ATS för att hämta Gruppattribut som synkroniseras från Active Directory och en grupp inte innehåller de attributen tas de inte med i anspråken.
- Grupp anspråk i token inkluderar kapslade grupper.   Om en användare är medlem i GroupB och GroupB är medlem i Groupa, kommer grupp anspråk för användaren att innehålla både Groupa och GroupB. För organisationer med tung användning av kapslade grupper och användare med ett stort antal grupp medlemskap kan antalet grupper i token växa.   Azure Active Directory begränsar antalet grupper som den genererar i en token till 150 för SAML-kontroller och 200 för JWT för att förhindra att token får för stora.  Om en användare är medlem i ett större antal grupper än gränsen, genereras grupperna tillsammans med en länk till diagrammets slut punkt för att hämta grupp information.

> Krav för att använda Gruppattribut synkroniserade från Active Directory:   Grupperna måste synkroniseras från Active Directory med Azure AD Connect.

Det finns två steg för att konfigurera Azure Active Directory att generera grupp namn för Active Directory grupper.

1. **Synkronisera grupp namn från Active Directory** Innan Azure Active Directory kan generera grupp namn eller lokalt grupp-SID i grupp-eller roll anspråk måste de attribut som krävs synkroniseras från Active Directory.  Du måste köra Azure AD Connect version 1.2.70 eller senare.   Före version 1.2.70 Azure AD Connect synkroniseras grupp objekt från Active Directory, men innehåller inte de obligatoriska attributen för grupp namn som standard.  Du bör uppgradera till den aktuella versionen.

2. **Konfigurera program registreringen i Azure Active Directory att inkludera grupp anspråk i token** Grupp anspråk kan konfigureras i avsnittet företags program i portalen för ett galleri eller icke-Galleri för SAML SSO-program, eller med hjälp av applikations manifestet i avsnittet program registreringar.  Om du vill konfigurera grupp anspråk i program manifestet, se "Konfigurera Azure Active Directory program registrering för Gruppattribut" nedan.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Konfigurera grupp anspråk för SAML-program med SSO-konfiguration

Om du vill konfigurera grupp anspråk för ett galleri eller ett icke-Galleri SAML-program öppnar du företags program, klickar på programmet i listan och väljer enkel inloggnings konfiguration.

Välj redigerings ikonen bredvid "grupper som returneras i token"

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Använd alternativ knapparna för att välja vilka grupper som ska ingå i token

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Val | Beskrivning |
|----------|-------------|
| **Alla grupper** | Skapar säkerhets grupper och distributions listor.   Det leder också till att katalog roller som användaren har tilldelats genereras i ett ' wids '-anspråk och alla program roller som användaren är tilldelad för att genereras i rollerna. |
| **Säkerhets grupper** | Genererar säkerhets grupper som användaren är medlem i i gruppen grupp anspråk |
| **Distributions listor** | Genererar distributions listor som användaren är medlem i |
| **Katalog roller** | Om användaren har tilldelats katalog roller genereras de som ett ' wids '-anspråk (grupp anspråk genereras inte) |

Om du till exempel vill generera alla säkerhets grupper som användaren är medlem i väljer du säkerhets grupper

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Om du vill generera grupper med hjälp av Active Directory attribut som synkroniserats från Active Directory i stället för Azure AD objectIDs väljer du det format som krävs från List rutan.  Detta ersätter objekt-ID i anspråken med sträng värden som innehåller grupp namn.   Endast grupper som synkroniseras från Active Directory tas med i anspråken.

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>Avancerade alternativ

Hur grupp anspråk genereras kan ändras av inställningarna under avancerade alternativ

Anpassa namnet på grupp anspråket:  Om det här väljs kan en annan anspråks typ anges för grupp anspråk.   Ange anspråks typ i fältet namn och det valfria namn området för anspråket i fältet namn område.

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Vissa program kräver att grupp medlemskaps informationen visas i "roll"-anspråket. Du kan välja att generera användarens grupper som roller genom att markera rutan generera grupper av en roll anspråk.

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Om alternativet att generera grupp data som roller används, visas endast grupper i roll anspråket.  Alla program roller som användaren är tilldelad visas inte i roll anspråket.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurera Azure AD Application registration för Gruppattribut

Grupp anspråk kan också konfigureras i avsnittet [valfria anspråk](../../active-directory/develop/active-directory-optional-claims.md) i [applikations manifestet](../../active-directory/develop/reference-app-manifest.md).

1. I portalen-> Azure Active Directory-> program registreringar – > Välj program-> manifest

2. Aktivera grupp medlemskaps anspråk genom att ändra groupMembershipClaim

   Giltiga värden är:

   - Vissa
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Exempel:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Som standard genereras grupp ObjectIDs i grupp anspråks värdet.  Om du vill ändra anspråks värdet så att det innehåller lokala Gruppattribut, eller ändra anspråks typen till roll, använder du OptionalClaims-konfiguration enligt följande:

3. Ange alternativ för grupp namns konfiguration valfria anspråk.

   Om du vill att grupperna i token ska innehålla attributen för lokal AD-grupp i det valfria anspråks avsnittet anger du vilken tokentyp som valfritt anspråk ska tillämpas på, namnet på valfritt begärt anspråk och eventuella ytterligare egenskaper som önskas.  Flera typer av token kan visas:

   - idToken för OIDC-ID-token
   - accessToken för OAuth/OIDC-åtkomsttoken
   - Saml2Token för SAML-token.

   > [!NOTE]
   > Saml2Token-typen gäller för både SAML 1.1 och SAML 2.0-format-token

   För varje relevant tokentyp ändrar du grupp anspråk till att använda OptionalClaims-avsnittet i manifestet. OptionalClaims-schemat ser ut så här:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Valfritt anspråks schema | Value |
   |----------|-------------|
   | **Namn:** | Måste vara "grupper" |
   | **källicensservern** | Används inte. Utelämna eller ange null |
   | **största** | Används inte. Utelämna eller ange falskt |
   | **additionalProperties:** | Lista över ytterligare egenskaper.  Valid options are "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name”, "emit_as_roles" |

   I additionalProperties krävs bara en av "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Om det finns fler än en används den första och andra ignoreras.

   Vissa program kräver grupp information om användaren i roll anspråket.  Om du vill ändra anspråks typen till från ett grupp anspråk till ett roll anspråk lägger du till "emit_as_roles" i ytterligare egenskaper.  Grupp värden genereras i roll anspråket.

   > [!NOTE]
   > Om "emit_as_roles" används alla program roller som kon figurer ATS som användaren är tilldelad visas inte i roll anspråket

### <a name="examples"></a>Exempel

Generera grupper som grupp namn i OAuth-åtkomsttoken i dnsDomainName\SAMAccountName-format

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Så här genererar du grupp namn som ska returneras i netbiosDomain\samAccountName-format som roll anspråk i SAML-och OIDC-ID-token:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Nästa steg

[Vad är hybrididentitet?](whatis-hybrid-identity.md)

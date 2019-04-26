---
title: Konfigurera gruppanspråk för program med Azure Active Directory | Microsoft Docs
description: Information om hur du konfigurerar gruppanspråk för användning med Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.component: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 622a3ce0f80bd09bd09fa7ff097f68155318142d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351297"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Konfigurera gruppanspråk för program med Azure Active Directory (förhandsversion)

Azure Active Directory kan ge en användarinformation om gruppmedlemskap i token för användning i program.  Två huvudsakliga mönster stöds:

- Grupper som identifieras av sina Azure Active Directory-objektidentifierare (OID) (allmänt tillgänglig)
- Grupper som identifieras av SAMAccountName eller GroupSID för Active Directory (AD) synkroniseras grupper och användare (offentlig förhandsversion)

> [!Note]
> Stöd för användning av namn och en lokal säkerhetsidentifierare (SID) är satt så att flytta befintliga program från AD FS.    Grupper som hanteras i Azure AD innehåller inte attribut som krävs för att generera dessa anspråk.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-idps"></a>Gruppanspråk för program som migrerar från AD FS och andra IDP: er

Många program som är konfigurerade för att autentisera med AD FS är beroende av information om gruppmedlemskap i form av attributen för Windows AD.   Dessa attribut är gruppen SAMAccountName, vilket kan vara av-domännamnet eller Windows-grupp-SID.  När programmet är federerad med AD FS använder AD FS TokenGroups-funktion för att hämta gruppmedlemskap för användaren.

För att matcha den token som en app skulle ta emot från AD FS anspråk för gruppen och rollen kan genereras som innehåller domänen kvalificerade SAMAccountName i stället för gruppens Azure Active Directory objectID.

Format som stöds för gruppanspråk är:

- **Azure Active Directory-GroupObjectId** (tillgänglig för alla grupper)
- **SAMAccountName** (tillgänglig för grupper som synkroniseras från Active Directory)
- **NetbiosDomain\samAccountName** (tillgänglig för grupper som synkroniseras från Active Directory)
- **DNSDomainName\samAccountName** (tillgänglig för grupper som synkroniseras från Active Directory)

> [!NOTE]
> SAMAccountName och OnPremisesGroupSID attribut är bara tillgängliga på grupp-objekt som synkroniseras från Active Directory.   De är inte tillgängliga på grupper som skapats i Azure Active Directory eller Office 365.   Program som är beroende av den lokala Gruppattribut hämta dem för synkroniserade grupper.

## <a name="options-for-applications-to-consume-group-information"></a>Alternativ för program kan använda gruppinformation

Ett sätt för program att hämta information om är att anropa Graph grupper slutpunkten för att hämta gruppmedlemskap för autentiserade användare. Det här anropet garanterar att alla grupper som en användare är medlem i är tillgängliga även om det finns ett stort antal grupper ingår och programmet måste räkna upp alla grupper som användaren är medlem i.  Gruppen-uppräkningen är sedan oberoende av storleksbegränsningar för token.

Men om ett befintligt program redan förväntar sig att använda gruppinformation via anspråken i token som tas emot, kan Azure Active Directory konfigureras med ett antal olika anspråk alternativ efter behov av programmet.  Överväg följande alternativ:

- När du använder gruppmedlemskap för ett visst syfte för auktorisering (om medlemskap erhålls från token eller från Graph), är det bättre att använda gruppen ObjectID, vilket är oföränderligt och unikt i Azure Active Directory och är tillgängliga för alla grupper .
- Om använder gruppen SAMAccountName för auktorisering Använd domän kvalificerade namn.  Det har mindre risken för situationer som uppstår till följd har namn som krockar. SAMAccountName på egen hand kan vara unika inom en Active Directory-domän, men om fler än en Active Directory-domän synkroniseras med en Azure Active Directory-klient är möjligheten för mer än en grupp har samma namn.
- Överväg att använda [programroller](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) att tillhandahålla ett lager av trick mellan gruppmedlemskapet och programmet.   Sedan gör interna auktoriseringsbeslut baserat på rollen venusmusslor i token.
- Om programmet är konfigurerat för att hämta attributen som synkroniseras från Active Directory och en grupp innehåller inte de attribut som inte tas med i anspråken.
- Gruppanspråk i token innehålla kapslade grupper.   Om en användare är medlem i GroupB och GroupB är medlem i GroupA, innehåller gruppanspråk för användaren både GroupA och GroupB. Antalet grupper som visas i token kan växa token storleken för organisationer med stor förbrukning av kapslade grupper och användare med stort antal gruppmedlemskap.   Azure Active Directory begränsar antalet grupper som den genererar i en SAML intyg 150 och 200 för JWT-token.

> Krav för att använda Gruppattribut som synkroniseras från Active Directory:   Grupperna som måste synkroniseras från Active Directory med Azure AD Connect.

Det finns två steg för att konfigurera Azure Active Directory för att generera gruppnamn för Active Directory-grupper.

1. **Synkronisera gruppnamn från Active Directory** innan Azure Active Directory kan generera namnen på eller på plats grupp SID i gruppen eller rollen anspråk, obligatoriska attribut måste synkroniseras från Active Directory.  Du måste köra Azure AD Connect version 1.2.70 eller senare.   Före version 1.2.70 Azure AD Connect synkroniserar th gruppobjekt från Active Directory men omfattar inte de nödvändiga gruppen namnattribut som standard.  Du bör uppgradera till den aktuella versionen.

2. **Konfigurera programmet registreringen i Azure Active Directory för att inkludera gruppanspråk i token** gruppanspråk kan vara konfigurerad i företagsprogram i portalen för ett galleri eller icke-galleriet SAML SSO-program, eller med hjälp av applikationsmanifestet i avsnittet Programregistreringar.  Att konfigurera gruppanspråk i application manifest ser ”Konfigurera Azure Active Directory programregistrering för gruppattributen” nedan.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Konfigurera gruppanspråk för SAML-program som använder SSO-konfiguration

Om du vill konfigurera gruppanspråk för ett galleri eller icke-galleriet SAML-program, öppna företagsprogram, klicka på programmet i listan och välj Single Sign On konfiguration.

Välj Redigera-ikonen bredvid ”grupper returneras i token”

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Använd alternativknapparna för att välja vilka grupper som ska ingå i token

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Val | Beskrivning |
|----------|-------------|
| **Alla grupper** | Genererar säkerhetsgrupper och distribution visas.   Det gör även Directory-roller som användaren har tilldelats genereras i ett ”wids”-anspråk och eventuella programroller som användaren har tilldelats genereras i rollanspråk. |
| **Säkerhetsgrupper** | Genererar säkerhetsgrupper som användaren är medlem i grupperna anspråket |
| **Distributionslista** | Genererar distributionslistor som användaren är medlem i |
| **Katalogroll** | Om användaren har tilldelats katalogroller genereras de som en wids anspråk (grupper anspråk inte genereras) |

Välj exempelvis säkerhetsgrupper för att skapa de säkerhetsgrupper som användaren är medlem i

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

### <a name="advanced-options"></a>Avancerade alternativ

Hur gruppanspråk genereras kan ändras av inställningarna under avancerade alternativ

Anpassa grupp anspråkets namn:  Om en annan Anspråkstyp kan anges för gruppanspråk.   Ange typ av anspråk i fältet namn och valfri namnområdet för anspråk i fältet namnområde.

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

För att skapa grupper med hjälp av Active Directory attribut i stället för Azure AD objectIDs kryssrutan ”returnerar grupper som visas i stället för ID: N” och väljer formatet från listrutan.  Det här ersätter objekt-ID i anspråken strängvärden som innehåller gruppnamn.   Endast de grupper som synkroniseras från Active Directory inkluderas i anspråken.

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Vissa program kräver information ska visas i rollanspråk gruppmedlemskap. Du kan eventuellt sända de användargrupper som roller genom att markera kryssrutan ”Skapa grupper som en roll anspråk”.

![anspråk UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Om alternativet för att generera Gruppdata som roller används, visas endast grupper i rollen anspråket.  Alla roller för programmet som användaren har tilldelats visas inte i rollen anspråket.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurera Azure AD-programregistrering för Gruppattribut

Gruppanspråk kan också konfigureras i den [valfria anspråk](../../active-directory/develop/active-directory-optional-claims.md) delen av den [applikationsmanifestet](../../active-directory/develop/reference-app-manifest.md).

1. I portalen Azure Active Directory -> program -> registreringar -> Välj program -> Manifest

2. Aktivera medlemskap gruppanspråk genom att ändra groupMembershipClaim

   Giltiga värden är:

   - ”Alla”
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Exempel:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Som standard grupp ObjectIDs ska genereras i gruppen anspråksvärde.  Om du vill ändra anspråkets värde ska innehålla på attributen för plats eller ändra Anspråkstypen roll, använder du OptionalClaims konfigurationen på följande sätt:

3. Ange grupp configuration valfria anspråk.

   Om du vill grupper i token som innehåller den lokala AD-Gruppattribut i avsnittet valfria anspråk anger vilken typ tokenu valfria anspråk ska tillämpas på, namnet på valfria anspråk som begärs och eventuella ytterligare egenskaper som önskade.  Flera typer av token kan visas:

   - idToken för OIDC-ID-token
   - accessToken för den OAuth/OIDC åtkomst-token
   - Saml2Token för SAML-token.

   > [!NOTE]
   > Typen Saml2Token gäller både SAML1.1 och SAML2.0 format-token

   För varje relevant tokentypen ändra grupper anspråk att använda OptionalClaims-avsnitt i manifestet. OptionalClaims schemat är följande:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Valfria anspråk Schema | Värde |
   |----------|-------------|
   | **Namn:** | Måste vara ”grupper” |
   | **Källa:** | Används inte. Utelämna eller ange null |
   | **essential:** | Används inte. Utelämna eller ange false |
   | **additionalProperties:** | Lista över ytterligare egenskaper.  Valid options are "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name”, "emit_as_roles" |

   In additionalProperties only one of "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name” are required.  Om mer än en sådan finns, används först och andra ignoreras.

   Vissa program kräver gruppinformation om användaren i rollen anspråket.  Om du vill ändra typ av anspråk till anspråk till en rollanspråk från en grupp att lägga till ”emit_as_roles” till ytterligare egenskaper.  Gruppvärden ska genereras i rollen anspråket.

   > [!NOTE]
   > Om du använder ”emit_as_roles” alla roller för program som konfigurerats att användaren har tilldelats kommer visas inte i rollanspråk

### <a name="examples"></a>Exempel

Skapa grupper som gruppen i OAuth-åtkomsttoken i dnsDomainName\SAMAccountName format

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Att skapa gruppnamn som ska returneras i netbiosDomain\samAccountName format som rollerna anspråk i SAML- och OIDC ID-token:

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
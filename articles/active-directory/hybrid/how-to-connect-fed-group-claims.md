---
title: Konfigurera gruppanspråk för program med Azure Active Directory | Microsoft-dokument
description: Information om hur du konfigurerar gruppanspråk för användning med Azure AD.
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
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408410"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Konfigurera gruppanspråk för program med Azure Active Directory (offentlig förhandsversion)

Azure Active Directory kan tillhandahålla en information om användare gruppmedlemskap i token för användning i program.  Två huvudmönster stöds:

- Grupper som identifieras av deras Azure Active Directory-objektidentifiering (OID) attribut (allmänt tillgängliga)
- Grupper som identifieras av sAMAccountName- eller GroupSID-attribut för Active Directory -synkroniserade grupper och användare (offentlig förhandsversion)

> [!IMPORTANT]
> Det finns ett antal varningar att notera för den här förhandsgranskningsfunktionen:
>
>- Stöd för användning av sid-attribut (sAMAccountName och security identifier) som synkroniserats från lokala objekt är utformat för att möjliggöra flyttning av befintliga program från AD FS och andra identitetsleverantörer. Grupper som hanteras i Azure AD innehåller inte de attribut som krävs för att avge dessa anspråk.
>- I större organisationer kan antalet grupper som en användare är medlem i överskrida den gräns som Azure Active Directory lägger till i en token. 150 grupper för en SAML-token och 200 för en JWT. Detta kan leda till oförutsägbara resultat. Om användarna har ett stort antal gruppmedlemskap rekommenderar vi att du använder alternativet för att begränsa de grupper som avges i anspråk till relevanta grupper för programmet.  
>- För ny programutveckling, eller i fall där programmet kan konfigureras för det, och där kapslad gruppstöd inte krävs, rekommenderar vi att behörigheten i appen baseras på programroller i stället för grupper.  Detta begränsar mängden information som behöver gå in i token, är säkrare och separerar användartilldelning från appkonfiguration.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Gruppanspråk för ansökningar som migrerar från AD FS och andra identitetsleverantörer

Många program som konfigurerats för att autentisera med AD FS är beroende av information om gruppmedlemskap i form av Windows AD-gruppattribut.   Dessa attribut är gruppen sAMAccountName, som kan kvalificeras efter domännamn, eller Windows Group Security Identifier (GroupSID).  När programmet är federerat med AD FS använder AD FS funktionen TokenGroups för att hämta gruppmedlemskapen för användaren.

En app som har flyttats från AD FS behöver anspråk i samma format. Grupp- och rollanspråk kan skickas från Azure Active Directory som innehåller domänkvalificerat sAMAccountName eller GroupSID som synkroniserats från Active Directory i stället för gruppens Azure Active Directory-objectID.

Formaten som stöds för gruppanspråk är:

- **Azure Active Directory Group ObjectId** (tillgängligt för alla grupper)
- **sAMAccountName** (Tillgängligt för grupper synkroniserade från Active Directory)
- **NetbiosDomain\sAMAccountName** (Tillgängligt för grupper synkroniserade från Active Directory)
- **DNSDomainName\sAMAccountName** (Tillgängligt för grupper som synkroniserats från Active Directory)
- **I säkerhetsidentifierare** för lokal grupp (tillgänglig för grupper synkroniserade från Active Directory)

> [!NOTE]
> sid-attribut för sAMAccountName och On Premises Group är endast tillgängliga i gruppobjekt som synkroniserats från Active Directory.   De är inte tillgängliga för grupper som skapats i Azure Active Directory eller Office365.   Program som konfigurerats i Azure Active Directory för att synkronisera lokala gruppattribut får dem endast för synkroniserade grupper.

## <a name="options-for-applications-to-consume-group-information"></a>Alternativ för program att använda gruppinformation

Program kan anropa slutpunkten MS Graph-grupper för att hämta gruppinformation för den autentiserade användaren. Det här anropet säkerställer att alla grupper som en användare är medlem i är tillgängliga även när det finns ett stort antal grupper inblandade.  Gruppuppräkning är sedan oberoende av begränsningar för tokenstorlek.

Men om ett befintligt program förväntar sig att använda gruppinformation via anspråk kan Azure Active Directory konfigureras med ett antal olika anspråksformat.  Tänk på följande alternativ:

- När du använder gruppmedlemskap för auktorisering i program är det att föredra att använda Grupp ObjectID. GruppobjektID är oföränderligt och unikt i Azure Active Directory och är tillgängligt för alla grupper.
- Om du använder den lokala gruppen sAMAccountName för auktorisering använder du domänkvalificerade namn.  Det finns mindre chans att namn kolliderar. sAMAccountName kan vara unikt inom en Active Directory-domän, men om mer än en Active Directory-domän synkroniseras med en Azure Active Directory-klient finns det en möjlighet för mer än en grupp att ha samma namn.
- Överväg att använda [programroller](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) för att tillhandahålla ett lager av indirection mellan gruppmedlemskapet och programmet.   Programmet fattar sedan interna auktoriseringsbeslut baserat på rollmusslor i token.
- Om programmet är konfigurerat för att hämta gruppattribut som synkroniseras från Active Directory och en grupp inte innehåller dessa attribut inkluderas det inte i anspråken.
- Gruppanspråk i token inkluderar kapslade grupper utom när du använder alternativet att begränsa gruppanspråken till grupper som tilldelats programmet.  Om en användare är medlem i GroupB och GroupB är medlem i GroupA, kommer gruppanspråken för användaren att innehålla både GroupA och GroupB. När en organisations användare har ett stort antal gruppmedlemskap kan antalet grupper som anges i token öka tokenstorleken.  Azure Active Directory begränsar antalet grupper som den kommer att avge i en token till 150 för SAML-påståenden och 200 för JWT.  Om en användare är medlem i ett större antal grupper utelämnas grupperna och en länk till graph-slutpunkten för att hämta gruppinformation inkluderas i stället.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Förutsättningar för att använda gruppattribut synkroniserade från Active Directory

Gruppmedlemskapsanspråk kan skickas ut i token för valfri grupp om du använder ObjectId-formatet. Om du vill använda gruppanspråk i andra format än gruppen ObjectId måste grupperna synkroniseras från Active Directory med Azure AD Connect.

Det finns två steg för att konfigurera Azure Active Directory för att avge gruppnamn för Active Directory-grupper.

1. **Synkronisera gruppnamn från Active Directory** Innan Azure Active Directory kan avge gruppnamnen eller lokalgrupp SID i grupp- eller rollanspråk måste de nödvändiga attributen synkroniseras från Active Directory.  Du måste köra Azure AD Connect version 1.2.70 eller senare.   Tidigare versioner av Azure AD Connect än 1.2.70 synkroniserar gruppobjekten från Active Directory, men kommer inte att innehålla de obligatoriska gruppnamnsattributen.  Uppgradera till den aktuella versionen.

2. **Konfigurera programregistreringen i Azure Active Directory så att gruppanspråk inkluderas i token** Gruppanspråk kan konfigureras i avsnittet Företagsprogram på portalen eller använda programmanifestet i avsnittet Programregistreringar.  Information om hur du konfigurerar gruppanspråk i programmanifestet finns i "Konfigurera Azure Active Directory Application Registration for group attributes" nedan.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Lägga till gruppanspråk i token för SAML-program med SSO-konfiguration

Om du vill konfigurera gruppanspråk för ett SAML-program i galleri eller icke-galleri öppnar du **Företagsprogram,** klickar på programmet i listan, väljer **Konfiguration för enkel inloggning**och väljer sedan **Användarattribut & anspråk**.

Klicka på **Lägg till ett gruppanspråk**  

![anspråk användargränssnitt](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Använd alternativknapparna för att välja vilka grupper som ska ingå i token

![anspråk användargränssnitt](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Val | Beskrivning |
|----------|-------------|
| **Alla grupper** | Avger säkerhetsgrupper och distributionslistor och roller.  |
| **Säkerhetsgrupper** | Avger säkerhetsgrupper som användaren är medlem i i gruppanspråket |
| **Katalogroller** | Om användaren tilldelas katalogroller skickas de ut som ett wids-anspråk (gruppanspråk kommer inte att avges) |
| **Grupper som tilldelats programmet** | Avger endast de grupper som uttryckligen har tilldelats programmet och användaren är medlem i |

Om du till exempel vill släppa ut alla säkerhetsgrupper som användaren är medlem i väljer du Säkerhetsgrupper

![anspråk användargränssnitt](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Om du vill avge grupper med Active Directory-attribut som synkroniserats från Active Directory i stället för Azure AD-objekt-ID:er väljer du det format som krävs i listrutan. Endast grupper som synkroniseras från Active Directory inkluderas i anspråken.

![anspråk användargränssnitt](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Om du bara vill släppa ut grupper som tilldelats programmet väljer du **Grupper som tilldelats programmet**

![anspråk användargränssnitt](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Grupper som tilldelats programmet inkluderas i token.  Andra grupper som användaren är medlem i utelämnas.  Med det här alternativet inkluderas inte kapslade grupper och användaren måste vara en direkt medlem i den grupp som tilldelats programmet.

Om du vill ändra de grupper som tilldelats programmet markerar du programmet i listan **Företagsprogram** och klickar sedan på **Användare och grupper** på programmets navigeringsmeny till vänster.

Mer information om hur du hanterar grupptilldelning till program finns i dokumentet [Tilldela en användare eller grupp till en företagsapp.](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

### <a name="advanced-options"></a>Avancerade alternativ

Hur gruppanspråk släpps ut kan ändras av inställningarna under Avancerade alternativ

Anpassa namnet på gruppanspråket: Om du väljer det här alternativet kan en annan anspråkstyp anges för gruppanspråk.   Ange anspråkstypen i fältet Namn och det valfria namnområdet för anspråket i namnområdesfältet.

![anspråk användargränssnitt](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Vissa program kräver att information om gruppmedlemskap ska visas i "roll"-anspråket. Du kan också avge användarens grupper som roller genom att markera rutan "Gruppera en rollanspråk".

![anspråk användargränssnitt](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Om alternativet att avge gruppdata som roller används visas endast grupper i rollanspråket.  Alla programroller som användaren tilldelas visas inte i rollanspråket.

### <a name="edit-the-group-claims-configuration"></a>Redigera konfigurationen av gruppanspråk

När en gruppanspråkskonfiguration har lagts till i konfigurationen för användarattribut & anspråk, kommer alternativet att lägga till ett gruppanspråk att vara nedtonat.  Om du vill ändra gruppanspråkskonfigurationen klickar du på gruppanspråket i listan **Ytterligare anspråk.**

![anspråk användargränssnitt](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Konfigurera Azure AD-programregistrering för gruppattribut

Gruppanspråk kan också konfigureras i avsnittet [Valfria anspråk](../../active-directory/develop/active-directory-optional-claims.md) i [programmanifestet](../../active-directory/develop/reference-app-manifest.md).

1. I portalen ->Azure Active Directory -> Application Registrations->Select Application->Manifest

2. Aktivera gruppmedlemsanspråk genom att ändra gruppenMedlemskapClaim

Giltiga värden är:

| Val | Beskrivning |
|----------|-------------|
| **"Allt"** | Avger säkerhetsgrupper, distributionslistor och roller |
| **"SecurityGroup"** | Avger säkerhetsgrupper som användaren är medlem i i gruppanspråket |
| **"DirectoryRole** | Om användaren tilldelas katalogroller skickas de ut som ett wids-anspråk (gruppanspråk kommer inte att avges) |
| **"ApplicationGroup (På ett år)** | Avger endast de grupper som uttryckligen har tilldelats programmet och användaren är medlem i |

   Ett exempel:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Som standard kommer gruppobjekt-ID:er att skickas ut i gruppanspråksvärdet.  Om du vill ändra anspråksvärdet så att det innehåller lokala gruppattribut eller ändrar anspråkstypen till roll använder du OptionalClaims-konfigurationen på följande sätt:

3. Ange valfria anspråk för gruppnamnkonfiguration.

   Om du vill att grupperna i token ska innehålla de lokala AD-gruppattributen anger du vilket valfritt anspråk av tokentyp som ska tillämpas på i det valfria anspråksavsnittet.  Flera tokentyper kan visas:

   - idToken för OIDC ID-token
   - accessToken för OAuth/OIDC-åtkomsttoken
   - Saml2Token för SAML-tokens.

   > [!NOTE]
   > Typen Saml2Token gäller för token i både SAML1.1- och SAML2.0-format

   För varje relevant tokentyp ändrar du gruppanspråket för att använda avsnittet OptionalClaims i manifestet. Schemat OptionalClaims är följande:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Valfritt anspråksschema | Värde |
   |----------|-------------|
   | **Namn:** | Måste vara "grupper" |
   | **Källkod:** | Används inte. Utelämna eller ange null |
   | **Viktigt:** | Används inte. Utelämna eller ange falskt |
   | **ytterligareegenskaper:** | Lista över ytterligare egenskaper.  Giltiga alternativ är "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   I ytterligareegenskaper krävs endast en av "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Om det finns mer än en används den första och andra ignoreras.

   Vissa program kräver gruppinformation om användaren i rollanspråket.  Om du vill ändra anspråkstypen till från ett gruppanspråk till ett rollanspråk lägger du till "emit_as_roles" i ytterligare egenskaper.  Gruppvärdena kommer att släppas ut i rollanspråket.

   > [!NOTE]
   > Om "emit_as_roles" används visas inte alla programroller som konfigurerats som användaren har tilldelats i rollanspråket

### <a name="examples"></a>Exempel

Emit grupper som gruppnamn i OAuth-åtkomsttoken i dnsDomainName\SAMAccountName-format

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Så här avger du gruppnamn som ska returneras i netbiosDomain\samAccountName-format som rollanspråk i SAML- och OIDC ID-token:

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

[Tilldela en användare eller grupp till en företagsapp](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[Konfigurera rollanspråk](../../active-directory/develop/active-directory-enterprise-app-role-management.md)

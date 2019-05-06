---
title: Lär dig att ange valfria anspråk till din Azure AD-program | Microsoft Docs
description: En guide för att lägga till anpassade eller ytterligare anspråk till SAML 2.0 och JSON Web token (JWT) token som utfärdas av Azure Active Directory.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2019
ms.author: celested
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd7b05a5411c03e1324871fbff3c29061ce7b3d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139246"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Anvisningar: Ange valfria anspråk till din Azure AD-app

Den här funktionen används av programutvecklare för att ange vilka anspråk som de vill ha i token som skickas till sina program. Du kan använda valfria anspråk till:

- Välj ytterligare anspråk ska ingå i token för ditt program.
- Ändra beteendet för vissa anspråk som Azure AD returnerar i token.
- Lägg till och få åtkomst till anpassade anspråk för ditt program.

Listan över standard anspråk finns i den [åtkomsttoken](access-tokens.md) och [id_token](id-tokens.md) anspråk dokumentation. 

Finns stöd för valfria anspråk i såväl v1.0 och v2.0 format token, som SAML-tokens får de flesta av deras värde när du flyttar från v1.0 till v2.0. Ett av målen med den [v2.0 Azure AD-slutpunkten](active-directory-appmodel-v2-overview.md) är token är mindre säkerställa optimala prestanda av klienter. Därför kan flera anspråk som tidigare ingår i åtkomst- och ID-token finns inte längre i v2.0-token och måste be dig om särskilt på basis av per program.

**Tabell 1: Tillämplighet**

| Kontotyp | V1.0 token | V2.0-token  |
|--------------|---------------|----------------|
| Personligt Microsoft-konto  | Gäller inte  | Stöds|
| Azure AD-konto      | Stöds | Stöds |

## <a name="v10-and-v20-optional-claims-set"></a>V1.0 och V2.0 valfria anspråk

Uppsättningen med valfria anspråk som är tillgängliga som standard att användas av program finns nedan. Om du vill lägga till anpassade valfria anspråk för ditt program, se [Katalogtillägg](#configuring-directory-extension-optional-claims)nedan. När du lägger till anspråk till den **åtkomsttoken**, den här ändringen träder åtkomsttoken begärs *för* programmet (ett webb-API), inte de *av* programmet. Detta säkerställer att oavsett klienten åtkomst till ditt API, rätt data finns i den åtkomsttoken som de använder för att autentisera mot ditt API.

> [!NOTE]
> Flesta av dessa anspråk kan tas med i JWTs för v1.0 och v2.0 token, men inte SAML-tokens, utom där annat anges i kolumnen tokentypen. Konsument-konton har en delmängd av de här anspråken som markerats i kolumnen ”användartyp”.  Många av de anspråk som anges gäller inte för konsumentanvändare (de har ingen klient, så `tenant_ctry` har inte något värde).  

**Tabell 2: V1.0 och V2.0 valfritt anspråksuppsättningen**

| Namn                       |  Beskrivning   | Tokentyp | Användartyp | Anteckningar  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Tid när användaren senast autentiserade. Se OpenID Connect-specifikationen.| JWT        |           |  |
| `tenant_region_scope`      | Region för resurs-klient | JWT        |           | |
| `home_oid`                 | För gästanvändare, objekt-ID för användaren i användarens startklientorganisation.| JWT        |           | |
| `sid`                      | Sessions-ID används för tillfälliga användaren logga ut. | JWT        |  Personliga och Azure AD-konton.   |         |
| `platf`                    | Enhetsplattform    | JWT        |           | Begränsat till hanterade enheter som kan verifiera typ av enhet.|
| `verified_primary_email`   | Ursprung användarens PrimaryAuthoritativeEmail      | JWT        |           |         |
| `verified_secondary_email` | Ursprung användarens SecondaryAuthoritativeEmail   | JWT        |           |        |
| `enfpolids`                | Tvingande princip-ID: N. En lista över ID: N som har utvärderats för den aktuella användaren. | JWT |  |  |
| `vnet`                     | Information om virtuellt nätverk specificerare. | JWT        |           |      |
| `fwd`                      | IP-adress.| JWT    |   | Lägger till den ursprungliga IPv4-adressen för den begärande klienten (i ett virtuellt nätverk) |
| `ctry`                     | Användarens land | JWT |  | Azure AD-returnerar den `ctry` valfria anspråk om den finns och anspråkets värde är en standard två bokstäver landskod, till exempel FR, JP, SZ och så vidare. |
| `tenant_ctry`              | Resursen klientens land/region | JWT | | |
| `xms_pdl`          | Önskad Dataplats   | JWT | | För Multi-Geo-innehavare är detta 3 bokstäver koden som visar den geografiska region som användaren tillhör. Mer information finns i den [Azure AD Connect-dokumentationen om önskad Dataplats](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Till exempel: `APC` för Asien/Stillahavsområdet. |
| `xms_pl`                   | Användarens förvalda språk  | JWT ||Användaren prioriterade språk, om ange. Ursprung sina startklientorganisation i scenarier för gäst-åtkomst. Formaterad lla kopia (”en-us”). |
| `xms_tpl`                  | Klient-språk| JWT | | Resurs-klienten prioriterade språk, om ange. Formaterad lla (”SV”). |
| `ztdid`                    | Zero touch-distributions-ID | JWT | | Enhetsidentitet används för [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Den adresserbara e-postadress för användaren, om användaren har en.  | JWT, SAML | MSA, AAD | Det här värdet ingår som standard om användaren inte är gäst i klienten.  För hanterade användare (de i klienten), måste det begäras via den här valfria anspråk eller på v2.0, med OpenID-området.  För hanterade användare, e-postadressen måste anges i den [administrationsportalen för Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Valfritt formatering för gruppanspråk |JWT, SAML| |Används tillsammans med inställningen GroupMembershipClaims i den [programmanifestet](reference-app-manifest.md), som också måste anges. Mer information finns i [gruppen anspråk](#Configuring-group-optional claims) nedan. Läs mer på gruppanspråk [så här konfigurerar du gruppanspråk](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Status för användare i klienten. | JWT, SAML | | Om användaren är medlem i klienten, är värdet `0`. Om de är en gäst, är värdet `1`. |
| `upn`                      | UserPrincipalName anspråk. | JWT, SAML  |           | Även om det här anspråket medföljer automatiskt, kan du ange det som ett valfritt anspråk att koppla ytterligare egenskaper om du vill ändra sitt beteende i fallet för gäst-användare.  |

### <a name="v20-optional-claims"></a>V2.0 valfria anspråk

De här anspråken är alltid är inkluderad i v1.0 Azure AD-token, men inte ingår i v2.0 token såvida inte begär. De här anspråken gäller endast för JWTs (ID-token och åtkomsttoken). 

**Tabell 3: Endast v2.0 valfria anspråk**

| JWT-anspråk     | Namn                            | Beskrivning                                | Anteckningar |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-adress                      | IP-adressen loggat in från klienten.   |       |
| `onprem_sid`  | Lokal säkerhetsidentifierare |                                             |       |
| `pwd_exp`     | Förfallotid för lösenord        | Datum/tid då lösenordet upphör att gälla. |       |
| `pwd_url`     | Ändra lösenord URL             | En URL som användaren kan besöka för att ändra sina lösenord.   |   |
| `in_corp`     | Inifrån företagsnätverket        | Signaler om klienten inloggning från företagsnätverket. Om de inte är inte anspråket är inkluderat.   |  Baserade ut från den [tillförlitliga IP-adresser](../authentication/howto-mfa-mfasettings.md#trusted-ips) inställningar i MFA.    |
| `nickname`    | Smeknamn                        | Ytterligare ett namn för användaren, separat från förnamn eller efternamn. | 
| `family_name` | Efternamn                       | Innehåller den senaste namn, efternamn eller namn för användaren som definierats i användarobjektet. <br>"family_name":"Miller" | Stöds i MSA och AAD   |
| `given_name`  | Förnamn                      | Innehåller först eller ”förnamn” för användaren som angetts på användarobjektet.<br>"given_name": ”Frank”                   | Stöds i MSA och AAD  |
| `upn`         | User Principal Name | En identifierare för den användare som kan användas med parametern username_hint.  Inte en varaktigt ID för användaren och bör inte användas till att viktiga data. | Se [ytterligare egenskaper](#additional-properties-of-optional-claims) nedan för att konfigurera anspråket. |

### <a name="additional-properties-of-optional-claims"></a>Ytterligare egenskaper för valfria anspråk

Vissa valfria anspråk kan konfigureras för att ändra hur anspråket returneras. Dessa ytterligare egenskaper används huvudsakligen för att migrera lokala program med olika förväntningar (till exempel `include_externally_authenticated_upn_without_hash` hjälper till med klienter som inte kan hantera hash-tecken (`#`) UPN)

**Tabell 4: Värden för att konfigurera valfria anspråk**

| Egenskapsnamn  | Ytterligare egenskapsnamn | Beskrivning |
|----------------|--------------------------|-------------|
| `upn`          |                          | Kan användas för både SAML- och JWT-svar och för v1.0 och v2.0-token. |
|                | `include_externally_authenticated_upn`  | Innehåller gästen UPN som lagras i resurs-klient. Till exempel, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Samma som ovan, förutom att hashen markerar (`#`) ersätts med understreck (`_`), till exempel `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Ytterligare egenskaper-exempel

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Det här objektet OptionalClaims orsakar ID-token som returneras till klienten med en annan upn med ytterligare startklientorganisation och klientinformation för resursen. Detta endast ändrar de `upn` anspråk i token om användaren inte är gäst i klienten (som använder en annan IDP: N för autentisering). 

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

Du kan konfigurera valfria anspråk för ditt program genom att ändra programmanifestet (se exemplet nedan). Mer information finns i den [förstå Azure AD application manifest artikeln](reference-app-manifest.md).

**Exemplet schema:**

```json
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": false
             }
      ],
      "accessToken": [
             {
                    "name": "ipaddr", 
                    "essential": false
              }
      ],
      "saml2Token": [
              {
                    "name": "upn", 
                    "essential": false
               },
               {
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": false
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>OptionalClaims typ

Anger de valfria anspråk som begärs av ett program. Ett program kan konfigurera valfria anspråk som ska returneras i var och en av tre typer av token (ID-token, token, SAML 2 åtkomsttoken) som kan tas emot från säkerhetstokentjänsten. Programmet kan konfigurera en annan uppsättning valfria anspråk som ska returneras i varje typ av token. Egenskapen OptionalClaims för entiteten program är ett OptionalClaims-objekt.

**Tabell 5: OptionalClaims egenskaper**

| Namn        | Typ                       | Beskrivning                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Samling (OptionalClaim) | De valfria anspråk som returneras i JWT-ID-token. |
| `accessToken` | Samling (OptionalClaim) | Det returnerade valfria anspråk i JWT-åtkomsttoken. |
| `saml2Token`  | Samling (OptionalClaim) | De valfria anspråk som returneras i SAML-token.   |

### <a name="optionalclaim-type"></a>OptionalClaim typ

Innehåller ett valfritt anspråk som är associerad med ett program eller ett huvudnamn för tjänsten. IdToken och accessToken saml2Token egenskaperna för den [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) typ är en samling OptionalClaim.
Om det stöds av specifika anspråk, kan du också ändra beteendet för OptionalClaim med hjälp av fältet AdditionalProperties.

**Tabell 6: OptionalClaim egenskaper**

| Namn                 | Typ                    | Beskrivning                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Namnet på det valfria anspråket.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Källan (directory object) för anspråket. Det finns fördefinierade anspråk och anpassade anspråk från tilläggsegenskaper. Om värdet för datakällan är null, är ett fördefinierat valfria anspråk i anspråket. Om värdet för datakällan är användare, är värdet i egenskapen name tilläggsegenskapen från användarobjektet. |
| `essential`            | Edm.Boolean             | Om värdet är true, är anspråk som anges av klienten nödvändigt för att säkerställa en smidig auktorisering upplevelse för en viss aktivitet som begärts av användaren. Standardvärdet är FALSKT.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Ytterligare egenskaper för anspråket. Om en egenskap finns i den här samlingen, ändrar beteendet för den valfria anspråk som anges i egenskapen name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurera valfria anspråk för directory-tillägget

Du kan också konfigurera token för att inkludera directory-schemautökningar utöver uppsättningen standard valfria anspråk. Mer information finns i [Directory-schemautökningar](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Den här funktionen är användbar för att bifoga ytterligare information som din app kan använda – till exempel, en ytterligare identifierare eller viktiga konfigurationsalternativ som användaren har angett. 

> [!Note]
> Directory-schematillägg är en funktion för endast AAD-så om programmets manifest begäranden ett anpassat tilläggs- och en MSA-användare loggar in på din app kan returneras de här tilläggen inte.

### <a name="directory-extension-formatting"></a>Directory-tillägget formatering

För tilläggsattribut, använder du det fullständiga namnet på filnamnstillägget (i formatet: `extension_<appid>_<attributename>`) i manifestet. Den `<appid>` måste matcha ID för programmet begär anspråket. 

Inom JWT, kommer dessa anspråk genereras med följande namnformat: `extn.<attributename>`.

I SAML-tokens kommer dessa anspråk genereras med följande URI-format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Konfigurera valfria gruppanspråk

   > [!NOTE]
   > Möjligheten att generera gruppnamn för användare och grupper som synkroniserats från en lokal plats är allmänt tillgänglig förhandsversion

Det här avsnittet beskrivs konfigurationsalternativ under valfria anspråk för att ändra gruppattributen som används i gruppanspråk från standard grupp objectID för attribut som synkroniseras från Active Directory för den lokala Windows
> [!IMPORTANT]
> Se [konfigurera gruppanspråk för program med Azure Active Directory](../hybrid/how-to-connect-fed-group-claims.md) mer information, inklusive viktiga varningar för den offentliga förhandsversionen av gruppanspråk från en lokal attribut.

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

**Exempel:** Skapa grupper som gruppen i OAuth-åtkomsttoken i dnsDomainName\sAMAccountName format

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Att skapa gruppnamn som ska returneras i netbiosDomain\sAMAccountName format som rollerna anspråk i SAML- och OIDC ID-token:

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

## <a name="optional-claims-example"></a>Exempel på valfria anspråk

I det här avsnittet ska gå du igenom ett scenario för att se hur du kan använda funktionen valfria anspråk för ditt program.
Det finns flera alternativ för att uppdatera egenskaperna på ett programs identitet konfiguration för att aktivera och konfigurera valfria anspråk:
-   Du kan ändra programmanifestet. Exemplet nedan använder den här metoden för att utföra konfigurationen. Läs den [förstå Azure AD application manifest dokumentet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) första en introduktion till manifestet.
-   Det är också möjligt att skriva ett program som använder den [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) att uppdatera ditt program. Den [entitet och komplex typreferens](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) i Graph API-referensen handboken kan hjälpa dig med att konfigurera de valfria anspråk.

**Exempel:** I exemplet nedan, ändrar du en programmets manifest för att lägga till anspråk till åtkomst-ID och SAML token är avsedda för programmet.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. När du har autentiserats kan du välja Azure AD-klienten genom att välja den från det övre högra hörnet på sidan.
1. Välj **Appregistreringar** från vänster sida.
1. Hitta det program du vill konfigurera valfria anspråk för i listan och klicka på den.
1. Programsidan klickar du på **Manifest** att öppna redigeraren infogade manifest. 
1. Du kan redigera manifestet med den här redigeraren direkt. Manifestet följer schemat för den [program entitet](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest), och automatisk-format manifestet sparas en gång. Nya element ska läggas till i `OptionalClaims` egenskapen.

    ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
            "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
            "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }

    ```

    I det här fallet har olika valfria anspråk lagts till varje typ av token som programmet kan ta emot. ID-token ska nu innehålla UPN-namnet för federerade användare i fullständig form (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Åtkomsttoken som andra klienter begär för det här programmet innehåller nu auth_time anspråket. SAML-token innehåller nu skypeId directory-schematillägget (i det här exemplet är det app-ID för den här appen ab603c56068041afb2f6832e2a17e237). Skype-ID som visas i SAML-tokens `extension_skypeId`.

1. När du är klar uppdaterar manifestet klickar du på **spara** att spara manifestet

## <a name="next-steps"></a>Nästa steg

Mer information om standard anspråk som tillhandahålls av Azure AD.

- [ID-token](id-tokens.md)
- [Åtkomsttoken](access-tokens.md)

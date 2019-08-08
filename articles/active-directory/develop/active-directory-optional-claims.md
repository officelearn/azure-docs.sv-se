---
title: Lär dig att tillhandahålla valfria anspråk till ditt Azure AD-program | Microsoft Docs
description: En guide för att lägga till anpassade eller ytterligare anspråk till token för SAML 2,0 och JSON Web tokens (JWT) som utfärdats av Azure Active Directory.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6e097df21051019495b3bf9bb6c83cb3dba03c8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835329"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Anvisningar: Tillhandahåll valfria anspråk till din Azure AD-App

Programutvecklare kan använda valfria anspråk i sina Azure AD-appar för att ange vilka anspråk de vill ha i token som skickas till deras program. 

Du kan använda valfria anspråk för att:

- Välj ytterligare anspråk som ska inkluderas i token för ditt program.
- Ändra beteendet för vissa anspråk som Azure AD returnerar i tokens.
- Lägg till och få till gång till anpassade anspråk för ditt program.

Listor över standard anspråk finns i dokumentationen för åtkomsttoken [](access-tokens.md) och [id_token](id-tokens.md) -anspråk. 

Även om det finns stöd för valfria anspråk i både v 1.0-och v 2.0-format-token, och även SAML-token, ger de mest av sitt värde när de flyttas från v 1.0 till v 2.0. Ett av målen för [v 2.0 Microsoft Identity Platform](active-directory-appmodel-v2-overview.md) -slutpunkten är mindre token-storlekar för att säkerställa optimala prestanda av klienter. Det innebär att flera anspråk som tidigare inkluderats i åtkomst-och ID-token inte längre finns i v 2.0-token och måste tillfrågas specifikt för varje program.

**Tabell 1: Tillämpningen**

| Kontotyp | v 1.0-token | v 2.0-token  |
|--------------|---------------|----------------|
| Personlig Microsoft-konto  | Gäller inte  | Stöds |
| Azure AD-konto      | Stöds | Stöds |

## <a name="v10-and-v20-optional-claims-set"></a>v 1.0 och v 2.0 valfria anspråks uppsättningar

Den uppsättning valfria anspråk som är tillgängliga som standard för program som ska användas visas nedan. Om du vill lägga till anpassade valfria anspråk för programmet, se [katalog tillägg](#configuring-directory-extension-optional-claims)nedan. När anspråk läggs till iåtkomsttoken gäller detta för de åtkomsttoken som begärs *för* programmet (ett webb-API), inte *av* programmet. Detta säkerställer att de rätta data finns i den åtkomsttoken som de använder för att autentisera mot ditt API, oavsett vilken klient som har åtkomst till ditt API.

> [!NOTE]
> Majoriteten av dessa anspråk kan inkluderas i JWTs för v 1.0-och v 2.0-token, men inte SAML-token, förutom vad som anges i kolumnen tokentyp. Konsument konton har stöd för en delmängd av dessa anspråk som marker ATS i kolumnen "användar typ".  Många av anspråken i listan gäller inte konsument användare (de har ingen innehavare, så `tenant_ctry` har inget värde).  

**Tabell 2: v 1.0 och v 2.0 valfri anspråks uppsättning**

| Namn                       |  Beskrivning   | Tokentyp | Användartyp | Anteckningar  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Tid när användaren senast autentiserades. Se OpenID Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Resurs innehavarens region | JWT        |           | |
| `home_oid`                 | För gäst användare: objekt-ID för användaren i användarens hem klient.| JWT        |           | |
| `sid`                      | Sessions-ID som används för användar utloggning per session. | JWT        |  Personliga och Azure AD-konton.   |         |
| `platf`                    | Enhetsplattform    | JWT        |           | Begränsat till hanterade enheter som kan verifiera enhets typ.|
| `verified_primary_email`   | Källan från användarens PrimaryAuthoritativeEmail      | JWT        |           |         |
| `verified_secondary_email` | Källan från användarens SecondaryAuthoritativeEmail   | JWT        |           |        |
| `enfpolids`                | Tvingade princip-ID: n. En lista med princip-ID: n som utvärderats för den aktuella användaren. | JWT |  |  |
| `vnet`                     | Information om VNET-specifikation. | JWT        |           |      |
| `fwd`                      | IP-adress.| JWT    |   | Lägger till den ursprungliga IPv4-adressen för den begär ande klienten (i ett VNET) |
| `ctry`                     | Användarens land | JWT |  | Azure AD returnerar det `ctry` valfria anspråket om det finns och värdet för anspråket är en vanlig landskod i två bokstäver, till exempel fr, JP, sz och så vidare. |
| `tenant_ctry`              | Resurs innehavarens land | JWT | | |
| `xms_pdl`          | Önskad data plats   | JWT | | För multi-geo-klienter är detta den 3-bokstavs kod som visar den geografiska region som användaren är i. Mer information finns i Azure AD Connect- [dokumentationen om önskad data plats](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Exempel: `APC` för Asien och Stillahavsområdet. |
| `xms_pl`                   | Användarens prioriterade språk  | JWT ||Användarens önskade språk, om det är inställt. Från hem klienten, i scenarier med gäst åtkomst. Formaterat lla-CC ("en-US"). |
| `xms_tpl`                  | Föredraget klient språk| JWT | | Resurs innehavarens föredragna språk, om det är inställt. Formaterat lla ("en"). |
| `ztdid`                    | ID för noll-Touch-distribution | JWT | | Enhets identiteten som används för [Windows autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Det adresser bara e-postmeddelandet för den här användaren, om användaren har ett.  | JWT, SAML | MSA, Azure AD | Det här värdet ingår som standard om användaren är en gäst i klienten.  För hanterade användare (de i klienten) måste det begäras via detta valfria anspråk eller, endast v 2.0, med OpenID-omfånget.  För hanterade användare måste e-postadressen anges i [Office Admin-portalen](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Valfri formatering för grupp anspråk |JWT, SAML| |Används tillsammans med GroupMembershipClaims-inställningen i applikations [manifestet](reference-app-manifest.md), som även måste anges. Mer information finns i [grupp anspråk](#Configuring-group-optional claims) nedan. Mer information om grupp anspråk finns i [så här konfigurerar du grupp anspråk](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Användarens konto status i klient organisationen. | JWT, SAML | | Om användaren är medlem i klienten är `0`värdet. Om de är en gäst är `1`värdet. |
| `upn`                      | UserPrincipalName-anspråk. | JWT, SAML  |           | Även om det här anspråket ingår automatiskt kan du ange det som ett valfritt anspråk för att bifoga ytterligare egenskaper för att ändra dess beteende i gäst användarens ärende.  |

### <a name="v20-optional-claims"></a>v 2.0 valfria anspråk

De här anspråken ingår alltid i v 1.0 Azure AD-tokens, men ingår inte i v 2.0-token om det inte begärs. Dessa anspråk gäller endast för JWTs (ID-token och åtkomsttoken). 

**Tabell 3: v 2.0 – endast valfria anspråk**

| JWT-anspråk     | Namn                            | Beskrivning                                | Anteckningar |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-adress                      | IP-adressen som klienten loggade in från.   |       |
| `onprem_sid`  | Lokal säkerhetsidentifierare |                                             |       |
| `pwd_exp`     | Lösen ordets förfallo tid        | Det datum/tid-värde som lösen ordet upphör att gälla. |       |
| `pwd_url`     | Ändra lösen ordets URL             | En URL som användaren kan besöka för att ändra sina lösen ord.   |   |
| `in_corp`     | I företags nätverk        | Signalerar om klienten loggar in från företags nätverket. Om de inte är det inkluderas inte anspråket.   |  Baserat på de [betrodda IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) -inställningarna i MFA.    |
| `nickname`    | Smeknamn                        | Ett ytterligare namn för användaren, separat från förnamn eller efter namn. | 
| `family_name` | Efternamn                       | Innehåller användarens efter namn, efter namn eller familj som definierats i användarobjektet. <br>"family_name":"Miller" | Stöds i MSA och Azure AD   |
| `given_name`  | Förnamn                      | Anger det första eller "tilldelade" namnet på användaren, enligt vad som anges på användarobjektet.<br>"given_name": Frank                   | Stöds i MSA och Azure AD  |
| `upn`         | Användarens huvudnamn | En identifierare för den användare som kan användas med parametern username_hint.  Inte en varaktig identifierare för användaren och ska inte användas för nyckel data. | Se [Ytterligare egenskaper](#additional-properties-of-optional-claims) nedan för konfiguration av anspråket. |

### <a name="additional-properties-of-optional-claims"></a>Ytterligare egenskaper för valfria anspråk

Vissa valfria anspråk kan konfigureras för att ändra hur anspråket returneras. Dessa ytterligare egenskaper används främst för att hjälpa migrering av lokala program med olika data förväntningar (till exempel `include_externally_authenticated_upn_without_hash` för att hjälpa klienter som inte kan hantera hash-tecken (`#`) i UPN)

**Tabell 4: Värden för att konfigurera valfria anspråk**

| Egenskapsnamn  | Ytterligare egenskaps namn | Beskrivning |
|----------------|--------------------------|-------------|
| `upn`          |                          | Kan användas för både SAML-och JWT-svar och för v 1.0-och v 2.0-token. |
|                | `include_externally_authenticated_upn`  | Inkluderar gäst-UPN som lagrats i resurs klienten. Till exempel, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Samma som ovan, förutom att hash-tecknen (`#`) ersätts med under streck (`_`), till exempel`foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Exempel på ytterligare egenskaper

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

Detta OptionalClaims-objekt gör att ID-token returneras till klienten för att inkludera ett annat UPN med ytterligare information om hem klient och resurs klient. Detta ändrar `upn` bara anspråk i token om användaren är en gäst i klienten (som använder en annan IDP för autentisering). 

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

Du kan konfigurera valfria anspråk för programmet genom att ändra applikations manifestet (se exemplet nedan). Mer information finns i [artikeln förstå program manifestet för Azure AD](reference-app-manifest.md).

> [!IMPORTANT]
> Åtkomsttoken skapas **alltid** med hjälp av resurs manifestet, inte klienten.  Så i begäran `...scope=https://graph.microsoft.com/user.read...` är resursen Graph.  Därför skapas åtkomsttoken med hjälp av graf-manifestet, inte klientens manifest.  Att ändra manifestet för programmet kommer aldrig att orsaka att token för Graph ser annorlunda ut.  För att verifiera att `accessToken` ändringarna gäller, begär du en token för ditt program, inte en annan app.  

**Exempel schema:**

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

### <a name="optionalclaims-type"></a>Typ av OptionalClaims

Deklarerar de valfria anspråk som begärs av ett program. Ett program kan konfigurera valfria anspråk som ska returneras i var och en av tre typer av tokens (ID-token, åtkomsttoken, SAML 2-token) som den kan ta emot från säkerhetstokentjänsten. Programmet kan konfigurera en annan uppsättning valfria anspråk som ska returneras i varje tokentyp. Egenskapen OptionalClaims för programentiteten är ett OptionalClaims-objekt.

**Tabell 5: Egenskaper för OptionalClaims-typ**

| Namn        | Typ                       | Beskrivning                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Samling (OptionalClaim) | De valfria anspråk som returneras i JWT ID-token. |
| `accessToken` | Samling (OptionalClaim) | De valfria anspråk som returneras i JWT-åtkomsttoken. |
| `saml2Token`  | Samling (OptionalClaim) | De valfria anspråk som returneras i SAML-token.   |

### <a name="optionalclaim-type"></a>Typ av OptionalClaim

Innehåller ett valfritt anspråk som associeras med ett program eller ett huvud namn för tjänsten. Egenskaperna idToken, accessToken och saml2Token för [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) -typen är en samling OptionalClaim.
Om det stöds av ett angivet anspråk kan du också ändra beteendet för OptionalClaim med hjälp av fältet AdditionalProperties.

**Tabell 6: Egenskaper för OptionalClaim-typ**

| Namn                 | Typ                    | Beskrivning                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Namnet på det valfria anspråket.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Källa (katalog objekt) för anspråket. Det finns fördefinierade anspråk och användardefinierade anspråk från tilläggs egenskaper. Om source-värdet är null är anspråket ett fördefinierat valfritt anspråk. Om source-värdet är User är värdet i egenskapen name egenskapen Extension från objektet User. |
| `essential`            | Edm.Boolean             | Om värdet är True är det anspråk som anges av klienten nödvändigt för att säkerställa en smidig auktorisering för den specifika uppgift som användaren begärt. Standardvärdet är FALSKT.                                                                                                             |
| `additionalProperties` | Samling (EDM. String) | Ytterligare egenskaper för anspråket. Om det finns en egenskap i den här samlingen ändras beteendet för det valfria anspråket som anges i egenskapen Name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurerar valfria anspråk för katalog tillägg

Förutom de vanliga valfria anspråks uppsättningarna kan du också konfigurera tokens så att de inkluderar katalog schema tillägg. Mer information finns i [katalog schema tillägg](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Den här funktionen är användbar för att bifoga ytterligare användar information som din app kan använda – till exempel en ytterligare identifierare eller ett viktigt konfigurations alternativ som användaren har angett. 

> [!Note]
> - Katalog schema tillägg är endast en Azure AD-funktion, så om program manifestet begär ett anpassat tillägg och en MSA-användare loggar in i din app returneras inte dessa tillägg.
> - Valfria anspråk för Azure AD fungerar bara med Azure AD-tillägget och fungerar inte med Microsoft Graph Directory-tillägget. Båda API: erna `Directory.ReadWriteAll` kräver behörighet, som endast kan skickas av administratörer.

### <a name="directory-extension-formatting"></a>Formatering av katalog tillägg

För attribut för tillägg använder du det fullständiga namnet på tillägget (i formatet: `extension_<appid>_<attributename>`) i applikations manifestet. `<appid>` Måste matcha ID för programmet som begär anspråket. 

I JWT genereras dessa anspråk med följande namn format: `extn.<attributename>`.

I SAML-token genereras dessa anspråk med följande URI-format:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Konfigurera grupp tillvals anspråk

   > [!NOTE]
   > Möjligheten att generera grupp namn för användare och grupper som synkroniseras från lokal är offentlig för hands version.

Det här avsnittet beskriver konfigurations alternativen under valfria anspråk för att ändra de Gruppattribut som används i grupp anspråk från standard gruppen objectID till attribut som synkroniseras från lokala Windows-Active Directory.

> [!IMPORTANT]
> Se [Konfigurera grupp anspråk för program med Azure AD](../hybrid/how-to-connect-fed-group-claims.md) för mer information, inklusive viktiga varningar om den offentliga för hands versionen av grupp anspråk från lokala attribut.

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

   Om du vill att grupper i token ska innehålla attributen för lokal AD-grupp i det valfria anspråks avsnittet anger du vilken tokentyp som valfritt anspråk ska tillämpas på, namnet på valfritt begärt anspråk och eventuella ytterligare egenskaper som önskas.  Flera typer av token kan visas:

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

**Exempel:** Generera grupper som grupp namn i OAuth-åtkomsttoken i dnsDomainName\sAMAccountName-format

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Så här genererar du grupp namn som ska returneras i netbiosDomain\sAMAccountName-format som roll anspråk i SAML-och OIDC-ID-token:

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

I det här avsnittet kan du gå igenom ett scenario för att se hur du kan använda funktionen valfria anspråk för ditt program.
Det finns flera tillgängliga alternativ för att uppdatera egenskaperna för ett programs identitets konfiguration för att aktivera och konfigurera valfria anspråk:
-   Du kan ändra applikations manifestet. I exemplet nedan används den här metoden för att utföra konfigurationen. Läs [förstå Azure AD Application manifest-dokumentet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) först för en introduktion till manifestet.
-   Det är också möjligt att skriva ett program som använder [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) för att uppdatera ditt program. Referensen för [entiteter och komplex typ](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) i Graph API referens guide kan hjälpa dig att konfigurera de valfria anspråken.

**Exempel:** I exemplet nedan ändrar du ett programs manifest för att lägga till anspråk till åtkomst, ID och SAML-token avsedda för programmet.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. När du har autentiserat väljer du din Azure AD-klient genom att välja den från det övre högra hörnet på sidan.
1. Välj **app** -registreringar från vänster sida.
1. Hitta det program som du vill konfigurera valfria anspråk för i listan och klicka på det.
1. På sidan program klickar du på **manifest** för att öppna den infogade manifest redigeraren. 
1. Du kan redigera manifestet direkt med hjälp av den här redigeraren. Manifestet följer schemat för [programmets entitet](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)och formaterar automatiskt manifestet när det har sparats. Nya element kommer att läggas till i `OptionalClaims` egenskapen.

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

    I det här fallet har olika valfria anspråk lagts till för varje typ av token som programmet kan ta emot. ID-token kommer nu att innehålla UPN för federerade användare i fullständig form (`<upn>_<homedomain>#EXT#@<resourcedomain>`). De åtkomsttoken som andra klienter begär för det här programmet kommer nu att innehålla auth_time-anspråket. SAML-token kommer nu att innehålla skypeId Directory schema-tillägget (i det här exemplet är app-ID: t för den här appen ab603c56068041afb2f6832e2a17e237). SAML-token kommer att exponera Skype-ID `extension_skypeId`: t som.

1. När du är klar med att uppdatera manifestet klickar du på **Spara** för att spara manifestet

## <a name="next-steps"></a>Nästa steg

Läs mer om de standard anspråk som tillhandahålls av Azure AD.

- [ID-token](id-tokens.md)
- [Åtkomsttoken](access-tokens.md)

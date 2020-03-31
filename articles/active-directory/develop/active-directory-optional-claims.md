---
title: Ange valfria anspråk till Azure AD-appar | Azure
titleSuffix: Microsoft identity platform
description: Så här lägger du till anpassade eller ytterligare anspråk i JWT-token (SAML 2.0 och JSON Web Tokens) som utfärdats av Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 3/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 23d83b59c510f2565b2f66f78dad56c9c9592dd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136525"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Så här tillhandahåller du valfria anspråk till din Azure AD-app

Programutvecklare kan använda valfria anspråk i sina Azure AD-program för att ange vilka anspråk de vill ha i token som skickas till deras program. 

Du kan använda valfria anspråk för att:

- Välj ytterligare anspråk som ska inkluderas i token för ditt program.
- Ändra beteendet för vissa anspråk som Azure AD returnerar i token.
- Lägg till och komma åt anpassade anspråk för ditt program.

För listor över standardanspråk, se [åtkomsttoken](access-tokens.md) och [id_token](id-tokens.md) anspråksdokumentation. 

Även om valfria anspråk stöds i token för både v1.0- och v2.0-format, samt SAML-token, ger de det mesta av sitt värde när de flyttar från v1.0 till v2.0. Ett av målen för [slutpunkten för microsoft-identitetsplattformen v2.0](active-directory-appmodel-v2-overview.md) är mindre tokenstorlekar för att säkerställa optimal prestanda för klienter. Därför finns flera anspråk som tidigare ingick i åtkomst- och ID-token inte längre i v2.0-token och måste begäras specifikt per program.

**Tabell 1: Tillämplighet**

| Typ av konto | v1.0-token | v2.0-token  |
|--------------|---------------|----------------|
| Personligt Microsoft-konto  | Ej tillämpligt  | Stöds |
| Azure AD-konto      | Stöds | Stöds |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 och v2.0 valfria anspråksuppsättning

Den uppsättning valfria anspråk som är tillgängliga som standard för program som ska användas visas nedan. Om du vill lägga till anpassade valfria anspråk för ditt program finns i [Katalogtillägg](#configuring-directory-extension-optional-claims)nedan. När du lägger till anspråk i **åtkomsttoken**gäller anspråken för åtkomsttoken som begärs *för* programmet (ett webb-API), inte anspråk som *begärs av* programmet. Oavsett hur klienten kommer åt ditt API finns rätt data i åtkomsttoken som används för att autentisera mot ditt API.

> [!NOTE]
> Majoriteten av dessa anspråk kan inkluderas i JWTs för v1.0- och v2.0-token, men inte SAML-token, förutom när det anges i kolumnen Tokentyp. Konsumentkonton stöder en delmängd av dessa anspråk, markerade i kolumnen "Användartyp".  Många av de angivna anspråken gäller inte konsumentanvändare (de har ingen klient, så `tenant_ctry` har inget värde).  

**Tabell 2: v1.0 och v2.0 valfritt anspråksuppsättning**

| Namn                       |  Beskrivning   | Tokentyp | Användartyp | Anteckningar  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Tid då användaren senast autentiserades. Se OpenID Connect-spec.| Jwt        |           |  |
| `tenant_region_scope`      | Region för resursklienten | Jwt        |           | |
| `home_oid`                 | För gästanvändare är användarens objekt-ID i användarens hemklient.| Jwt        |           | |
| `sid`                      | Sessions-ID som används för användarloggning per session. | Jwt        |  Personliga och Azure AD-konton.   |         |
| `platf`                    | Enhetsplattform    | Jwt        |           | Begränsad till hanterade enheter som kan verifiera enhetstyp.|
| `verified_primary_email`   | Kommer från användarens PrimaryAuthoritativeEmail      | Jwt        |           |         |
| `verified_secondary_email` | Kommer från användarens SecondaryAuthoritativeEmail   | Jwt        |           |        |
| `enfpolids`                | Tvingande princip-ID:er. En lista över de princip-ID:er som utvärderades för den aktuella användaren. | Jwt |  |  |
| `vnet`                     | VNET-specificerarinformation. | Jwt        |           |      |
| `fwd`                      | IP-adress.| Jwt    |   | Lägger till den ursprungliga IPv4-adressen för den begärande klienten (i ett VNET) |
| `ctry`                     | Användarens land | Jwt |  | Azure AD `ctry` returnerar det valfria anspråket om det finns och värdet för anspråket är en standardkod med två bokstäver, till exempel FR, JP, SZ och så vidare. |
| `tenant_ctry`              | Resursklientens land | Jwt | | |
| `xms_pdl`          | Önskad dataplats   | Jwt | | För Multi-Geo-klienter är den önskade dataplatsen den trebokstavskod som visar det geografiska område som användaren befinner sig i. Mer information finns i [Azure AD Connect-dokumentationen om önskad dataplats](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Till exempel: `APC` för Asien och Stillahavsområdet. |
| `xms_pl`                   | Önskat språk för användare  | Jwt ||Användarens önskade språk, om det är inställt. Kommer från sin hemklient, i gäståtkomstscenarier. Formaterad LL-CC ("en-us"). |
| `xms_tpl`                  | Önskat språk för klient| Jwt | | Resursklientens önskade språk, om det anges. Formaterad LL ("en"). |
| `ztdid`                    | Distributions-ID för zero touch | Jwt | | Enhetens identitet som används för [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Den adresserbara e-postadressen för den här användaren, om användaren har en.  | JWT, SAML | MSA, Azure AD | Det här värdet inkluderas som standard om användaren är gäst i klienten.  För hanterade användare (användarna i klienten) måste det begäras via det här valfria anspråket eller, endast på v2.0, med OpenID-scopet.  För hanterade användare måste e-postadressen anges i [Office-administratörsportalen](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Valfri formatering för gruppanspråk |JWT, SAML| |Används tillsammans med gruppmedlemskapsanställningen i [ansökningsmanifestet](reference-app-manifest.md), som också måste ställas in. För mer information se [Koncernens anspråk](#configuring-groups-optional-claims) nedan. Mer information om gruppanspråk finns i [Så här konfigurerar du gruppanspråk](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Användarkontostatus i klienten. | JWT, SAML | | Om användaren är medlem i klienten `0`är värdet . Om de är en gäst `1`är värdet . |
| `upn`                      | Anspråk på UserPrincipalName. | JWT, SAML  |           | Även om det här anspråket inkluderas automatiskt kan du ange det som ett valfritt anspråk för att bifoga ytterligare egenskaper för att ändra dess beteende i gästanvändarfallet.  |

## <a name="v20-specific-optional-claims-set"></a>v2.0-specifika valfria anspråksuppsättning

Dessa anspråk ingår alltid i v1.0 Azure AD-token, men ingår inte i v2.0-token om det inte begärs. Dessa anspråk gäller endast för JWTs (ID-token och Åtkomsttokens). 

**Tabell 3: endast v2.0-anspråk**

| JWT Fordran     | Namn                            | Beskrivning                                | Anteckningar |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-adress                      | IP-adressen som klienten loggade in från.   |       |
| `onprem_sid`  | Lokal säkerhetsidentifierare |                                             |       |
| `pwd_exp`     | Förfallotid för lösenord        | Den datum då lösenordet upphör att gälla. |       |
| `pwd_url`     | Ändra url till lösenord             | En URL som användaren kan besöka för att ändra sitt lösenord.   |   |
| `in_corp`     | Inifrån företagsnätverket        | Signalerar om klienten loggar in från företagsnätverket. Om de inte är det, ingår inte anspråket.   |  Baserat på de [betrodda IPs-inställningarna](../authentication/howto-mfa-mfasettings.md#trusted-ips) i MFA.    |
| `nickname`    | Smeknamn                        | Ytterligare ett namn för användaren. Smeknamnet är skilt från för- eller efternamn. Kräver `profile` omfattningen.| 
| `family_name` | Efternamn                       | Anger användarens efternamn, efternamn eller efternamn enligt definitionen i användarobjektet. <br>"family_name":"Mjölnare" | Stöds i MSA och Azure AD. Kräver `profile` omfattningen.   |
| `given_name`  | Förnamn                      | Anger användarens första eller "givna" namn, enligt anvisningarna för användarobjektet.<br>"given_name": "Frank"                   | Stöds i MSA och Azure AD .  Kräver `profile` omfattningen. |
| `upn`         | User Principal Name | En identifer för användaren som kan användas med parametern username_hint.  Inte en varaktig identifierare för användaren och bör inte användas för att knappa data. | Se [ytterligare egenskaper](#additional-properties-of-optional-claims) nedan för konfiguration av anspråket. Kräver `profile` omfattningen.|

### <a name="additional-properties-of-optional-claims"></a>Ytterligare egenskaper för valfria anspråk

Vissa valfria anspråk kan konfigureras för att ändra hur anspråket returneras. Dessa ytterligare egenskaper används främst för att hjälpa migrering av lokala `include_externally_authenticated_upn_without_hash` program med olika dataförväntningar (till exempel hjälper till med klienter som inte kan hantera hash-markeringar (`#`) i UPN)

**Tabell 4: Värden för att konfigurera valfria anspråk**

| Egenskapsnamn  | Ytterligare egenskapsnamn | Beskrivning |
|----------------|--------------------------|-------------|
| `upn`          |                          | Kan användas för både SAML och JWT-svar och för v1.0- och v2.0-tokens. |
|                | `include_externally_authenticated_upn`  | Inkluderar gästen UPN som lagras i resursklienten. Till exempel, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Samma som ovan, förutom att`#`hash-märkena (`_`) ersätts med understreck ( ), till exempel`foo_hometenant.com_EXT_@resourcetenant.com` |

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

Det här optionalClaims-objektet gör att ID-token som returneras till klienten innehåller ett upn-anspråk med ytterligare information om hemklienten och resursklienten. Anspråket `upn` ändras endast i token om användaren är gäst i klienten (som använder ett annat IDP för autentisering). 

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

> [!IMPORTANT]
> Åtkomsttoken genereras **alltid** med hjälp av resursens manifest, inte klienten.  Så i `...scope=https://graph.microsoft.com/user.read...` begäran resursen är Microsoft Graph API.  Åtkomsttoken skapas alltså med hjälp av Microsoft Graph API-manifestet, inte klientens manifest.  Om du ändrar manifestet för ditt program kommer tokens för Microsoft Graph API att se annorlunda ut.  För att verifiera `accessToken` att ändringarna är i kraft, begär en token för ditt program, inte en annan app.  


Du kan konfigurera valfria anspråk för ditt program via användargränssnittet eller programmanifestet.

1. Gå till [Azure-portalen](https://portal.azure.com). Sök efter och välj **Azure Active Directory**.
1. Välj **Appregistreringar**i avsnittet **Hantera** .
1. Välj det program som du vill konfigurera valfria anspråk för i listan.

**Konfigurera valfria anspråk via användargränssnittet:**

[![Visar hur du konfigurerar valfria anspråk med hjälp av användargränssnittet](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Välj **Tokenkonfiguration (förhandsgranskning)** i avsnittet **Hantera** .
2. Välj **Lägg till valfritt anspråk**.
3. Välj den tokentyp som du vill konfigurera.
4. Välj valfria anspråk att lägga till.
5. Klicka på **Lägg till**.

**Konfigurera valfria anspråk via programmanifestet:**

[![Visar hur du konfigurerar valfria anspråk med hjälp av appmanifestet](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Välj **Manifest**i avsnittet **Hantera** . En webbaserad manifestredigerare öppnas, så att du kan redigera manifestet. Du kan också välja **Ladda ned** och redigera manifestet lokalt, och sedan använda **Ladda upp** för att tillämpa det på appen igen. Mer information om programmanifestet finns i [artikeln Förstå azure AD-programmanifestet](reference-app-manifest.md).

    Följande programmanifestpost lägger till auth_time, ipaddr och upn valfria anspråk på ID-, åtkomst- och SAML-token.

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

2. När det är klart klickar du på **Spara**. Nu inkluderas de angivna valfria anspråken i token för ditt program.    


### <a name="optionalclaims-type"></a>ValfriClaims-typ

Deklarerar de valfria anspråk som begärs av en ansökan. Ett program kan konfigurera valfria anspråk som ska returneras i var och en av tre typer av token (ID-token, åtkomsttoken, SAML 2-token) som det kan ta emot från säkerhetstokentjänsten. Programmet kan konfigurera en annan uppsättning valfria anspråk som ska returneras i varje tokentyp. Egenskapen OptionalClaims för applicationentiteten är ett OptionalClaims-objekt.

**Tabell 5: Egenskaper för valfria egenskaper för typen Klasar**

| Namn        | Typ                       | Beskrivning                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Samling (valfrittClaim) | De valfria anspråk som returneras i JWT ID-token. |
| `accessToken` | Samling (valfrittClaim) | De valfria anspråk som returneras i JWT-åtkomsttoken. |
| `saml2Token`  | Samling (valfrittClaim) | De valfria anspråken som returneras i SAML-token.   |

### <a name="optionalclaim-type"></a>ValfriClaim-typ

Innehåller ett valfritt anspråk som är associerat med ett program eller ett huvudnamn för tjänsten. Egenskaperna idToken, accessToken och saml2Token för typen [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) är en samling optionalClaim.
Om det stöds av ett visst anspråk kan du också ändra beteendet för OptionalClaim med hjälp av fältet AdditionalProperties.

**Tabell 6: Egenskaper för valfriknapptyp**

| Namn                 | Typ                    | Beskrivning                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Namnet på det frivilliga anspråket.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Anspråkets källa (katalogobjekt). Det finns fördefinierade anspråk och användardefinierade anspråk från tilläggsegenskaper. Om källvärdet är null är anspråket ett fördefinierat valfritt anspråk. Om källvärdet är användaren är värdet i egenskapen name egenskap egenskapen extension från användarobjektet. |
| `essential`            | Edm.Boolean             | Om värdet är sant är anspråket som anges av klienten nödvändigt för att säkerställa en smidig auktoriseringsupplevelse för den specifika uppgift som slutanvändaren begär. Standardvärdet är false.                                                                                                             |
| `additionalProperties` | Samling (Edm.String) | Ytterligare egenskaper för anspråket. Om det finns en egenskap i den här samlingen ändrar den beteendet för det valfria anspråk som anges i egenskapen name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Konfigurera valfria anspråk för katalogtillägg

Förutom standardvalanspråksuppsättningen kan du också konfigurera token till tillägg. Mer information finns i dokumentationen till [Microsoft Graph extensionProperty](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0) - observera att schema och öppna tillägg inte stöds av valfria anspråk, bara katalogtilläggen för AAD-Graph-format. Den här funktionen är användbar när du vill koppla ytterligare användarinformation som din app kan använda, till exempel en ytterligare identifierare eller ett viktigt konfigurationsalternativ som användaren har angett. Se längst ner på den här sidan i ett exempel.

> [!NOTE]
> - Katalogschematillägg är en Azure AD-funktionen, så om ditt programmanifest begär ett anpassat tillägg och en MSA-användare loggar in i din app returneras inte dessa tillägg.

### <a name="directory-extension-formatting"></a>Formatering för katalogtillägg

När du konfigurerar valfria anspråk på katalogtillägg med hjälp av programmanifestet använder du tilläggets fullständiga namn (i formatet: `extension_<appid>_<attributename>`). Måste `<appid>` matcha ID för den ansökan som begär anspråket. 

Inom JWT kommer dessa anspråk att avges med `extn.<attributename>`följande namnformat: .

Inom SAML-token kommer dessa anspråk att skickas ut med följande URI-format:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Konfigurera valfria anspråk för grupper

   > [!NOTE]
   > Möjligheten att avge gruppnamn för användare och grupper som synkroniserats från lokala är Offentlig förhandsversion.

Det här avsnittet beskriver konfigurationsalternativen under valfria anspråk för att ändra gruppattribut som används i gruppanspråk från standardgruppen objectID till attribut som synkroniserats från lokala Windows Active Directory. Du kan konfigurera valfria anspråk för dina program via användargränssnittet eller programmanifestet.

> [!IMPORTANT]
> Mer information om att inkludera viktiga varningar för offentlig förhandsversion av gruppanspråk från lokala attribut finns i [Konfigurera gruppanspråk för program med Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Konfigurera valfria anspråk för grupper via användargränssnittet:**
1. Logga in på [Azure-portalen](https://portal.azure.com)
1. När du har autentiserat väljer du din Azure AD-klient genom att välja den i det övre högra hörnet på sidan
1. Välj **Azure Active Directory** på menyn till vänster
1. Under avsnittet **Hantera** väljer du **Appregistreringar**
1. Välj det program som du vill konfigurera valfria anspråk för i listan
1. Välj **Tokenkonfiguration (förhandsgranskning)** under avsnittet **Hantera**
2. Välj **anspråk på Lägg till grupper**
3. Markera de grupptyper som ska returneras (**Alla grupper,** **Säkerhetsgrupper**eller **DirectoryRole**). Alternativet **Alla grupper** innehåller **SecurityGroup,** **DirectoryRole**och **DistributionList**
4. Valfritt: Klicka på egenskaperna för specifik tokentyp för att ändra gruppanspråksvärdet som ska innehålla lokala gruppattribut eller för att ändra anspråkstypen till en roll
5. Klicka på **Spara**

**Konfigurera valfria anspråk för grupper via programmanifestet:**
1. Logga in på [Azure-portalen](https://portal.azure.com)
1. När du har autentiserat väljer du din Azure AD-klient genom att välja den i det övre högra hörnet på sidan
1. Välj **Azure Active Directory** på menyn till vänster
1. Välj det program som du vill konfigurera valfria anspråk för i listan
1. Välj **Manifest** under avsnittet **Hantera**
3. Lägg till följande post med manifestredigeraren:

   De giltiga värdena är:

   - "Alla" (det här alternativet inkluderar SecurityGroup, DirectoryRole och DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   Ett exempel:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Som standard kommer gruppobjekt-ID:er att skickas ut i gruppanspråksvärdet.  Om du vill ändra anspråksvärdet så att det innehåller lokala gruppattribut eller ändrar anspråkstypen till roll använder du OptionalClaims-konfigurationen på följande sätt:

3. Ange valfria anspråk för gruppnamnkonfiguration.

   Om du vill gruppera i token för att innehålla de lokala AD-gruppattributen i det valfria anspråksavsnittet anger vilket valfritt anspråk av tokentyp som ska tillämpas på, namnet på det begärda anspråket och eventuella ytterligare egenskaper som önskas.  Flera tokentyper kan visas:

   - idToken för OIDC ID-token
   - accessToken för OAuth-åtkomsttoken
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

**Exempel:**

1) Emit grupper som gruppnamn i OAuth-åtkomsttoken i dnsDomainName\sAMAccountName-format

    
    **Konfiguration av användargränssnitt:**

    [![Visar hur du konfigurerar valfria anspråk med hjälp av användargränssnittet](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Anmatning av programmanifest:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Gruppernamn för emit som ska returneras i netbiosDomain\sAMAccountName-format som rollerna gör anspråk på i SAML- och OIDC ID-token

    **Konfiguration av användargränssnitt:**

    [![Visar hur du konfigurerar valfria anspråk med hjälp av användargränssnittet](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Anmatning av programmanifest:**
    
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
    ``` 
     

## <a name="optional-claims-example"></a>Exempel på valfria anspråk

I det här avsnittet kan du gå igenom ett scenario för att se hur du kan använda den valfria anspråksfunktionen för ditt program.
Det finns flera alternativ för uppdatering av egenskaperna för ett programs identitetskonfiguration för att aktivera och konfigurera valfria anspråk:
-    Du kan använda **användargränssnittet för tokenkonfiguration (förhandsgranskning)** (se exempel nedan)
-    Du kan använda **Manifestet** (se exempel nedan). Läs [förstå Azure AD-programmanifestdokumentet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) först för en introduktion till manifestet.
-   Det är också möjligt att skriva ett program som använder [Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) för att uppdatera ditt program. Den [valfria kladdtypen](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) i Microsoft Graph API-referensguiden kan hjälpa dig att konfigurera valfria anspråk.

**Exempel:** I exemplet nedan använder du användargränssnittet **tokenkonfiguration (förhandsversion)** och **Manifest** för att lägga till valfria anspråk i åtkomst-, ID- och SAML-token som är avsedda för ditt program. Olika valfria anspråk läggs till i varje typ av token som programmet kan ta emot:
-    ID-token innehåller nu UPN för federerade användare i`<upn>_<homedomain>#EXT#@<resourcedomain>`den fullständiga formen ( ).
-    De åtkomsttoken som andra klienter begär för det här programmet innehåller nu auth_time anspråk
-    SAML-token innehåller nu tillägget skypeId-katalogschema (i det här exemplet är app-ID för den här appen ab603c56068041afb2f6832e2a17e237). SAML-tokens exponerar Skype-ID:t som `extension_skypeId`.

**Konfiguration av användargränssnitt:**

1. Logga in på [Azure-portalen](https://portal.azure.com)

1. När du har autentiserat väljer du din Azure AD-klient genom att välja den i det övre högra hörnet på sidan.

1. Välj **Azure Active Directory** på menyn till vänster.

1. Under avsnittet **Hantera** väljer du **Appregistreringar**.

1. Leta reda på det program som du vill konfigurera valfria anspråk för i listan och klicka på det.

1. Klicka på **Tokenkonfiguration (förhandsgranskning)** under avsnittet **Hantera** .

1. Välj **Lägg till valfritt anspråk,** välj **ID-tokentyp,** välj **upn** i listan med anspråk och klicka sedan på Lägg **till**.

1. Välj **Lägg till valfritt anspråk**, välj tokentyp för **Access,** välj **auth_time** i listan med anspråk och klicka sedan på **Lägg till**.

1. Klicka på pennikonen bredvid **upn**på översiktsskärmen för tokenkonfiguration, klicka på den **externt autentiserade** växlingsknappen och klicka sedan på **Spara**.

1. Välj **Lägg till valfritt anspråk**, välj TYPEN **SAML-token,** välj **extn.skypeID** i listan över anspråk (gäller endast om du har skapat ett Azure AD-användarobjekt som heter skypeID) och klicka sedan på **Lägg till**.

    [![Visar hur du konfigurerar valfria anspråk med hjälp av användargränssnittet](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Manifestkonfiguration:**
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. När du har autentiserat väljer du din Azure AD-klient genom att välja den i det övre högra hörnet på sidan.
1. Välj **Azure Active Directory** på menyn till vänster.
1. Leta reda på det program som du vill konfigurera valfria anspråk för i listan och klicka på det.
1. Klicka på **Manifest** under avsnittet **Hantera** för att öppna den infogade manifestredigeraren.
1. Du kan redigera manifestet direkt med hjälp av den här redigeraren. Manifestet följer schemat för [entiteten Program](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)och formaterar manifestet automatiskt när det har sparats. Nya element läggs till `OptionalClaims` i egenskapen.

    ```json
            "optionalClaims": {
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
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)

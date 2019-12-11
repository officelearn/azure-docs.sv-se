---
title: Tillhandahålla valfria anspråk till Azure AD-appar | Azure
titleSuffix: Microsoft identity platform
description: Hur du lägger till anpassade eller ytterligare anspråk till SAML 2,0 och JWT-token (JSON Web token) som utfärdats av Azure Active Directory.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcaf347eb91f8777b56bb2ea4d26985b2d75f645
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967260"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Gör så här: tillhandahålla valfria anspråk till din Azure AD-App

Programutvecklare kan använda valfria anspråk i sina Azure AD-program för att ange vilka anspråk de vill ha i token som skickas till deras program. 

Du kan använda valfria anspråk för att:

- Välj ytterligare anspråk som ska inkluderas i token för ditt program.
- Ändra beteendet för vissa anspråk som Azure AD returnerar i tokens.
- Lägg till och få till gång till anpassade anspråk för ditt program.

Listor över standard anspråk finns i [åtkomsttoken](access-tokens.md) och [id_token](id-tokens.md) dokumentation om anspråk. 

Även om det finns stöd för valfria anspråk i både v 1.0-och v 2.0-format-token, och även SAML-token, ger de mest av sitt värde när de flyttas från v 1.0 till v 2.0. Ett av målen för [v 2.0 Microsoft Identity Platform-slutpunkten](active-directory-appmodel-v2-overview.md) är mindre token-storlekar för att säkerställa optimala prestanda av klienter. Det innebär att flera anspråk som tidigare inkluderats i åtkomst-och ID-token inte längre finns i v 2.0-token och måste tillfrågas specifikt för varje program.

**Tabell 1: tillämplighet**

| Kontotyp | v 1.0-token | v 2.0-token  |
|--------------|---------------|----------------|
| Personlig Microsoft-konto  | Gäller inte  | Stöds |
| Azure AD-konto      | Stöds | Stöds |

## <a name="v10-and-v20-optional-claims-set"></a>v 1.0 och v 2.0 valfria anspråks uppsättningar

Den uppsättning valfria anspråk som är tillgängliga som standard för program som ska användas visas nedan. Om du vill lägga till anpassade valfria anspråk för programmet, se [katalog tillägg](#configuring-directory-extension-optional-claims)nedan. När anspråk läggs till i **åtkomsttoken gäller**detta för de åtkomsttoken som begärs *för* programmet (ett webb-API), inte *av* programmet. Detta säkerställer att de rätta data finns i den åtkomsttoken som de använder för att autentisera mot ditt API, oavsett vilken klient som har åtkomst till ditt API.

> [!NOTE]
> Majoriteten av dessa anspråk kan inkluderas i JWTs för v 1.0-och v 2.0-token, men inte SAML-token, förutom vad som anges i kolumnen tokentyp. Konsument konton har stöd för en delmängd av dessa anspråk som marker ATS i kolumnen "användar typ".  Många av anspråken i listan gäller inte konsument användare (de har ingen klient organisation, så `tenant_ctry` har inget värde).  

**Tabell 2: v 1.0 och v 2.0 valfri anspråks uppsättning**

| Namn                       |  Beskrivning   | Tokentyp | Användar typ | Anteckningar  |
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
| `ctry`                     | Användarens land | JWT |  | Azure AD returnerar `ctry` valfria anspråk om det finns och anspråkets värde är en vanlig landskod, till exempel FR, JP, SZ, och så vidare. |
| `tenant_ctry`              | Resurs innehavarens land | JWT | | |
| `xms_pdl`          | Önskad data plats   | JWT | | För multi-geo-klienter är detta den 3-bokstavs kod som visar den geografiska region som användaren är i. Mer information finns i Azure AD Connect- [dokumentationen om önskad data plats](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Exempel: `APC` för Asien och stillahavsområdet. |
| `xms_pl`                   | Användarens prioriterade språk  | JWT ||Användarens önskade språk, om det är inställt. Från hem klienten, i scenarier med gäst åtkomst. Formaterat lla-CC ("en-US"). |
| `xms_tpl`                  | Föredraget klient språk| JWT | | Resurs innehavarens föredragna språk, om det är inställt. Formaterat lla ("en"). |
| `ztdid`                    | ID för noll-Touch-distribution | JWT | | Enhets identiteten som används för [Windows autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Det adresser bara e-postmeddelandet för den här användaren, om användaren har ett.  | JWT, SAML | MSA, Azure AD | Det här värdet ingår som standard om användaren är en gäst i klienten.  För hanterade användare (de i klienten) måste det begäras via detta valfria anspråk eller, endast v 2.0, med OpenID-omfånget.  För hanterade användare måste e-postadressen anges i [Office Admin-portalen](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Valfri formatering för grupp anspråk |JWT, SAML| |Används tillsammans med GroupMembershipClaims-inställningen i [applikations manifestet](reference-app-manifest.md), som även måste anges. Mer information finns i [grupp anspråk](#configuring-groups-optional-claims) nedan. Mer information om grupp anspråk finns i [så här konfigurerar du grupp anspråk](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Användarens konto status i klient organisationen. | JWT, SAML | | Om användaren är medlem i klienten är värdet `0`. Om de är en gäst är värdet `1`. |
| `upn`                      | UserPrincipalName-anspråk. | JWT, SAML  |           | Även om det här anspråket ingår automatiskt kan du ange det som ett valfritt anspråk för att bifoga ytterligare egenskaper för att ändra dess beteende i gäst användarens ärende.  |

## <a name="v20-specific-optional-claims-set"></a>v 2.0 – angivna valfria anspråks uppsättningar

De här anspråken ingår alltid i v 1.0 Azure AD-tokens, men ingår inte i v 2.0-token om det inte begärs. Dessa anspråk gäller endast för JWTs (ID-token och åtkomsttoken). 

**Tabell 3: v 2.0 – endast valfria anspråk**

| JWT-anspråk     | Namn                            | Beskrivning                                | Anteckningar |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-adress                      | IP-adressen som klienten loggade in från.   |       |
| `onprem_sid`  | Lokal säkerhets identifierare |                                             |       |
| `pwd_exp`     | Förfallotid för lösenord        | Det datum/tid-värde som lösen ordet upphör att gälla. |       |
| `pwd_url`     | Ändra lösen ordets URL             | En URL som användaren kan besöka för att ändra sina lösen ord.   |   |
| `in_corp`     | Inifrån företagsnätverket        | Signalerar om klienten loggar in från företags nätverket. Om de inte är det inkluderas inte anspråket.   |  Baserat på de [betrodda IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) -inställningarna i MFA.    |
| `nickname`    | Smeknamn                        | Ett ytterligare namn för användaren, separat från förnamn eller efter namn. | 
| `family_name` | Efternamn                       | Innehåller användarens efter namn, efter namn eller familj som definierats i användarobjektet. <br>"family_name":"Miller" | Stöds i MSA och Azure AD   |
| `given_name`  | Förnamn                      | Anger det första eller "tilldelade" namnet på användaren, enligt vad som anges på användarobjektet.<br>"given_name": "Frank"                   | Stöds i MSA och Azure AD  |
| `upn`         | User Principal Name | En identifierare för den användare som kan användas med parametern username_hint.  Inte en varaktig identifierare för användaren och ska inte användas för nyckel data. | Se [Ytterligare egenskaper](#additional-properties-of-optional-claims) nedan för konfiguration av anspråket. |

### <a name="additional-properties-of-optional-claims"></a>Ytterligare egenskaper för valfria anspråk

Vissa valfria anspråk kan konfigureras för att ändra hur anspråket returneras. Dessa ytterligare egenskaper används främst för att hjälpa migrering av lokala program med olika data förväntningar (till exempel `include_externally_authenticated_upn_without_hash` hjälpa till klienter som inte kan hantera hash-märken (`#`) i UPN)

**Tabell 4: värden för konfiguration av valfria anspråk**

| Egenskapsnamn  | Ytterligare egenskaps namn | Beskrivning |
|----------------|--------------------------|-------------|
| `upn`          |                          | Kan användas för både SAML-och JWT-svar och för v 1.0-och v 2.0-token. |
|                | `include_externally_authenticated_upn`  | Inkluderar gäst-UPN som lagrats i resurs klienten. Till exempel, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Samma som ovan, förutom att hash-tecknen (`#`) ersätts med under streck (`_`), till exempel `foo_hometenant.com_EXT_@resourcetenant.com` |

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

Detta OptionalClaims-objekt gör att ID-token returneras till klienten för att inkludera ett annat UPN med ytterligare information om hem klient och resurs klient. Detta ändrar endast `upn`-anspråk i token om användaren är en gäst i klienten (som använder en annan IDP för autentisering). 

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

> [!IMPORTANT]
> Åtkomsttoken skapas **alltid** med hjälp av resurs manifestet, inte klienten.  Så i begäran `...scope=https://graph.microsoft.com/user.read...` resursen är grafen.  Därför skapas åtkomsttoken med hjälp av graf-manifestet, inte klientens manifest.  Att ändra manifestet för programmet kommer aldrig att orsaka att token för Graph ser annorlunda ut.  För att verifiera att dina `accessToken` ändringar gäller, begär du en token för ditt program, inte en annan app.  

Du kan konfigurera valfria anspråk för ditt program via användar gränssnittet eller applikations manifestet.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. När du har autentiserat väljer du din Azure AD-klient genom att välja den från det övre högra hörnet på sidan.
1. Välj **Azure Active Directory** på menyn till vänster.
1. I avsnittet **Hantera** väljer du **Appregistreringar**.
1. Välj det program som du vill konfigurera valfria anspråk för i listan.

**Konfigurera valfria anspråk genom användar gränssnittet:**

[![visar hur du konfigurerar valfria anspråk med hjälp av användar gränssnittet](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. I avsnittet **Hantera** väljer du **konfiguration av token (för hands version)** .
2. Välj **Lägg till valfritt anspråk**.
3. Välj den tokentyp som du vill konfigurera.
4. Välj de valfria anspråk som ska läggas till.
5. Klicka på **Lägg till**.

**Konfigurera valfria anspråk via applikations manifestet:**

[![visar hur du konfigurerar valfria anspråk med hjälp av app-manifestet](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. I avsnittet **Hantera** väljer du **manifest**. En webbaserad manifest redigerare öppnas, så att du kan redigera manifestet. Du kan också välja **Ladda ned** och redigera manifestet lokalt, och sedan använda **Ladda upp** för att tillämpa det på appen igen. Mer information om applikations manifestet finns i [artikeln förstå program manifestet för Azure AD](reference-app-manifest.md).

    Följande program manifest post lägger till de auth_time, ipaddr och UPN valfria anspråk till ID, åtkomst och SAML-token.

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

2. When finished, click **Save**. Now the specified optional claims will be included in the tokens for your application.    


### OptionalClaims type

Declares the optional claims requested by an application. An application can configure optional claims to be returned in each of three types of tokens (ID token, access token, SAML 2 token) it can receive from the security token service. The application can configure a different set of optional claims to be returned in each token type. The OptionalClaims property of the Application entity is an OptionalClaims object.

**Table 5: OptionalClaims type properties**

| Name        | Type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | The optional claims returned in the JWT ID token. |
| `accessToken` | Collection (OptionalClaim) | The optional claims returned in the JWT access token. |
| `saml2Token`  | Collection (OptionalClaim) | The optional claims returned in the SAML token.   |

### OptionalClaim type

Contains an optional claim associated with an application or a service principal. The idToken, accessToken, and saml2Token properties of the [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) type is a collection of OptionalClaim.
If supported by a specific claim, you can also modify the behavior of the OptionalClaim using the AdditionalProperties field.

**Table 6: OptionalClaim type properties**

| Name                 | Type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | The name of the optional claim.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | The source (directory object) of the claim. There are predefined claims and user-defined claims from extension properties. If the source value is null, the claim is a predefined optional claim. If the source value is user, the value in the name property is the extension property from the user object. |
| `essential`            | Edm.Boolean             | If the value is true, the claim specified by the client is necessary to ensure a smooth authorization experience for the specific task requested by the end user. The default value is false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Additional properties of the claim. If a property exists in this collection, it modifies the behavior of the optional claim specified in the name property.                                                                                                                                               |
## Configuring directory extension optional claims

In addition to the standard optional claims set, you can also configure tokens to include directory schema extensions. For more info, see [Directory schema extensions](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). This feature is useful for attaching additional user information that your app can use – for example, an additional identifier or important configuration option that the user has set. See the bottom of this page for an example.

> [!NOTE]
> - Directory schema extensions are an Azure AD-only feature, so if your application manifest requests a custom extension and an MSA user logs into your app, these extensions will not be returned.
> - Azure AD optional claims only work with Azure AD Graph extensions and do not work with Microsoft Graph directory extensions. Both APIs require the `Directory.ReadWriteAll` permission, which can only be consented by admins.

### Directory extension formatting

When configuring directory extension optional claims using the application manifest, use the full name of the extension (in the format: `extension_<appid>_<attributename>`). The `<appid>` must match the ID of the application requesting the claim. 

Within the JWT, these claims will be emitted with the following name format:  `extn.<attributename>`.

Within the SAML tokens, these claims will be emitted with the following URI format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## Configuring groups optional claims

   > [!NOTE]
   > The ability to emit group names for users and groups synced from on-premises is Public Preview.

This section covers the configuration options under optional claims for changing the group attributes used in group claims from the default group objectID to attributes synced from on-premises Windows Active Directory. You can configure groups optional claims for your application through the UI or application manifest.

> [!IMPORTANT]
> See [Configure group claims for applications with Azure AD](../hybrid/how-to-connect-fed-group-claims.md) for more details including important caveats for the public preview of group claims from on-premises attributes.

**Configuring groups optional claims through the UI:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Under the **Manage** section, select **App registrations**
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Token configuration (preview)**
2. Select **Add groups claim**
3. Select the group types to return (**All Groups**, **SecurityGroup** or **DirectoryRole**). The **All Groups** option includes **SecurityGroup**, **DirectoryRole** and **DistributionList**
4. Optional: click on the specific token type properties to modify the groups claim value to contain on premises group attributes or to change the claim type to a role
5. Click **Save**

**Configuring groups optional claims through the application manifest:**
1. Sign in to the [Azure portal](https://portal.azure.com)
1. After you've authenticated, choose your Azure AD tenant by selecting it from the top right corner of the page
1. Select **Azure Active Directory** from the left hand menu
1. Select the application you want to configure optional claims for in the list
1. Under the **Manage** section, select **Manifest**
3. Add the following entry using the manifest editor:

   The valid values are:

   - "All" (this option includes SecurityGroup, DirectoryRole and DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   For example:

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

   | Valfritt anspråks schema | Värde |
   |----------|-------------|
   | **Namn:** | Måste vara "grupper" |
   | **källicensservern** | Används inte. Utelämna eller ange null |
   | **största** | Används inte. Utelämna eller ange falskt |
   | **additionalProperties:** | Lista över ytterligare egenskaper.  Valid options are "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name”, "emit_as_roles" |

   I additionalProperties krävs bara en av "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Om det finns fler än en används den första och andra ignoreras.

   Vissa program kräver grupp information om användaren i roll anspråket.  Om du vill ändra anspråks typen till från ett grupp anspråk till ett roll anspråk lägger du till "emit_as_roles" i ytterligare egenskaper.  Grupp värden genereras i roll anspråket.

   > [!NOTE]
   > Om emit_as_roles används alla program roller som kon figurer ATS som användaren är tilldelad visas inte i roll anspråket

**Exempel:**

1) Generera grupper som grupp namn i OAuth-åtkomsttoken i dnsDomainName\sAMAccountName-format

    
    **GRÄNSSNITTs konfiguration:**

    [![visar hur du konfigurerar valfria anspråk med hjälp av användar gränssnittet](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Post för program manifest:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) Genererar grupp namn som ska returneras i netbiosDomain\sAMAccountName-format som roll anspråk i SAML-och OIDC-ID-token

    **GRÄNSSNITTs konfiguration:**

    [![visar hur du konfigurerar valfria anspråk med hjälp av användar gränssnittet](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Post för program manifest:**
    
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

I det här avsnittet kan du gå igenom ett scenario för att se hur du kan använda funktionen valfria anspråk för ditt program.
Det finns flera tillgängliga alternativ för att uppdatera egenskaperna för ett programs identitets konfiguration för att aktivera och konfigurera valfria anspråk:
-    Du kan använda användar gränssnittet för **token-konfiguration (förhands granskning)** (se exemplet nedan)
-    Du kan använda **manifestet** (se exemplet nedan). Läs [förstå Azure AD Application manifest-dokumentet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) först för en introduktion till manifestet.
-   Det är också möjligt att skriva ett program som använder [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) för att uppdatera ditt program. [Referensen för entiteter och komplex typ](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) i Graph API referens guide kan hjälpa dig att konfigurera de valfria anspråken.

**Exempel:** I exemplet nedan använder du användar gränssnittet för **token-konfiguration (för hands version)** och **manifestet** för att lägga till valfria anspråk i åtkomst, ID och SAML-token som är avsedda för ditt program. Olika valfria anspråk kommer att läggas till i varje tokentyp som programmet kan ta emot:
-    ID-token kommer nu att innehålla UPN för federerade användare i fullständig form (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
-    De åtkomsttoken som andra klienter begär för det här programmet kommer nu att innehålla auth_time-anspråk
-    SAML-token kommer nu att innehålla skypeId Directory schema-tillägget (i det här exemplet är app-ID: t för den här appen ab603c56068041afb2f6832e2a17e237). SAML-token kommer att exponera Skype-ID: t som `extension_skypeId`.

**GRÄNSSNITTs konfiguration:**

1. Logga in på [Azure-portalen](https://portal.azure.com)

1. När du har autentiserat väljer du din Azure AD-klient genom att välja den från det övre högra hörnet på sidan.

1. Välj **Azure Active Directory** på menyn till vänster.

1. Under avsnittet **Hantera** väljer du **Appregistreringar**.

1. Hitta det program som du vill konfigurera valfria anspråk för i listan och klicka på det.

1. Under avsnittet **Hantera** klickar du på **token-konfiguration (för hands version)** .

1. Välj **Lägg till valfritt anspråk**, Välj typ av **ID-** token, Välj **UPN** i listan över anspråk och klicka sedan på **Lägg till**.

1. Välj **Lägg till valfritt anspråk**, Välj typ **av åtkomsttoken,** Välj **auth_time** i listan över anspråk och klicka sedan på **Lägg till**.

1. På skärmen översikt över token konfiguration klickar du på Penn ikonen bredvid **UPN**, klickar på den **externt autentiserade** växlingen och klickar sedan på **Spara**.

1. Välj **Lägg till valfritt anspråk**, Välj **SAML** -tokentyp och välj **EXTn. skypeID** i listan över anspråk (gäller endast om du har skapat ett Azure AD-användarobjektet som heter SkypeID) och klickar sedan på **Lägg till**.

    [![visar hur du konfigurerar valfria anspråk med hjälp av användar gränssnittet](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Manifest konfiguration:**
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. När du har autentiserat väljer du din Azure AD-klient genom att välja den från det övre högra hörnet på sidan.
1. Välj **Azure Active Directory** på menyn till vänster.
1. Hitta det program som du vill konfigurera valfria anspråk för i listan och klicka på det.
1. Öppna den infogade manifest redigeraren genom att klicka på **manifest** under avsnittet **Hantera** .
1. Du kan redigera manifestet direkt med hjälp av den här redigeraren. Manifestet följer schemat för entiteten [program]. (https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) och formaterar automatiskt manifestet när det har sparats. Nya element läggs till i egenskapen `OptionalClaims`.

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

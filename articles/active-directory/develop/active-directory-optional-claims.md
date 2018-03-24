---
title: Lär dig att ange valfria anspråk till Azure AD-program | Microsoft Docs
description: En guide för att lägga till anpassade och ytterligare anspråk till tokens SAML 2.0 och token JWT (JSON Web) som utfärdats av Azure Active Directory.
documentationcenter: na
author: hpsin
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/15/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 0cfa79b9c44953c613eaec8d701f351c6f2ce212
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="optional-claims-in-azure-ad-preview"></a>Valfria anspråk i Azure AD (förhandsgranskning)

Den här funktionen används av programutvecklare för att ange vilka anspråk som de vill ha i token som skickas till sina program. Du kan använda valfri anspråk till:
-   Välj ytterligare anspråk ska ingå i token för ditt program.
-   Ändra beteendet för vissa anspråk som Azure AD returnerar i token.
-   Lägg till och komma åt anpassade anspråk för ditt program. 

> [!Note]
> Den här funktionen är för närvarande i förhandsversion. Var beredd på att återställa eller ta bort alla ändringar. Funktionen är tillgänglig i alla Azure AD-prenumeration under förhandsversion. När funktionen blir allmänt tillgänglig, kan vissa aspekter av funktionen kräver en Azure AD premium-prenumeration.

Lista över standard anspråk och hur de används i token, finns det [grunderna för token som utfärdats av Azure AD](active-directory-token-and-claims.md). 

Ett av målen för den [v2.0 Azure AD-slutpunkten](active-directory-appmodel-v2-overview.md) är token mindre säkerställa optimala prestanda av klienter.  Därför inte längre finns i v2.0 token flera anspråk som tidigare inkluderad i åtkomst och ID-token och att uppge särskilt på en applikationsspecifik basis.  

**Tabell 1: tillämplighet**

| Kontotyp | V1.0 slutpunkt                      | V2.0-slutpunkten  |
|--------------|------------------------------------|----------------|
| MSA          | Ej tillämpligt - RPS biljetter används i stället | Stöd för kommer |
| AAD          | Stöds                          | Stöds      |

## <a name="standard-optional-claims-set"></a>Standard valfria anspråksuppsättningen
Nedan visas en uppsättning valfria anspråk som är tillgängliga som standard för program att använda.  Om du vill lägga till anpassade valfria anspråk för ditt program, se [Katalogtillägg](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions), nedan. 

> [!Note]
>Flesta av dessa anspråk kan ingå i JWTs, men inte SAML-token, förutom där annat anges i kolumnen tokentyp.  Dessutom valfria anspråk är endast stöd för AAD-användare för närvarande har MSA stöd lagts till.  MSA med valfritt anspråk stöd på v2.0-slutpunkten kan ange kolumnen användartyp om ett anspråk är tillgänglig för en användare AAD eller MSA.  

**Tabell 2: Standard valfria anspråksuppsättningen**

| Namn                     | Beskrivning                                                                                                                                                                                     | Tokentypen | Användartyp | Anteckningar                                                                                                                                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `auth_time`                | Tid när det senast autentisera användaren.  Se OpenID Connect-specifikationen.                                                                                                                                | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_region_scope`      | Region för resurs-klienten                                                                                                                                                                   | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `signin_state`             | Logga in tillstånd anspråk                                                                                                                                                                             | JWT        |           | 6 returvärden som flaggor:<br> ”dvc_mngd”: enheten hanteras<br> ”dvc_cmp”: enheten är kompatibel<br> ”dvc_dmjd”: enheten är ansluten till en domän<br> ”dvc_mngd_app”: enheten hanteras via MDM<br> ”inknownntwk”: enheten är i ett kända nätverk.<br> ”kmsi”: Behåll mig inloggad i användes. <br> |
| `controls`                 | Flervärden är anspråk som innehåller session-kontroller som tillämpas av principer för villkorlig åtkomst.                                                                                                       | JWT        |           | 3 värden:<br> ”app_res”: appen behöver använda mer detaljerade begränsningarna. <br> ”ca_enf”: tillämpning av villkorlig åtkomst sköts upp och fortfarande krävs. <br> ”no_cookie”: denna token är tillräckligt för att exchange-cookies i webbläsaren. <br>                              |
| `home_oid`                 | För gästanvändare, objekt-ID för användaren i användarens hemnätverk klient.                                                                                                                           | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `sid`                      | Sessions-ID används för sessions-användaren utloggning.                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `platf`                    | Enhetsplattform                                                                                                                                                                                 | JWT        |           | Begränsad till hanterade enheter som kan verifiera typ av enhet.                                                                                                                                                                                                                              |
| `verified_primary_email`   | Kommer från användarens PrimaryAuthoritativeEmail                                                                                                                                               | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `verified_secondary_email` | Kommer från användarens SecondaryAuthoritativeEmail                                                                                                                                             | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `enfpolids`                | Tvingande princip-ID: N. En lista över ID: N som har utvärderats för den aktuella användaren.                                                                                                         | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `vnet`                     | VNET-specificerare information.                                                                                                                                                                     | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `fwd`                      | IP-adress.  Lägger till den ursprungliga IPv4-adressen för den begärande klienten (inom ett VNET)                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `ctry`                     | Användarens land                                                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_ctry`              | Resurs-klient land                                                                                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `is_device_known`          | Anger om enheten är ansluten till arbetsplatsen. Relaterade till principen för villkorlig åtkomst                                                                                                                 | SAML       |           | Konvergerat till signin_state för JWTs,                                                                                                                                                                                                                                                   |
| `is_device_managed`        | Anger om enheten har MDM installerad. Relaterade till principen för villkorlig åtkomst.                                                                                                                  | SAML       |           | Konvergerat till signin_state för JWTs,                                                                                                                                                                                                                                                   |
| `is_device_compliant`      | Anger att MDM har fastställt enheten är kompatibel med säkerhetsprinciper för enheter i organisationen.                                                                                  | SAML       |           | Konvergerat till signin_state för JWTs,                                                                                                                                                                                                                                                   |
| `kmsi`                     | Anger om användaren har valt alternativet hålla mig inloggad i.                                                                                                                                    | SAML       |           | Konvergerat till signin_state för JWTs,                                                                                                                                                                                                                                                   |
| `upn`                      | UserPrincipalName claim.  Även om det här anspråket ingår automatiskt, kan du ange den som ett valfritt anspråk att koppla ytterligare egenskaper om du vill ändra sitt beteende gästen användaren om. | JWT SAML  |           | Ytterligare egenskaper: <br> include_externally_authenticated_upn <br> include_externally_authenticated_upn_without_hash                                                                                                                                                                 |
| `groups`                   | De grupper som användaren är medlem i.                                                                                                                                                               | JWT SAML  |           | Ytterligare egenskaper: <br> Sam_account_name<br> Dns_domain_and_sam_account_name<br> Netbios_domain_and_sam_account<br> Max_size_limit<br> Emit_as_roles<br>                                                                                                                            |

### <a name="v20-optional-claims"></a>V2.0 valfria anspråk
De här anspråken ska ingå alltid i v1.0 token, men tas bort från v2.0-token, såvida inte begär.  Dessa anspråk kan endast användas för JWTs (ID-token och åtkomst-token).  

**Tabell 3: Endast V2.0 valfria anspråk**

| JWT-anspråk     | Namn                            | Beskrivning                                                                                                                    | Anteckningar |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | IP-adress                      | IP-adressen loggade in från klienten.                                                                                      |       |
| `onprem_sid`  | Säkerhetsidentifieraren för lokalt |                                                                                                                                |       |
| `pwd_exp`     | Förfallotid för lösenord        | Datetime lösenordet går ut.                                                                                    |       |
| `pwd_url`     | Ändra lösenord URL             | En URL som användaren besöker om du vill ändra sina lösenord.                                                                        |       |
| `in_corp`     | Inifrån företagsnätverket        | Signalerar om klienten loggar in från företagsnätverket. Om inte ingår anspråket inte                     |       |
| `nickname`    | Smeknamn                        | Ytterligare ett namn för användaren, separat från förnamn eller efternamn.                                                             |       |                                                                                                                |       |
| `family_name` | Efternamn                       | Innehåller sista namn, efternamn eller efternamn för användaren som har definierats i Azure AD-användarobjektet. <br>"family_name":"Miller" |       |
| `given_name`  | Förnamn                      | Innehåller först eller ”” namnet på användaren som angetts i Azure AD-användarobjektet.<br>”given_name”: ”Frank”                   |       |

### <a name="additional-properties-of-optional-claims"></a>Ytterligare egenskaper för valfri anspråk

Vissa valfria anspråk kan konfigureras för att ändra hur anspråket returneras.  Dessa ytterligare egenskaper intervall från formateringsändringar (till exempel `include_externally_authenticated_upn_without_hash`) att ändra en uppsättning data returneras (`Dns_domain_and_sam_account_name`).

**Tabell 4: Värden för att konfigurera standard valfria anspråk**

| Egenskapsnamn                                     | Ytterligare egenskapsnamn                                                                                                             | Beskrivning |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `Upn`                                                 |                                                                                                                                      |             |
| | `include_externally_authenticated_upn`              | Innehåller gästen UPN som lagras i resurs-klient.  Till exempel, `foo_hometenant.com#EXT#@resourcetenant.com`                            |             
| | `include_externally_authenticated_upn_without_hash` | Samma som ovan, med undantag för att hashmarks (`#`) ersätts med understreck (`_`), till exempel `foo_hometenant.com_EXT_@resourcetenant.com` |             
| `groups`                                              |                                                                                                                                      |             |
| | `sam_account_name`                                  |                                                                                                                                      |             
| | `dns_domain_and_sam_account_name`                   |                                                                                                                                      |             
| | `netbios_domain_and_sam_account_name`               |                                                                                                                                      |             
| | `max_size_limit`                                    | Genererar antalet grupper som returneras till max grupp storleksgränsen (1 000).                                                            |             
| | `emit_as_roles`                                     | Skickar ett ”roller” anspråk i stället för ”grupper”-anspråk med samma värden.  Avsedd för appar som migrerar från en lokal miljö där RBAC traditionellt styrs via gruppmedlemskap.   |             

> [!Note]
>Anger valfria anspråket upn utan ytterligare en egenskap inte ändras någon funktion – för att visa ett nytt anspråk som utfärdats i token, måste minst en av de ytterligare egenskaperna läggas till. 
>
>Den `account_name` ytterligare egenskaper för grupper är inte kompatibla, och sorteringen av ytterligare egenskaper som är viktigt – endast det första kontot namnegenskap ytterligare anges kommer att användas. 

#### <a name="additional-properties-example"></a>Ytterligare egenskaper som exempel:

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "groups", 
            "essential": false,
                "additionalProperties": [ "netbios_domain_and_sam_account_name", "sam_account_name" , "emit_as_roles"]  
              }
        ]
}
```

Det här OptionalClaims-objektet som returneras av samma `groups` anspråk som om `sam_account_name` inte ingår – eftersom den är efter `netbios_domain_and_sam_account_name`, ignoreras. 

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

Du kan konfigurera valfria anspråk för ditt program genom att ändra programmanifestet (se exemplet nedan). Mer information finns i [förstå Azure AD application manifest artikel](active-directory-application-manifest.md).

**Exempelschema:**

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
                    "name": "upn", 
                    "essential": true
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>OptionalClaims typ

Deklarerar valfria anspråk som begärs av ett program. Ett program kan konfigurera valfria anspråk som ska returneras i var och en av tre typer av token (ID token, token, SAML 2 åtkomsttoken) kan ta emot från säkerhetstokentjänsten. Programmet kan konfigurera en annan uppsättning valfria anspråk som ska returneras i varje typ av token. Egenskapen OptionalClaims för entiteten programmet är ett OptionalClaims-objekt.

**Tabell 5: OptionalClaims egenskaper**

| Namn        | Typ                       | Beskrivning                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Samling (OptionalClaim) | Valfria anspråk som returneras i ID för JWT-token.     |
| `accessToken` | Samling (OptionalClaim) | Valfria anspråken returneras i JWT åtkomst-token. |
| `saml2Token`  | Samling (OptionalClaim) | Valfria anspråken returneras i SAML-token.       |


### <a name="optionalclaim-type"></a>OptionalClaim typ

Innehåller ett valfritt anspråk som är associerad med ett program eller ett huvudnamn för tjänsten. IdToken och accessToken saml2Token egenskaperna för den [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) typ är en samling OptionalClaim.
Du kan också ändra beteendet för OptionalClaim med hjälp av fältet AdditionalProperties om det stöds av en specifik begäran.

**Tabell 6: OptionalClaim egenskaper**

| Namn                 | Typ                    | Beskrivning                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Namnet på valfri anspråket.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | Källan (katalogobjekt) för anspråk. Det finns fördefinierade anspråk och anpassade anspråk från egenskaperna för tillägget. Om källvärdet är null är i anspråk ett fördefinierat valfria anspråk. Om källvärdet är användaren, är värdet i egenskapen name extension-egenskapen från användarobjektet. |
| `essential`            | Edm.Boolean             | Om värdet är true, är anspråk som angetts av klienten nödvändigt för att säkerställa en smidig auktorisering upplevelse för den uppgift som begärs av användaren. Standardvärdet är false.                                                                                                                 |
| `additionalProperties` | Samling (Edm.String) | Ytterligare egenskaper för anspråk. Om det finns en egenskap i den här samlingen, ändrar beteendet för den valfria anspråk som anges i egenskapen name.                                                                                                                                                   |

## <a name="configuring-custom-claims-via-directory-extensions"></a>Konfigurera anpassade anspråk via katalogtillägg

Utöver anspråksuppsättningen standard valfria token också konfigureras för att omfatta schematillägg för katalogen (finns i [Directory schema tillägg artikel](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) mer information).  Den här funktionen är användbart för att koppla ytterligare användarinformation som din app kan använda – till exempel, en ytterligare identifierare eller viktiga konfigurationsalternativ som användaren har angett. 

> [!Note]
> Directory-schemautökningar är endast AAD-funktionen, så om din applikationsmanifestet begäranden ett anpassat tillägg och en MSA-användare loggar in på din app, returneras dessa tillägg inte. 

### <a name="values-for-configuring-additional-optional-claims"></a>Värden för att konfigurera ytterligare valfria anspråk 

För tilläggsattribut, använder du det fullständiga namnet på filnamnstillägget (i formatet: `extension_<appid>_<attributename>`) i programmanifestet. Den `<appid>` måste matcha id för programmet begär anspråket. 

Inom JWT, kommer dessa anspråk ska skickas med namnformatet: `extn.<attributename>`.

I SAML-token kommer dessa anspråk ska skickas med formatet URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="optional-claims-example"></a>Exempel på valfria anspråk

I det här avsnittet kan du gå igenom ett scenario för att se hur du kan använda funktionen valfria anspråk för ditt program.
Det finns flera alternativ för att uppdatera egenskaperna för ett program identitet konfiguration för att aktivera och konfigurera valfria anspråk:
-   Du kan ändra programmanifestet. Exemplet nedan använder den här metoden för att utföra konfigurationen. Läs den [förstå Azure AD application manifest dokumentet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) första en introduktion till manifestet.
-   Det är också möjligt att skriva ett program som använder den [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) att uppdatera ditt program. Den [entitetstyper och komplexa Typreferensen](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) i referens för Graph API handboken hjälper dig med att konfigurera valfria anspråk.

**Exempel:** i exemplet ovan, ändrar du ett programmanifest för att lägga till anspråk till åtkomst-ID och SAML token är avsedda för programmet.
1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  När du har autentiserats, väljer du din Azure AD-klient genom att välja den i det övre högra hörnet på sidan.
3.  Välj **Azure AD-tillägget** från den vänstra navigeringsfönstret och klicka på **App registreringar**.
4.  Sök efter programmet som du vill konfigurera valfria anspråk för i listan och klicka på den.
5.  Program-sidan klickar du på **Manifest** att öppna redigeraren infogade manifestet. 
6.  Du kan redigera manifestet med den här redigeraren direkt. Manifestet följer schemat för den [programmet entiteten](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity), och automatisk format manifestet sparas en gång. Nya elemets kommer att läggas till i `OptionalClaims` egenskap.

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
      I det här fallet har olika valfria anspråk lagts till varje typ av token som programmet kan ta emot. ID-token innehåller nu UPN för externa användare i den fullständiga formen (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Åtkomst-token kommer nu att få auth_time anspråket. SAML-token innehåller nu skypeId directory-schematillägget (i det här exemplet är det app-ID för den här appen ab603c56068041afb2f6832e2a17e237).  SAML-token ska visa Skype-ID som `extension_skypeId`.

7.  När du är klar uppdaterar manifestet klickar du på **spara** spara manifestet


## <a name="related-content"></a>Relaterat innehåll
* Lär dig mer om den [standard anspråk](active-directory-token-and-claims.md) tillhandahålls av Azure AD. 
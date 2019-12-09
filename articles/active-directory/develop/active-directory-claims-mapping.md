---
title: Anpassa anspråk för Azure AD-klientens appar
titleSuffix: Microsoft identity platform
description: Den här sidan beskriver Azure Active Directory anspråks mappning.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8d15631c30566d7588b562f1bb0d6ba5280e699
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918431"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Gör så här: anpassa anspråk som skickas i token för en angiven app i en klient (för hands version)

> [!NOTE]
> Den här funktionen ersätter och ersätter den [anpassning av anspråk](active-directory-saml-claims-customization.md) som erbjuds via portalen idag. Om du i samma program anpassar anspråk med hjälp av portalen, förutom den graf/PowerShell-metod som beskrivs i det här dokumentet, kommer tokens som utfärdats för programmet att ignorera konfigurationen i portalen. Konfigurationer som görs genom de metoder som beskrivs i det här dokumentet visas inte i portalen.

Den här funktionen används av klient administratörer för att anpassa de anspråk som skickas i token för ett särskilt program i klienten. Du kan använda anspråk mappnings principer för att:

- Välj vilka anspråk som ska ingå i tokens.
- Skapa anspråks typer som inte redan finns.
- Välj eller ändra data källan som skickas i vissa anspråk.

> [!NOTE]
> Den här funktionen finns för närvarande i offentlig för hands version. Observera att du kan behöva återställa eller ta bort eventuella ändringar. Funktionen är tillgänglig i en Azure Active Directory (Azure AD)-prenumeration under den offentliga för hands versionen. Men när funktionen blir allmänt tillgänglig kan vissa aspekter av funktionen kräva en Azure AD Premium-prenumeration. Den här funktionen stöder konfigurering av anspråks mappnings principer för WS-utfodras-, SAML-, OAuth-och OpenID Connect-protokoll.

## <a name="claims-mapping-policy-type"></a>Princip typ för anspråks mappning

I Azure AD representerar ett **princip** objekt en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation. Varje typ av princip har en unik struktur med en uppsättning egenskaper som sedan tillämpas på objekt som de är tilldelade till.

En princip för anspråks mappning är en typ av **princip** objekt som ändrar de anspråk som skickats i token som utfärdas för vissa program.

## <a name="claim-sets"></a>Anspråks uppsättningar

Det finns vissa uppsättningar med anspråk som definierar hur och när de används i tokens.

| Anspråks uppsättning | Beskrivning |
|---|---|
| Uppsättning Core-anspråk | Förekommer i varje token oavsett principen. Dessa anspråk anses också vara begränsade och kan inte ändras. |
| Grundläggande anspråks uppsättning | Innehåller de anspråk som genereras som standard för token (utöver uppsättningen med kärn anspråk). Du kan utelämna eller ändra grundläggande anspråk genom att använda anspråks mappnings principerna. |
| Begränsad anspråks uppsättning | Kan inte ändras med en princip. Det går inte att ändra data källan och ingen omvandling används när dessa anspråk genereras. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabell 1: begränsad anspråks uppsättning för JSON Web Token (JWT)

| Anspråks typ (namn) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| AMR |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| undanta |
| appidacr |
| assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| CNF |
| code |
| kontroller |
| credential_keys |
| csr |
| csr_type |
| DeviceID |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-post |
| slutpunkt |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| grupper |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| ISS |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| NameID |
| nbf |
| netbios_name |
| Nnär |
| OID |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| lösenord |
| platf |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| roll |
| roles |
| omfång |
| punkten |
| sid |
| signature |
| signin_state |
| src1 |
| src2 |
| Build |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| bekräfta |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| användarnamn |
| uti: er |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabell 2: begränsad SAML-anspråks uppsättning

| Anspråks typ (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Princip egenskaper för anspråks mappning

Du styr vilka anspråk som ska genereras och var data kommer från genom att använda egenskaperna för en princip för anspråks mappning. Om en princip inte har angetts utfärdar systemet token som innehåller den kärn anspråks uppsättningen, den grundläggande anspråks uppsättningen och eventuella [valfria anspråk](active-directory-optional-claims.md) som programmet har valt att ta emot.

### <a name="include-basic-claim-set"></a>Inkludera grundläggande anspråks uppsättning

**Sträng:** IncludeBasicClaimSet

**Datatyp:** Boolesk (sant eller falskt)

**Sammanfattning:** Den här egenskapen avgör om den grundläggande anspråks uppsättningen ingår i tokens som påverkas av den här principen.

- Om värdet är true genereras alla anspråk i den grundläggande anspråks uppsättningen i tokens som påverkas av principen. 
- Om värdet är false är anspråk i den grundläggande anspråks uppsättningen inte i tokens, om de inte individuellt läggs till i egenskapen anspråk schema för samma princip.

> [!NOTE] 
> Anspråk i den kärn anspråks uppsättningen finns i varje token, oavsett vad den här egenskapen är inställd på. 

### <a name="claims-schema"></a>Anspråks schema

**Sträng:** ClaimsSchema

**Datatyp:** JSON-BLOB med en eller flera anspråks schema poster

**Sammanfattning:** Den här egenskapen definierar vilka anspråk som finns i de token som påverkas av principen, förutom den grundläggande anspråks uppsättningen och kärn anspråks uppsättningen.
För varje anspråks schema post som definieras i den här egenskapen krävs viss information. Ange var data kommer från (**värde** eller **käll-ID-par**) och vilka anspråk data genereras som (**anspråks typ**).

### <a name="claim-schema-entry-elements"></a>Poster för anspråks schema poster

**Värde:** Värdet element definierar ett statiskt värde som de data som ska genereras i anspråket.

**Käll-/ID-par:** Käll-och ID-elementen definierar var data i anspråket ska hämtas från. 

Ange käll elementet till något av följande värden: 

- "användare": data i anspråket är en egenskap för objektet användare. 
- "program": data i anspråket är en egenskap på program tjänstens huvud namn (klient). 
- "resurs": data i anspråket är en egenskap för resurs tjänstens huvud namn.
- "Audience": data i anspråket är en egenskap för tjänstens huvud namn som är mål gruppen för token (antingen klienten eller resurs tjänstens huvud namn).
- "företag": data i anspråket är en egenskap för resurs innehavarens företags objekt.
- "omvandling": data i anspråket är från en anspråks omvandling (se avsnittet "anspråks omvandling" längre fram i den här artikeln).

Om källan är omvandling, måste **TransformationID** -elementet inkluderas i denna anspråks definition också.

ID-elementet identifierar vilken egenskap på källan som innehåller värdet för anspråket. I följande tabell visas värdena för de ID som är giltiga för varje värde för källa.

#### <a name="table-3-valid-id-values-per-source"></a>Tabell 3: giltiga ID-värden per källa

| Källa | ID | Beskrivning |
|-----|-----|-----|
| Användare | surname | Familjenamn |
| Användare | givenname | Förnamn |
| Användare | displayname (visningsnamn) | Visningsnamn |
| Användare | objectID | ObjectID |
| Användare | e-post | E-postadress |
| Användare | userPrincipalName | User Principal Name |
| Användare | avdelning|Avdelning|
| Användare | egna namnet onpremisessamaccountname | Namn på lokal SAM-konto |
| Användare | NetbiosName| NetBios-namn |
| Användare | dnsdomainname | DNS-domännamn |
| Användare | onpremisesecurityidentifier | Lokal säkerhets identifierare |
| Användare | CompanyName| Organisationens namn |
| Användare | streetaddress | Gatuadress |
| Användare | post nummer | Postnummer |
| Användare | preferredlanguange | Önskat språk |
| Användare | onpremisesuserprincipalname | Lokalt UPN |
| Användare | MailNickName | E-postsmek namn |
| Användare | extensionattribute1 | Attribut 1 för tillägg |
| Användare | extensionattribute2 | Attribut för tillägg 2 |
| Användare | extensionattribute3 | Attribut för tillägg 3 |
| Användare | extensionattribute4 | Attribut för tillägg 4 |
| Användare | extensionattribute5 | Attribut för tillägg 5 |
| Användare | extensionattribute6 | Attribut för tillägg 6 |
| Användare | extensionattribute7 | Attribut för tillägg 7 |
| Användare | extensionattribute8 | Attribut för tillägg 8 |
| Användare | extensionattribute9 | Attribut för tillägg 9 |
| Användare | extensionattribute10 | Attribut för tillägg 10 |
| Användare | extensionattribute11 | Attribut för tillägg 11 |
| Användare | extensionattribute12 | Attribut för tillägg 12 |
| Användare | extensionattribute13 | Attribut för tillägg 13 |
| Användare | extensionattribute14 | Attribut för tillägg 14 |
| Användare | extensionattribute15 | Attribut för tillägg 15 |
| Användare | othermail | Annan e-post |
| Användare | land | Land |
| Användare | city | Ort |
| Användare | state | Status |
| Användare | befattning | Befattning |
| Användare | employeeid | Anställnings-ID |
| Användare | facsimiletelephonenumber | Facsimile-telefonnummer |
| program, resurs, mål grupp | displayname (visningsnamn) | Visningsnamn |
| program, resurs, mål grupp | inobjekt | ObjectID |
| program, resurs, mål grupp | tags | Tjänstens huvud namns etikett |
| Företag | tenantcountry | Innehavarens land |

**TransformationID:** TransformationID-elementet får bara anges om käll elementet har angetts till "Transformation".

- Elementet måste matcha ID-elementet för omvandlings posten i **ClaimsTransformation** -egenskapen som definierar hur data för det här anspråket genereras.

**Anspråks typ:** **JwtClaimType** -och **SamlClaimType** -elementen definierar vilka anspråk som den här anspråks schema posten refererar till.

- JwtClaimType måste innehålla namnet på det anspråk som ska genereras i JWTs.
- SamlClaimType måste innehålla URI: n för det anspråk som ska genereras i SAML-token.

> [!NOTE]
> Namn och URI: er för anspråk i den begränsade anspråks uppsättningen kan inte användas för anspråks typ element. Mer information finns i avsnittet "undantag och begränsningar" längre fram i den här artikeln.

### <a name="claims-transformation"></a>Anspråkstransformering

**Sträng:** ClaimsTransformation

**Datatyp:** JSON-BLOB med en eller flera omvandlings poster 

**Sammanfattning:** Använd den här egenskapen för att tillämpa vanliga transformeringar för källdata för att generera utdata för anspråk som anges i anspråks schemat.

**ID:** Använd ID-elementet för att referera till den här omvandlings posten i schema posten TransformationID Claims. Det här värdet måste vara unikt för varje omvandlings post i den här principen.

**TransformationMethod:** TransformationMethod-elementet identifierar vilken åtgärd som utförs för att generera data för anspråket.

Baserat på den valda metoden förväntas en uppsättning indata och utdata. Definiera indata och utdata med hjälp av elementen **InputClaims**, **indataparametrar** och **OutputClaims** .

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabell 4: omvandlings metoder och förväntade indata och utdata

|TransformationMethod|Förväntad Indatatyp|Förväntad utdata|Beskrivning|
|-----|-----|-----|-----|
|Slå ihop|sträng1, sträng2, avgränsare|outputClaim|Kopplar ihop inmatade strängar med hjälp av en avgränsare mellan. Exempel: sträng1: "foo@bar.com", sträng2: "sandbox", avgränsare: "." resulterar i outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|e-post|outputClaim|Extraherar den lokala delen av en e-postadress. Exempel: mail: "foo@bar.com" resulterar i outputClaim: "foo". Om det inte finns något \@-tecken returneras den ursprungliga Indatasträngen som den är.|

**InputClaims:** Använd ett InputClaims-element för att skicka data från en anspråks schema post till en omvandling. Det har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId** är ansluten med ID-elementet för anspråks schema posten för att hitta rätt indatamängds-anspråk. 
- **TransformationClaimType** används för att ge det unika namnet för den här indatamängden. Det här namnet måste matcha en av förväntade indata för omvandlings metoden.

**Indataparametrar:** Använd ett indataparametrar för att skicka ett konstant värde till en Transformation. Det har två attribut: **Value** och **ID**.

- **Värde** är det faktiska konstantvärde som ska skickas.
- **ID** används för att ge indatatypen ett unikt namn. Namnet måste matcha en av förväntade indata för Transformations metoden.

**OutputClaims:** Använd ett OutputClaims-element för att lagra data som genereras av en omvandling och koppla dem till en anspråks schema post. Det har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId** är kopplat till ID: t för anspråks schema posten för att hitta rätt utgående anspråk.
- **TransformationClaimType** används för att ge utdata ett unikt namn. Namnet måste matcha en av förväntade utdata för omvandlings metoden.

### <a name="exceptions-and-restrictions"></a>Undantag och begränsningar

**SAML-NameID och UPN:** De attribut som du använder för att källa NameID-och UPN-värden, och de anspråk omvandlingar som tillåts, är begränsade. Se tabell 5 och tabell 6 för att se tillåtna värden.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabell 5: attribut som tillåts som data källa för SAML-NameID

|Källa|ID|Beskrivning|
|-----|-----|-----|
| Användare | e-post|E-postadress|
| Användare | userPrincipalName|User Principal Name|
| Användare | egna namnet onpremisessamaccountname|Namn på lokal SAM-konto|
| Användare | employeeid|Anställnings-ID|
| Användare | extensionattribute1 | Attribut 1 för tillägg |
| Användare | extensionattribute2 | Attribut för tillägg 2 |
| Användare | extensionattribute3 | Attribut för tillägg 3 |
| Användare | extensionattribute4 | Attribut för tillägg 4 |
| Användare | extensionattribute5 | Attribut för tillägg 5 |
| Användare | extensionattribute6 | Attribut för tillägg 6 |
| Användare | extensionattribute7 | Attribut för tillägg 7 |
| Användare | extensionattribute8 | Attribut för tillägg 8 |
| Användare | extensionattribute9 | Attribut för tillägg 9 |
| Användare | extensionattribute10 | Attribut för tillägg 10 |
| Användare | extensionattribute11 | Attribut för tillägg 11 |
| Användare | extensionattribute12 | Attribut för tillägg 12 |
| Användare | extensionattribute13 | Attribut för tillägg 13 |
| Användare | extensionattribute14 | Attribut för tillägg 14 |
| Användare | extensionattribute15 | Attribut för tillägg 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabell 6: omvandlings metoder som tillåts för SAML-NameID

| TransformationMethod | Begränsningar |
| ----- | ----- |
| ExtractMailPrefix | Inget |
| Slå ihop | Det suffix som anslöts måste vara en verifierad domän för resurs klienten. |

### <a name="custom-signing-key"></a>Anpassad signerings nyckel

En anpassad signerings nyckel måste tilldelas till tjänstens huvud objekt för att en anspråks mappnings princip ska börja gälla. Detta säkerställer bekräftelse på att token har ändrats av skaparen av anspråks mappnings principen och skyddar program från principer för anspråk mappning som skapats av skadliga aktörer.  Appar som har aktive rad anspråks mappning måste kontrol lera en särskild URI för sina token signerings nycklar genom att lägga till `appid={client_id}` till deras [OpenID Connect metadata-begäranden](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document).  

### <a name="cross-tenant-scenarios"></a>Scenarier mellan klienter

Principer för anspråks mappning gäller inte för gäst användare. Om en gäst användare försöker få åtkomst till ett program med en anspråks mappnings princip som är tilldelad till tjänstens huvud namn, utfärdas standardtoken (principen har ingen påverkan).

## <a name="claims-mapping-policy-assignment"></a>Princip tilldelning för anspråks mappning

Principer för anspråks mappning kan bara tilldelas till tjänstens huvud objekt.

### <a name="example-claims-mapping-policies"></a>Exempel på anspråks mappnings principer

I Azure AD är många scenarier möjliga när du kan anpassa anspråk som skickas i tokens för särskilda tjänst huvud namn. I det här avsnittet går vi igenom några vanliga scenarier som hjälper dig att grepp hur du använder princip typen för anspråks mappning.

#### <a name="prerequisites"></a>Krav

I följande exempel kan du skapa, uppdatera, länka och ta bort principer för tjänstens huvud namn. Om du är nybörjare på Azure AD rekommenderar vi att du [Lär dig hur du skaffar en Azure AD-klient](quickstart-create-new-tenant.md) innan du fortsätter med de här exemplen.

Gör så här för att komma igång:

1. Ladda ned den senaste [Azure AD PowerShell-modulens offentliga för hands version](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör kommandot Connect för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gången du startar en ny session.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Kör följande kommando för att se alla principer som har skapats i din organisation. Vi rekommenderar att du kör det här kommandot efter de flesta åtgärder i följande scenarier för att kontrol lera att dina principer skapas som förväntat.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exempel: skapa och tilldela en princip för att utelämna grundläggande anspråk från token som utfärdats till ett huvud namn för tjänsten

I det här exemplet skapar du en princip som tar bort den grundläggande anspråks uppsättningen från token som utfärdats till länkade tjänstens huvud namn.

1. Skapa en princip för anspråks mappning. Den här principen, som är länkad till särskilda tjänstens huvud namn, tar bort den grundläggande anspråks uppsättningen från tokens.
   1. Kör följande kommando för att skapa principen: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Kör följande kommando för att se den nya principen och hämta principen ObjectId:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvud namn. Du måste också hämta ObjectId för tjänstens huvud namn.
   1. Om du vill se alla företagets huvud namn för tjänsten kan du [fråga Microsoft Graph](/graph/traverse-the-graph). Du kan också logga in på ditt Azure AD-konto i [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
   2. När du har ObjectId för ditt huvud namn för tjänsten kör du följande kommando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exempel: skapa och tilldela en princip för att inkludera fälten Anställningsnr och TenantCountry som anspråk i token som utfärdats till ett huvud namn för tjänsten

I det här exemplet skapar du en princip som lägger till fälten Anställningsnr och TenantCountry för token som utfärdats till länkade tjänstens huvud namn. Anställningsnr genereras som namn anspråks typ i både SAML-tokens och JWTs. TenantCountry genereras som land anspråks typ i både SAML-tokens och JWTs. I det här exemplet fortsätter vi att inkludera de grundläggande anspråks uppsättningarna i tokens.

1. Skapa en princip för anspråks mappning. Den här principen, som är länkad till särskilda tjänst huvud namn, lägger till anspråken Anställningsnr och TenantCountry till tokens.
   1. Kör följande kommando för att skapa principen:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Kör följande kommando för att se den nya principen och hämta principen ObjectId:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvud namn. Du måste också hämta ObjectId för tjänstens huvud namn. 
   1. Om du vill se alla företagets huvud namn för tjänsten kan du [fråga Microsoft Graph](/graph/traverse-the-graph). Du kan också logga in på ditt Azure AD-konto i [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
   2. När du har ObjectId för ditt huvud namn för tjänsten kör du följande kommando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exempel: skapa och tilldela en princip som använder en anspråks omvandling i token som utfärdats till ett huvud namn för tjänsten

I det här exemplet skapar du en princip som ger ett anpassat anspråk "JoinedData" till JWTs som utfärdats till länkade tjänstens huvud namn. Det här anspråket innehåller ett värde som skapats genom att koppla data som lagras i attributet extensionAttribute1 i användarobjektet med ". Sandbox". I det här exemplet utesluter vi de grundläggande anspråks uppsättningarna i tokens.

1. Skapa en princip för anspråks mappning. Den här principen, som är länkad till särskilda tjänst huvud namn, lägger till anspråken Anställningsnr och TenantCountry till tokens.
   1. Kör följande kommando för att skapa principen:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Kör följande kommando för att se den nya principen och hämta principen ObjectId: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvud namn. Du måste också hämta ObjectId för tjänstens huvud namn. 
   1. Om du vill se alla företagets huvud namn för tjänsten kan du [fråga Microsoft Graph](/graph/traverse-the-graph). Du kan också logga in på ditt Azure AD-konto i [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
   2. När du har ObjectId för ditt huvud namn för tjänsten kör du följande kommando: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Se också

Information om hur du anpassar anspråk som utfärdats i SAML-token via Azure Portal finns i [så här gör du: anpassa anspråk som utfärdats i SAML-token för företags program](active-directory-saml-claims-customization.md)
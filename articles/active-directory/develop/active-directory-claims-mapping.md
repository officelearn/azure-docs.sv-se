---
title: Anpassa anspråk på Azure AD-klientprogram (PowerShell)
titleSuffix: Microsoft identity platform
description: På den här sidan beskrivs Azure Active Directory-anspråksmappning.
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
ms.openlocfilehash: 49860504da8dd2a1b994a23a24df95f59c959c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263197"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Så här anpassar du anspråk som skickas ut i token för en viss app i en klientorganisation (förhandsversion)

> [!NOTE]
> Den här funktionen ersätter och ersätter den [anspråksanpassning](active-directory-saml-claims-customization.md) som erbjuds via portalen idag. Om du anpassar anspråk med hjälp av portalen utöver den Graph/PowerShell-metod som beskrivs i det här dokumentet ignoreras konfigurationen i portalen om du anpassar anspråk med hjälp av portalen utöver den Graph/PowerShell-metod som beskrivs i det här dokumentet. Konfigurationer som görs med hjälp av de metoder som beskrivs i det här dokumentet kommer inte att återspeglas i portalen.

Den här funktionen används av klientadministratörer för att anpassa anspråk som skickas ut i token för ett visst program i deras klientorganisation. Du kan använda principer för anspråksmappning för att:

- Välj vilka anspråk som ingår i token.
- Skapa anspråkstyper som inte redan finns.
- Välj eller ändra källan till data som släpps ut i specifika anspråk.

> [!NOTE]
> Den här funktionen är för närvarande i offentlig förhandsversion. Observera att du kan behöva återställa eller ta bort eventuella ändringar. Funktionen är tillgänglig i alla Azure Active Directory-prenumerationer (Azure AD) under offentlig förhandsversion. Men när funktionen blir allmänt tillgänglig kan vissa aspekter av funktionen kräva en Azure AD premium-prenumeration. Den här funktionen stöder konfiguration av anspråksmappningsprinciper för WS-Fed-, SAML-, OAuth- och OpenID Connect-protokoll.

## <a name="claims-mapping-policy-type"></a>Principtyp för anspråksmappning

I Azure AD representerar ett **principobjekt** en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation. Varje typ av princip har en unik struktur, med en uppsättning egenskaper som sedan tillämpas på objekt som de är tilldelade.

En princip för anspråksmappning är en typ av **principobjekt** som ändrar anspråk som skickas ut i token som utfärdats för specifika program.

## <a name="claim-sets"></a>Anspråksuppsättningar

Det finns vissa uppsättningar av anspråk som definierar hur och när de används i tokens.

| Anspråksuppsättning | Beskrivning |
|---|---|
| Uppsättning kärnanspråk | Finns i varje token oavsett policy. Dessa anspråk anses också vara begränsade och kan inte ändras. |
| Grundläggande anspråksuppsättning | Inkluderar anspråk som avges som standard för token (utöver den grundläggande anspråksuppsättningen). Du kan utelämna eller ändra grundläggande anspråk med hjälp av anspråksmappningsprinciperna. |
| Begränsad anspråksuppsättning | Det går inte att ändra med hjälp av principen. Det går inte att ändra datakällan och ingen omvandling används när dessa anspråk skapas. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabell 1: JSON Web Token (JWT) begränsad anspråksuppsättning

| Anspråkstyp (namn) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| Skådespelare |
| skådespelaretoken |
| Aio |
| altsecid (olika) |
| Amr |
| app_chain |
| app_displayname |
| app_res |
| appctx (på ett sätt) |
| appctxsender (appctxsender) |
| Appid |
| appidacr (på ett år) |
| Påstående |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp (aska) |
| azpacr ( |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| Cnf |
| kod |
| kontroller |
| credential_keys |
| Csr |
| csr_type |
| Deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-post |
| slutpunkt |
| enfpolids (enfpolids) |
| exp |
| expires_on |
| grant_type |
| Diagram |
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
| Iat |
| identitetsprovider |
| Idp |
| in_corp |
| Instans |
| ipaddr |
| isbrowserhostedapp |
| Iss |
| jwk (na) |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid (namn) |
| Nbf |
| netbios_name |
| Nonce |
| Oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| platf (platf) |
| polids (polids) |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid (puid) |
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
| Scp |
| Sid |
| Signatur |
| signin_state |
| src1 (src1) |
| src2 (src2) |
| Sub |
| tbid (tbid) |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| betroddafordring |
| unique_name |
| Upn |
| user_setting_sync_url |
| användarnamn |
| Uvi |
| Ver |
| verified_primary_email |
| verified_secondary_email |
| wids (på en) |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabell 2: FAST på ett begränsat skadeståndskrav

| Anspråkstyp (URI) |
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

## <a name="claims-mapping-policy-properties"></a>Principegenskaper för anspråksmappning

Om du vill styra vilka anspråk som släpps ut och var data kommer ifrån använder du egenskaperna för en princip för anspråksmappning. Om en princip inte har angetts utfärdar systemet token som innehåller den grundläggande anspråksuppsättningen, den grundläggande anspråksuppsättningen och [eventuella valfria anspråk](active-directory-optional-claims.md) som programmet har valt att ta emot.

### <a name="include-basic-claim-set"></a>Inkludera grundläggande anspråksuppsättning

**Sträng:** InkluderaBasicClaimSet

**Datatyp:** Boolesk (sant eller falskt)

**Sammanfattning:** Den här egenskapen avgör om den grundläggande anspråksuppsättningen ingår i token som påverkas av den här principen.

- Om värdet är True skickas alla anspråk i den grundläggande anspråksuppsättningen i token som påverkas av principen. 
- Om värdet är False finns inte anspråk i den grundläggande anspråksuppsättningen i token, såvida de inte läggs till individuellt i egenskapen skadeschema för samma princip.

> [!NOTE] 
> Anspråk i huvudanspråksuppsättningen finns i varje token, oavsett vad den här egenskapen är inställd på. 

### <a name="claims-schema"></a>Schema för anspråk

**Sträng:** ClaimsSchema

**Datatyp:** JSON-blob med en eller flera anspråksschemaposter

**Sammanfattning:** Den här egenskapen definierar vilka anspråk som finns i de token som påverkas av principen, utöver den grundläggande anspråksuppsättningen och kärnanspråksuppsättningen.
För varje anspråksschemapost som definieras i den här egenskapen krävs viss information. Ange var data kommer ifrån (**Värde** eller **Källa/ID-par**) och vilket påstående uppgifterna släpps ut som (**Anspråkstyp**).

### <a name="claim-schema-entry-elements"></a>Element för anspråksschemainmatning

**Värde:** Värdeelementet definierar ett statiskt värde som de data som ska släppas ut i anspråket.

**Käll-/ID-par:** Käll- och ID-elementen definierar var data i anspråket kommer ifrån. 

Ange källelementet till något av följande värden: 

- "användare": Data i anspråket är en egenskap på användarobjektet. 
- "application": Uppgifterna i anspråket är en egenskap på programmets (klient)-tjänstens huvudnamn. 
- "resurs": Data i anspråket är en egenskap på resurstjänstens huvudnamn.
- "målgrupp": Data i anspråket är en egenskap på tjänstens huvudnamn som är målgruppen för token (antingen klienten eller resurstjänstens huvudnamn).
- "företag": Data i anspråket är en egenskap på resursens företagsobjekt.
- "transformation": Data i anspråket kommer från anspråksomvandling (se avsnittet "Anspråksomvandling" senare i den här artikeln).

Om källan är omvandling måste **TransformationID-elementet** också inkluderas i den här anspråksdefinitionen.

ID-elementet identifierar vilken egenskap på källan som ger värdet för anspråket. I följande tabell visas de värden för ID som gäller för varje värde i Källa.

#### <a name="table-3-valid-id-values-per-source"></a>Tabell 3: Giltiga ID-värden per källa

| Källa | ID | Beskrivning |
|-----|-----|-----|
| Användare | surname | Efternamn |
| Användare | givenname | Förnamn |
| Användare | displayname (visningsnamn) | Visningsnamn |
| Användare | Objectid | ObjectID |
| Användare | e-post | E-postadress |
| Användare | användarnamn | User Principal Name |
| Användare | avdelning|Avdelning|
| Användare | onpremisessamaccountname | Lokalt SAM-kontonamn |
| Användare | netbiosname (netbiosname)| NetBios Namn |
| Användare | dnsdomainname | DNS-domännamn |
| Användare | om säkerhetsidentifierare | Lokal säkerhetsidentifierare |
| Användare | företagets namn| Organisationsnamn |
| Användare | streetaddress (streetaddress) | Gatuadress |
| Användare | Postnummer | Postnummer |
| Användare | preferredlanguange | Önskat språk |
| Användare | påpremisesuserprincipalnamn | Lokala UPN |
| Användare | mailnicknamn | Smeknamn för e-post |
| Användare | förlängningattribut1 | Tilläggsattribut 1 |
| Användare | förlängningstillägg2 | Tilläggsattribut 2 |
| Användare | förlängningattribut3 | Tilläggsattribut 3 |
| Användare | förlängningar4 | Tilläggsattribut 4 |
| Användare | förlängningar5 | Tilläggsattribut 5 |
| Användare | förlängningat16 | Tilläggsattribut 6 |
| Användare | förlängningat7 | Tilläggsattribut 7 |
| Användare | förlängningat 8 | Tilläggsattribut 8 |
| Användare | förlängningar9 | Tilläggsattribut 9 |
| Användare | förlängningat10 | Tilläggsattribut 10 |
| Användare | förlängningat11 | Tilläggsattribut 11 |
| Användare | förlängningar12 | Tilläggsattribut 12 |
| Användare | förlängningar13 | Tilläggsattribut 13 |
| Användare | förlängningar14 | Tilläggsattribut 14 |
| Användare | förlängningar15 | Tilläggsattribut 15 |
| Användare | othermail (annanpost) | Annan e-post |
| Användare | land | Land/region |
| Användare | city | Ort |
| Användare | state | Status |
| Användare | jobtitle (jobtitle) | Befattning |
| Användare | employeeid | Anställnings-ID |
| Användare | facsimiletelephonenumber | Faxtelefonnummer |
| program, resurs, målgrupp | displayname (visningsnamn) | Visningsnamn |
| program, resurs, målgrupp | Invände | ObjectID |
| program, resurs, målgrupp | tags | Tjänstens huvudmärke |
| Företag | hyresgästerland | Hyresgästens land |

**TransformationID:** TransformationID-elementet får endast anges om källelementet är inställt på "omvandling".

- Det här elementet måste matcha ID-elementet för omvandlingsposten i egenskapen **ClaimsTransformation** som definierar hur data för det här anspråket genereras.

**Typ av anspråk:** **JwtClaimType-** och **SamlClaimType-elementen** definierar vilket anspråk som den här anspråksschemaposten refererar till.

- JwtClaimType måste innehålla namnet på det anspråk som ska sändas ut i JV.
- SamlClaimType måste innehålla URI för anspråk som ska sändas i SAML-token.

> [!NOTE]
> Namn och URI:er för anspråk i den begränsade anspråksuppsättningen kan inte användas för elementen för anspråkstyp. Mer information finns i avsnittet "Undantag och begränsningar" senare i den här artikeln.

### <a name="claims-transformation"></a>Anspråkstransformering

**Sträng:** Skadetransformation

**Datatyp:** JSON blob, med en eller flera omvandlingsposter 

**Sammanfattning:** Använd den här egenskapen om du vill använda vanliga omvandlingar på källdata för att generera utdata för anspråk som anges i anspråksschemat.

**ID:** Använd ID-elementet för att referera till den här omvandlingsposten itransformidanvisningsschemaposten. Det här värdet måste vara unikt för varje omvandlingspost i den här principen.

**TransformationMethod:** Elementet TransformationMethod identifierar vilken åtgärd som utförs för att generera data för anspråket.

Baserat på den valda metoden förväntas en uppsättning in- och utdata. Definiera in- och utdata med hjälp av **elementen InputClaims,** **InputParameters** och **OutputClaims.**

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabell 4: Omvandlingsmetoder och förväntade in- och utdata

|TransformationMethod|Förväntad inmatning|Förväntad utdata|Beskrivning|
|-----|-----|-----|-----|
|Slå ihop|sträng1, sträng2, avgränsare|outputClaim|Sammanfogar indatasträngar med hjälp av en avgränsare däremellan. Till exempel: sträng1:"foo@bar.com" , string2:"sandbox", separator:"."foo@bar.com.sandboxresulterar i outputClaim:" " "|
|ExtractMailPrefix|e-post|outputClaim|Extraherar den lokala delen av en e-postadress. Till exempel: mail:"foo@bar.com" resulterar i outputClaim:"foo". Om \@ det inte finns något tecken returneras den ursprungliga indatasträngen som den är.|

**InputClaims:** Använd ett InputClaims-element för att skicka data från en anspråksschemapost till en omvandling. Den har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId** är kopplad till ID-elementet i anspråksschemaposten för att hitta rätt indataanspråk. 
- **TransformationClaimType** används för att ge den här indata ett unikt namn. Det här namnet måste matcha en av de förväntade indata för omvandlingsmetoden.

**InputParametrar:** Använd ett InputParameters-element för att skicka ett konstant värde till en omformning. Den har två attribut: **Värde** och **ID**.

- **Värdet** är det faktiska konstantvärdet som ska skickas.
- **ID** används för att ge indata ett unikt namn. Namnet måste matcha en av de förväntade indata för omvandlingsmetoden.

**OutputClaims:** Använd ett OutputClaims-element för att lagra data som genereras av en omvandling och koppla det till en anspråksschemapost. Den har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId** är ansluten med ID för anspråk schema posten för att hitta rätt utdata anspråk.
- **TransformationClaimType** används för att ge utdata ett unikt namn. Namnet måste matcha en av de förväntade utgångarna för omvandlingsmetoden.

### <a name="exceptions-and-restrictions"></a>Undantag och begränsningar

**SAML NameID och UPN:** De attribut som du hämtar NameID- och UPN-värdena från och de anspråksomformningar som är tillåtna är begränsade. Se tabell 5 och tabell 6 för att se de tillåtna värdena.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabell 5: Attribut som tillåts som datakälla för SAML NameID

|Källa|ID|Beskrivning|
|-----|-----|-----|
| Användare | e-post|E-postadress|
| Användare | användarnamn|User Principal Name|
| Användare | onpremisessamaccountname|Namn på lokalt Sam-konto|
| Användare | employeeid|Anställnings-ID|
| Användare | förlängningattribut1 | Tilläggsattribut 1 |
| Användare | förlängningstillägg2 | Tilläggsattribut 2 |
| Användare | förlängningattribut3 | Tilläggsattribut 3 |
| Användare | förlängningar4 | Tilläggsattribut 4 |
| Användare | förlängningar5 | Tilläggsattribut 5 |
| Användare | förlängningat16 | Tilläggsattribut 6 |
| Användare | förlängningat7 | Tilläggsattribut 7 |
| Användare | förlängningat 8 | Tilläggsattribut 8 |
| Användare | förlängningar9 | Tilläggsattribut 9 |
| Användare | förlängningat10 | Tilläggsattribut 10 |
| Användare | förlängningat11 | Tilläggsattribut 11 |
| Användare | förlängningar12 | Tilläggsattribut 12 |
| Användare | förlängningar13 | Tilläggsattribut 13 |
| Användare | förlängningar14 | Tilläggsattribut 14 |
| Användare | förlängningar15 | Tilläggsattribut 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabell 6: Omvandlingsmetoder som tillåts för SAML NameID

| TransformationMethod | Begränsningar |
| ----- | ----- |
| ExtractMailPrefix | Inget |
| Slå ihop | Suffixet som sammanfogas måste vara en verifierad domän för resursklienten. |

### <a name="custom-signing-key"></a>Anpassad signeringsnyckel

En anpassad signeringsnyckel måste tilldelas tjänstens huvudobjekt för att en anspråksmappningsprincip ska börja gälla. Detta säkerställer bekräftelse på att token har ändrats av skaparen av anspråksmappningsprincipen och skyddar program från anspråksmappningsprinciper som skapats av skadliga aktörer. Om du vill lägga till en anpassad signeringsnyckel `new-azureadapplicationkeycredential` kan du använda Azure PowerShell-cmdleten för att skapa en symmetrisk nyckelautentiseringsuppgifter för ditt programobjekt. Mer information om den här Azure PowerShell-cmdleten finns i [New-AzureADApplicationKeyCredential](https://docs.microsoft.com/powerShell/module/Azuread/New-AzureADApplicationKeyCredential?view=azureadps-2.0).

Appar som har aktiverat anspråksmappning måste validera `appid={client_id}` sina tokensigneringsnycklar genom att lägga till deras [OpenID Connect-metadatabegäranden](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Nedan visas formatet för det OpenID Connect-metadatadokument som du bör använda: 

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Scenarier mellan innehavare

Principer för anspråksmappning gäller inte för gästanvändare. Om en gästanvändare försöker komma åt ett program med en anspråksmappningsprincip som tilldelats tjänstens huvudnamn utfärdas standardtoken (principen har ingen effekt).

## <a name="claims-mapping-policy-assignment"></a>Principtilldelning av anspråksmappning

Principer för anspråksmappning kan endast tilldelas huvudobjekt för tjänsten.

### <a name="example-claims-mapping-policies"></a>Exempel på principer för anspråksmappning

I Azure AD är många scenarier möjliga när du kan anpassa anspråk som skickas ut i token för specifika tjänsthuvudnamn. I det här avsnittet går vi igenom några vanliga scenarier som kan hjälpa dig att förstå hur du använder principtypen för anspråksmappning.

#### <a name="prerequisites"></a>Krav

I följande exempel skapar, uppdaterar, länkar och tar du bort principer för tjänstens huvudnamn. Om du inte har gjort det tidigare i Azure AD rekommenderar vi att du lär dig mer om hur du [skaffar en Azure AD-klient innan](quickstart-create-new-tenant.md) du fortsätter med de här exemplen.

Så här kommer du igång:

1. Ladda ned den senaste [versionen av den offentliga förhandsversionen av Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör kommandot Anslut för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gång du startar en ny session.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Om du vill visa alla principer som har skapats i organisationen kör du följande kommando. Vi rekommenderar att du kör det här kommandot efter de flesta åtgärder i följande scenarier för att kontrollera att dina principer skapas som förväntat.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exempel: Skapa och tilldela en princip för att utelämna grundläggande anspråk från token som utfärdats till ett tjänsthuvudnamn

I det här exemplet skapar du en princip som tar bort den grundläggande anspråksuppsättningen från token som utfärdats till länkade tjänsthuvudnamn.

1. Skapa en princip för anspråksmappning. Den här principen, som är länkad till specifika tjänsthuvudnamn, tar bort den grundläggande anspråksuppsättningen från token.
   1. Om du vill skapa principen kör du det här kommandot: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Om du vill se din nya princip och hämta principen ObjectId kör du följande kommando:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta ObjectId för din tjänst huvudnamn.
   1. Om du vill visa alla organisationens tjänsthuvudnamn kan du [fråga microsoft graph API](/graph/traverse-the-graph). Eller logga in på ditt Azure AD-konto i [Microsoft Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer)
   2. Kör följande kommando när du har ObjectId för tjänstens huvudnamn:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exempel: Skapa och tilldela en princip för att inkludera EmployeeID och TenantCountry som anspråk i token som utfärdats till ett tjänsthuvudnamn

I det här exemplet skapar du en princip som lägger till EmployeeID och TenantCountry i token som utfärdats till länkade tjänsthuvudnamn. EmployeeID avges som namnanspråkstyp i både SAML-token och JWTs. TenantCountry avges som landanspråkstyp i både SAML-token och JWTs. I det här exemplet fortsätter vi att inkludera de grundläggande anspråksuppsättningen i token.

1. Skapa en princip för anspråksmappning. Den här principen, som är länkad till specifika tjänsthuvudnamn, lägger till anspråk på EmployeeID och TenantCountry i tokens.
   1. Om du vill skapa principen kör du följande kommando:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Om du vill se din nya princip och hämta principen ObjectId kör du följande kommando:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta ObjectId för din tjänst huvudnamn. 
   1. Om du vill visa alla organisationens tjänsthuvudnamn kan du [fråga microsoft graph API](/graph/traverse-the-graph). Eller logga in på ditt Azure AD-konto i [Microsoft Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer)
   2. Kör följande kommando när du har ObjectId för tjänstens huvudnamn:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exempel: Skapa och tilldela en princip som använder en anspråkstransformation i token som utfärdats till ett tjänsthuvudnamn

I det här exemplet skapar du en princip som avger ett anpassat anspråk "JoinedData" till JWTs som utfärdats till länkade tjänsthuvudnamn. Det här anspråket innehåller ett värde som skapas genom att koppla data som lagras i attributet extensionattribute1 på användarobjektet med ".sandbox". I det här exemplet utesluter vi de grundläggande anspråksuppsättningen i token.

1. Skapa en princip för anspråksmappning. Den här principen, som är länkad till specifika tjänsthuvudnamn, lägger till anspråk på EmployeeID och TenantCountry i tokens.
   1. Om du vill skapa principen kör du följande kommando:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Om du vill se din nya princip och hämta principen ObjectId kör du följande kommando: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta ObjectId för din tjänst huvudnamn. 
   1. Om du vill visa alla organisationens tjänsthuvudnamn kan du [fråga microsoft graph API](/graph/traverse-the-graph). Eller logga in på ditt Azure AD-konto i [Microsoft Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer)
   2. Kör följande kommando när du har ObjectId för tjänstens huvudnamn: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Se även

Mer information om hur du anpassar anspråk som utfärdats i SAML-token via Azure-portalen finns [i Så här: Anpassa anspråk som utfärdats i SAML-token för företagsprogram](active-directory-saml-claims-customization.md)

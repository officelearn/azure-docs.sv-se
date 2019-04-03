---
title: Anpassa anspråk som släpps i token för en viss app i en Azure AD-klient (offentlig förhandsversion)
description: Den här sidan beskriver Anspråksmappning för Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: celested
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e492586f0c70d4cd3013ef8f466afd6bb2bb65ac
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884085"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Anvisningar: Anpassa anspråk som släpps i token för en viss app i en klient (förhandsversion)

> [!NOTE]
> Den här funktionen ersätter och ersätter den [anspråk anpassning](active-directory-saml-claims-customization.md) erbjuds via portalen idag. På samma program, om du har ändrat anspråk med hjälp av portalen förutom Graph/PowerShell-metoden som beskrivs i det här dokumentet har tokens som utfärdats för att programmet kommer att ignorera konfigurationen i portalen. Konfigurationer som görs via metoderna som beskrivs i det här dokumentet visas inte i portalen.

Den här funktionen används av klientadministratörer för att anpassa anspråk som släpps i token för ett visst program i deras klienter. Du kan använda principer för mappning av anspråk att:

- Välj vilka anspråk som ingår i token.
- Skapa anspråkstyper som inte redan finns.
- Välj eller ändra datakällan som hänvisas till i specifika anspråk.

> [!NOTE]
> Den här funktionen är för närvarande i offentlig förhandsversion. Observera att du kan behöva återställa eller ta bort eventuella ändringar. Funktionen är tillgänglig i alla Azure Active Directory (Azure AD)-prenumeration allmänt tillgängliga förhandsversionen. När funktionen blir allmänt tillgänglig, kan vissa aspekter av funktionen kräver en Azure AD premium-prenumeration. Den här funktionen stöder konfigurera principer för mappning av anspråk för WS-Fed, SAML, OAuth och OpenID Connect-protokoll.

## <a name="claims-mapping-policy-type"></a>Anspråksmappning Principtyp

I Azure AD en **princip** -objektet representerar en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation. Varje typ av princip som har en unik struktur, där en uppsättning egenskaper som sedan tillämpas på objekt som de har tilldelats.

Ett anspråk mappning princip är en typ av **princip** objekt som ändrar anspråk som släpps i token som utfärdas för specifika program.

## <a name="claim-sets"></a>Anspråksuppsättningar

Det finns vissa typer av anspråk som definierar hur och när de används i token.

| Anspråksuppsättningen | Beskrivning |
|---|---|
| Core anspråksuppsättningen | Finns i varje token oavsett principen. De här anspråken anses också vara begränsad och kan inte ändras. |
| Grundläggande anspråksuppsättningen | Innehåller de anspråk som genereras som standard för token (utöver anspråksuppsättningen core). Du kan utelämna eller ändra grundläggande anspråk med hjälp av principer för Anspråksmappning. |
| Begränsade anspråksuppsättningen | Kan inte ändras med principen. Datakällan kan inte ändras, och ingen transformation används när du genererar dessa anspråk. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabell 1: JSON Web Token (JWT) begränsade anspråksuppsättningen

| Anspråkstyp (namn) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| aktör |
| actortoken |
| AIO |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| försäkran |
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
| cnf |
| Kod |
| kontroller |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-post |
| slutpunkt |
| enfpolids |
| EXP |
| expires_on |
| _typ av beviljande |
| graf |
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
| IAT |
| identityprovider |
| IDP: n |
| in_corp |
| instans |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
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
| PUID |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resurs |
| roll |
| roles |
| omfång |
| scp |
| SID |
| signatur |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| UPN |
| user_setting_sync_url |
| användarnamn |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabell 2: SAML begränsade anspråksuppsättningen

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

## <a name="claims-mapping-policy-properties"></a>Anspråksmappning Principegenskaper

Använd egenskaperna för en princip för Anspråksmappning för att styra vilka anspråk genereras och varifrån data kommer från. Om en princip inte har angetts, problem med system-token som innehåller grundläggande anspråk uppsättningen, den grundläggande anspråksuppsättningen och alla [valfria anspråk](active-directory-optional-claims.md) som programmet har valt att ta emot.

### <a name="include-basic-claim-set"></a>Innehåller grundläggande anspråksuppsättningen

**sträng:** IncludeBasicClaimSet

**Datatyp:** Booleskt värde (True eller False)

**Sammanfattning:** Den här egenskapen anger om den grundläggande anspråksuppsättningen ingår i token som påverkas av den här principen.

- Om värdet är True, alla anspråk i den grundläggande anspråksuppsättningen genereras i token som påverkas av principen. 
- Om inställt på False, anspråk i den grundläggande anspråksuppsättningen inte är i token, om de inte enskilt läggs i egenskapen anspråk schemat för samma princip.

> [!NOTE] 
> Anspråk i core-anspråksuppsättningen finns i varje token, oavsett vad den här egenskapen är inställd på. 

### <a name="claims-schema"></a>Anspråk schema

**sträng:** ClaimsSchema

**Datatyp:** JSON-blob med en eller flera anspråk schemat poster

**Sammanfattning:** Den här egenskapen definierar vilka anspråk finns i de token som påverkas av principen, förutom den grundläggande anspråksuppsättningen och anspråksuppsättningen core.
För varje anspråk schemat post definieras i den här egenskapen, krävs viss information. Anger var data kommer från (**värdet** eller **käll-/ ID par**), och som anspråksdata genereras som (**anspråk typ**).

### <a name="claim-schema-entry-elements"></a>Anspråk schemaelement post

**Värde:** Elementet värde definierar ett statiskt värde som data som ska genereras i anspråket.

**Källa/ID par:** Käll- och ID-element definiera där data i anspråket kommer från. 

Ange käll-elementet till någon av följande värden: 

- ”användare”: Data i anspråket är en egenskap på användarobjektet. 
- ”program”: Data i anspråket är en egenskap för programmet (klient) tjänstens huvudnamn. 
- ”resurser”: Data i anspråket är en egenskap på resurs-tjänstens huvudnamn.
- ”målgrupp”: Data i anspråket är en egenskap för tjänstens huvudnamn som är målgruppen för token (när det gäller antingen klient- eller resursen, tjänstens huvudnamn).
- ”företag”: Data i anspråket är en egenskap på resursen klientens företagets objekt.
- ”omvandling”: Data i anspråket är från anspråkstransformering (se avsnittet ”omvandling av anspråk” senare i den här artikeln).

Om källan är omvandling, den **TransformationID** elementet måste finnas med i den här anspråksdefinitionen.

ID-element som identifierar vilken egenskap på källan innehåller värdet för anspråket. I följande tabell visas värdena för ID som är giltig för varje värde i källan.

#### <a name="table-3-valid-id-values-per-source"></a>Tabell 3: Giltiga värden för ID per källa

| Källa | ID | Beskrivning |
|-----|-----|-----|
| Användare | surname | Produktfamilj |
| Användare | givenname | Förnamn |
| Användare | displayname (visningsnamn) | Visningsnamn |
| Användare | objekt-ID | ObjectId |
| Användare | e-post | E-postadress |
| Användare | userprincipalname | User Principal Name |
| Användare | avdelning|Avdelning|
| Användare | onpremisessamaccountname | Den lokala SAM-kontonamn |
| Användare | netbiosname| NetBios-namn |
| Användare | DNS-domännamn | DNS-domännamn |
| Användare | onpremisesecurityidentifier | lokala säkerhetsidentifierare |
| Användare | Företagsnamn| Organisationens namn |
| Användare | streetaddress | Gatuadress |
| Användare | Postnummer | Postnummer |
| Användare | preferredlanguange | Önskat språk |
| Användare | onpremisesuserprincipalname | lokala UPN |
| Användare | mailNickname | Smeknamn för e-post |
| Användare | extensionattribute1 | Tilläggsattribut 1 |
| Användare | extensionattribute2 | Tilläggsattribut 2 |
| Användare | extensionattribute3 | Tilläggsattribut 3 |
| Användare | extensionattribute4 | Tilläggsattribut 4 |
| Användare | extensionattribute5 | Tilläggsattribut 5 |
| Användare | extensionattribute6 | Tilläggsattribut 6 |
| Användare | extensionattribute7 | Tilläggsattribut 7 |
| Användare | extensionattribute8 | Tilläggsattribut 8 |
| Användare | extensionattribute9 | Tilläggsattribut 9 |
| Användare | extensionattribute10 | Tilläggsattribut 10 |
| Användare | extensionattribute11 | Tilläggsattribut 11 |
| Användare | extensionattribute12 | Tilläggsattribut 12 |
| Användare | extensionattribute13 | Tilläggsattribut 13 |
| Användare | extensionattribute14 | Tilläggsattribut 14 |
| Användare | extensionattribute15 | Tilläggsattribut 15 |
| Användare | othermail | Andra e-post |
| Användare | Land | Land/region |
| Användare | city | Ort |
| Användare | state | Status |
| Användare | jobtitle | Befattning |
| Användare | employeeid | Anställnings-ID |
| Användare | facsimiletelephonenumber | Telefonnummer för fax |
| program, resurs, målgrupp | displayname (visningsnamn) | Visningsnamn |
| program, resurs, målgrupp | objekt | ObjectId |
| program, resurs, målgrupp | tags | Service Principal Tag |
| Företag | tenantcountry | Klientens land/region |

**TransformationID:** TransformationID-element måste anges bara om käll-elementet är inställt på ”omvandling”.

- Det här elementet måste matcha ID-element i posten transformering i den **ClaimsTransformation** egenskap som definierar hur data för det här anspråket ska skapas.

**Typ av anspråk:** Den **JwtClaimType** och **SamlClaimType** element definierar vilka anspråk anspråk schemat transaktionen refererar till.

- JwtClaimType måste innehålla namnet på anspråk som ska skickas i JWTs.
- SamlClaimType måste innehålla URI för anspråket som ska skickas i SAML-tokens.

> [!NOTE]
> Namn och URI: er av anspråk i den begränsade anspråksuppsättningen kan inte användas för anspråk typen element. Mer information finns i avsnittet ”undantag och begränsningar” senare i den här artikeln.

### <a name="claims-transformation"></a>Anspråkstransformering

**sträng:** ClaimsTransformation

**Datatyp:** JSON-blob med en eller flera poster för omvandling 

**Sammanfattning:** Använd den här egenskapen för att använda vanliga omformningar källdata, att generera utdata för anspråk som angetts i schemat för anspråk.

**ID:** Använda ID-element för att referera till den här omvandlingen posten i posten TransformationID anspråk schemat. Det här värdet måste vara unikt för varje transformering transaktion i den här principen.

**TransformationMethod:** Elementet TransformationMethod identifierar vilka åtgärden utförs för att generera data för anspråket.

Baserat på vilken metod som valts, förväntas en uppsättning indata och utdata. Definiera indata och utdata genom att använda den **InputClaims**, **indataparametrar** och **OutputClaims** element.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabell 4: Omvandling metoder och förväntade indata och utdata

|TransformationMethod|Förväntade indata|Utdata som förväntas|Beskrivning|
|-----|-----|-----|-----|
|Slå ihop|sträng1, sträng2, avgränsare|outputClaim|Kopplingar kan du ange strängar med hjälp av en avgränsare mellan. Till exempel: sträng1 ”:foo@bar.com”, sträng2: ”sandlåda”, avgränsare ”:”. resulterar i outputClaim ”:foo@bar.com.sandbox”|
|ExtractMailPrefix|e-post|outputClaim|Extraherar den lokala delen av en e-postadress. Till exempel: e-post ”:foo@bar.com” resulterar i outputClaim: ”foo”. Om ingen \@ logga finns sedan ursprungliga Indatasträngen returneras skick.|

**InputClaims:** Använd ett InputClaims-element för att skicka data från en post för anspråk schemat till en omvandling. Den har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId** är kopplad till ID-element för registerposten anspråk schemat för att hitta lämplig inkommande anspråk. 
- **TransformationClaimType** används för att ge ett unikt namn för denna indata. Det här namnet måste matcha en av de förväntade indata för metoden omvandling.

**Indataparametrar:** Använda ett indataparametrar-element för att skicka ett konstant värde till en omvandling. Den har två attribut: **Värdet** och **ID**.

- **Värdet** är det faktiska konstanta värdet som ska skickas.
- **ID** används för att ge ett unikt namn för indata. Namnet måste matcha en av de förväntade indata för metoden omvandling.

**OutputClaims:** Använd en OutputClaims-elementet för att lagra data som genereras av en omvandling och koppla dem till en post för anspråk schemat. Den har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId** med ID för posten anspråk schemat ska hitta lämpliga utdata-anspråket.
- **TransformationClaimType** används för att ge ett unikt namn till utdata. Namnet måste matcha en av de förväntade utdata för metoden omvandling.

### <a name="exceptions-and-restrictions"></a>Undantag och begränsningar

**SAML NameID- och UPN:** De attribut som du använder källbaserad NameID- och UPN-värden och anspråksomvandlingar som tillåts är begränsade. Se tabellerna 5 och 6 för att visa de tillåtna värdena.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabell 5: Attribut användas som en datakälla för SAML NameID

|Källa|ID|Beskrivning|
|-----|-----|-----|
| Användare | e-post|E-postadress|
| Användare | userprincipalname|User Principal Name|
| Användare | onpremisessamaccountname|På lokala Sam-kontonamn|
| Användare | employeeid|Anställnings-ID|
| Användare | extensionattribute1 | Tilläggsattribut 1 |
| Användare | extensionattribute2 | Tilläggsattribut 2 |
| Användare | extensionattribute3 | Tilläggsattribut 3 |
| Användare | extensionattribute4 | Tilläggsattribut 4 |
| Användare | extensionattribute5 | Tilläggsattribut 5 |
| Användare | extensionattribute6 | Tilläggsattribut 6 |
| Användare | extensionattribute7 | Tilläggsattribut 7 |
| Användare | extensionattribute8 | Tilläggsattribut 8 |
| Användare | extensionattribute9 | Tilläggsattribut 9 |
| Användare | extensionattribute10 | Tilläggsattribut 10 |
| Användare | extensionattribute11 | Tilläggsattribut 11 |
| Användare | extensionattribute12 | Tilläggsattribut 12 |
| Användare | extensionattribute13 | Tilläggsattribut 13 |
| Användare | extensionattribute14 | Tilläggsattribut 14 |
| Användare | extensionattribute15 | Tilläggsattribut 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabell 6: Omvandling metoder som tillåts för SAML NameID

| TransformationMethod | Begränsningar |
| ----- | ----- |
| ExtractMailPrefix | Ingen |
| Slå ihop | Suffixet är ansluten måste vara en verifierad domän för resurs-klienten. |

### <a name="custom-signing-key"></a>Anpassad nyckel för signeringscertifikatet

En anpassad signeringsnyckel måste tilldelas det tjänstens huvudnamnsobjekt för en Anspråksmappning princip ska börja gälla. Alla token som utfärdas och som har påverkats av principen som har signerats med anpassade signeringsnyckeln och program som måste konfigureras för att acceptera token signerats med signeringsnyckeln. Detta säkerställer bekräftelse att token har ändrats av skaparen av Anspråksmappning princip och skyddar program från Anspråksmappning principer som skapats av skadliga aktörer.

### <a name="cross-tenant-scenarios"></a>Scenarier för flera klienter

Anspråksmappning principer gäller inte för gästanvändare. Om en gästanvändare försöker få åtkomst till ett program med en princip som tilldelats dess huvudnamn för tjänsten för Anspråksmappning, standardtoken utfärdas (principen har ingen effekt).

## <a name="claims-mapping-policy-assignment"></a>Anspråksmappning principtilldelning

Anspråksmappning principer kan endast tilldelas objekt för tjänstens huvudnamn.

### <a name="example-claims-mapping-policies"></a>Exempel anspråk mappning principer

Många scenarier kan utföras i Azure AD när du kan anpassa anspråk som genereras i token för specifika tjänstens huvudnamn. I det här avsnittet ska går vi igenom några vanliga scenarier som kan hjälpa dig rapportelementen hur du använder Anspråksmappning Principtyp.

#### <a name="prerequisites"></a>Förutsättningar

I följande exempel har du skapa, uppdatera, länka och ta bort principer för tjänstens huvudnamn. Om du är nybörjare på Azure AD, rekommenderar vi att du [Lär dig mer om hur du hämtar en Azure AD-klient](quickstart-create-new-tenant.md) innan du fortsätter med de här exemplen.

Utför följande steg för att komma igång:

1. Hämta senaste [offentliga förhandsversionen av Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör kommandot Connect för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gång startar du en ny session.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Kör följande kommando om du vill se alla principer som har skapats i din organisation. Vi rekommenderar att du kör det här kommandot när du har de flesta åtgärderna i följande scenarier för att kontrollera att dina principer skapas som förväntat.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exempel: Skapa och tilldela en princip för att utelämna de grundläggande anspråk från token som utfärdas till ett huvudnamn för tjänsten

I det här exemplet skapar du en princip som tar bort den grundläggande anspråksuppsättningen från token som utfärdas till länkade tjänstens huvudnamn.

1. Skapa en princip för Anspråksmappning. Den här principen, länkade till specifika tjänstens huvudnamn, tar bort den grundläggande anspråksuppsättningen från token.
   1. Om du vill skapa principen, kör du följande kommando: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Se din nya princip och för att få principen ObjectId, kör du följande kommando:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta ObjectId för tjänsten huvudnamn.
   1. Om du vill se din organisations tjänstens huvudnamn, kan du fråga Microsoft Graph. Eller i Azure AD Graph Explorer, logga in på din Azure AD-konto.
   2. När du har ObjectId för din tjänstens huvudnamn, kör du följande kommando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exempel: Skapa och tilldela en princip för att inkludera EmployeeID och TenantCountry som anspråk i token som utfärdas till ett huvudnamn för tjänsten

I det här exemplet skapar du en princip som lägger tillför EmployeeID och TenantCountry token som utfärdas till länkade tjänstens huvudnamn. EmployeeID genereras som namn på Anspråkstyp i både SAML-token och JWTs. TenantCountry genereras som Anspråkstyp för land i både SAML-token och JWTs. I det här exemplet fortsätter vi att inkludera grundläggande anspråken i token.

1. Skapa en princip för Anspråksmappning. Den här principen, länkade till specifika tjänstens huvudnamn, lägger till EmployeeID och TenantCountry anspråk till tokens.
   1. Om du vill skapa principen, kör du följande kommando:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Se din nya princip och för att få principen ObjectId, kör du följande kommando:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta ObjectId för tjänsten huvudnamn. 
   1. Om du vill se din organisations tjänstens huvudnamn, kan du fråga Microsoft Graph. Eller i Azure AD Graph Explorer, logga in på din Azure AD-konto.
   2. När du har ObjectId för din tjänstens huvudnamn, kör du följande kommando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exempel: Skapa och tilldela en princip som använder en omvandling av anspråk i token som utfärdas till ett huvudnamn för tjänsten

I det här exemplet skapar du en princip som genererar ett anpassat anspråk ”JoinedData” till JWTs som utfärdats till länkade tjänstens huvudnamn. Denna begäran innehåller ett värde som skapats genom att gå med data som lagras i attributet extensionattribute1 användarobjektet med ”.sandbox”. I det här exemplet utesluter vi grundläggande anspråken i token.

1. Skapa en princip för Anspråksmappning. Den här principen, länkade till specifika tjänstens huvudnamn, lägger till EmployeeID och TenantCountry anspråk till tokens.
   1. Om du vill skapa principen, kör du följande kommando:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Se din nya princip och för att få principen ObjectId, kör du följande kommando: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta ObjectId för tjänsten huvudnamn. 
   1. Om du vill se din organisations tjänstens huvudnamn, kan du fråga Microsoft Graph. Eller i Azure AD Graph Explorer, logga in på din Azure AD-konto.
   2. När du har ObjectId för din tjänstens huvudnamn, kör du följande kommando: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Se också

Läs hur du anpassar anspråk som utfärdats i SAML-token via Azure portal i [så här: Anpassa anspråk som utfärdats i SAML-token för företagsprogram](active-directory-saml-claims-customization.md)
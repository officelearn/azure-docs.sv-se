---
title: Anspråk mappning i Azure Active Directory (förhandsversion) | Microsoft Docs
description: Den här sidan beskrivs Azure Active Directory anspråk mappning.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: billmath
ms.openlocfilehash: e35a33cbe77d9d29b975ede8535abbded2cde4c3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="claims-mapping-in-azure-active-directory-public-preview"></a>Anspråk mappning i Azure Active Directory (förhandsversion)

>[!NOTE]
>Den här funktionen ersätter och ersätter den [anspråk anpassning](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) idag erbjuds via portalen. Om du har ändrat anspråk med hjälp av portalen förutom diagram/PowerShell-metoden som beskrivs i det här dokumentet för samma program Utfärdad token för att programmet kommer att ignorera konfigurationen i portalen.
Konfigurationer som görs via metoderna som beskrivs i det här dokumentet visas inte i portalen.

Den här funktionen används av innehavaradministratörer anpassa anspråk som hänvisas till i token för ett visst program i klientorganisationen. Du kan använda anspråk mappning av principer för att:

- Välj vilka anspråk som ingår i token.
- Skapa anspråkstyper som inte redan finns.
- Välj eller ändra datakällan som hänvisas till i specifika anspråk.

>[!NOTE]
>Den här funktionen är för närvarande i förhandsversion. Var beredd på att återställa eller ta bort alla ändringar. Funktionen är tillgänglig i alla Azure Active Directory (Azure AD)-prenumeration under förhandsversion. När funktionen blir allmänt tillgänglig, kan vissa aspekter av funktionen kräver en Azure Active Directory premium-prenumeration. Konfigurera principer för mappning av anspråk för WS-Fed, SAML, OAuth och OpenID Connect protokoll har stöd för den här funktionen.

## <a name="claims-mapping-policy-type"></a>Anspråk mappning Principtyp
I Azure AD, en **princip** -objektet representerar en uppsättning regler som tillämpas på enskilda program, eller på alla program i en organisation. Varje princip har ett unikt struktur med en uppsättning egenskaper som sedan tillämpas på objekt som de har tilldelats.

Ett anspråk mappning princip är en typ av **princip** objekt som ändrar anspråk som hänvisas till i token som utfärdats för specifika program.

## <a name="claim-sets"></a>Anspråksuppsättningar
Det finns vissa uppsättningar av anspråk som definierar hur och när de används i token.

### <a name="core-claim-set"></a>Kärnor anspråksuppsättningen
Anspråk i kärnor anspråksuppsättningen finns i varje token, oavsett princip. De här anspråken anses också vara begränsad och kan inte ändras.

### <a name="basic-claim-set"></a>Grundläggande anspråksuppsättningen
Den grundläggande anspråksuppsättningen innehåller de anspråk som släpps som standard för token (förutom kärnor anspråksuppsättningen). Dessa anspråk kan utelämnas eller ändras med hjälp av anspråk Mappa principer.

### <a name="restricted-claim-set"></a>Begränsad anspråksuppsättningen
Begränsat anspråk kan inte ändras med hjälp av Grupprincip. Datakällan kan inte ändras, och ingen transformation som används vid generering av dessa anspråk.

#### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabell 1: JSON-Webbtoken (JWT) begränsad anspråksuppsättning
|Anspråkstyp (namn)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|acr|
|aktören|
|actortoken|
|AIO|
|altsecid|
|amr|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|appid|
|appidacr|
|kontrollen|
|at_hash|
|eller|
|auth_data|
|auth_time|
|authorization_code|
|azp|
|azpacr|
|c_hash|
|ca_enf|
|cc|
|cert_token_use|
|client_id|
|cloud_graph_host_name|
|cloud_instance_name|
|cnf|
|Koden|
|kontroller|
|credential_keys|
|csr|
|csr_type|
|DeviceID|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|e-post|
|slutpunkt|
|enfpolids|
|exp|
|expires_on|
|grant_type|
|graf|
|group_sids|
|grupper|
|hasgroups|
|hash_alg|
|home_oid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier|
|IAT|
|identityprovider|
|IDP|
|in_corp|
|instans|
|ipaddr|
|isbrowserhostedapp|
|ISS|
|jwk|
|key_id|
|key_type|
|mam_compliance_url|
|mam_enrollment_url|
|mam_terms_of_use_url|
|mdm_compliance_url|
|mdm_enrollment_url|
|mdm_terms_of_use_url|
|nameid|
|nbf|
|netbios_name|
|temporärt ID|
|oid|
|on_prem_id|
|onprem_sam_account_name|
|onprem_sid|
|openid2_id|
|lösenord|
|platf|
|polids|
|pop_jwk|
|preferred_username|
|previous_refresh_token|
|primary_sid|
|PUID|
|pwd_exp|
|pwd_url|
|redirect_uri|
|refresh_token|
|refreshtoken|
|request_nonce|
|resurs|
|roll|
|roles|
|Omfång|
|SCP|
|sid|
|Signatur|
|signin_state|
|src1|
|src2|
|Sub|
|tbid|
|tenant_display_name|
|tenant_region_scope|
|thumbnail_photo|
|tid|
|tokenAutologonEnabled|
|trustedfordelegation|
|unique_name|
|upn|
|user_setting_sync_url|
|användarnamn|
|uti|
|ver|
|verified_primary_email|
|verified_secondary_email|
|wids|
|win_ver|

#### <a name="table-2-security-assertion-markup-language-saml-restricted-claim-set"></a>Tabell 2: Security Assertion Markup Language (SAML) begränsad anspråksuppsättning
|Anspråkstyp (URI)|
| ----- |
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.microsoft.com/identity/claims/accesstoken|
|http://schemas.microsoft.com/identity/claims/openid2_id|
|http://schemas.microsoft.com/identity/claims/identityprovider|
|http://schemas.microsoft.com/identity/claims/objectidentifier|
|http://schemas.microsoft.com/identity/claims/puid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
|http://schemas.microsoft.com/identity/claims/tenantid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groups|
|http://schemas.microsoft.com/claims/groups.link|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/role|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/wids|
|http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant|
|http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown|
|http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged|
|http://schemas.microsoft.com/2014/03/psso|
|http://schemas.microsoft.com/claims/authnmethodsreferences|
|http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier|
|http://schemas.microsoft.com/identity/claims/scope|

## <a name="claims-mapping-policy-properties"></a>Anspråk mappning Principegenskaper
Använd egenskaperna för ett anspråk mappning av princip för att styra vilka anspråk som släpps och där data kommer från. Om ingen princip har angetts system problem token som innehåller grundläggande anspråk uppsättningen, den grundläggande anspråksuppsättningen och alla [valfria anspråk](develop/active-directory-optional-claims.md) som programmet har valt att ta emot.

### <a name="include-basic-claim-set"></a>Innehåller grundläggande anspråksuppsättningen

**Sträng:** IncludeBasicClaimSet

**Datatyp:** booleskt värde (True eller False)

**Sammanfattning:** den här egenskapen anger om den grundläggande anspråksuppsättningen ingår i token som påverkas av den här principen. 

- Om värdet är True, alla anspråk i den grundläggande regeluppsättningen orsakat i token som påverkas av principen. 
- Om inställd på False, anspråk i den grundläggande regeluppsättningen inte är i token, om de inte enskilt läggs i egenskapen anspråk schemat i samma princip.

>[!NOTE] 
>Anspråk i kärnor anspråksuppsättningen finns i varje token, oavsett vad den här egenskapen. 

### <a name="claims-schema"></a>Anspråk schema

**Sträng:** ClaimsSchema

**Datatyp:** JSON-blob med en eller flera anspråk schemat poster

**Sammanfattning:** den här egenskapen definierar vilka anspråk finns i de token som påverkas av principen, förutom att den grundläggande anspråksuppsättningen och kärnor anspråksuppsättningen.
Viss information som krävs för varje anspråk schemat post definieras i den här egenskapen. Måste du ange var data kommer från (**värdet** eller **käll-ID par**), och som anspråksdata genereras som (**anspråk typen**).

### <a name="claim-schema-entry-elements"></a>Anspråk schemaelement post

**Värde:** värde definierar ett statiskt värde som data som ska skickas i anspråket.

**Käll-ID par:** Source och ID-element definiera där data i begäran kommer från. 

Källelementet måste anges till något av följande: 


- ”användare”: data i begäran är en egenskap i användarobjektet. 
- ”program”: data i begäran är en egenskap för programmet (klient) tjänstens huvudnamn. 
- ”resurser”: data i begäran är en egenskap på resursen tjänstens huvudnamn.
- ”målgruppen”: data i begäran är en egenskap som är målgruppen för token (när det gäller klienten eller resurs, tjänstens huvudnamn) tjänstens huvudnamn.
- ”företag”: data i begäran är en egenskap på resursen klientens företagets objekt.
- ”omvandling”: data i begäran är från anspråkstransformering (se avsnittet ”omvandling av anspråk” senare i den här artikeln). 

Om källan är omvandling ger den **TransformationID** element måste inkluderas i den här anspråksdefinitionen.

ID-elementet identifierar vilken egenskap på källan som innehåller värdet för anspråket. I följande tabell visas värdena för ID för varje värde i datakällan.

#### <a name="table-3-valid-id-values-per-source"></a>Tabell 3: Giltig ID-värden per källa
|Källa|ID|Beskrivning|
|-----|-----|-----|
|Användare|Efternamn|Efternamn|
|Användare|givenName|Förnamn|
|Användare|displayname|Visningsnamn|
|Användare|objekt-ID|ObjectId|
|Användare|E-post|E-postadress|
|Användare|userprincipalname|Användarens huvudnamn|
|Användare|Avdelning|Avdelning|
|Användare|onpremisessamaccountname|På lokala Sam-kontonamn|
|Användare|NetBIOS-namn|NetBios-namn|
|Användare|dnsdomainname|DNS-domännamn|
|Användare|onpremisesecurityidentifier|lokala säkerhetsidentifierare|
|Användare|Företagsnamn|Organisationens namn|
|Användare|streetAddress|Gatuadress|
|Användare|postalcode|Postnummer|
|Användare|preferredlanguange|Önskat språk|
|Användare|onpremisesuserprincipalname|lokal UPN|
|Användare|mailNickname|Smeknamn för e-post|
|Användare|extensionattribute1|Attributet för anknytning 1|
|Användare|extensionattribute2|Attributet för anknytning 2|
|Användare|extensionattribute3|Attributet för anknytning 3|
|Användare|extensionattribute4|Attributet för anknytning 4|
|Användare|extensionattribute5|Attributet för anknytning 5|
|Användare|extensionattribute6|Attributet för anknytning 6|
|Användare|extensionattribute7|Attributet för anknytning 7|
|Användare|extensionattribute8|Attributet för anknytning 8|
|Användare|extensionattribute9|Attributet för anknytning 9|
|Användare|extensionattribute10|Attributet för anknytning 10|
|Användare|extensionattribute11|Attributet för anknytning 11|
|Användare|extensionattribute12|Attributet för anknytning 12|
|Användare|extensionattribute13|Attributet för anknytning 13|
|Användare|extensionattribute14|Attributet för anknytning 14|
|Användare|extensionattribute15|Attributet för anknytning 15|
|Användare|othermail|Andra e-post|
|Användare|Land|Land/region|
|Användare|city|Ort|
|Användare|state|Status|
|Användare|befattning|Befattning|
|Användare|EmployeeID|Anställnings-ID|
|Användare|facsimiletelephonenumber|Fax telefonnummer|
|program, resurs, målgrupp|displayname|Visningsnamn|
|program, resurs, målgrupp|objekt|ObjectId|
|program, resurs, målgrupp|tags|Tjänstens huvudnamn tagg|
|Företag|tenantcountry|Klientens land|

**TransformationID:** TransformationID-elementet måste anges endast om källelementet är inställd på ”omvandling”.

- Det här elementet måste matcha ID-elementet i posten omvandling i den **ClaimsTransformation** egenskap som definierar hur data för denna begäran ska skapas.

**Anspråkstyp:** den **JwtClaimType** och **SamlClaimType** element definiera vilka anspråk anspråk schemat transaktionen refererar till.

- JwtClaimType måste innehålla namnet på anspråk som ska skickas i JWTs.
- SamlClaimType måste innehålla URI för anspråk som ska skickas i SAML-token.

>[!NOTE]
>Namn och URI: er av anspråk i den begränsade anspråksuppsättningen kan inte användas för anspråk typen element. Mer information finns i avsnittet ”undantag och begränsningar” senare i den här artikeln.

### <a name="claims-transformation"></a>Omvandling av anspråk

**Sträng:** ClaimsTransformation

**Datatyp:** JSON-blob med en eller flera poster för omvandling 

**Sammanfattning:** Använd egenskapen för att använda vanliga omformningar på källdata att generera utdata för anspråk som angetts i schemat för anspråk.

**ID:** Använd ID-elementet för att referera omvandling posten i posten TransformationID anspråk schemat. Värdet måste vara unikt för varje omvandling transaktion i den här principen.

**TransformationMethod:** TransformationMethod-element som identifierar vilka åtgärden utförs för att generera data för begäran.

Baserat på vilken metod som valts, förväntas en uppsättning av in- och utdataenheter. Dessa definieras med hjälp av den **InputClaims**, **indataparametrar** och **OutputClaims** element.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabell 4: Omvandling metoder och förväntade in- och utdataenheter
|TransformationMethod|Förväntade indata|Utdata som förväntas|Beskrivning|
|-----|-----|-----|-----|
|Slå ihop|sträng1, sträng2, avgränsare|outputClaim|Kopplingar ange strängar med avgränsare mellan. Till exempel: sträng1 ”:foo@bar.com”, sträng2: ”sandbox”, avgränsare ”:”. resulterar i outputClaim ”:foo@bar.com.sandbox”|
|ExtractMailPrefix|E-post|outputClaim|Extraherar den lokala delen av en e-postadress. Till exempel: e-post ”:foo@bar.com” resulterar i outputClaim: ”foo”. Om Nej @ finns logga returneras orignal Indatasträngen som är.|

**InputClaims:** använda ett InputClaims-element för att skicka data från en post i anspråk schema till en omvandling. Den har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId** kopplas till ID-elementet för registerposten anspråk schemat att hitta lämplig inkommande anspråk. 
- **TransformationClaimType** används för att ge ett unikt namn för den här indata. Det här namnet måste matcha en förväntade indata för metoden omvandling.

**Indataparametrar:** använder ett indataparametrar-element för att skicka ett konstant värde till en omvandling. Den har två attribut: **värdet** och **ID**.

- **Värdet** är det faktiska konstanta värdet som ska skickas.
- **ID** används för att ge ett unikt namn för den här indata. Det här namnet måste matcha en förväntade indata för metoden omvandling.

**OutputClaims:** använda ett OutputClaims element ska innehålla data som genereras av en omvandling och koppla den till en post i anspråk schemat. Den har två attribut: **ClaimTypeReferenceId** och **TransformationClaimType**.

- **ClaimTypeReferenceId** med ID: T för posten anspråk schemat att hitta lämplig utgående anspråk.
- **TransformationClaimType** används för att ge ett unikt namn för den här utdatan. Det här namnet måste matcha en förväntad utdata för transformation-metod.

### <a name="exceptions-and-restrictions"></a>Undantag och begränsningar

**SAML-NameID och UPN:** attribut från vilken du käll-NameID och UPN-värden och anspråksomvandlingar som tillåts är begränsade.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabell 5: Attribut användas som en datakälla för SAML-NameID
|Källa|ID|Beskrivning|
|-----|-----|-----|
|Användare|E-post|E-postadress|
|Användare|userprincipalname|Användarens huvudnamn|
|Användare|onpremisessamaccountname|På lokala Sam-kontonamn|
|Användare|EmployeeID|Anställnings-ID|
|Användare|extensionattribute1|Attributet för anknytning 1|
|Användare|extensionattribute2|Attributet för anknytning 2|
|Användare|extensionattribute3|Attributet för anknytning 3|
|Användare|extensionattribute4|Attributet för anknytning 4|
|Användare|extensionattribute5|Attributet för anknytning 5|
|Användare|extensionattribute6|Attributet för anknytning 6|
|Användare|extensionattribute7|Attributet för anknytning 7|
|Användare|extensionattribute8|Attributet för anknytning 8|
|Användare|extensionattribute9|Attributet för anknytning 9|
|Användare|extensionattribute10|Attributet för anknytning 10|
|Användare|extensionattribute11|Attributet för anknytning 11|
|Användare|extensionattribute12|Attributet för anknytning 12|
|Användare|extensionattribute13|Attributet för anknytning 13|
|Användare|extensionattribute14|Attributet för anknytning 14|
|Användare|extensionattribute15|Attributet för anknytning 15|

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabell 6: Omvandling metoder som tillåts för SAML-NameID
|TransformationMethod|Begränsningar|
| ----- | ----- |
|ExtractMailPrefix|Ingen|
|Slå ihop|Suffixet som ansluten måste vara en verifierad domän för resurs-klienten.|

### <a name="custom-signing-key"></a>Anpassad signeringsnyckel
En anpassad signeringsnyckel måste tilldelas till serviceobjektet huvudnamn för ett anspråksprovider-mappning principen ska gälla. Alla token som utfärdas som har påverkats av principen signeras med den här nyckeln. Program som måste konfigureras för att acceptera token signeras med den här nyckeln. Detta säkerställer att bekräftelse att token har ändrats av skapar anspråk mappning av principen. Detta skyddar program från anspråk som mappning av principer som skapas med skadliga aktörer.

### <a name="cross-tenant-scenarios"></a>Scenarier för flera innehavare
Anspråk Mappa principer gäller inte för gästanvändare. Om en gästanvändaren försöker komma åt ett program med ett anspråk mappning av principer som tilldelas till dess huvudnamn för tjänsten, standard-token utfärdas (principen har ingen effekt).

## <a name="claims-mapping-policy-assignment"></a>Anspråk mappning tilldelning av principer
Anspråk Mappa principer kan endast tilldelas service principal objekt.

### <a name="example-claims-mapping-policies"></a>Exempel anspråk mappning principer

Många scenarier är möjliga i Azure AD när du kan anpassa anspråk som hänvisas till i token för specifika tjänstens huvudnamn. I det här avsnittet kommer vi att gå igenom några vanliga scenarier som kan hjälpa dig tag i hur du använder anspråk mappning principtypen.

#### <a name="prerequisites"></a>Förutsättningar
I följande exempel du skapa, uppdatera, länkar och ta bort principer för tjänstens huvudnamn. Om du har använt Azure AD, rekommenderar vi att du lär dig mer om hur du hämtar en Azure AD-klient innan du fortsätter med de här exemplen. 

Utför följande steg för att komma igång:


1. Ladda ned senaste [Azure AD PowerShell-modulen offentliga förhandsversionen](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.127).
2.  Kör kommandot Connect för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gång startar du en ny session.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
3.  Kör följande kommando för att se alla principer som har skapats i din organisation. Vi rekommenderar att du kör det här kommandot efter de flesta åtgärder i följande scenarier för att kontrollera att dina principer som skapas som förväntat.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exempel: Skapa och tilldela en princip för att utelämna grundläggande anspråk från token som utfärdas till en tjänstens huvudnamn.
I det här exemplet skapar du en princip som tar bort den grundläggande anspråk från token som utfärdas till länkade tjänstens huvudnamn.


1. Skapa ett anspråk mappning av principen. Den här principen, länkade till specifika tjänstens huvudnamn tar den grundläggande anspråk från token.
    1. Om du vill skapa principen, kör du kommandot: 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims” -Type "ClaimsMappingPolicy"
    ```
    2. Se din nya principen och be om principen ObjectId, kör du följande kommando:
    
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Tilldela principen till tjänstens huvudnamn. Du måste hämta ObjectId din tjänsts huvudnamn. 
    1.  Om du vill se alla organisationens tjänstens huvudnamn, kan du fråga Microsoft Graph. Eller i Azure AD Graph Explorer, logga in på Azure AD-kontot.
    2.  När du har ObjectId din tjänstens huvudnamn, kör du följande kommando:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exempel: Skapa och tilldela en princip för att inkludera EmployeeID och TenantCountry som anspråk i token som utfärdas till en tjänstens huvudnamn.
I det här exemplet skapar du en princip som lägger tillför EmployeeID och TenantCountry token som utfärdas till länkade tjänstens huvudnamn. EmployeeID genereras som Anspråkstyp namn i både SAML-token och JWTs. TenantCountry genereras som Anspråkstyp för land i både SAML-token och JWTs. I det här exemplet fortsätter vi att innehåller enkla anspråk i token.

1. Skapa ett anspråk mappning av principen. Den här principen, länkade till specifika tjänstens huvudnamn, lägger till EmployeeID och TenantCountry anspråk till tokens.
    1. Om du vill skapa principen, kör du kommandot:  
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":" tenantcountry ","SamlClaimType":" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country ","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample” -Type "ClaimsMappingPolicy"
    ```
    
    2. Se din nya principen och be om principen ObjectId, kör du följande kommando:
     
     ``` powershell  
    Get-AzureADPolicy
    ```
2.  Tilldela principen till tjänstens huvudnamn. Du måste hämta ObjectId din tjänsts huvudnamn. 
    1.  Om du vill se alla organisationens tjänstens huvudnamn, kan du fråga Microsoft Graph. Eller i Azure AD Graph Explorer, logga in på Azure AD-kontot.
    2.  När du har ObjectId din tjänstens huvudnamn, kör du följande kommando:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exempel: Skapa och tilldela en princip som använder en anspråkstransformering i token som utfärdas till en tjänstens huvudnamn.
I det här exemplet skapar du en princip som genererar ett anpassat anspråk ”JoinedData” till JWTs utfärdat till länkade tjänstens huvudnamn. Denna begäran innehåller ett värde som skapats genom att ansluta till data som lagras i attributet extensionattribute1 på användarobjekt med ”.sandbox”. I det här exemplet exkludera vi enkla anspråk i token.


1. Skapa ett anspråk mappning av principen. Den här principen, länkade till specifika tjänstens huvudnamn, lägger till EmployeeID och TenantCountry anspråk till tokens.
    1. Om du vill skapa principen, kör du kommandot: 
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy" 
    ```
    
    2. Se din nya principen och be om principen ObjectId, kör du följande kommando: 
     
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Tilldela principen till tjänstens huvudnamn. Du måste hämta ObjectId din tjänsts huvudnamn. 
    1.  Om du vill se alla organisationens tjänstens huvudnamn, kan du fråga Microsoft Graph. Eller i Azure AD Graph Explorer, logga in på Azure AD-kontot.
    2.  När du har ObjectId din tjänstens huvudnamn, kör du följande kommando: 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```

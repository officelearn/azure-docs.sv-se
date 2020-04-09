---
title: Förstå Azure Active Directory-appmanifestet
description: Detaljerad täckning av Azure Active Directory-appmanifestet, som representerar ett programs identitetskonfiguration i en Azure AD-klientorganisation, och används för att underlätta OAuth-auktorisering, medgivandeupplevelse med mera.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/23/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 487559aaf632a30b7efcda490f92a90d4f59b8f5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883482"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-appmanifest

Programmanifestet innehåller en definition av alla attribut för ett programobjekt i Microsofts identitetsplattform. Det fungerar också som en mekanism för att uppdatera programobjektet. Mer information om programmettiteten och dess schema finns i [dokumentationen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)för Graph API Application Entity .

Du kan konfigurera en apps attribut via Azure-portalen eller programmässigt med [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) eller [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Det finns dock vissa scenarier där du måste redigera appmanifestet för att konfigurera en apps attribut. Några vanliga scenarier:

* Om du har registrerat appen som Azure AD-konton med flera innehavare och personliga Microsoft-konton kan du inte ändra de Microsoft-konton som stöds i användargränssnittet. I stället måste du använda programmanifestredigeraren för att ändra kontotypen som stöds.
* Om du behöver definiera behörigheter och roller som appen stöder måste du ändra programmanifestet.

## <a name="configure-the-app-manifest"></a>Konfigurera appmanifestet

Så här konfigurerar du programmanifestet:

1. Gå till [Azure-portalen](https://portal.azure.com). Sök efter och välj **Azure Active Directory-tjänsten.**
1. Välj **Appregistreringar**.
1. Välj den app som du vill konfigurera.
1. På appens **översiktssida** väljer du avsnittet **Manifest**. En webbaserad manifestredigerare öppnas, så att du kan redigera manifestet i portalen. Du kan också välja **Hämta** för att redigera manifestet lokalt och sedan använda **Ladda upp** för att använda det på nytt i ditt program.

## <a name="manifest-reference"></a>Manifestreferens

I det här avsnittet beskrivs de attribut som finns i programmanifestet.

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| åtkomstTillkenAcceptedVersion | Nullable Int32 |

Anger den åtkomsttokenversion som resursen förväntas. Den här parametern ändrar versionen och formatet för den JWT som produceras oberoende av slutpunkten eller klienten som används för att begära åtkomsttoken.

Slutpunkten som används, v1.0 eller v2.0, väljs av klienten och påverkar endast versionen av id_tokens. Resurser måste uttryckligen `accesstokenAcceptedVersion` konfigurera för att ange det åtkomsttokenformat som stöds.

Möjliga värden `accesstokenAcceptedVersion` för är 1, 2 eller null. Om värdet är null, den här parametern är 1, vilket motsvarar v1.0-slutpunkten.

Om `signInAudience` `AzureADandPersonalMicrosoftAccount`är , måste `2`värdet vara .

Exempel:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| Addins | Samling |

Definierar anpassat beteende som en tidskrävande tjänst kan använda för att anropa en app i specifika sammanhang. Program som kan återge filströmmar kan `addIns` till exempel ange egenskapen för dess "FileHandler"-funktion. Med den här parametern kan tjänster som Office 365 anropa programmet i kontexten för ett dokument som användaren arbetar med.

Exempel:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| allowPublicClient | Boolesk |

Anger programtypen för återgång. Azure AD härleder programtypen från replyUrlsWithType som standard. Det finns vissa scenarier där Azure AD inte kan avgöra klientapptypen. Ett sådant scenario är till exempel [ROPC-flödet](https://tools.ietf.org/html/rfc6749#section-4.3) där HTTP-begäran sker utan en URL-omdirigering). I sådana fall tolkar Azure AD programtypen baserat på värdet för den här egenskapen. Om det här värdet är inställt på true anges reservprogramtypen som offentlig klient, till exempel en installerad app som körs på en mobil enhet. Standardvärdet är falskt vilket innebär att reservprogramtypen är konfidentiell klient, till exempel webbapp.

Exempel:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>tillgängligtToOtherTenants-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| tillgängligTillOtherTenants | Boolesk |

Ange till true om programmet delas med andra klienter. annars falskt.

> [!NOTE]
> Det här attributet är endast tillgängligt i **appregistreringarna (Legacy)** . Ersatt `signInAudience` av i [upplevelsen av appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="appid-attribute"></a>appId-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| appId | Sträng |

Anger den unika identifieraren för appen som tilldelas en app av Azure AD.

Exempel:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| appRoles | Samling |

Anger den samling av roller som en app kan deklarera. Dessa roller kan tilldelas användare, grupper eller tjänsthuvudnamn. Mer exempel och information finns i [Lägga till approller i ditt program och ta emot dem i token](howto-add-app-roles-in-azure-ad-apps.md).

Exempel:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>attributet displayName

| Nyckel | Värdetyp |
| :--- | :--- |
| displayName | Sträng |

Appens visningsnamn.

> [!NOTE]
> Det här attributet är endast tillgängligt i **appregistreringarna (Legacy)** . Ersatt `name` av i [upplevelsen av appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="errorurl-attribute"></a>errorUrl-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| errorUrl | Sträng |

Unsupported.

### <a name="groupmembershipclaims-attribute"></a>attributet groupMembershipClaims

| Nyckel | Värdetyp |
| :--- | :--- |
|gruppMedlemskapKlamerna | Sträng |

Konfigurerar `groups` anspråket som utfärdats i en användare eller OAuth 2.0-åtkomsttoken som appen förväntar sig. Om du vill ange det här attributet använder du något av följande giltiga strängvärden:

- `"None"`
- `"SecurityGroup"`(för säkerhetsgrupper och Azure AD-roller)
- `"All"`(Detta hämtar alla säkerhetsgrupper, distributionsgrupper och Azure AD-katalogroller som den inloggade användaren är medlem i.

Exempel:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>attributet hemsida

| Nyckel | Värdetyp |
| :--- | :--- |
| Hemsida |Sträng |

URL:en till programmets startsida.

> [!NOTE]
> Det här attributet är endast tillgängligt i **appregistreringarna (Legacy)** . Ersatt `signInUrl` av i [upplevelsen av appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="objectid-attribute"></a>attributet objectId

| Nyckel | Värdetyp |
| :--- | :--- |
|Objectid | Sträng |

Den unika identifieraren för appen i katalogen.

Detta är endast tillgängligt i **appregistreringarna (Legacy)** upplevelsen. Ersatt `id` av i [upplevelsen av appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)

Exempel:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>attributet optionalClaims

| Nyckel | Värdetyp |
| :--- | :--- |
| tillvalKlamörer | Sträng |

De valfria anspråk som returneras i token av säkerhetstokentjänsten för den här specifika appen.

För närvarande kan appar som stöder både personliga konton och Azure AD (registrerade via appregistreringsportalen) inte använda valfria anspråk. Appar som bara är registrerade för Azure AD med v2.0-slutpunkten kan dock få de valfria anspråk som de begärde i manifestet. Mer information finns i [Valfria anspråk](active-directory-optional-claims.md).

Exempel:

```json
    "optionalClaims": null,
```

### <a name="id-attribute"></a>attribut för id

| Nyckel | Värdetyp |
| :--- | :--- |
| id | Sträng |

Den unika identifieraren för appen i katalogen. Det här ID:t är inte den identifierare som används för att identifiera appen i en protokolltransaktion. Den används för att referera till objektet i katalogfrågor.

Exempel:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="identifieruris-attribute"></a>attributet identifierUris

| Nyckel | Värdetyp |
| :--- | :--- |
| identifierarUris | Strängmatris |

Användardefinierade URI:er som unikt identifierar en webbapp i azure AD-klienten eller inom en verifierad anpassad domän om appen är flera innehavare.

Exempel:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>attributet informationalUrls

| Nyckel | Värdetyp |
| :--- | :--- |
| informationsurer | Sträng |

Anger länkarna till appens användarvillkor och sekretesspolicy. Villkoren för tjänsten och sekretesspolicyn visas för användarna genom användarsamtyckesupplevelsen. Mer information finns i [Så här lägger du till användarvillkor och sekretesspolicy för registrerade Azure AD-appar](howto-add-terms-of-service-privacy-statement.md).

Exempel:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>attributet keyCredentials

| Nyckel | Värdetyp |
| :--- | :--- |
| keyCredentials | Samling |

Innehåller referenser till apptilldelade autentiseringsuppgifter, strängbaserade delade hemligheter och X.509-certifikat. Dessa autentiseringsuppgifter används när du begär åtkomsttoken (när appen fungerar som en klient snarare som en resurs).

Exempel:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>känt attributetClientApplications

| Nyckel | Värdetyp |
| :--- | :--- |
| kändaClientApplications | Strängmatris |

Används för att bunta samtycke om du har en lösning som innehåller två delar: en klientapp och en anpassad webb-API-app. Om du anger klientappens appID i det här värdet behöver användaren bara godkänna en gång till klientappen. Azure AD vet att samtycke till klienten innebär att implicit samtycka till webb-API:et. Det kommer automatiskt att tillhandahålla tjänstens huvudnamn för både klient- och webb-API:et samtidigt. Både klienten och webb-API-appen måste vara registrerade i samma klient.

Exempel:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>attributet logoUrl

| Nyckel | Värdetyp |
| :--- | :--- |
| logoUrl (logotypUrl) | Sträng |

Skrivskyddat värde som pekar på CDN-URL:en till-logotypen som laddades upp i portalen.

Exempel:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>attributet logoutUrl

| Nyckel | Värdetyp |
| :--- | :--- |
| logoutUrl | Sträng |

URL:en för att logga ut från appen.

Exempel:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>namnattribut

| Nyckel | Värdetyp |
| :--- | :--- |
| namn | Sträng |

Appens visningsnamn.

Exempel:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolesk |

Anger om den här webbappen kan begära OAuth2.0 implicita flödesåtkomsttoken. Standardvärdet är falskt. Den här flaggan används för webbläsarbaserade appar, till exempel JavaScript-appar med en sida. Om du vill `OAuth 2.0 implicit grant flow` veta mer anger du i innehållsförteckningen och läser avsnitten om implicit flöde.

Exempel:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolesk |

Anger om den här webbappen kan begära OAuth2.0 implicita flödes-ID-token. Standardvärdet är falskt. Den här flaggan används för webbläsarbaserade appar, till exempel JavaScript-appar med en sida.

Exempel:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>attributet oauth2Permissions

| Nyckel | Värdetyp |
| :--- | :--- |
| oauth2Permissions | Samling |

Anger samlingen av OAuth 2.0-behörighetsscope som webb-API-appen (resurs)visar för klientappar. Dessa behörighetsscope kan beviljas klientappar under medgivandet.

Exempel:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>attributet oauth2RequiredPostResponse

| Nyckel | Värdetyp |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolesk |

Anger om Azure AD, som en del av OAuth 2.0-tokenbegäranden, tillåter POST-begäranden, i motsats till GET-begäranden. Standardvärdet är falskt, vilket anger att endast GET-begäranden tillåts.

Exempel:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>attributet parentalControlSettings

| Nyckel | Värdetyp |
| :--- | :--- |
| föräldrakontroller | Sträng |

- `countriesBlockedForMinors`anger i vilka länder appen är blockerad för minderåriga.
- `legalAgeGroupRule`anger den regel om juridisk åldersgrupp som gäller för användare av appen. Kan ställas `Allow`in `RequireConsentForPrivacyServices` `RequireConsentForMinors`på `RequireConsentForKids`, `BlockMinors`, , eller .  

Exempel:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>attributet passwordCredentials

| Nyckel | Värdetyp |
| :--- | :--- |
| lösenordSkruven | Samling |

Se beskrivningen för `keyCredentials` egenskapen.

Exempel:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>attributet preAuthorizedApplications

| Nyckel | Värdetyp |
| :--- | :--- |
| preAuthorizedApplications | Samling |

Visar program och begärda behörigheter för implicit medgivande. Kräver att en administratör har gett samtycke till programmet. preAuthorizedApplications kräver inte att användaren samtycker till de begärda behörigheterna. Behörigheter som anges i preAuthorizedApplications kräver inte användarens medgivande. Alla ytterligare begärda behörigheter som inte anges i preAuthorizedApplications kräver dock användarens medgivande.

Exempel:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>attributet publicClient

| Nyckel | Värdetyp |
| :--- | :--- |
| offentligaClient | Boolesk|

Anger om det här programmet är en offentlig klient (till exempel ett installerat program som körs på en mobil enhet). 

Den här egenskapen är endast tillgänglig i **appregistreringarna (Legacy)** upplevelsen. Ersatt `allowPublicClient` av i [upplevelsen av appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="publisherdomain-attribute"></a>attributet publisherDomain

| Nyckel | Värdetyp |
| :--- | :--- |
| utgivareDomain | Sträng |

Den verifierade utgivardomänen för programmet. Skrivskyddad.

Exempel:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>attributet replyUrls

| Nyckel | Värdetyp |
| :--- | :--- |
| svaraUrls | Strängmatris |

Den här egenskapen med flera värden innehåller listan över registrerade redirect_uri värden som Azure AD accepterar som mål när token returneras.

Den här egenskapen är endast tillgänglig i **appregistreringarna (Legacy)** upplevelsen. Ersatt `replyUrlsWithType` av i [upplevelsen av appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="replyurlswithtype-attribute"></a>attributet replyUrlsWithType

| Nyckel | Värdetyp |
| :--- | :--- |
| replyUrlsWithType | Samling |

Den här egenskapen med flera värden innehåller listan över registrerade redirect_uri värden som Azure AD accepterar som mål när token returneras. Varje URI-värde ska innehålla ett associerat apptypsvärde. Typvärden som stöds är:

- `Web`
- `InstalledClient`

Mer information finns i [replyUrl-begränsningar och begränsningar](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

Exempel:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess-attributet

| Nyckel | Värdetyp |
| :--- | :--- |
| requiredResourceAccess | Samling |

Med dynamiskt `requiredResourceAccess` medgivande, driver administratören samtycke erfarenhet och användarens medgivande erfarenhet för användare som använder statiskt samtycke. Den här parametern driver dock inte användarmedgivandeupplevelsen för det allmänna fallet.

- `resourceAppId`är den unika identifieraren för den resurs som appen kräver åtkomst till. Det här värdet ska vara lika med den appId som deklarerats i målresursappen.
- `resourceAccess`är en matris som visar behörighetsomfattningar och approller som appen kräver från den angivna resursen. Innehåller `id` värdena `type` och för de angivna resurserna.

Exempel:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl-attribut

| Nyckel | Värdetyp |
| :--- | :--- |
| samlMetadataUrl | Sträng |

URL:en till SAML-metadata för appen.

Exempel:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>attributet signInUrl

| Nyckel | Värdetyp |
| :--- | :--- |
| signInUrl | Sträng |

Anger URL:en till appens startsida.

Exempel:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>attributet signInAudience

| Nyckel | Värdetyp |
| :--- | :--- |
| teckenInAudience | Sträng |

Anger vilka Microsoft-konton som stöds för det aktuella programmet. Värden som stöds är:
- `AzureADMyOrg`- Användare med ett Microsoft-arbets- eller skolkonto i organisationens Azure AD-klientorganisation (till exempel en enda klientorganisation)
- `AzureADMultipleOrgs`- Användare med ett Microsoft-arbets- eller skolkonto i en organisations Azure AD-klientorganisation (till exempel flera innehavare)
- `AzureADandPersonalMicrosoftAccount`- Användare med ett personligt Microsoft-konto, eller ett arbets- eller skolkonto i en organisations Azure AD-klientorganisation
- `PersonalMicrosoftAccount`- Personliga konton som används för att logga in på tjänster som Xbox och Skype.

Exempel:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>attribut för taggar

| Nyckel | Värdetyp |
| :--- | :--- |
| tags | Strängmatris  |

Anpassade strängar som kan användas för att kategorisera och identifiera programmet.

Exempel:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Vanliga problem

### <a name="manifest-limits"></a>Manifestgränser

Ett programmanifest har flera attribut som kallas samlingar. till exempel appRoles, keyCredentials, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess och oauth2Permissions. Inom det fullständiga ansökningsmanifestet för alla ansökningar har det totala antalet poster i alla samlingar som tillsammans har begränsats till 1200. Om du tidigare har angett 100 omdirigerings-URI:er i programmanifestet, är du bara kvar med 1100 återstående poster som ska användas i alla andra samlingar som har kombinerats och som utgör manifestet.

> [!NOTE]
> Om du försöker lägga till mer än 1200 poster i programmanifestet kan du se ett felmeddelande **"Det gick inte att uppdatera programmet xxxxxx. Felinformation: Manifestets storlek har överskridit gränsen. Minska antalet värden och försök igen med din begäran."**

### <a name="unsupported-attributes"></a>Attribut som inte stöds

Programmanifestet representerar schemat för den underliggande programmodellen i Azure AD. När det underliggande schemat utvecklas uppdateras manifestredigeraren så att det återspeglar det nya schemat från tid till annan. Därför kan du märka nya attribut som visas i programmanifestet. I sällsynta fall kan du märka en syntaktisk eller semantisk förändring i befintliga attribut eller så kan du hitta ett attribut som fanns tidigare stöds inte längre. Du kommer till exempel att se nya attribut i [appregistreringarna](https://go.microsoft.com/fwlink/?linkid=2083908), som är kända med ett annat namn i appregistreringarna (Legacy) .

| Appregistreringar (äldre)| Appregistreringar           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Beskrivningar för dessa attribut finns i [manifestreferensavsnittet.](#manifest-reference)

När du försöker ladda upp ett tidigare nedladdat manifest kan du se något av följande fel. Det här felet beror sannolikt på att manifestredigeraren nu stöder en nyare version av schemat, som inte matchar med den du försöker ladda upp.

* "Det gick inte att uppdatera xxxxxx-programmet. Feldetalj: Ogiltig objektidentifierare "odefinierad". []."
* "Det gick inte att uppdatera xxxxxx-programmet. Felinformation: Ett eller flera angivna egenskapsvärden är ogiltiga. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Felinformation: Det går inte att ange tillgängligaTillOtherTenants i den här api-versionen för uppdatering. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Felinformation: Det är inte tillåtet att uppdatera egenskapen replyUrls för det här programmet. Använd egenskapen replyUrlsWithType i stället. []."
* "Det gick inte att uppdatera xxxxxx-programmet. Feldetalj: Ett värde utan typnamn hittades och ingen förväntad typ är tillgänglig. När modellen anges måste varje värde i nyttolasten ha en typ som antingen kan anges i nyttolasten, uttryckligen av anroparen eller implicit härledas från det överordnade värdet. []"

När du ser något av dessa fel rekommenderar vi följande åtgärder:

1. Redigera attributen individuellt i manifestredigeraren i stället för att ladda upp ett tidigare nedladdat manifest. Använd [manifestreferenstabellen](#manifest-reference) för att förstå syntaxen och semantiken för gamla och nya attribut så att du kan redigera de attribut du är intresserad av. 
1. Om arbetsflödet kräver att du sparar manifesten i källdatabasen för senare användning föreslår vi att du återbaserar de sparade manifesten i databasen med det du ser i **appregistreringsupplevelsen.**

## <a name="next-steps"></a>Nästa steg

* Mer information om relationen mellan en apps program- och tjänsthuvudobjekt finns [i Principobjekt för program och tjänst i Azure AD](app-objects-and-service-principals.md).
* Se [Microsofts identitetsplattformsutvecklarordlista](developer-glossary.md) för definitioner av vissa grundläggande koncept för utvecklare av microsoft-identitetsplattformar.

Använd följande kommentarsavsnitt för att ge feedback som hjälper till att förfina och forma vårt innehåll.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

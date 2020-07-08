---
title: 'Använd Microsoft Graph-API: er för att konfigurera SAML-baserad enkel inloggning'
titleSuffix: Azure Active Directory
description: 'Behöver du konfigurera SAML-baserad enkel inloggning för flera instanser av ett program? Lär dig hur du sparar tid genom att använda Microsoft Graph API: er för att automatisera konfigurationen av SAML-baserad enkel inloggning.'
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 50ee9e3c22c885931e2586f65ba2fa3353fccfeb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85355853"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Automatisera SAML-baserad SSO-app-konfiguration med Microsoft Graph-API

I den här artikeln får du lära dig hur du skapar och konfigurerar ett program från Azure Active Directory-galleriet (Azure AD). I den här artikeln används AWS som ett exempel, men du kan använda stegen i den här artikeln för alla SAML-baserade appar i Azure AD-galleriet.

**Steg för att använda Microsoft Graph API: er för att automatisera konfigurationen av SAML-baserad enkel inloggning**

| Steg  | Information  |
|---------|---------|
| [1. skapa Galleri programmet](#step-1-create-the-gallery-application) | Logga in på API-klienten <br> Hämta Galleri programmet <br> Skapa Galleri programmet|
| [2. Konfigurera enkel inloggning](#step-2-configure-single-sign-on) | Hämta app-objekt-ID och objekt-ID för tjänstens huvud namn <br> Ange läge för enkel inloggning <br> Ange grundläggande SAML-URL: er, till exempel identifierare, svars-URL, inloggnings-URL <br> Lägg till app-roller (valfritt)|
| [3. Konfigurera anspråks mappning](#step-3-configure-claims-mapping) | Skapa anspråks mappnings princip <br> Tilldela en princip för anspråk mappning till tjänstens huvud namn|
| [4. Konfigurera signerings certifikat](#step-4-configure-signing-certificate) | Skapa ett certifikat <BR> Lägg till en anpassad signerings nyckel <br> Aktivera den anpassade signerings nyckeln|
| [5. tilldela användare](#step-5-assign-users) | Tilldela användare och grupper till programmet
| [6. Konfigurera program Sidan](#step-6-configure-the-application-side)| Hämta Azure AD SAML-metadata

**Lista över alla API: er som används i dokumentationen**

Kontrol lera att du har motsvarande behörighet för att anropa följande API: er.

|Resurstyp |Metod |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[Visa lista applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[Instansiera applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[Service princip ALS](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)|[Uppdatera servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-1.0&tabs=http) <br> [Skapa appRoleAssignments](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-1.0&tabs=http) <br> [Tilldela claimsMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[ansökan](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Uppdatera program](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-1.0&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [Skapa claimsMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>Svars objekt som visas i den här artikeln kan bli kortare för läsbarhet. Alla egenskaper kommer att returneras från ett faktiskt anrop.

## <a name="step-1-create-the-gallery-application"></a>Steg 1: skapa Galleri programmet

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Logga in på Microsoft Graph Explorer (rekommenderas), Postman eller någon annan API-klient som du använder

1. Starta [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
2. Välj **Logga in med Microsoft** och logga in med en global administratör för Azure AD eller en app admin-autentiseringsuppgifter.
3. Vid lyckad inloggning visas användar konto informationen i den vänstra rutan.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Hämta mall-ID för Galleri program

Program i Azure AD-programgalleriet har en [Programmall](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) som beskriver metadata för programmet. Med den här mallen kan du skapa en instans av programmet och tjänstens huvud namn i din klient organisation för hantering.

#### <a name="request"></a>Förfrågan

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Svar

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Skapa Galleri programmet

Med det mall-ID som du hämtade för programmet i det sista steget [skapar du en instans](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) av programmet och tjänstens huvud namn i din klient organisation.

> [!NOTE] 
> Du kan använda applicationTemplate API för att instansiera [appar som inte är gallerier](add-non-gallery-app.md). Använd applicationTemplateId `8adf8e6e-67b2-4cf2-a259-e3dc5476c621` .
#### <a name="request"></a>Förfrågan

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Svar


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>Steg 2: Konfigurera enkel inloggning

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>Hämta app-objekt-ID och objekt-ID för tjänstens huvud namn

Använd svaret från föregående anrop för att hämta och spara programobjekt-ID och objekt-ID för tjänstens huvud namn.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Ange läge för enkel inloggning

I det här exemplet ska du ange `saml` som enkel inloggnings läge i [resurs typen servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0). Andra egenskaper för SAML SSO som du kan konfigurera är: `notificationEmailAddresses` , `loginUrl` , och`samlSingleSignOnSettings.relayState`

#### <a name="request"></a>Förfrågan

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml"
}
```

#### <a name="response"></a>Svar

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Ange grundläggande SAML-URL: er, till exempel identifierare, svars-URL, inloggnings-URL

Ange ID och svars-URL: er för AWS i programobjektet.

#### <a name="request"></a>Förfrågan

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Svar

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>Lägg till app-roller (valfritt)

Om programmet kräver roll information i token lägger du till definitionen av rollerna i programobjektet. För AWS kan du [Aktivera användar etablering](../app-provisioning/application-provisioning-configure-api.md) för att hämta alla roller från det AWS-kontot. 

Mer information finns i [Konfigurera roll anspråk som utfärdats i SAML-token](../develop/active-directory-enterprise-app-role-management.md)

> [!NOTE] 
> Ändra inte standard roll msiam_access när du lägger till app-roller. 

#### <a name="request"></a>Förfrågan

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Svar

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>Steg 3: Konfigurera anspråks mappning

### <a name="create-claims-mapping-policy"></a>Skapa anspråks mappnings princip

Förutom de grundläggande anspråken konfigurerar du följande anspråk för Azure AD för att generera i SAML-token:

| Anspråks namn | Källa  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userPrincipalName |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| roles | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userPrincipalName |

Mer information finns i [Anpassa anspråk som har avsänts i token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

#### <a name="request"></a>Förfrågan

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Svar

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Tilldela en princip för anspråk mappning till tjänstens huvud namn

#### <a name="request"></a>Förfrågan

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Svar

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>Steg 4: Konfigurera signerings certifikat

Att använda [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) -API: et skapar inte ett signerings certifikat som standard. Skapa ditt anpassade signerings certifikat och tilldela det till programmet. 

### <a name="create-a-custom-signing-certificate"></a>Skapa ett anpassat signerings certifikat

För att testa kan du använda följande PowerShell-kommando för att hämta ett självsignerat certifikat. Använd den bästa säkerhets metoden från företaget för att skapa ett signerings certifikat för produktion.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>Lägg till en anpassad signerings nyckel

Lägg till följande information till tjänstens huvud namn:

* Privat nyckel
* lösenordsinställning
* Offentlig nyckel 

Extrahera den privata och offentliga nyckeln Base64-kodad från PFX-filen. Om du vill veta mer om egenskaperna läser du [resurs typen autentiseringsuppgifter för inloggning](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0).

Kontrol lera att keyId för de autentiseringsuppgifter som används för "Sign" matchar keyId för passwordCredential.

Du kan generera `customkeyIdentifier` genom att hämta hashen för certifikatets tumavtryck.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Förfrågan

> [!NOTE]
> Värdet "Key" i egenskapen Key credentials förkortas för läsbarhet. Värdet är Base 64-kodat. Egenskapen `usage` är "Sign" för den privata nyckeln. Egenskapen `usage` är "verifiera" för den offentliga nyckeln.

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Svar

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>Aktivera den anpassade signerings nyckeln

Du måste ange `preferredTokenSigningKeyThumbprint` egenskapen till tumavtrycket för det certifikat som du vill att Azure AD ska använda för att signera SAML-svaret. 

#### <a name="request"></a>Förfrågan

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Svar

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>Steg 5: tilldela användare

### <a name="assign-users-and-groups-to-the-application"></a>Tilldela användare och grupper till programmet

Tilldela följande användare till tjänstens huvud namn och tilldela AWS_Role1. 

| Name  | ID  |
|---------|---------|
| Användar-ID (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Typ (principalType) | Användare |
| App Role-ID (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | 515f62cb-d18a-4dca-bec3-bb0bf31deeea |

#### <a name="request"></a>Förfrågan

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```
#### <a name="response"></a>Svar

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```

Mer information finns i resurs typen [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0) .

## <a name="step-6-configure-the-application-side"></a>Steg 6: Konfigurera program Sidan

### <a name="get-azure-ad-saml-metadata"></a>Hämta Azure AD SAML-metadata

Använd följande URL för att hämta Azure AD SAML-metadata för det specifika konfigurerade programmet. Metadata innehåller information som signerings certifikat, Azure AD-entityID och Azure AD-SingleSignOnService, bland annat.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>Nästa steg
- [Konfigurera användar etablering med hjälp av Microsoft Graph-API: er](../app-provisioning/application-provisioning-configure-api.md)
- [Använda rapporten AD FS program aktivitet för att migrera program till Azure AD](migrate-adfs-application-activity.md)

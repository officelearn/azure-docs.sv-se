---
title: Förstå Azure AD-schemat och anpassade uttryck
description: I den här artikeln beskrivs Azure AD-schemat, de attribut som etablerings agenten flödar och anpassade uttryck.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd013b44454cc0283ef84d6a978b15400eca8786
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022502"
---
# <a name="understand-the-azure-ad-schema"></a>Förstå Azure AD-schemat
Ett objekt i Azure Active Directory (Azure AD), som vilken katalog som helst, är en program mässig data konstruktion på hög nivå som representerar till exempel användare, grupper och kontakter. När du skapar en ny användare eller kontakt i Azure AD skapar du en ny instans av objektet. Dessa instanser kan särskiljas baserat på deras egenskaper.

Egenskaper i Azure AD är de element som ansvarar för att lagra information om en instans av ett objekt i Azure AD.

Azure AD-schemat definierar regler för vilka egenskaper som kan användas i en post, vilka typer av värden som dessa egenskaper kan ha och hur användarna kan interagera med dessa värden. 

Azure AD har två typer av egenskaper:
- **Inbyggda egenskaper**: egenskaper som är fördefinierade av Azure AD-schemat. Dessa egenskaper ger olika användnings områden och kanske inte är tillgängliga.
- **Katalog tillägg**: egenskaper som anges så att du kan anpassa Azure AD efter eget bruk. Om du till exempel har utökat din lokala Active Directory med ett visst attribut och vill flöda detta attribut kan du använda en av de anpassade egenskaperna som anges. 

## <a name="attributes-and-expressions"></a>Attribut och uttryck
När ett objekt, till exempel en användare, är etablerad i Azure AD, skapas en ny instans av användarobjektet. Den här genereringen innehåller egenskaperna för objektet, som även kallas attribut. Först har det nyligen skapade objektet attributen inställd på värden som bestäms av reglerna för synkronisering. Dessa attribut hålls sedan uppdaterade via moln etablerings agenten.

![Objekt etablering](media/concept-attributes/attribute1.png)

En användare kan till exempel vara en del av en marknadsförings avdelning. Deras Azure AD Department-attribut skapas först när de har etablerats och värdet är inställt på Marketing. Sex månader senare om de ändras till försäljning ändras deras lokala Active Directory avdelnings attribut till försäljning. Den här ändringen synkroniseras med Azure AD och visas i Azure AD-användarobjektet.

Det kan vara direkt att synkronisera attributet, där värdet i Azure AD är direkt inställt på värdet för det lokala attributet. Eller så kan ett programmerings uttryck hantera synkroniseringen. Ett programmerings uttryck krävs i fall där en logik eller en bestämning måste göras för att fylla i värdet.

Om du till exempel har fått e-postattributet "john.smith@contoso.com" och behövde ta bort "@contoso.com"-delen och bara flöda värdet "John. Smith" använder du något som liknar detta:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Exempel indata/utdata:** <br>

* **INDATA** (e-post) ”:john.smith@contoso.com”
* **Utdata**: "John. Svensson"

Mer information om hur du skriver anpassade uttryck och syntaxen finns i [skriva uttryck för mappningar av attribut i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

I följande tabell visas vanliga attribut och hur de synkroniseras till Azure AD.


|Lokalt Active Directory|Mappnings typ|Azure AD|
|-----|-----|-----|
|CN|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Uttryck|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|ProxyAddress|

## <a name="view-the-schema"></a>Visa schemat
Följ dessa steg om du vill visa schemat och kontrol lera det.

1.  Gå till [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
1.  Logga in med ditt globala administratörs konto.
1.  Till vänster väljer du **ändra behörigheter** och kontrollerar att **Directory. readwrite. all** har *samtyckt*.
1.  Kör frågan https://graph.microsoft.com/beta/serviceprincipals/? $filter = StartsWith (DisplayName, Active). Den här frågan returnerar en filtrerad lista över tjänstens huvud namn.
1.  Leta upp `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` och anteckna värdet för `"id"`.
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. Ersätt `{Service Principal id}` med ditt värde och kör frågan `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`.
1. Leta upp `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` och anteckna värdet för `"id"`.
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. Kör nu frågan `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`.
 
    Exempel: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Ersätt `{Service Principal Id}` och `{AD2ADD Provisioning Id}` med dina värden.

1. Den här frågan returnerar schemat.

   ![Returnerat schema](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Nästa steg

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)

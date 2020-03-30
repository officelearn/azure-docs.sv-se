---
title: Förstå Azure AD-schemat och anpassade uttryck
description: I den här artikeln beskrivs Azure AD-schemat, de attribut som etableringsagenten flödar och anpassade uttryck.
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
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299337"
---
# <a name="understand-the-azure-ad-schema"></a>Förstå Azure AD-schemat
Ett objekt i Azure Active Directory (Azure AD), som alla kataloger, är en programmatisk datakonstruktion på hög nivå som representerar till exempel användare, grupper och kontakter. När du skapar en ny användare eller kontakt i Azure AD skapar du en ny instans av objektet. Dessa instanser kan differentieras baserat på deras egenskaper.

Egenskaper i Azure AD är de element som ansvarar för att lagra information om en instans av ett objekt i Azure AD.

Azure AD-schemat definierar de regler för vilka egenskaper kan användas i en post, vilka typer av värden som dessa egenskaper kan ha och hur användare kan interagera med dessa värden. 

Azure AD har två typer av egenskaper:
- **Inbyggda egenskaper**: Egenskaper som är fördefinierade av Azure AD-schemat. Dessa egenskaper ger olika användningsområden och kanske eller kanske inte är tillgängliga.
- **Katalogtillägg**: Egenskaper som tillhandahålls så att du kan anpassa Azure AD för eget bruk. Om du till exempel har utökat den lokala Active Directory med ett visst attribut och vill flöda det attributet kan du använda en av de anpassade egenskaper som anges. 

## <a name="attributes-and-expressions"></a>Attribut och uttryck
När ett objekt som en användare etableras till Azure AD skapas en ny instans av användarobjektet. Den här skapelsen innehåller egenskaperna för det objektet, som också kallas attribut. Inledningsvis har det nyskapade objektet sina attribut inställda på värden som bestäms av synkroniseringsreglerna. Dessa attribut hålls sedan uppdaterade via molnetableringsagenten.

![Etablering av objekt](media/concept-attributes/attribute1.png)

En användare kan till exempel ingå i en marknadsavdelning. Deras Azure AD-avdelningsattribut skapas ursprungligen när de etableras och värdet anges till Marknadsföring. Sex månader senare om de ändras till Försäljning ändras attributet för den lokala Active Directory-avdelningen till Försäljning. Den här ändringen synkroniseras med Azure AD och återspeglas i deras Azure AD-användarobjekt.

Attributsynkronisering kan vara direkt, där värdet i Azure AD är direkt inställt på värdet för det lokala attributet. Ett programmatiskt uttryck kan också hantera synkroniseringen. Ett programmatiskt uttryck behövs i fall där viss logik eller en beslutsamhet måste göras för att fylla i värdet.

Om du till exempel hadejohn.smith@contoso.come-postattributet "@contoso.com" och behövde ta bort " " delen och flöda endast värdet "john.smith", skulle du använda ungefär så här:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Exempel på inmatning/utdata:** <br>

* **INPUT** (post):john.smith@contoso.com" "
* **UTGÅNG**: "john.smith"

Mer information om hur du skriver anpassade uttryck och syntaxen finns i [Skriva uttryck för attributmappningar i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

I följande tabell visas vanliga attribut och hur de synkroniseras med Azure AD.


|Lokalt Active Directory|Mappningstyp|Azure AD|
|-----|-----|-----|
|Cn|Direct|commonName (commonName)
|countryCode (landsKod)|Direct|countryCode (landsKod)|
|displayName|Direct|displayName|
|förnamn|Uttryck|förnamn|
|objectGUID|Direct|källaAnchorBinär|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress (proxyadress)|Direct|ProxyAdress|

## <a name="view-the-schema"></a>Visa schemat
> [!WARNING]
> Konfigurationen för molnetablering skapar ett huvudnamn för tjänsten. Tjänstens huvudnamn visas i Azure-portalen. Du bör inte ändra attributmappningarna med hjälp av tjänstens huvudupplevelse i Azure-portalen.  Detta stöds inte.

Så här visar du schemat och verifierar det.

1.  Gå till [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
1.  Logga in med ditt globala administratörskonto.
1.  Till vänster väljer du **ändra behörigheter** och se till att **Directory.ReadWrite.All** *medgivande*.
1.  Kör frågan `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`. Den här frågan returnerar en filtrerad lista över tjänstens huvudnamn.
1.  Leta `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` reda på `"id"`och notera värdet för .
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
1. Ersätt `{Service Principal id}` med ditt värde och `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`kör frågan .
1. Leta `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` reda på `"id"`och notera värdet för .
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
1. Kör nu `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`frågan .
 
    Exempel: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Ersätt `{Service Principal Id}` `{AD2ADD Provisioning Id}` och med dina värden.

1. Den här frågan returnerar schemat.

   ![Returnerat schema](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Nästa steg

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)

---
title: Använda Microsoft Graph API:er för att konfigurera etablering – Azure Active Directory | Microsoft-dokument
description: Behöver du ställa in etablering för flera instanser av ett program? Lär dig hur du sparar tid genom att använda Microsoft Graph API:er för att automatisera konfigurationen av automatisk etablering.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72217a565071f9531281af1862ba3681e353a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481474"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Konfigurera etablering med Hjälp av Microsoft Graph API:er

Azure-portalen är ett bekvämt sätt att konfigurera etablering för enskilda appar en i taget. Men om du skapar flera – eller till och med hundratals – instanser av ett program kan det vara enklare att automatisera skapandet och konfigurationen av appar med Microsoft Graph API:er. I den här artikeln beskrivs hur du automatiserar etableringskonfigurationen via API:er. Denna metod används ofta för program som [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**Översikt över steg för att använda Microsoft Graph API:er för att automatisera etableringskonfigurationen**


|Steg  |Information  |
|---------|---------|
|[Steg 1. Skapa galleriprogrammet](#step-1-create-the-gallery-application)     |Logga in på API-klienten <br> Hämta mallen galleriprogram <br> Skapa galleriprogrammet         |
|[Steg 2. Skapa etableringsjobb baserat på mall](#step-2-create-the-provisioning-job-based-on-the-template)     |Hämta mallen för etableringskopplingen <br> Skapa etableringsjobbet         |
|[Steg 3. Auktorisera åtkomst](#step-3-authorize-access)     |Testa anslutningen till programmet <br> Spara autentiseringsuppgifterna         |
|[Steg 4. Starta etableringsjobb](#step-4-start-the-provisioning-job)     |Starta jobbet         |
|[Steg 5. Övervaka etablering](#step-5-monitor-provisioning)     |Kontrollera status för etableringsjobbet <br> Hämta etableringsloggarna         |

> [!NOTE]
> Svarsobjekten som visas i den här artikeln kan förkortas för läsbarhet. Alla egenskaper returneras från ett verkligt anrop.

## <a name="step-1-create-the-gallery-application"></a>Steg 1: Skapa galleriet programmet

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Logga in på Microsoft Graph Explorer (rekommenderas), Brevbärare eller någon annan API-klient som du använder

1. Starta [Utforskaren för Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Välj knappen "Logga in med Microsoft" och logga in med Azure AD-globala administratörs- eller appadministratörsuppgifter.

    ![Logga in i diagram](./media/application-provisioning-configure-api/wd_export_02.png)

1. När du har loggat in visas information om användarkontot i den vänstra rutan.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Hämta mallidentifieraren för galleriprogram
Program i Azure AD-programgalleriet har var och en en [programmall](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) som beskriver metadata för det programmet. Med den här mallen kan du skapa en instans av programmet och tjänstens huvudnamn i din klient för hantering.

#### <a name="request"></a>*Förfrågan*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Svar*

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

### <a name="create-the-gallery-application"></a>Skapa galleriprogrammet

Använd mall-ID som hämtats för ditt program i det sista steget för att [skapa en instans](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) av programmets och tjänstens huvudnamn i din klientorganisation.

#### <a name="request"></a>*Förfrågan*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Svar*


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

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Steg 2: Skapa etableringsjobbet baserat på mallen

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Hämta mallen för etableringskopplingen

Program i galleriet som är aktiverade för etablering har mallar för att effektivisera konfigurationen. Använd begäran nedan för att [hämta mallen för etableringskonfigurationen](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http). Observera att du måste ange ID. ID:et refererar till föregående resurs, som i det här fallet är ServicePrincipal. 

#### <a name="request"></a>*Förfrågan*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Svar*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>Skapa etableringsjobbet
Om du vill aktivera etablering måste du först [skapa ett jobb](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http). Använd begäran nedan för att skapa ett etableringsjobb. Använd templateId från föregående steg när du anger mallen som ska användas för jobbet.

#### <a name="request"></a>*Förfrågan*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Svar*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>Steg 3: Auktorisera åtkomst

### <a name="test-the-connection-to-the-application"></a>Testa anslutningen till programmet

Testa anslutningen med tredjepartsprogrammet. Exemplet nedan är för ett program som kräver clientSecret och secretToken. Varje applikation har sina krav. Program använder ofta BaseAddress i stället för ClientSecret. Om du vill ta reda på vilka autentiseringsuppgifter din app kräver navigerar du till konfigurationssidan för etablering för ditt program och i utvecklarläge klickar du på testanslutning. Nätverkstrafiken visar de parametrar som används för autentiseringsuppgifter. Den fullständiga listan med autentiseringsuppgifter finns [här](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http). 

#### <a name="request"></a>*Förfrågan*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Svar*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Spara dina autentiseringsuppgifter

Konfigurera etablering kräver att ett förtroende upprättas mellan Azure AD och programmet. Auktorisera åtkomst till tredjepartsprogrammet. Exemplet nedan är för ett program som kräver clientSecret och secretToken. Varje applikation har sina krav. Granska [API-dokumentationen](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) för att se tillgängliga alternativ. 

#### <a name="request"></a>*Förfrågan*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Svar*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Steg 4: Starta etableringsjobbet
Nu när etableringsjobbet är konfigurerat använder du följande kommando för att [starta jobbet](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 


#### <a name="request"></a>*Förfrågan*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Svar*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Steg 5: Övervaka etablering

### <a name="monitor-the-provisioning-job-status"></a>Övervaka etableringsjobbstatus

Nu när etableringsjobbet körs använder du följande kommando för att spåra förloppet för den aktuella etableringscykeln samt statistik hittills, till exempel antalet användare och grupper som har skapats i målsystemet. 

#### <a name="request"></a>*Förfrågan*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Svar*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Övervaka etableringshändelser med hjälp av etableringsloggarna
Förutom att övervaka status för etableringsjobbet kan du använda [etableringsloggarna](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) för att fråga efter alla händelser som inträffar (t.ex. fråga efter en viss användare och avgöra om de har etablerats).

#### <a name="request"></a>*Förfrågan*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Svar*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>Relaterade artiklar

- [Granska dokumentationen för synkroniseringen av Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrera en anpassad SCIM-app med Azure AD](use-scim-to-provision-users-and-groups.md)

---
title: Tolka Azure Active Directory-granskningsloggschemat i Azure Monitor | Microsoft-dokument
description: Beskriv Azure AD-granskningsloggschemat för användning i Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9f58b213e50a021651f35112a48d8f74ae59571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68987942"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Tolka Azure AD-granskningsloggar schema i Azure Monitor (förhandsversion)

I den här artikeln beskrivs granskningsloggschemat för Azure Active Directory (Azure AD) i Azure Monitor. Varje enskild loggpost lagras som text och formateras som en JSON-blob, vilket visas i följande två exempel: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Fält- och egenskapsbeskrivningar

| Fältnamn | Beskrivning |
|------------|-------------|
| time       | Datum och tid (UTC). |
| operationName | Namnet på operationen. |
| operationVersion | REST API-versionen som begärs av klienten. |
| category | För närvarande är *Granskning* det enda värde som stöds. |
| tenantId (hyresgäst) | Klient-GUID som är associerad med loggarna. |
| resultType | Resultatet av operationen. Resultatet kan bli *framgång* eller *misslyckande*. |
| resultSignature |  Det här fältet är avmappat och du kan ignorera det på ett säkert sätt. | 
| resultDescription | Ytterligare en beskrivning av resultatet, om tillgängligt. | 
| durationMs |  Det här fältet är avmappat och du kan ignorera det på ett säkert sätt. |
| callerIpAddress | IP-adressen för klienten som gjorde begäran. | 
| correlationId | Ett valfritt GUID som skickas av klienten. Det kan hjälpa korrelera åtgärder på klientsidan med åtgärder på serversidan och det är användbart när du spårar loggar som sträcker sig över tjänster. |
| identity | Identiteten från token som presenterades när du gjorde begäran. Identiteten kan vara ett användarkonto, ett systemkonto eller tjänsthuvudnamn. |
| nivå | Meddelandetypen. För granskningsloggar är nivån alltid *informativ*. |
| location | Platsen för datacentret. |
| properties | Visar en lista över egenskaper som stöds och som är relaterade till en granskningslogg. Mer information finns i nästa tabell. | 

<br>

| Egenskapsnamn | Beskrivning |
|---------------|-------------|
| AuditEventKategori | Typ av granskningshändelse. Det kan vara *Användarhantering,* *Programhantering*eller annan typ.|
| Identitetstyp | Typen kan vara *Program* eller *Användare*. |
| Åtgärdstyp | Typen kan vara *Lägg till*, *Uppdatera*, *Ta bort*. eller *Annat*. |
| Målresurstyp | Anger den målresurstyp som åtgärden utfördes på. Typen kan vara *Program,* *Användare*, *Roll*, *Princip* | 
| Namn på målresurs | Namnet på målresursen. Det kan vara ett programnamn, ett rollnamn, ett användarnamn eller ett huvudnamn för tjänsten. |
| ytterligareMål | Visar en lista över ytterligare egenskaper för specifika åtgärder. För en uppdateringsåtgärd visas till exempel de gamla värdena och de nya värdena under *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Nästa steg

* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Azure diagnostik loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Vanliga frågor och kända problem](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
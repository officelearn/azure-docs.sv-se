---
title: Tolka Azure Active Directory Granska loggen schemat i Azure Monitor (förhandsversion) | Microsoft Docs
description: Beskriv Azure AD audit log-schema för användning i Azure Monitor (förhandsversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240014"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>Tolka Azure Active Directory granska loggarna schemat i Azure Monitor (förhandsversion)

Den här artikeln beskrivs Azure AD audit log schemat i Azure Monitor. Varje enskild loggpost lagras som text, formaterad som en JSON-blob som visas i de två exemplen nedan. 

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

| Fältnamn | Beskrivning |
|------------|-------------|
| time       | Datum och tid (UTC) |
| operationName | Åtgärdens namn |
| operationVersion | REST API-version som begärs av klienten |
| category | För närvarande *Audit* är det enda värde som stöds |
| TenantId | Klient-Guid som är associerade med loggarna |
| resultType | Resultatet av åtgärden, kan vara *lyckades* eller *fel* |
| resultSignature |  Detta är omappade och du kan ignorera det här fältet. | 
| resultDescription | Ytterligare beskrivning av resultatet, där det är tillgängligt | 
| durationMs |  Detta är omappade och du kan ignorera det här fältet. |
| callerIpAddress | IP-adressen för klienten som gjorde begäran | 
| correlationId | Valfritt Guid som skickas av klienten. Detta kan hjälpa att korrelera klientsidan åtgärder med serversidan operations och är användbart när spårningsloggarna som sträcker sig över olika tjänster. |
| identity | Identiteten från den token som angavs när den skickar förfrågan. Kan vara ett användarkonto, system-kontot eller tjänstens huvudnamn. |
| nivå | Typ av meddelande. För granskningsloggar, är detta alltid *information* |
| location | Datacenterplats |
| properties | Visar en lista över egenskaper som stöds relaterade till en granskningslogg. Mer information finns i tabellen nedan. | 


| Egenskapsnamn | Beskrivning |
|---------------|-------------|
| AuditEventCategory | Typ av händelse. Kan vara *Användarhantering*, *programhantering* osv.|
| Identitetstypen | *Programmet* eller *användare* |
| Åtgärdstyp | Kan vara *Lägg till*, *uppdatering*, *ta bort* eller *andra* |
| Målresurstyp | Anger resource måltypen som åtgärden utfördes på. Kan vara *programmet*, *användaren*, *rollen*, *princip* | 
| Mål-resursnamn | Namnet på målresursen. Detta kan exempelvis vara ett programnamn, ett rollnamn, ett huvudsakligt användarnamn eller tjänstens huvudnamn |
| additionalTargets | Visar en lista över ytterligare egenskaper för specifika åtgärder. Till exempel för en uppdateringsåtgärd gamla värdena och de nya värdena visas under *targetUpdatedProperties* | 

## <a name="next-steps"></a>Nästa steg

* [Tolka inloggning loggar schemat i Azure monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Läs mer om Azure-diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Vanliga frågor och kända problem](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)
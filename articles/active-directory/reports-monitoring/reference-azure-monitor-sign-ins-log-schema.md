---
title: Azure Active Directory-inloggning log-schemat i Azure Monitor | Microsoft Docs
description: Beskriv de Azure AD-inloggningen log-schemat för användning i Azure Monitor
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
ms.openlocfilehash: a8ac6c56dca100ea9836158f46881c4eb12213e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285203"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Tolka Azure AD-inloggningen loggar schemat i Azure Monitor

Den här artikeln beskrivs Azure Active Directory (Azure AD)-inloggningen log schemat i Azure Monitor. De flesta av den information som rör inloggningar tillhandahålls enligt den *egenskaper* attributet för den `records` objekt.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Fältbeskrivningar

| Fältnamn | Beskrivning |
|------------|-------------|
| Tid | Datum och tid i UTC. |
| ResourceId | Det här värdet är omappade och du kan ignorera det här fältet.  |
| OperationName | För inloggningar, det här värdet är alltid *inloggningsaktivitet*. |
| OperationVersion | REST API-versionen som begärs av klienten. |
| Category | För inloggningar, det här värdet är alltid *inloggning från*. | 
| TenantId | Klient-GUID som är associerat med loggarna. |
| resultType | Resultatet av åtgärden logga in kan vara *lyckades* eller *fel*. | 
| ResultSignature | Innehåller felkoden för inloggning igen. |
| ResultDescription | Innehåller felbeskrivningen för logga in igen. |
| . durationMs |  Det här värdet är omappade och du kan ignorera det här fältet.|
| CallerIpAddress | IP-adressen för klienten som gjorde begäran. | 
| CorrelationId | Valfritt GUID som skickades av klienten. Det här värdet kan hjälpa att korrelera klientsidan åtgärder med serversidan operations och det är användbart när du följer upp loggar som sträcker sig över tjänster. |
| Identitet | Identiteten från den token som angavs när du gjorde begäran. Det kan vara ett användarkonto, system-kontot eller tjänstens huvudnamn. |
| Nivå | Tillhandahåller typ av meddelande. För granskning, är det alltid *information*. |
| Location | Anger platsen för inloggningsaktivitet. |
| Egenskaper | Visar en lista över alla egenskaper som är associerade med inloggningar. Mer information finns i [Microsoft Graph API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Det här schemat använder samma attributnamn som resursen inloggning för läsbarhet.

## <a name="next-steps"></a>Nästa steg

* [Tolka schema för spårningsloggar i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Läs mer om Azure diagnostikloggar](../../azure-monitor/platform/diagnostic-logs-overview.md)
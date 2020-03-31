---
title: Inloggningsloggschema i Azure Monitor | Microsoft-dokument
description: Beskriv Azure AD-inloggningsloggschemat för användning i Azure Monitor
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
ms.openlocfilehash: 5525f2f8ab4ef83ba9c3aeeff945bc9d875600d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748661"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Tolka Azure AD-inloggningsloggschemat i Azure Monitor

I den här artikeln beskrivs inloggningsloggschemat för Azure Active Directory (Azure AD) i Azure Monitor. Det mesta av informationen som är relaterad till *Properties* inloggningar finns `records` under egenskapsattributet Egenskaper för objektet.


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
| Tid | Datum och tid, i UTC. |
| ResourceId | Det här värdet är avmappat och du kan ignorera det här fältet på ett säkert sätt.  |
| OperationName | För inloggningar är det här värdet alltid *inloggningsaktivitet*. |
| OperationVersion | REST API-versionen som begärs av klienten. |
| Kategori | För inloggningar är det här värdet alltid *SignIn*. | 
| TenantId | Klient-GUID som är associerad med loggarna. |
| Resultattyp | Resultatet av inloggningen kan bli *lyckades* eller *misslyckas*. | 
| ResultSignature | Innehåller eventuell felkod för inloggningsåtgärden. |
| ResultatBeskrivning | Innehåller felbeskrivningen för inloggningsåtgärden. |
| riskDetail | riskDetail | Anger "orsaken" bakom ett visst tillstånd för en riskfylld användare, inloggning eller en riskidentifiering. De möjliga värdena `adminGeneratedTemporaryPassword` `userPerformedSecuredPasswordChange`är: `adminConfirmedSigninSafe` `none`, `userPassedMFADrivenByRiskBasedPolicy` `adminDismissedAllRiskForUser`, `adminConfirmedSigninCompromised` `unknownFutureValue`, `userPerformedSecuredPasswordReset`, `aiConfirmedSigninSafe`, , , , , . Värdet `none` innebär att ingen åtgärd har utförts på användaren eller inloggningen hittills. <br>**Anm.:** Information om den här egenskapen kräver en Azure AD Premium P2-licens. Andra licenser returnerar `hidden`värdet . |
| riskEventTypes | riskEventTypes | Riskidentifieringstyper som är associerade med inloggningen. De möjliga värdena `anonymizedIPAddress` `maliciousIPAddress`är: `malwareInfectedIPAddress` `unlikelyTravel`, `leakedCredentials` `investigationsThreatIntelligence`, `generic`, `unknownFutureValue` `unfamiliarFeatures`, `suspiciousIPAddress`, , , , och . |
| riskLevelAggregated | riskNivå | Aggregerad risknivå. De möjliga värdena `low` `medium`är: `hidden` `none`, `unknownFutureValue`, , `high`, och . Värdet `hidden` innebär att användaren eller inloggningen inte var aktiverad för Azure AD Identity Protection. **Anm.:** Information om den här egenskapen är endast tillgänglig för Azure AD Premium P2-kunder. Alla andra kunder `hidden`returneras. |
| riskLevelUnderingSignIn | riskNivå | Risknivå under inloggning. De möjliga värdena `low` `medium`är: `hidden` `none`, `unknownFutureValue`, , `high`, och . Värdet `hidden` innebär att användaren eller inloggningen inte var aktiverad för Azure AD Identity Protection. **Anm.:** Information om den här egenskapen är endast tillgänglig för Azure AD Premium P2-kunder. Alla andra kunder `hidden`returneras. |
| riskState (riskState) | riskState (riskState) | Rapporterar status för den riskfyllda användaren, inloggningen eller en riskidentifiering. De möjliga värdena `confirmedSafe` `remediated`är: `none` `confirmedCompromised`, `unknownFutureValue`, , `dismissed`, `atRisk`, , . |
| VaraktighetMämmer |  Det här värdet är avmappat och du kan ignorera det här fältet på ett säkert sätt. |
| UppringarenSadress | IP-adressen för klienten som gjorde begäran. | 
| CorrelationId | Det valfria GUID som skickas av klienten. Det här värdet kan hjälpa till att korrelera åtgärder på klientsidan med åtgärder på serversidan, och det är användbart när du spårar loggar som sträcker sig över tjänster. |
| Identitet | Identiteten från token som presenterades när du gjorde begäran. Det kan vara ett användarkonto, ett systemkonto eller tjänsthuvudnamn. |
| Nivå | Innehåller typen av meddelande. För granskning är det alltid *informativt*. |
| Location | Anger platsen för inloggningsaktiviteten. |
| Egenskaper | Visar en lista över alla egenskaper som är associerade med inloggningar. Mer information finns i [Microsoft Graph API Reference](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). I det här schemat används samma attributnamn som inloggningsresursen för läsbarhet.

## <a name="next-steps"></a>Nästa steg

* [Tolka granskningsloggschema i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Läs mer om Diagnostikloggar i Azure](../../azure-monitor/platform/platform-logs-overview.md)

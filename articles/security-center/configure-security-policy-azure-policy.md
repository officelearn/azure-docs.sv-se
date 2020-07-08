---
title: Skapa och redigera Azure Policy säkerhets principer med hjälp av REST API
description: Läs mer om hur du Azure Policy princip hantering via en REST API.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77430949"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Konfigurera en säkerhets princip i Azure Policy med hjälp av REST API

Som en del av den interna integreringen med Azure Policy kan du Azure Security Center dra nytta av Azure Policy REST API för att skapa princip tilldelningar. Följande anvisningar beskriver hur du skapar princip tilldelningar, samt anpassningar av befintliga tilldelningar. 

Viktiga begrepp i Azure Policy: 

- En **princip definition** är en regel 

- Ett **initiativ** är en samling princip definitioner (regler) 

- En **tilldelning** är ett program för ett initiativ eller en princip för en specifik omfattning (hanterings grupp, prenumeration osv.) 

Security Center har ett inbyggt initiativ som innehåller alla säkerhets principer. Om du vill utvärdera Security Centers principer på dina Azure-resurser, bör du skapa en tilldelning i hanterings gruppen eller prenumerationen som du vill utvärdera.

Det inbyggda initiativet har alla Security Centers principer som är aktiverade som standard. Du kan välja att inaktivera vissa principer från det inbyggda initiativet. Om du till exempel vill använda alla Security Centers principer utom **brand vägg för webbaserade program**, ändrar du värdet för principens gällande parameter till **inaktive rad**. 

## <a name="api-examples"></a>API-exempel

Ersätt följande variabler i följande exempel:

- **{scope}** ange namnet på hanterings gruppen eller prenumerationen som du tillämpar principen på.
- **{policyAssignmentName}** ange [namnet på den relevanta princip tilldelningen](#policy-names).
- **{Name}** ange ditt namn eller namnet på administratören som godkände princip ändringen.

Det här exemplet visar hur du tilldelar det inbyggda Security Center initiativ för en prenumeration eller hanterings grupp
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Det här exemplet visar hur du tilldelar det inbyggda Security Center initiativ för en prenumeration, med följande principer inaktiverade: 

- System uppdateringar ("systemUpdatesMonitoringEffect") 

- Säkerhetskonfigurationer ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Det här exemplet visar hur du tar bort en tilldelning:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Princip namn referens<a name="policy-names"></a>

|Princip namn i Security Center|Princip namn som visas i Azure Policy |Parameter namn för princip påverkan|
|----|----|----|
|SQL-kryptering |Övervaka okrypterad SQL Database i Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL-granskning |Övervaka en SQL-databas som inte har granskats i Azure Security Center |sqlAuditingMonitoringEffect|
|Systemuppdateringar |Övervaka system uppdateringar som saknas i Azure Security Center |systemUpdatesMonitoringEffect|
|Lagringskryptering |Granska avsaknad av blobkryptering för lagringskonton |storageEncryptionMonitoringEffect|
|Åtkomst till JIT-nätverk |Övervaka möjliga nätverks åtkomst för just-in-Time (JIT) i Azure Security Center |jitNetworkAccessMonitoringEffect |
|Anpassningsbara programkontroller |Övervaka möjlig app-vit listning i Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Nätverkssäkerhetsgrupper |Övervaka tillåtad nätverks åtkomst i Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Säkerhetskonfigurationer |Övervaka OS-sårbarheter i Azure Security Center |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Övervaka saknade Endpoint Protection i Azure Security Center |endpointProtectionMonitoringEffect |
|Diskkryptering |Övervaka okrypterade VM-diskar i Azure Security Center |diskEncryptionMonitoringEffect|
|Sårbarhetsbedömning |Övervaka säkerhets risker i virtuella datorer i Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Brandvägg för webbaserade program |Övervaka oskyddat webb program i Azure Security Center |webApplicationFirewallMonitoringEffect |
|Nästa generations brandvägg |Övervaka oskyddade nätverks slut punkter i Azure Security Center| |


## <a name="next-steps"></a>Nästa steg

Information om annat relaterat material finns i följande artiklar: 

- [Anpassade säkerhets principer](custom-security-policies.md)
- [Översikt över säkerhets princip](tutorial-security-policy.md)
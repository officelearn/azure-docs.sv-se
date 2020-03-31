---
title: Skapa och redigera Azure Policy-säkerhetsprinciper med REST API
description: Lär dig mer om Azure Policy principhantering via ett REST API.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430949"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Konfigurera en säkerhetsprinciper i Azure-principen med REST API

Som en del av den inbyggda integreringen med Azure Policy kan du med Azure Security Center dra nytta av Azure Policy REST API för att skapa principtilldelningar. Följande instruktioner går igenom skapandet av principtilldelningar samt anpassning av befintliga tilldelningar. 

Viktiga begrepp i Azure Policy: 

- En **principdefinition** är en regel 

- Ett **initiativ** är en samling politiska definitioner (regler) 

- En **tilldelning** är en tillämpning av ett initiativ eller en princip till ett visst omfång (ledningsgrupp, prenumeration, etc.) 

Security Center har ett inbyggt initiativ som innehåller alla dess säkerhetsprinciper. Om du vill bedöma Security Centers principer på dina Azure-resurser bör du skapa en tilldelning för hanteringsgruppen eller prenumerationen som du vill utvärdera.

Det inbyggda initiativet har alla Security Centers principer aktiverade som standard. Du kan välja att inaktivera vissa principer från det inbyggda initiativet. Om du till exempel vill använda alla Security Centers principer utom **brandvägg för webbprogram**ändrar du värdet för principens effektparameter till **Inaktiverad**. 

## <a name="api-examples"></a>API-exempel

I följande exempel ersätter du dessa variabler:

- **{scope}** anger namnet på den hanteringsgrupp eller prenumeration som du tillämpar principen på.
- **{policyAssignmentName}** anger [namnet på den relevanta principtilldelningen](#policy-names).
- **{name}** ange ditt namn eller namnet på administratören som godkände principändringen.

I det här exemplet visas hur du tilldelar det inbyggda Security Center-initiativet till en prenumeration eller hanteringsgrupp
 
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

I det här exemplet visas hur du tilldelar det inbyggda Security Center-initiativet till en prenumeration, med följande principer inaktiverade: 

- Systemuppdateringar ("systemUpdatesMonitoringEffect") 

- Säkerhetskonfigurationer ("systemKonfigurationerÖvervakaeffekt") 

- Slutpunktsskydd ("endpointProtectionMonitoringEffect") 

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
I det här exemplet visas hur du tar bort en tilldelning:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Referens för principnamn<a name="policy-names"></a>

|Principnamn i Security Center|Principnamn som visas i Azure Policy |Parameternamn för principeffekt|
|----|----|----|
|SQL-kryptering |Övervaka okrypterad SQL-databas i Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL-granskning |Övervaka oreviderad SQL-databas i Azure Security Center |sqlAuditingÖvervakaEffekt|
|Systemuppdateringar |Övervaka saknade systemuppdateringar i Azure Security Center |systemUppdatesÖvervakaEffekt|
|Lagringskryptering |Granska avsaknad av blobkryptering för lagringskonton |lagringFörflagningÖvervakaNdeEffekt|
|ÅTKOMST TILL JIT-nätverk |Övervaka eventuell åtkomst till nätverk just-in-time (JIT) i Azure Security Center |jitNetworkAccessMonitoringEffect |
|Anpassningsbara programkontroller |Övervaka möjlig app vitlistning i Azure Security Center |adaptivTillämningKontrollerÖvervakaEffektEffekt|
|Nätverkssäkerhetsgrupper |Övervaka tillåtande nätverksåtkomst i Azure Security Center |nätverkSäkerhetsgrupperÖvervakaEffektEffect| 
|Säkerhetskonfigurationer |Övervaka os-sårbarheter i Azure Security Center |systemKonfigurationerÖvervakaEffekt| 
|Slutpunktsskydd |Övervaka saknade slutpunktsskydd i Azure Security Center |slutpunktSkyddÖvervakaNdeEffekt |
|Diskkryptering |Övervaka okrypterade VM-diskar i Azure Security Center |diskEncryptionÖvervakaEffektEffect|
|Sårbarhetsbedömning |Övervaka vm-sårbarheter i Azure Security Center |sårbarhetAssessmentMonitoringEffect|
|Brandvägg för webbaserade program |Övervaka oskyddade webbprogram i Azure Security Center |webApplicationFirewallMonitoringEffect |
|Nästa generations brandvägg |Övervaka oskyddade nätverksslutpunkter i Azure Security Center| |


## <a name="next-steps"></a>Nästa steg

För annat relaterat material, se följande artiklar: 

- [Anpassade säkerhetsprinciper](custom-security-policies.md)
- [Översikt över säkerhetsprincip](tutorial-security-policy.md)
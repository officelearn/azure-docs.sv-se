---
title: Redigera säkerhetsprinciper i Azure Policy | Microsoft Docs
description: Redigera säkerhetsprinciper i Azure Policy.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: d6cc216f71efcd3b3973cd37349dd5145237f02f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839338"
---
# <a name="edit-security-policies-in-azure-policy"></a>Redigera säkerhetsprinciper i Azure Policy
Security Center kan du visa status för säkerhetsprinciper och hur de används för dina arbetsbelastningar. Azure Security Center tilldelar automatiskt dess [inbyggda säkerhetsprinciper](security-center-policy-definitions.md) på varje prenumeration som har publicerats. Du kan konfigurera dem i [Azure Policy](../azure-policy/azure-policy-introduction.md), eller med hjälp av REST-API, som även möjligt att ange principer över hanteringsgrupper och över flera prenumerationer. Mer information finns i [Integrera Security Center-säkerhetsprinciper med Azure Policy](security-center-azure-policy.md). I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera en säkerhetsprincip med hjälp av REST-API
> * Utvärdera säkerheten för dina resurser

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom funktionerna i den här självstudien måste du ha standardnivån i Security Center. Du kan prova Security Center Standard utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). Snabbstarten för att [registrera Azure-prenumerationen till Security Center Standard](security-center-get-started.md) vägleder dig genom uppgraderingen till Standard.

## <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurera en säkerhetsprincip med hjälp av REST-API

Som en del av den interna integreringen med Azure Policy kan Azure Security Center du dra nytta Azure Policy REST API för att skapa principtilldelningar. Följande instruktioner vägleder dig genom skapandet av principtilldelningar, samt anpassning av befintliga tilldelningar. 

Viktiga begrepp i Azure Policy: 

- En **principdefinition** är en regel 

- En **initiativ** är en samling principdefinitioner (regler) 

- En **tilldelning** är ett program i ett initiativ eller en princip för ett visst område (hanteringsgruppen, prenumeration osv.) 

Security Center har en inbyggd initiativ som innehåller alla dess säkerhetspolicys. För att kunna utvärdera Security Center-principerna på dina Azure-resurser, bör du skapa en tilldelning på hanteringsgruppen eller prenumerationen som du vill utvärdera.  

Inbyggda initiativ har alla Security Center-principerna är aktiverade som standard. Du kan välja att inaktivera vissa principer från det inbyggda initiativet, till exempel kan du använda alla Security Center principer utom **Brandvägg för webbaserade program**, genom att ändra värdet för parametern för principens effekt till  **Inaktiverad**. 

### <a name="api-examples"></a>API-exempel

Ersätt dessa variabler i följande exempel:

- **{omfång}**  anger du namnet på hanteringsgruppen eller prenumerationen du tillämpar principen till.
- **{poicyAssignmentName}**  ange den [namn på relevanta principtilldelningen](#policy-names).
- **{name}**  ange ditt namn eller namnet på den administratör som godkänts principändringen.

Det här exemplet visar hur du tilldelar initiativet inbyggda Security Center på en prenumeration eller hanteringsgrupp
 
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

Det här exemplet visar hur du tilldelar initiativet inbyggda Security Center på en prenumeration med följande principer inaktiverad: 

- Systemuppdateringar (”systemUpdatesMonitoringEffect”) 

- Säkerhetskonfigurationer (”systemConfigurationsMonitoringEffect”) 

- Slutpunktsskydd (”endpointProtectionMonitoringEffect”) 

 
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

Det här exemplet visar hur du tar bort en tilldelning:

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


## Referens för namn <a name="policy-names"></a>

|Principnamnet i Security Center|Principnamn som visas i Azure Policy |Parameternamn för principen effekt|
|----|----|----|
|SQL-kryptering |Övervaka okrypterade SQL-databas i Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL-granskning |Övervaka ogranskad SQL-databas i Azure Security Center |sqlAuditingMonitoringEffect|
|Systemuppdateringar |Övervaka systemuppdateringar som saknas i Azure Security Center |systemUpdatesMonitoringEffect|
|Lagringskryptering |Granska blobbkryptering som saknas för lagringskonton |storageEncryptionMonitoringEffect|
|JIT-nätverksåtkomst |Övervaka möjliga precis i tid JIT-nätverksåtkomst i Azure Security Center |jitNetworkAccessMonitoringEffect |
|Anpassningsbara programkontroller |Övervaka möjliga tillåtna appar i Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Nätverkssäkerhetsgrupper |Övervaka tillåten åtkomst till nätverk i Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Säkerhetskonfigurationer |Övervaka OS-säkerhetsproblem i Azure Security Center |systemConfigurationsMonitoringEffect| 
|Slutpunktsskydd |Övervaka saknad Endpoint Protection i Azure Security Center |endpointProtectionMonitoringEffect |
|Diskkryptering |Övervaka okrypterade Virtuella Datordiskar i Azure Security Center |diskEncryptionMonitoringEffect|
|Sårbarhetsbedömning |Övervaka säkerhetsrisker i virtuell dator i Azure Security Center |vulnerabilityAssesmentMonitoringEffect|
|Brandvägg för webbaserade program |Övervaka oskyddat webbprogram i Azure Security Center |webApplicationFirewallMonitoringEffect |
|Nästa generations brandvägg |Övervaka oskyddade nätverksslutpunkter i Azure Security Center| |





## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att redigera säkerhetsprinciper i Azure Policy. I följande artiklar kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md): Här får du lära dig att planera och vad du behöver tänka på när det gäller design när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Få synlighet i hela klientorganisationen för Azure Security Center](security-center-management-groups.md): Lär dig hur du konfigurerar hanteringsgrupper för Azure Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

Mer information om Azure Policy finns i [Vad är Azure Policy?](../azure-policy/azure-policy-introduction.md)

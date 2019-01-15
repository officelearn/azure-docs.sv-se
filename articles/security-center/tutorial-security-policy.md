---
title: Arbeta med säkerhetsprinciper | Microsoft Docs
description: Den här artikeln beskrivs hur du arbetar med säkerhetsprinciper i Azure Security Center.
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
ms.date: 1/4/2019
ms.author: rkarlin
ms.openlocfilehash: 52af6051b4534ba65b4822205cb5395a59ef9d6a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259972"
---
# <a name="working-with-security-policies"></a>Arbeta med säkerhetsprinciper

Den här artikeln förklarar hur säkerhetsprinciper har konfigurerats och hur du visar dem i Security Center. Azure Security Center tilldelar automatiskt dess [inbyggda säkerhetsprinciper](security-center-policy-definitions.md) på varje prenumeration som har publicerats. Du kan konfigurera dem i [Azure Policy](../azure-policy/azure-policy-introduction.md), som också kan du ange principer över hanteringsgrupper och över flera prenumerationer.

Anvisningar om hur du anger principer med hjälp av PowerShell finns i [snabbstarten: Skapa en principtilldelning som identifierar icke-kompatibla resurser med hjälp av Azure RM PowerShell-modulen](../azure-policy/assign-policy-definition-ps.md).

>[!NOTE]
> Security Center igång dess integrering med Azure Policy. Befintliga kunder kommer att migreras automatiskt till det nya inbyggda initiativet i Azure Policy, i stället för föregående säkerhetsprinciper i Security Center. Den här ändringen påverkar inte dina resurser eller miljö utom förekomst av det nya initiativet i Azure Policy.

## <a name="what-are-security-policies"></a>Vad är säkerhetsprinciper?
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. Du kan definiera principer för dina Azure-prenumerationer och anpassa dem till din typ av arbetsbelastning eller känslighet för dina data i Azure Policy. Program som använder reglerade data, till exempel personligt identifierbar information kan till exempel kräva en högre säkerhetsnivå än andra arbetsbelastningar. Om du vill ange en princip för prenumerationer eller hanteringsgrupper, ställa in dem [Azure Policy](../azure-policy/azure-policy-introduction.md).

Dina säkerhetsprinciper innehåller säkerhetsrekommendationer som du får i Azure Security Center. Du kan övervaka efterlevnad med dem för att hjälpa dig att upptäcka potentiella säkerhetsrisker och avhjälpa hot. Mer information om hur du avgör vilket alternativ som passar dig finns i listan över [inbyggda säkerhetsprinciper](security-center-policy-definitions.md).

När du aktiverar Security Center, visas säkerhetsprincipen som är inbyggd i Security Center i Azure Policy som en inbyggd initiativ under kategorin Security Center. Den inbyggda intitiative tilldelas automatiskt till alla Security Center registrerad prenumerationer (kostnadsfri eller Standard-nivå). Inbyggda initiativet innehåller endast granskningsprinciper. 


### <a name="management-groups"></a>Hanteringsgrupper
Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsprinciper på hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de principer som tillämpas på hanteringsgruppen. Varje katalog tilldelas en hanteringsgrupp på översta nivån som kallas rothanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med hjälp av rothanteringsgruppen kan globala principer och RBAC-tilldelningar tillämpas på katalognivå. Om du vill konfigurera hanteringsgrupper för användning med Azure Security Center, följer du anvisningarna i [klienttäckande insyn för Azure Security Center](security-center-management-groups.md).

> [!NOTE]
> Det är viktigt att du är införstådd med hierarkin för hanteringsgrupper och prenumerationer. Du kan läsa mer om hanteringsgrupper, rothantering och hanteringsgruppåtkomst i artikeln om att [organisera dina resurser med Azures hanteringsgrupper](../governance/management-groups/index.md#root-management-group-for-each-directory).
>

## <a name="how-security-policies-work"></a>Hur fungerar säkerhetsprinciper?
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Du kan redigera principer i Azure-principen för att göra följande:
- Skapa nya principdefinitioner.
- Tilldela principer över hanteringsgrupper och prenumerationer som motsvarar en hel organisation eller affärsenhet inom organisationen.
- Övervaka principefterlevnad.

Om du vill ha mer information om Azure Policy kan du läsa [Create and manage policies to enforce compliance](../azure-policy/create-manage-policy.md) (Skapa och hantera principer för att genomdriva efterlevnad).

En Azure-princip består av följande komponenter:

- En **princip** är en regel.
- En **initiativ** är en samling av principer.
- En **tilldelning** är program till ett initiativ eller en princip för ett visst område (hanteringsgruppen, prenumeration eller resursgrupp).

## <a name="view-security-policies"></a>Visa säkerhetsprinciper

Visa dina säkerhetsprinciper i Security Center:

1. I den **Security Center** instrumentpanelen, väljer **säkerhetsprincip**.

    ![Fönstret för principhantering](./media/security-center-policies/security-center-policy-mgt.png)

  I den **principhantering** skärmen, du kan se antalet hanteringsgrupper, prenumerationer och arbetsytor, samt dina hanteringsgruppsstruktur.

  > [!NOTE]
  > - Instrumentpanelen i Security Center kan indikera ett högre antal prenumerationer under **prenumerationstäckning** än antalet prenumerationer som visas under **principhantering**. Prenumerationstäckningen visar antalet prenumerationer av typen Standard, Kostnadsfri och Omfattas inte. ”Inte motsvarar”-prenumerationer har inte Security Center aktiverat och visas inte **principhantering**.
  >

  Tabellens kolumner:

 - **Principinitiativsuppgift** – Security Center [inbyggda principer](security-center-policy-definitions.md) och initiativ som har tilldelats en prenumerations- eller grupp.
 - **Täckning** – identifierar prisnivån kostnadsfri eller Standard som hanteringsgruppen, prenumeration eller arbetsyta körs på.  Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
 - **Inställningar för** – prenumerationer har länken **redigera inställningar för**. Att välja **redigera inställningar för** låter dig uppdatera ditt [Security Center-inställningar](security-center-policies-overview.md) för varje prenumerations- eller grupp.
 - **Säker poäng** – [säker poäng](security-center-secure-score.md) ger ett mått på hur säker din arbetsbelastning säkerhetsposition och hjälper dig att prioritera rekommendationer för förbättring.

2. Välj den prenumeration eller hanteringsgrupp grupp vars principer som du vill visa.

  - Den **säkerhetsprincip** skärmen återspeglar den åtgärd som de principer som tilldelats för prenumerations- eller gruppen som du har valt.
  - Överst på sidan använder du länkarna för att öppna varje princip **tilldelning** som gäller för prenumerations- eller gruppen. Du kan använda länkarna för att komma åt tilldelningen och redigera eller inaktivera principen. Om du ser att en viss principtilldelning effektivt nekas slutpunktsskydd kan använda du länken till åtkomst till principen och redigera eller inaktivera den.
  - I listan med principer ser du en effektiv tillämpning av principen på din prenumeration eller hanteringsgrupp. Det innebär att inställningarna för varje princip som gäller för omfånget beaktas och du får det kumulativa resultatet av vilka åtgärder som vidtas av principen. Om principen är inaktiverad i en tilldelning, men i en annan som den är inställd på AuditIfNotExist, gäller den ackumulerade effekten AuditIfNotExist. Mer aktiva effekten har alltid företräde.
  - De principer börjar gälla kan vara: Lägga till, granskning, AuditIfNotExists, neka, DeployIfNotExists, inaktiverad. Mer information om hur effekterna tillämpas finns [Förstå princip effekterna](../governance/policy/concepts/effects.md).

   ![princip för skärmen](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - När du visa tilldelade principer, du kan se flera tilldelningar och du kan se hur varje uppgift är konfigurerad på egen hand.

## <a name="edit-security-policies"></a>Redigera säkerhetsprinciper
Du kan redigera standardsäkerhetsprincipen för var och en av dina Azure-prenumerationer och hanteringsgrupper i [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Om du vill ändra en säkerhetsprincip måste du vara ägare, deltagare eller säkerhetsadministratör för prenumerationen eller hanteringsgruppen som innehåller den.

Instruktioner om hur du redigerar en säkerhetsprincip i Azure Policy finns och [skapa och hantera principer för att tvinga kompatibilitet](../governance/policy/tutorials/create-and-manage.md).

Du kan redigera säkerhetsprinciper via Azure Policy-portalen via REST API eller med hjälp av Windows PowerShell. I följande exempel innehåller instruktioner för redigering med REST API.

### <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurera en säkerhetsprincip med hjälp av REST-API

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


### Referens för namn <a name="policy-names"></a>

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
|Sårbarhetsbedömning |Övervaka säkerhetsrisker i virtuella datorer i Azure Security Center |vulnerabilityAssesmentMonitoringEffect|
|Brandvägg för webbaserade program |Övervaka oskyddat webbprogram i Azure Security Center |webApplicationFirewallMonitoringEffect |
|Nästa generations brandvägg |Övervaka oskyddade nätverksslutpunkter i Azure Security Center| |


### <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhetsprinciper?
Security Center använder rollbaserad åtkomstkontroll (RBAC), som erbjuder inbyggda roller som kan tilldelas till användare, grupper och tjänster i Azure. När användarna öppnar Security Center, visas endast information som är relaterad till resurser som de har åtkomst till. Vilket innebär att användare har tilldelats rollen ägare, deltagare eller läsare i prenumeration eller resursgrupp gruppen som en resurs hör till. Förutom dessa roller finns två specifika roller i Security Center:

- Säkerhetsläsare: Har läsbehörigheter till Security Center, som innehåller rekommendationer, aviseringar, principer och hälsa, men de kan inte göra ändringar.
- Säkerhetsadministratör: Ha samma visa rättigheter som security-läsare och de kan också uppdatera säkerhetsprinciper och stänga rekommendationer och aviseringar.



## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att redigera säkerhetsprinciper i Azure Policy. I följande artiklar kan du lära dig mer om Security Center:

* [Azure Security Center planerings- och bruksanvisning](security-center-planning-and-operations-guide.md): Lär dig hur du planerar och designaspekter om Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Få insyn i klienttäckande för Azure Security Center](security-center-management-groups.md): Lär dig hur du ställer in hanteringsgrupper för Azure Security Center.
* [Vanliga frågor om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures Säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

Mer information om Azure Policy finns i [Vad är Azure Policy?](../azure-policy/azure-policy-introduction.md)

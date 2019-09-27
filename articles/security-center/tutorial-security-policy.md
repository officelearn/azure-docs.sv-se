---
title: Arbeta med säkerhets principer | Microsoft Docs
description: Den här artikeln beskriver hur du arbetar med säkerhets principer i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: memildin
ms.openlocfilehash: 488210e4f2c5d3a8a978079d0c7293ce091d998b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338779"
---
# <a name="working-with-security-policies"></a>Arbeta med säkerhetsprinciper

Den här artikeln förklarar hur du konfigurerar säkerhets principer och hur du visar dem i Security Center. Azure Security Center tilldelar automatiskt de [inbyggda säkerhets principerna](security-center-policy-definitions.md) för varje prenumeration som har publicerats. Du kan konfigurera dem i [Azure policy](../governance/policy/overview.md), vilket innebär att du också kan ange principer för hanterings grupper och över flera prenumerationer.

Instruktioner för hur du anger principer med hjälp av PowerShell finns [i snabb start: Skapa en princip tilldelning för att identifiera icke-kompatibla resurser med hjälp av](../governance/policy/assign-policy-powershell.md)Azure PowerShell-modulen.

>[!NOTE]
> Security Center börjat integreras med Azure Policy. Befintliga kunder migreras automatiskt till det nya inbyggda initiativet i Azure Policy, i stället för de tidigare säkerhets principerna i Security Center. Den här ändringen påverkar inte dina resurser eller din miljö, förutom förekomsten av det nya initiativet i Azure Policy.

## <a name="what-are-security-policies"></a>Vad är säkerhetsprinciper?
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. I Azure Policy kan du definiera principer för dina Azure-prenumerationer och skräddarsy dem till din typ av arbets belastning eller känsligheten för dina data. Till exempel kan program som använder reglerade data, till exempel personliga data eller kund data, kräva en högre säkerhets nivå än andra arbets belastningar. Om du vill ange en princip över prenumerationer eller på hanterings grupper anger du dem i [Azure policy](../governance/policy/overview.md).

Dina säkerhets principer styr de säkerhets rekommendationer du får i Azure Security Center. Du kan övervaka efterlevnaden med dem för att hjälpa dig att identifiera potentiella sårbarheter och minimera hot. Mer information om hur du avgör vilket alternativ som passar dig bäst finns i listan över [inbyggda säkerhets principer](security-center-policy-definitions.md).

När du aktiverar Security Center avspeglas säkerhets principen som är inbyggd i Security Center Azure Policy som ett inbyggt initiativ under kategorin Security Center. Det inbyggda initiativet tilldelas automatiskt till alla Security Center-registrerade prenumerationer (på kostnadsfri nivå eller standardnivå). Det inbyggda initiativet innehåller endast granskningsprinciper.


### <a name="management-groups"></a>Hanteringsgrupper
Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsprinciper på hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de principer som tillämpas på hanteringsgruppen. Varje katalog tilldelas en hanteringsgrupp på översta nivån som kallas rothanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med hjälp av rothanteringsgruppen kan globala principer och RBAC-tilldelningar tillämpas på katalognivå. Om du vill konfigurera hanterings grupper som ska användas med Azure Security Center följer du anvisningarna i [få insyn i hela klienten för Azure Security Center](security-center-management-groups.md).

> [!NOTE]
> Det är viktigt att du är införstådd med hierarkin för hanteringsgrupper och prenumerationer. Du kan läsa mer om hanteringsgrupper, rothantering och hanteringsgruppåtkomst i artikeln om att [organisera dina resurser med Azures hanteringsgrupper](../governance/management-groups/overview.md#root-management-group-for-each-directory).
>

## <a name="how-security-policies-work"></a>Hur fungerar säkerhetsprinciper?
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Du kan redigera principerna i Azure Policy om du vill göra följande:
- Skapa nya principdefinitioner.
- Tilldela principer över hanteringsgrupper och prenumerationer som motsvarar en hel organisation eller affärsenhet inom organisationen.
- Övervaka principefterlevnad.

Om du vill ha mer information om Azure Policy kan du läsa [Create and manage policies to enforce compliance](../governance/policy/tutorials/create-and-manage.md) (Skapa och hantera principer för att genomdriva efterlevnad).

En Azure-princip består av följande komponenter:

- En **princip** är en regel.
- Ett **initiativ** är en samling principer.
- En **tilldelning** är programmet för ett initiativ eller en princip för en specifik omfattning (hanterings grupp, prenumeration eller resurs grupp).

## <a name="view-security-policies"></a>Visa säkerhetsprinciper

Visa dina säkerhetsprinciper i Security Center:

1. I instrument panelen **Security Center** väljer du **säkerhets princip**.

    ![Fönstret för principhantering](./media/security-center-policies/security-center-policy-mgt.png)

   På skärmen **princip hantering** kan du se antalet hanterings grupper, prenumerationer och arbets ytor samt hanterings gruppens struktur.

   > [!NOTE]
   > Security Center instrument panelen kan visa ett högre antal prenumerationer under **prenumerations täckningen** än antalet prenumerationer som visas under **princip hantering**. Prenumerationstäckningen visar antalet prenumerationer av typen Standard, Kostnadsfri och Omfattas inte. Prenumerationerna "ej belagda" har inte Security Center aktive rad och visas inte under **princip hantering**.
   >

2. Välj den prenumeration eller hanterings grupp vars principer du vill visa.

   - Skärmen **säkerhets princip** visar den åtgärd som vidtas av de principer som har tilldelats den prenumeration eller hanterings grupp du valt.
   - Använd de länkar som finns högst upp för att öppna varje princip **tilldelning** som gäller för prenumerationen eller hanterings gruppen. Du kan använda länkarna för att komma åt tilldelningen och redigera eller inaktivera principen. Om du till exempel ser att en viss princip tilldelning på ett effektivt sätt nekar Endpoint Protection kan du använda länken för att komma åt principen och redigera eller inaktivera den.
   - I listan över principer kan du se det effektiva tillämpnings programmet för principen i din prenumeration eller hanterings grupp. Det innebär att inställningarna för varje princip som gäller för omfånget beaktas och du får det ackumulerade resultatet av vilken åtgärd som utförs av principen. Om t. ex. principen är inaktive rad i en tilldelning, men i en annan den är inställd på AuditIfNotExist, tillämpas den kumulativa påverkan AuditIfNotExist. Den mer aktiva effekter har alltid företräde.
   - Policys-effekterna kan vara: Lägg till, granska, AuditIfNotExists, neka, DeployIfNotExists, inaktive rad. Mer information om hur effekter tillämpas finns i [Förstå princip effekter](../governance/policy/concepts/effects.md).

   ![princip skärm](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> När du visar tilldelade principer kan du se flera tilldelningar och du kan se hur varje tilldelning har kon figurer ATS på egen hand.

## <a name="edit-security-policies"></a>Redigera säkerhetsprinciper
Du kan redigera standardsäkerhetsprincipen för var och en av dina Azure-prenumerationer och hanteringsgrupper i [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Om du vill ändra en säkerhets princip måste du vara ägare eller säkerhets administratör för prenumerationen eller den som innehåller hanterings gruppen.

Instruktioner för hur du redigerar en säkerhets princip i Azure Policy finns i och [skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md).

Du kan redigera säkerhets principer via Azure Policy-portalen via REST API eller med hjälp av Windows PowerShell. I följande exempel finns anvisningar för hur du redigerar med REST API.


## <a name="disable-security-policies"></a>Inaktivera säkerhets principer
Om standard säkerhets principen genererar en rekommendation som inte är relevant för din miljö kan du stoppa den genom att inaktivera den princip definition som skickar rekommendationen.
Mer information om rekommendationer finns i [hantera säkerhets rekommendationer](security-center-recommendations.md).

1. I Security Center går du till avsnittet **princip & efterlevnad** och klickar på **säkerhets princip**.

   ![princip hantering](./media/tutorial-security-policy/policy-management.png)

2. Klicka på den prenumeration eller hanterings grupp som du vill inaktivera rekommendationen för.

   > [!Note]
   > Kom ihåg att en hanterings grupp tillämpar sina principer på sina prenumerationer. Om du inaktiverar en prenumerations princip och prenumerationen tillhör en hanterings grupp som fortfarande använder samma princip, kommer du att fortsätta att ta emot princip rekommendationerna. Principen kommer fortfarande att tillämpas från hanterings nivån och rekommendationerna kommer fortfarande att genereras.

1. Klicka på den tilldelade principen.

   ![Inaktivera princip](./media/tutorial-security-policy/security-policy.png)

1. I avsnittet **parametrar** söker du efter principen som anropar rekommendationen som du vill inaktivera. Välj inaktive **rad** i list rutan.

   ![Inaktivera princip](./media/tutorial-security-policy/disable-policy.png)
1. Klicka på **Spara**.
   > [!Note]
   > Det kan ta upp till 12 timmar innan ändringar av principen inaktive ras.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurera en säkerhets princip med hjälp av REST API

Som en del av den interna integreringen med Azure Policy kan du Azure Security Center dra nytta av Azure Policy REST API för att skapa princip tilldelningar. Följande anvisningar beskriver hur du skapar princip tilldelningar, samt anpassningar av befintliga tilldelningar. 

Viktiga begrepp i Azure Policy: 

- En **princip definition** är en regel 

- Ett **initiativ** är en samling princip definitioner (regler) 

- En **tilldelning** är ett program för ett initiativ eller en princip för en specifik omfattning (hanterings grupp, prenumeration osv.) 

Security Center har ett inbyggt initiativ som innehåller alla säkerhets principer. För att kunna utvärdera Security Centers principer på dina Azure-resurser, bör du skapa en tilldelning i hanterings gruppen eller prenumerationen som du vill utvärdera.

Det inbyggda initiativet har alla Security Centers principer som är aktiverade som standard. Du kan välja att inaktivera vissa principer från det inbyggda initiativet, till exempel om du vill använda alla Security Centers principer utom **brand vägg för webbaserade program**, genom att ändra värdet för principens gällande parameter till **inaktive rad**. 

### <a name="api-examples"></a>API-exempel

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

### Princip namn referens<a name="policy-names"></a>

|Princip namn i Security Center|Princip namn som visas i Azure Policy |Parameter namn för princip påverkan|
|----|----|----|
|SQL-kryptering |Övervaka okrypterad SQL Database i Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL-granskning |Övervaka en SQL-databas som inte har granskats i Azure Security Center |sqlAuditingMonitoringEffect|
|Systemuppdateringar |Övervaka system uppdateringar som saknas i Azure Security Center |systemUpdatesMonitoringEffect|
|Lagringskryptering |Granska saknad BLOB-kryptering för lagrings konton |storageEncryptionMonitoringEffect|
|Åtkomst till JIT-nätverk |Övervaka möjliga JIT-åtkomst (Network just in Time) i Azure Security Center |jitNetworkAccessMonitoringEffect |
|Anpassningsbara programkontroller |Övervaka möjlig app-vit listning i Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Nätverkssäkerhetsgrupper |Övervaka tillåtad nätverks åtkomst i Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Säkerhetskonfigurationer |Övervaka OS-sårbarheter i Azure Security Center |systemConfigurationsMonitoringEffect| 
|Slutpunktsskydd |Övervaka saknade Endpoint Protection i Azure Security Center |endpointProtectionMonitoringEffect |
|Diskkryptering |Övervaka okrypterade VM-diskar i Azure Security Center |diskEncryptionMonitoringEffect|
|Sårbarhetsbedömning |Övervaka säkerhets risker i virtuella datorer i Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Brandvägg för webbaserade program |Övervaka oskyddat webb program i Azure Security Center |webApplicationFirewallMonitoringEffect |
|Nästa generations brandvägg |Övervaka oskyddade nätverks slut punkter i Azure Security Center| |


### <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhets principer?
Security Center använder rollbaserad Access Control (RBAC), som innehåller inbyggda roller som kan tilldelas användare, grupper och tjänster i Azure. När användarna öppnar Security Center ser de bara information som är relaterad till de resurser som de har åtkomst till. Det innebär att användarna tilldelas rollen som ägare, deltagare eller läsare till den prenumeration eller resurs grupp som en resurs tillhör. Förutom dessa roller finns två specifika roller i Security Center:

- Säkerhets läsare: Ha behörighet att Security Center, som innehåller rekommendationer, aviseringar, principer och hälsa, men som inte kan göra ändringar.
- Säkerhets administratör: Ha samma visnings rättigheter som säkerhets läsaren, och de kan också uppdatera säkerhets principen och stänga rekommendationer och aviseringar.



## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du redigerar säkerhets principer i Azure Policy. I följande artiklar kan du lära dig mer om Security Center:

* [Azure Security Center planerings-och drift guide](security-center-planning-and-operations-guide.md): Lär dig hur du planerar och förstår design överväganden för Azure Security Center.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partner lösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Få överblick över hela organisationen för Azure Security Center](security-center-management-groups.md): Lär dig hur du konfigurerar hanterings grupper för Azure Security Center.
* [Azure Security Center vanliga frågor och svar](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azure-säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

Mer information om Azure Policy finns i [Vad är Azure Policy?](../governance/policy/overview.md)

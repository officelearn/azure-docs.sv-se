---
title: Styrning av Azure DevTest Labs-infrastruktur - Resurs
description: Den här artikeln behandlar justering och hantering av resurser för Azure DevTest Labs inom din organisation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8bb00c770c61a0a5462a01ae552bd7e40a7cdb36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470657"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Styrning av Azure DevTest Labs-infrastruktur - Resurser
Den här artikeln behandlar justering och hantering av resurser för DevTest Labs inom organisationen. 

## <a name="align-within-an-azure-subscription"></a>Justera inom en Azure-prenumeration 

### <a name="question"></a>Fråga
Hur justerar jag DevTest Labs-resurser i en Azure-prenumeration?

### <a name="answer"></a>Svar
Innan en organisation börjar använda Azure för allmän programutveckling bör IT-planerare först granska hur du introducerar funktionen som en del av deras övergripande portfölj av tjänster. Granskningsområden bör ta itu med följande frågor:

- Hur mäter du kostnaden som är associerad med livscykeln för programutveckling?
- Hur anpassar organisationen det föreslagna tjänsteerbjudandet till företagets säkerhetspolicy? 
- Krävs segmentering för att separera utvecklings- och produktionsmiljöer? 
- Vilka kontroller införs för långsiktig enkel förvaltning, stabilitet och tillväxt?

Den **första rekommenderade metoden** är att granska organisationers Azure-taxonomi där uppdelningarna mellan produktions- och utvecklingsprenumerationer beskrivs. I följande diagram tillåter den föreslagna taxonomin en logisk separation av utvecklings-/testnings- och produktionsmiljöer. Med den här metoden kan en organisation införa faktureringskoder för att spåra kostnader som är associerade med varje miljö separat. Mer information finns i [Föreskrivande prenumerationsstyrning](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Dessutom kan du använda [Azure-taggar](../azure-resource-manager/management/tag-resources.md) för att ordna resurser för spårning och fakturering.

Den **andra rekommenderade metoden** är att aktivera DevTest-prenumerationen i Azure Enterprise-portalen. Det gör det möjligt för en organisation att köra klientoperativsystem som vanligtvis inte är tillgängliga i en Azure enterprise-prenumeration. Använd sedan företagsprogram där du bara betalar för beräkningen och oroa dig inte för licensiering. Det säkerställer att faktureringen för utsedda tjänster, inklusive galleriavbildningar i IaaS, till exempel Microsoft SQL Server, endast baseras på förbrukning. Information om Azure DevTest-prenumerationen finns [här](https://azure.microsoft.com/offers/ms-azr-0148p/) för EA-kunder (Enterprise Agreement) och [här](https://azure.microsoft.com/offers/ms-azr-0023p/) för Pay as you Go-kunder.

![Resursjustering med prenumerationer](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Den här modellen ger en organisation flexibiliteten att distribuera Azure DevTest Labs i stor skala. En organisation kan stödja hundratals laboratorier för olika affärsenheter med 100 till 1000 virtuella datorer som körs parallellt. Det främjar begreppet en centraliserad företagslabblösning som kan dela samma principer för konfigurationshantering och säkerhetskontroller.

Den här modellen säkerställer också att organisationen inte uttömmer sina resursgränser som är associerade med deras Azure-prenumeration. Mer information om prenumerations- och tjänstgränser finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md). Etableringsprocessen för DevTest Labs kan förbruka ett stort antal resursgrupper. Du kan begära att gränserna utökas via en supportbegäran i Azure DevTest-prenumerationen. Resurserna i produktionsprenumerationen påverkas inte när utvecklingsprenumerationen ökar i bruk. Mer information om hur du skalar DevTest Labs finns i [Skala kvoter och begränsningar i DevTest Labs](devtest-lab-scale-lab.md).

En gemensam prenumerationsnivågräns som måste beaktas är hur nätverks-IP-intervalltilldelningarna allokeras för att stödja både produktions- och utvecklingsprenumerationer. Dessa tilldelningar bör ta hänsyn till tillväxt över tid (förutsatt lokal anslutning eller annan nätverkstopologi som kräver att företaget hanterar sin nätverksstack i stället för att standard till Azures implementering). Den rekommenderade metoden är att ha några virtuella nätverk som har en stor IP-adress prefix tilldelas och delas med många stora undernät snarare än att ha flera virtuella nätverk med små undernät. Med 10 prenumerationer kan du till exempel definiera 10 virtuella nätverk (ett för varje prenumeration). Alla labb som inte kräver isolering kan dela samma undernät på prenumerationens virtuella nätverk.

## <a name="maintain-naming-conventions"></a>Underhåll namngivningskonventioner

### <a name="question"></a>Fråga
Hur upprätthåller jag en namngivningskonvention i hela min DevTest Labs-miljö?

### <a name="answer"></a>Svar
Du kanske vill utöka aktuella företagsnamngivningskonventioner till Azure-åtgärder och göra dem konsekventa i devtest labs-miljön.

När du distribuerar DevTest Labs rekommenderar vi att du har specifika startprinciper. Du distribuerar dessa principer med ett centralt skript och JSON-mallar för att framtvinga konsekvens. Namngivningsprinciper kan implementeras via Azure-principer som tillämpas på prenumerationsnivå. För JSON-exempel för Azure Policy finns i [Azure Policy-exempel](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Antal användare per labb och labb per organisation

### <a name="question"></a>Fråga 
Hur avgör jag förhållandet mellan användare per labb och det totala antalet labb som behövs i en organisation?

### <a name="answer"></a>Svar
Vi rekommenderar att affärsenheter och utvecklingsgrupper som är associerade med samma utvecklingsprojekt associeras med samma labb. Det gör att samma typer av principer, bilder och avstängningsprinciper kan tillämpas på båda grupperna. 

Du kan också behöva överväga geografiska gränser. Utvecklare i nordöstra USA (USA) kan till exempel använda ett labb som etablerats i östra US2. Och utvecklare i Dallas, Texas och Denver, Colorado kan riktas för att använda en resurs i US South Central. Om det finns ett samarbete med en extern tredje part kan de tilldelas ett labb som inte används av interna utvecklare. 

Du kan också använda ett labb för ett specifikt projekt inom Azure DevOps-projekt. Sedan tillämpar du säkerhet via en angiven Azure Active Directory-grupp, som ger åtkomst till båda resursuppsättningen. Det virtuella nätverket som tilldelats labbet kan vara en annan gräns för att konsolidera användare.

## <a name="deletion-of-resources"></a>Radering av resurser

### <a name="question"></a>Fråga
Hur kan vi förhindra att resurser tas bort i ett labb?

### <a name="answer"></a>Svar
Vi rekommenderar att du anger rätt behörigheter på labbnivå så att endast behöriga användare kan ta bort resurser eller ändra labbprinciper. Utvecklare bör placeras i gruppen **DevTest Labs-användare.** Huvudutvecklaren eller infrastrukturledningen ska vara **DevTest Labs-ägaren**. Vi rekommenderar att du bara har två labbägare. Den här principen sträcker sig till kodarkivet för att undvika skador. Labbanvändning har behörighet att använda resurser men kan inte uppdatera labbprinciper. Se följande artikel där du visar de roller och rättigheter som varje inbyggd grupp har i ett labb: [Lägg till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Flytta labbet till en annan resursgrupp 

### <a name="question"></a>Fråga
Stöds det att flytta ett labb till en annan resursgrupp?

### <a name="answer"></a>Svar
Ja. Navigera till sidan Resursgrupp från labbets startsida. Välj sedan **Flytta** i verktygsfältet och välj det labb som du vill flytta till en annan resursgrupp. När du skapar ett labb skapas en resursgrupp automatiskt åt dig. Du kanske vill flytta labbet till en annan resursgrupp som följer företagets namngivningskonventioner. 

## <a name="next-steps"></a>Nästa steg
Se [Hantera kostnader och ägande](devtest-lab-guidance-governance-cost-ownership.md).

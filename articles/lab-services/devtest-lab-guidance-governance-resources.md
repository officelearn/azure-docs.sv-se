---
title: Styrning i Azure DevTest Labs-infrastruktur
description: Den här artikeln innehåller riktlinjer för styrning i Azure DevTest Labs-infrastruktur.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 2edfa84530c147e1f716aef10c967ac36eadc922
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48251134"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Styrning av infrastruktur för Azure DevTest Labs - resurser
Den här artikeln tar upp justering och hantering av resurser för DevTest Labs i din organisation. 

## <a name="align-within-an-azure-subscription"></a>Justera inom en Azure-prenumeration 

### <a name="question"></a>Fråga
Hur jag för att justera DevTest Labs resurser inom en Azure-prenumeration?

### <a name="answer"></a>Svar
Innan en organisation börjar använda Azure för utveckling av allmänt program, granska IT-planerare först presentera kapaciteten som en del av deras övergripande portfölj av tjänster. Områden för granskning bör åtgärda följande problem:

- Hur du använder den kostnad som hör till programlivscykeln för utveckling?
- Hur justera föreslagna tjänsterbjudandet med företagets säkerhetsprinciper i organisationen? 
- Är segmentering som krävs för att avgränsa miljöer för utveckling och produktion? 
- Vilka kontroller har introducerats för långsiktig enkel hantering, stabilitet och tillväxt?

Den **först rekommenderar** är att granska organisationers Azure taxonomi där indelningar mellan produktion och utveckling prenumerationer beskrivs. I följande diagram kan den föreslagna taxonomin för en logisk avgränsning av utveckling och testning och produktionsmiljöer. Med den här metoden kan en organisation introducera fakturering koder för att spåra kostnader i samband med varje miljö separat. Mer information finns i [förebyggande Prenumerationsåtgärder](/architecture/cloud-adoption/appendix/azure-scaffold). Du kan dessutom använda [Azure taggar](../azure-resource-manager/resource-group-using-tags.md) organisera resurser för spårning och fakturering.

Den **andra rekommenderas** är att aktivera DevTest-prenumeration inom Azure Enterprise portal. Det gör att en organisation att köra klientoperativsystem som inte är normalt tillgängliga i en Azure enterprise-prenumeration. Använd sedan företagsprogram där du betalar bara för beräkningen och inte oroa dig inte om licensiering. Det innebär att faktureringen för avsedda tjänster, inklusive galleriavbildningar i IaaS, till exempel Microsoft SQL Server, är baserat på förbrukning endast. Information om Azure DevTest-prenumerationen finns [här](https://azure.microsoft.com/offers/ms-azr-0148p/) för kunder med Enterprise Agreement (EA) och [här](https://azure.microsoft.com/offers/ms-azr-0023p/) för kunder med Användningsbaserad betalning.

![Justering av resursen med prenumerationer](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Den här modellen ger en organisation flexibiliteten att distribuera Azure DevTest Labs i stor skala. En organisation stöder hundratals labs för olika affärsenheter med 100 till 1000 virtuella datorer som körs parallellt. Den främjar begreppet en centraliserad lab företagslösning som kan dela samma principer för konfigurationshantering och säkerhetskontroller.

Den här modellen innebär också att organisationen inte använt sina resursbegränsningar som är associerad med deras Azure-prenumeration. Mer information om prenumeration och tjänstbegränsningar finns [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md). DevTest Labs etableringsprocessen kan använda många resursgrupper. Du kan begära för begränsningar för ökas genom en supportförfrågan i Azure DevTest-prenumeration. Resurserna i produktion prenumerationen påverkas inte när prenumerationen utveckling växer används. Läs mer om att skala DevTest Labs [skala kvoter och begränsningar i DevTest Labs](devtest-lab-scale-lab.md).

En gemensam nivå prenumerationsgränsen som måste ta hänsyn till är hur IP-adressintervall nätverksallokeringarna allokeras för att stödja både produktions- och prenumerationer för utveckling. De här tilldelningarna bör hänsyn till tillväxten över tid (förutsatt att den lokala anslutningen eller en annan nätverkets topologi som kräver att företag kan hantera sina nätverksstacken i stället för standardvärdet är Azures implementering). Den rekommenderade metoden är att ha några virtuella nätverk som har ett stort IP-adressprefix tilldelas och dividerat med många stora undernät i stället för att ha flera virtuella nätverk med liten undernät. Du kan till exempel definiera 10 virtuella nätverk (en för varje prenumeration) med 10 prenumerationer. Alla labb som inte kräver isolering kan dela samma undernät för prenumerationens virtuella nätverk.

## <a name="maintain-naming-conventions"></a>Underhålla namngivningskonventioner

### <a name="question"></a>Fråga
Hur jag för att bibehålla en namngivningskonvention för min labb-miljö?

### <a name="answer"></a>Svar
Du kanske vill utöka aktuella enterprise namnkonventioner för Azure-åtgärder och göra dem konsekvent i labb-miljö.

Vi rekommenderar att du har specifika från principer när du distribuerar DevTest Labs. Du distribuerar dessa principer genom att en central skript och JSON-mallar för att upprätthålla konsekvens. Namngivning av principer kan implementeras via Azure-principer tillämpas på prenumerationsnivån. JSON-exempel för Azure Policy, se [Azure Policy-exempel](../azure-policy/json-samples.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Antalet användare per labb och labb per organisation

### <a name="question"></a>Fråga 
Hur vet jag antalet användare per labb och det totala antalet labs som behövs i en organisation?

### <a name="answer"></a>Svar
Vi rekommenderar att affärsenheter och utvecklingsgrupper som är associerade med samma utvecklingsprojekt är associerade med samma labbet. Det möjliggör samma typer av principer, bilder och Stäng av principer som ska tillämpas på båda grupperna. 

Du kan också behöva tänka på geografiska gränser. Exempelvis kan utvecklare i norra östra USA (USA) använda ett labb som etablerats i östra usa2. Och utvecklare i Dallas, Texas, och Denver, Colorado kan dirigeras till att använda en resurs i södra centrala USA. Om det finns en gemensam ansträngning med en extern från tredje part, kan de tilldelas till ett labb som inte används av egna utvecklare. 

Du kan också använda ett labb för ett specifikt projekt i Visual Studio Team Services-grupprojekt. Sedan kan tillämpa du säkerhet via en angiven Azure Active Directory-grupp, vilket ger åtkomst till båda uppsättning resurser. Det virtuella nätverket som tilldelats labbet kan vara en annan gräns att konsolidera användare.

## <a name="deletion-of-resources"></a>Borttagning av resurser

### <a name="question"></a>Fråga
Hur kan vi för att förhindra borttagningen av resurser i ett laboratorium?

### <a name="answer"></a>Svar
Vi rekommenderar att du anger rätt behörigheter på nivån labb så att endast behöriga användare kan ta bort resurser eller ändra principer för labbet. Utvecklare bör placeras i den **DevTest Labs-användare** grupp. Lead-utvecklare och infrastruktur lead bör vara den **DevTest Labs ägare**. Vi rekommenderar att du har bara två labbägare. Den här principen utökar mot kodlagringsplatsen att undvika skada. Labbet använder har behörighet att använda resurser, men kan inte uppdatera principer för labbet. Se följande artikel som visar en lista över roller och behörigheter som varje inbyggd grupp som har i ett laboratorium: [Lägg till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Flytta labb till en annan resursgrupp 

### <a name="question"></a>Fråga
Finns det stöd för att flytta ett labb till en annan resursgrupp?

### <a name="answer"></a>Svar
Ja. Gå till sidan resursgrupp från startsidan för labbet. Välj **flytta** verktygsfältet och välj labb som du vill flytta till en annan resursgrupp. När du skapar ett labb, skapas en resursgrupp automatiskt åt dig. Dock kan du flytta labbet till en annan resursgrupp som följer namngivningskonventionerna i företaget. 

## <a name="next-steps"></a>Nästa steg
Se [hantera kostnader och ägarskap](devtest-lab-guidance-governance-cost-ownership.md).
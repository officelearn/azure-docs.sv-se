---
title: Översikt över Azure Resource Manager | Microsoft Docs
description: Beskriver hur du använder Azure Resource Manager för distribution, hantering och åtkomstkontroll av resurser i Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: overview
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 525271fdc94e0fd1b2cafe4a5a222ce9100156ef
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888481"
---
# <a name="azure-resource-manager-overview"></a>Översikt över Azure Resource Manager

Azure Resource Manager är Azures tjänst för distribution och hantering. Det tillhandahåller ett hanterings lager som gör att du kan skapa, uppdatera och ta bort resurser i din Azure-prenumeration. Du kan använda hanterings funktioner som åtkomst kontroll, lås och taggar för att skydda och organisera dina resurser efter distributionen.

Mer information om Azure Resource Manager mallar finns [malldistribution översikt](template-deployment-overview.md).

## <a name="consistent-management-layer"></a>Enhetligt hanteringslager

När en användare skickar en begäran från något av Azure-verktyg, API: er eller SDK: er, tar Resource Manager emot begäran. Den autentiserar och auktoriserar begäran. Resource Manager skickar begäran till Azure-tjänsten, som tar den begärda åtgärden. Eftersom alla förfrågningar hanteras via samma API visas enhetliga resultat och funktionerna i de olika verktygen.

Följande bild visar hur roll Azure Resource Manager spelar i hantering av Azure-begäranden. 

![Modell för Resource Manager-begäranden](./media/resource-group-overview/consistent-management-layer.png)

Alla funktioner som är tillgängliga i portalen är även tillgängliga via PowerShell, Azure CLI, REST-API:er och klient-SDK:er. Funktioner som ursprungligen publiceras via API:er kommer att representeras i portalen inom 180 dagar efter den första publiceringen.

## <a name="terminology"></a>Terminologi

Om du inte har arbetat med Azure Resource Manager tidigare finns det några termer som kanske är nya för dig.

* **resurs** – Ett hanterbart objekt som är tillgängligt via Azure. Virtuella datorer, lagringskonton, webbappar, databaser och virtuella nätverk är exempel på resurser.
* **resursgrupp** – En container som innehåller relaterade resurser för en Azure-lösning. En resursgrupp innehåller de resurser du vill hantera som en grupp. Du bestämmer vilka resurser som ska ingå i en resurs grupp baserat på vad som är bäst för din organisation. Mer information finns i [Resursgrupper](#resource-groups).
* **resursprovider** – en tjänst som tillhandahåller Azure-resurser. Till exempel är en gemensam resurs leverantör Microsoft. Compute, som tillhandahåller resursen för den virtuella datorn. Microsoft. Storage är en annan gemensam resurs leverantör. Se [Resource providers och-typer](resource-manager-supported-services.md).
* **Resource Manager-mall** – En JSON-fil (JavaScript Object Notation) som definierar en eller flera resurser som ska distribueras till en resursgrupp eller en prenumeration. Mallen kan användas för att distribuera resurserna på ett konsekvent sätt och upprepade gånger. Se [malldistribution översikt](template-deployment-overview.md).
* **deklarativ syntax** – Syntax som låter dig ange vad du vill skapa utan att du behöver skriva sekvensen med programmeringskommandon för att skapa det. Resource Manager-mallen är ett exempel på deklarativ syntax. I filen definierar du egenskaperna för infrastrukturen som ska distribueras till Azure.  Se [malldistribution översikt](template-deployment-overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>Fördelarna med att använda Resource Manager

Med Resource Manager kan du:

* Hantera din infrastruktur genom deklarativ mallar i stället för skript.

* Distribuera, hantera och övervaka alla resurser för din lösning som en grupp, i stället för att hantera resurserna individuellt.

* Distribuera om lösningen under utvecklings livs cykeln och få förtroende för att dina resurser distribueras i ett konsekvent tillstånd.

* Definiera beroenden mellan resurser så att de distribueras i rätt ordning.

* Använd åtkomst kontroll för alla tjänster i resurs gruppen eftersom rollbaserad Access Control (RBAC) är inbyggt integrerat i hanterings plattformen.

* Använd taggar för resurser för att logiskt organisera alla resurser i din prenumeration.

* Klargör organisationens fakturering genom att Visa kostnaderna för en grupp av resurser som delar samma tagg.

## <a name="understand-scope"></a>Förstå omfång

Azure tillhandahåller fyra nivåer av omfång: [hanterings grupper](../governance/management-groups/overview.md), prenumerationer, [resurs grupper](#resource-groups)och resurser. Följande bild visar ett exempel på dessa lager.

![Omfång](./media/resource-group-overview/scope-levels.png)

Du tillämpar hanteringsinställningar på vilken som helst av dessa omfångsnivåer. Den nivå nu väljer avgör hur brett inställningen tillämpas. Lägre nivåer ärver inställningar från högre nivåer. När du till exempel tillämpar en [princip](../governance/policy/overview.md) för prenumerationen tillämpas principen på alla resurs grupper och resurser i din prenumeration. När du tillämpar en princip på resurs gruppen tillämpas den här principen på resurs gruppen och alla dess resurser. En annan resurs grupp har dock inte den princip tilldelningen.

Du kan distribuera mallar till hanterings grupper, prenumerationer eller resurs grupper.

## <a name="resource-groups"></a>Resursgrupper

Det finns några viktiga faktorer att tänka på när du definierar en resursgrupp:

* Alla resurserna i din grupp måste ha samma livscykel. Du distribuerar, uppdaterar och tar bort dem tillsammans. Om en resurs, till exempel en databasserver, behöver finnas i en annan distributionscykel ska den höra till en annan resursgrupp.

* En enskild resurs kan bara finnas i en resursgrupp.

* Du kan lägga till eller ta bort en resurs i en resursgrupp när som helst.

* Du kan flytta en resurs från en resursgrupp till en annan grupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](resource-group-move-resources.md).

* En resursgrupp kan innehålla resurser som finns i olika regioner.

* En resursgrupp kan användas för att definiera omfattningen av åtkomstkontrollen för administrativa åtgärder.

* En resurs kan interagera med resurser i andra resursgrupper. Denna interaktion är vanlig när de två resurserna är relaterade, men inte delar samma livscykel (till exempel webbappar som är anslutna till en databas).

När du skapar en resursgrupp måste du ange en plats för denna resursgrupp. Du kanske undrar, "varför behöver en resursgrupp en plats? Och om resurserna kan ha andra platser än resursgruppen, varför spelar platsen för gruppresursen alls någon roll?" Resursgruppen lagrar metadata om resurserna. När du anger en plats för resurs gruppen anger du var metadata lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

Om resurs gruppens region är tillfälligt otillgänglig, kan du inte uppdatera resurser i resurs gruppen eftersom metadata inte är tillgängliga. Resurserna i andra regioner fungerar fortfarande som förväntat, men du kan inte uppdatera dem. Mer information om hur du skapar pålitliga program finns i [utforma pålitliga Azure-program](/azure/architecture/checklist/resiliency-per-service).

## <a name="resiliency-of-azure-resource-manager"></a>Azure Resource Managers återhämtning

Tjänsten Azure Resource Manager är utformad för återhämtning och kontinuerlig tillgänglighet. Resource Manager och kontroll Plans åtgärder (begär Anden som skickats till management.azure.com) i REST API:

* Fördelat över flera regioner. Vissa tjänster är regionala.

* Fördelat över Tillgänglighetszoner (och även region) på platser som har flera Tillgänglighetszoner.

* Inte beroende av ett enskilt logiskt Data Center.

* Vidtogs aldrig för underhålls aktiviteter.

Den här återhämtningen gäller för tjänster som tar emot förfrågningar via Resource Manager. Till exempel Key Vault fördelarna med den här återhämtnings förmågan.

## <a name="next-steps"></a>Nästa steg

* Alla åtgärder som erbjuds av resursproviders finns i [REST API:er för Azure](/rest/api/azure/).

* Mer information om hur du flyttar resurser finns i [Flytta resurser till en ny resurs grupp eller prenumeration](resource-group-move-resources.md).

* Mer information om att tagga resurser finns i [använda taggar för att ordna dina Azure-resurser](resource-group-using-tags.md).

* Information om hur du låser resurser finns i [låsa resurser för att förhindra oväntade ändringar](resource-group-lock-resources.md).

* Information om hur du skapar mallar för distributioner finns i [malldistribution översikt](template-deployment-overview.md).

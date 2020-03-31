---
title: Översikt
description: Beskriver hur du använder Azure Resource Manager för distribution, hantering och åtkomstkontroll av resurser i Azure.
ms.topic: overview
ms.date: 03/25/2020
ms.openlocfilehash: 1e2a6959117749b4e7d08a9768b4189b97ef08bd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80258149"
---
# <a name="what-is-azure-resource-manager"></a>Vad är Azure Resource Manager?

Azure Resource Manager är Azures tjänst för distribution och hantering. Det ger ett hanteringslager som gör att du kan skapa, uppdatera och ta bort resurser i din Azure-prenumeration. Du använder hanteringsfunktioner, till exempel åtkomstkontroll, lås och taggar, för att skydda och organisera dina resurser efter distributionen.

Mer information om Azure Resource Manager-mallar finns i [Översikt över malldistribution](../templates/overview.md).

## <a name="consistent-management-layer"></a>Enhetligt hanteringslager

När en användare skickar en begäran från något av Azure-verktygen, API:erna eller SDK:erna tar Resource Manager emot begäran. Den autentiserar och godkänner begäran. Resource Manager skickar begäran till Azure-tjänsten, som vidtar den begärda åtgärden. Eftersom alla förfrågningar hanteras via samma API visas enhetliga resultat och funktionerna i de olika verktygen.

Följande avbildning visar vilken roll Azure Resource Manager spelar vid hantering av Azure-begäranden.

![Modell för Resource Manager-begäranden](./media/overview/consistent-management-layer.png)

Alla funktioner som är tillgängliga i portalen är även tillgängliga via PowerShell, Azure CLI, REST-API:er och klient-SDK:er. Funktioner som ursprungligen publiceras via API:er kommer att representeras i portalen inom 180 dagar efter den första publiceringen.

## <a name="terminology"></a>Terminologi

Om du inte har arbetat med Azure Resource Manager tidigare finns det några termer som kanske är nya för dig.

* **resurs** – Ett hanterbart objekt som är tillgängligt via Azure. Virtuella datorer, lagringskonton, webbappar, databaser och virtuella nätverk är exempel på resurser.
* **resursgrupp** – En container som innehåller relaterade resurser för en Azure-lösning. En resursgrupp innehåller de resurser du vill hantera som en grupp. Du bestämmer vilka resurser som ska ingå i en resursgrupp baserat på vad som är bäst för organisationen. Mer information finns i [Resursgrupper](#resource-groups).
* **resursprovider** – en tjänst som tillhandahåller Azure-resurser. En vanlig resursprovider är till exempel Microsoft.Compute, som tillhandahåller resursen för virtuella datorer. Microsoft.Storage är en annan vanlig resursprovider. Se [Resursleverantörer och -typer](resource-providers-and-types.md).
* **Resource Manager-mall** – En JSON-fil (JavaScript Object Notation) som definierar en eller flera resurser som ska distribueras till en resursgrupp eller en prenumeration. Mallen kan användas för att distribuera resurserna på ett konsekvent sätt och upprepade gånger. Se [Översikt över malldistribution](../templates/overview.md).
* **deklarativ syntax** – Syntax som låter dig ange vad du vill skapa utan att du behöver skriva sekvensen med programmeringskommandon för att skapa det. Resource Manager-mallen är ett exempel på deklarativ syntax. I filen definierar du egenskaperna för infrastrukturen som ska distribueras till Azure.  Se [Översikt över malldistribution](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>Fördelarna med att använda Resource Manager

Med Resource Manager kan du:

* Hantera infrastrukturen med deklarativa mallar i stället för skript.

* Distribuera, hantera och övervaka alla resurser för din lösning som grupp, i stället för att hantera dessa resurser individuellt.

* Distribuera om din lösning under hela utvecklingslivscykeln och ha förtroende för att dina resurser distribueras i ett konsekvent tillstånd.

* Definiera beroenden mellan resurser så att de distribueras i rätt ordning.

* Använd åtkomstkontroll på alla tjänster i resursgruppen eftersom ROLLBaserad åtkomstkontroll (RBAC) är inbyggt integrerat i hanteringsplattformen.

* Använd taggar på resurser för att logiskt ordna alla resurser i prenumerationen.

* Förtydliga organisationens fakturering genom att visa kostnader för en grupp resurser som delar samma tagg.

## <a name="understand-scope"></a>Förstå omfång

Azure innehåller fyra nivåer av omfattning: [hanteringsgrupper,](../../governance/management-groups/overview.md)prenumerationer, [resursgrupper](#resource-groups)och resurser. Följande bild visar ett exempel på dessa lager.

![Omfång](./media/overview/scope-levels.png)

Du tillämpar hanteringsinställningar på vilken som helst av dessa omfångsnivåer. Den nivå nu väljer avgör hur brett inställningen tillämpas. Lägre nivåer ärver inställningar från högre nivåer. När du till exempel tillämpar en [princip](../../governance/policy/overview.md) på prenumerationen tillämpas principen på alla resursgrupper och resurser i prenumerationen. När du tillämpar en princip i resursgruppen tillämpas resursgruppen och alla dess resurser. En annan resursgrupp har dock inte den principtilldelningen.

Du kan distribuera mallar till hanteringsgrupper, prenumerationer eller resursgrupper.

## <a name="resource-groups"></a>Resursgrupper

Det finns några viktiga faktorer att tänka på när du definierar en resursgrupp:

* Alla resurserna i din grupp måste ha samma livscykel. Du distribuerar, uppdaterar och tar bort dem tillsammans. Om en resurs, till exempel en databasserver, behöver finnas i en annan distributionscykel ska den höra till en annan resursgrupp.

* En enskild resurs kan bara finnas i en resursgrupp.

* Du kan lägga till eller ta bort en resurs i en resursgrupp när som helst.

* Du kan flytta en resurs från en resursgrupp till en annan grupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

* En resursgrupp kan innehålla resurser som finns i olika regioner.

* En resursgrupp kan användas för att definiera omfattningen av åtkomstkontrollen för administrativa åtgärder.

* En resurs kan interagera med resurser i andra resursgrupper. Denna interaktion är vanlig när de två resurserna är relaterade, men inte delar samma livscykel (till exempel webbappar som är anslutna till en databas).

När du skapar en resursgrupp måste du ange en plats för denna resursgrupp. Du kanske undrar, "varför behöver en resursgrupp en plats? Och om resurserna kan ha andra platser än resursgruppen, varför spelar platsen för gruppresursen alls någon roll?" Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du var metadata lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

Om resursgruppens region inte är tillgänglig för tillfället kan du inte uppdatera resurser i resursgruppen eftersom metadata inte är tillgängliga. Resurserna i andra regioner fungerar fortfarande som förväntat, men du kan inte uppdatera dem. Mer information om hur du skapar tillförlitliga program finns i [Utforma tillförlitliga Azure-program](/azure/architecture/checklist/resiliency-per-service).

## <a name="resiliency-of-azure-resource-manager"></a>Återhämtning av Azure Resource Manager

Azure Resource Manager-tjänsten är utformad för återhämtning och kontinuerlig tillgänglighet. Resource Manager och kontrollplansåtgärder (begäranden som skickas till management.azure.com) i REST API:et är:

* Fördelat på regioner. Vissa tjänster är regionala.

* Distribueras över tillgänglighetszoner (samt regioner) på platser som har flera tillgänglighetszoner.

* Inte beroende av ett enda logiskt datacenter.

* Aldrig tas ner för underhållsaktiviteter.

Den här återhämtningen gäller för tjänster som får begäranden via Resource Manager. Key Vault drar till exempel nytta av den här återhämtningen.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du flyttar resurser finns i [Flytta resurser till ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

* Mer information om hur du taggar resurser finns i [Använda taggar för att ordna dina Azure-resurser](tag-resources.md).

* Mer information om hur du låser resurser finns i [Låsa resurser för att förhindra oväntade ändringar](lock-resources.md).

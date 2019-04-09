---
title: Översikt över Azure Resource Manager | Microsoft Docs
description: Beskriver hur du använder Azure Resource Manager för distribution, hantering och åtkomstkontroll av resurser i Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 76df7de1-1d3b-436e-9b44-e1b3766b3961
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2019
ms.author: tomfitz
ms.openlocfilehash: d4af11f5956d548ec25eb0dd9b553e2cf56b195f
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058551"
---
# <a name="azure-resource-manager-overview"></a>Översikt över Azure Resource Manager

Azure Resource Manager är Azures tjänst för distribution och hantering. Det ger ett konsekvent hanteringslager som hjälper dig att skapa, uppdatera och ta bort resurser i din Azure-prenumeration. Du kan använda dess åtkomstkontroll, granskning och taggningsfunktioner för att skydda och organisera dina resurser efter distributionen.

När du vidtar åtgärder via portalen, PowerShell, Azure CLI, REST API:er eller klient-SDK:er hanterar Azure Resource Manager API din begäran. Eftersom alla förfrågningar hanteras via samma API visas enhetliga resultat och funktionerna i de olika verktygen. Alla funktioner som är tillgängliga i portalen är även tillgängliga via PowerShell, Azure CLI, REST-API:er och klient-SDK:er. Funktioner som ursprungligen publiceras via API:er kommer att representeras i portalen inom 180 dagar efter den första publiceringen.

Följande bild visar hur alla verktyg interagerar med Azure Resource Manager-API:et. API:et skickar begäranden till Resource Manager-tjänsten, som autentiserar och auktoriserar begäranden. Resource Manager dirigerar sedan begäranden till lämplig tjänst.

![Modell för Resource Manager-begäranden](./media/resource-group-overview/consistent-management-layer.png)

## <a name="terminology"></a>Terminologi

Om du inte har arbetat med Azure Resource Manager tidigare finns det några termer som kanske är nya för dig.

* **resurs** – Ett hanterbart objekt som är tillgängligt via Azure. Virtuella datorer, lagringskonton, webbappar, databaser och virtuella nätverk är exempel på resurser.
* **resursgrupp** – En container som innehåller relaterade resurser för en Azure-lösning. En resursgrupp innehåller de resurser du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som passar din organisation bäst. Mer information finns i [Resursgrupper](#resource-groups).
* **resursprovider** – en tjänst som tillhandahåller Azure-resurser. En vanlig resursprovider är till exempel **Microsoft.Compute**, som tillhandahåller resursen för virtuella datorer. **Microsoft.Storage** är en annan vanlig resursprovider. Mer information finns i [Resursproviders](#resource-providers).
* **Resource Manager-mall** – En JSON-fil (JavaScript Object Notation) som definierar en eller flera resurser som ska distribueras till en resursgrupp eller en prenumeration. Mallen kan användas för att distribuera resurserna på ett konsekvent sätt och upprepade gånger. Mer information finns i [Malldistribution](#template-deployment).
* **deklarativ syntax** – Syntax som låter dig ange vad du vill skapa utan att du behöver skriva sekvensen med programmeringskommandon för att skapa det. Resource Manager-mallen är ett exempel på deklarativ syntax. I filen definierar du egenskaperna för infrastrukturen som ska distribueras till Azure.

## <a name="the-benefits-of-using-resource-manager"></a>Fördelarna med att använda Resource Manager

Resource Manager har flera fördelar:

* Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.
* Du kan distribuera lösningen flera gånger genom utvecklingslivscykeln och vara säker på att dina resurser distribueras på ett enhetligt sätt.
* Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.
* Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.
* Du kan använda åtkomstkontroll för alla tjänster i resursgruppen eftersom rollbaserad åtkomstkontroll (RBAC) är inbyggt i hanteringsplattformen.
* Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.
* Du kan tydliggöra din organisations fakturering genom att visa kostnaderna för en grupp av resurser som delar samma tagg.

## <a name="understand-management-scope"></a>Förstå hanteringsomfång

Azure erbjuder fyra nivåer av hanteringsomfång: hanteringsgrupper, prenumerationer, resursgrupper och resurser. [Hanteringsgrupper](../governance/management-groups/index.md) är i förhandsversion. Följande bild visar ett exempel på dessa lager.

![Scope](./media/resource-group-overview/scope-levels.png)

Du tillämpar hanteringsinställningar på vilken som helst av dessa omfångsnivåer. Den nivå nu väljer avgör hur brett inställningen tillämpas. Lägre nivåer ärver inställningar från högre nivåer. Till exempel när du tillämpar en [princip](../governance/policy/overview.md) prenumerationen, tillämpas principen för alla resursgrupper och resurser i din prenumeration. När du använder en princip på tillämpas den resursgrupp som principen är resursgruppen och alla dess resurser. En annan resursgrupp har dock inte den principtilldelningen.

## <a name="guidance"></a>Riktlinjer

Följande rekommendationer hjälper dig att dra full nytta av Resource Manager när du arbetar med dina lösningar.

* Definiera och distribuera infrastrukturen med den deklarativa syntaxen i Resource Manager-mallarna i stället för med tvingande kommandon.
* Definiera alla distributions- och konfigurationssteg i mallen. Inga manuella steg ska behövas för att konfigurera lösningen.
* Kör tvingande kommandon för att hantera resurser, exempelvis för att starta eller stoppa en app eller dator.
* Ordna resurser med samma livscykel i en resursgrupp. Använd taggar för all annan resursorganisation.

Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance?toc=%2fazure%2fazure-resource-manager%2ftoc.json).

Rekommendationer om att skapa Resource Manager-mallar finns i [Azure Resource Manager template best practices](template-best-practices.md) (Metodtips för att skapa Azure Resource Manager-mallar).

## <a name="resource-groups"></a>Resursgrupper
Det finns några viktiga faktorer att tänka på när du definierar en resursgrupp:

* Alla resurserna i din grupp måste ha samma livscykel. Du distribuerar, uppdaterar och tar bort dem tillsammans. Om en resurs, till exempel en databasserver, behöver finnas i en annan distributionscykel ska den höra till en annan resursgrupp.
* En enskild resurs kan bara finnas i en resursgrupp.
* Du kan lägga till eller ta bort en resurs i en resursgrupp när som helst.
* Du kan flytta en resurs från en resursgrupp till en annan grupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](resource-group-move-resources.md).
* En resursgrupp kan innehålla resurser som finns i olika regioner.
* En resursgrupp kan användas för att definiera omfattningen av åtkomstkontrollen för administrativa åtgärder.
* En resurs kan interagera med resurser i andra resursgrupper. Denna interaktion är vanlig när de två resurserna är relaterade, men inte delar samma livscykel (till exempel webbappar som är anslutna till en databas).

När du skapar en resursgrupp måste du ange en plats för denna resursgrupp. Du kanske undrar, "varför behöver en resursgrupp en plats? Och om resurserna kan ha andra platser än resursgruppen, varför spelar platsen för gruppresursen alls någon roll?" Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

Om den resursgrupp regionen är inte tillgänglig för tillfället, kan du inte uppdatera resurser i resursgruppen eftersom metadata är inte tillgänglig. Resurser i andra regioner fortsätter att fungera som förväntat, men du kan inte uppdatera dem. För att minimera risken att leta upp din resursgrupp och resurser i samma region.

## <a name="resource-providers"></a>Resursproviders

Varje resursprovider tillhandahåller en uppsättning resurser och åtgärder för arbete med dessa resurser. Om du till exempel vill lagra nycklar och hemligheter arbetar du med resursprovidern **Microsoft.KeyVault**. Den här resursprovidern erbjuder en resurstyp som heter **vaults** (valv) för att skapa nyckelvalvet.

Namnet på en resurstyp skrivs i formatet: **{resursprovidern}/{resurstypen}**. Resurstypen för ett nyckelvalv är **Microsoft.KeyVault\vaults**.

Innan du börjar distribuera dina resurser bör du ha en bra uppfattning om vilka resursprovidrar som finns. Att känna till namnen på resursprovidrar och resurser gör det lättare för dig att definiera resurser som du vill distribuera till Azure. Du måste också känner till giltiga platser och API-versioner för varje resurstyp. Mer information finns i [Resursproviders och typer](resource-manager-supported-services.md).

Alla åtgärder som erbjuds av resursproviders finns i [REST API:er för Azure](/rest/api/azure/).

## <a name="template-deployment"></a>Malldistribution

Med Resource Manager kan du skapa en mall (i JSON-format) som definierar Azure-lösningens infrastruktur och konfiguration. Genom att använda en mall kan du distribuera lösningen flera gånger under dess livscykel och vara säker på att dina resurser distribueras konsekvent.

Information om mallformatet och hur du skapar det finns i [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md). JSON-syntaxen för resurstyper finns i [Define resources in Azure Resource Manager templates](/azure/templates/) (Definiera resurser i Azure Resource Manager-mallar).

Resource Manager bearbetar mallen som alla andra förfrågningar. Den tolkar mallen och konverterar dess syntax till REST API-åtgärder för lämpliga resursleverantörer. Till exempel när Resource Manager tar emot en mall med följande resursdefinition:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Den konverterar definitionen till följande REST API-åtgärd, som skickas till resursprovidern Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

Det är helt upp till dig hur du definierar mallar och resursgrupper och hur du vill hantera din lösning. Du kan till exempel distribuera programmet i tre nivåer via en enda mall till en enda resursgrupp.

![mall med tre nivåer](./media/resource-group-overview/3-tier-template.png)

Du behöver dock inte definiera hela infrastrukturen i en enda mall. Ofta är det praktiskt att dela in dina distributionskrav i en uppsättning riktade mallar för specifika ändamål. Du kan enkelt återanvända dessa mallar för olika lösningar. Om du vill distribuera en viss lösning skapar du en huvudmall som länkar alla nödvändiga mallar. Följande bild visar hur du distribuerar en lösning i tre nivåer via en överordnad mall som innehåller tre kapslade mallar.

![mall med kapslad nivå](./media/resource-group-overview/nested-tiers-template.png)

Om du tänker dig att dina nivåer har separata livscykler kan du distribuera dina tre nivåer till separata resursgrupper. Observera att resurserna fortfarande kan länkas till resurser i andra resursgrupper.

![nivåmall](./media/resource-group-overview/tier-templates.png)

Mer information om kapslade mallar finns i [Använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).

Azure Resource Manager analyserar beroenden för att säkerställa att resurserna skapas i rätt ordning. Om en resurs bygger på ett värde från en annan resurs (till exempel en virtuell dator som behöver ett lagringskonto för diskar) anger du ett beroende. Mer information finns i [Definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md).

Du kan också använda mallen för uppdateringar av infrastrukturen. Du kan till exempel lägga till en resurs till din lösning och lägga till konfigurationsregler för resurser som redan har distribuerats. Om mallen definierar en resurs som redan finns uppdaterar Resource Manager den befintliga resursen istället för att skapa en ny.

Resource Manager tillhandahåller tillägg för scenarier då du behöver ytterligare åtgärder, t.ex. för att installera viss programvara som inte ingår i installationsprogrammet. Om du redan använder en konfigurationshanteringstjänst, t.ex. DSC, Chef eller Puppet, kan du fortsätta arbeta med den tjänsten genom att använda tillägg. Information om tillägg för virtuella datorer finns i [Om tillägg och funktioner för virtuella datorer](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

När du skapar en lösning från portalen innehåller den automatiskt en distributionsmall. Du behöver inte skapa mallen från scratch. I stället kan du börja med mallen för din lösning och anpassa den efter dina specifika behov. Ett exempel finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](./resource-manager-quickstart-create-templates-use-the-portal.md). Du kan även hämta en mall för en befintlig resursgrupp antingen genom att antingen exportera resursgruppens aktuella tillstånd eller genom att visa mallen som används för en viss distribution. Att granska den [exporterade mallen](./manage-resource-groups-portal.md#export-resource-groups-to-templates) är ett bra sätt att lära sig mer om mallsyntaxen.

Slutligen blir mallen en del av källkoden för din app. Du kan checka in den i databasen för din källkod och uppdatera den i takt med att appen utvecklas. Du kan redigera mallen från Visual Studio.

När du har definierat mallen är du redo att distribuera resurser till Azure. Information om att distribuera resurserna finns i:

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](resource-group-template-deploy-cli.md)
* [Distribuera resurser med Resource Manager-mallar och Azure Portal](resource-group-template-deploy-portal.md)
* [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](resource-group-template-deploy-rest.md)

## <a name="safe-deployment-practices"></a>Säkra distributionsmetoder

När du distribuerar en komplex tjänst till Azure kan du behöva distribuera tjänsten till flera regioner och kontrollera dess tillstånd innan du fortsätter till nästa steg. Samordna en stegvis distribution av tjänsten genom att använda [Azures distributionshanterare](deployment-manager-overview.md). Genom att mellanlagra distribution av tjänsten kan du upptäcka potentiella problem innan den har distribuerats till alla regioner. Om du inte behöver dessa försiktighetsåtgärder är distributionsåtgärderna i föregående avsnitt det bästa alternativet.

Distributionshanteraren finns för närvarande i en privat förhandsversion.

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du veta hur du använder Azure Resource Manager för distribution, hantering och åtkomstkontroll av resurser i Azure. Gå vidare till nästa artikel om du vill lära dig hur du skapar din första Azure Resource Manager-mall.

> [!div class="nextstepaction"]
> [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](./resource-manager-quickstart-create-templates-use-the-portal.md)

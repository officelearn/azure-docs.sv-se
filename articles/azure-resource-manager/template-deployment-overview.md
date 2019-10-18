---
title: Azure Resource Manager-mallar
description: Beskriver hur du använder Azure Resource Manager mallar för att distribuera resurser.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: tomfitz
ms.openlocfilehash: 96f140cfa5e6151ad53ca242d1fc87ba3397316e
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "71300158"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Med flytten till molnet har många team antagit smidiga utvecklings metoder. Dessa team itererar snabbt. De måste distribuera sina lösningar flera gånger till molnet och känna till att deras infrastruktur är i ett tillförlitligt tillstånd. Eftersom infrastrukturen har blivit en del av den iterativa processen har indelningen mellan drift och utveckling försvunnit. Team behöver hantera infrastruktur-och program kod genom en enhetlig process.

För att möta dessa utmaningar kan du automatisera distributioner och använda infrastrukturen som kod. I kod definierar du den infrastruktur som måste distribueras. Infrastruktur koden blir en del av projektet. Precis som program kod, lagrar du infrastruktur koden i en käll lagrings plats och version. Vilken som helst av dina team kan köra koden och distribuera liknande miljöer.

Använd Azure Resource Manager mallar om du vill implementera infrastruktur som kod för dina Azure-lösningar. Mallen är en JavaScript Object Notation-fil (JSON) som definierar infrastrukturen och konfigurationen för ditt projekt. Mallen använder deklarativ syntax, som låter dig ange vad du vill distribuera utan att du behöver skriva sekvensen med programmerings kommandon för att skapa den. I mallen anger du de resurser som ska distribueras och egenskaperna för dessa resurser.

## <a name="why-choose-resource-manager-templates"></a>Varför ska du välja Resource Manager-mallar?

Om du försöker bestämma mellan att använda Resource Manager-mallar och en annan infrastruktur som kod tjänster, bör du tänka på följande fördelar med att använda mallar:

* **Deklarativ syntax**: med Resource Manager-mallar kan du skapa och distribuera en hel Azure-infrastruktur i en deklarativ miljö. Du kan till exempel distribuera inte bara virtuella datorer, utan även nätverks infrastrukturen, lagrings system och andra resurser som du kan behöva.

* **Upprepnings bara resultat**: Distribuera infrastrukturen flera gånger under utvecklings livs cykeln och få förtroende för att resurserna distribueras på ett konsekvent sätt. Mallarna är idempotenta, vilket innebär att du kan distribuera samma mall flera gånger och få samma resurs typer i samma tillstånd. Du kan utveckla en mall som representerar det önskade läget, i stället för att utveckla många separata mallar som representerar uppdateringar.

* **Orchestration**: du behöver inte bekymra dig om de komplexa funktionerna för beställning. Resource Manager dirigerar distributionen av beroende resurser så att de skapas i rätt ordning. När så är möjligt distribuerar Resource Manager resurserna parallellt så att distributionerna slutar snabbare än seriella distributioner. Du distribuerar mallen via ett kommando i stället för att använda flera tvingande kommandon.

   ![Malldistribution jämförelse](./media/template-deployment-overview/template-processing.png)

* **Inbyggd verifiering**: din mall distribueras bara när du har överfört verifieringen. Resource Manager kontrollerar mallen innan du påbörjar distributionen för att kontrol lera att distributionen kommer att lyckas. Distributionen är mindre troligt att stoppas i ett halv klart tillstånd.

* **Modulära filer**: du kan dela upp dina mallar i mindre, återanvändbara komponenter och länka dem tillsammans vid distributions tiden. Du kan också kapsla en mall inuti en annan mall.

* **Skapa en Azure-resurs**: du kan använda nya Azure-tjänster och-funktioner direkt i mallar. Så snart en resurs leverantör inför nya resurser kan du distribuera dessa resurser via mallar. Du behöver inte vänta på att verktyg eller moduler uppdateras innan du använder de nya tjänsterna.

* **Spårade distributioner**: i Azure Portal kan du granska distributions historiken och få information om mall distributionen. Du kan se den mall som har distribuerats, de parameter värden som angavs i och eventuella värden. Annan infrastruktur som kod tjänster spåras inte via portalen.

   ![Distributions historik](./media/template-deployment-overview/deployment-history.png)

* **Princip som kod**: [Azure policy](../governance/policy/overview.md) är en princip som kod ramverk för att automatisera styrningen. Om du använder Azure-principer görs princip reparationer på icke-kompatibla resurser när de distribueras med hjälp av mallar.

* **Distributions ritningar**: du kan dra nytta av [skisser](../governance/blueprints/overview.md) som tillhandahålls av Microsoft för att uppfylla regler och efterlevnad. Dessa ritningar innehåller färdiga mallar för olika arkitekturer.

* **CI/CD-integrering**: du kan integrera mallar i verktygen för kontinuerlig integrering och distribution (CI/CD), vilket kan automatisera dina versions pipeliner för snabba och tillförlitliga program-och infrastruktur uppdateringar. Genom att använda Azure-DevOps och Resource Manager-postmall kan du använda Azure-pipelines för att kontinuerligt bygga och Distribuera Azure Resource Manager mal Lav projekt. Läs mer i [vs Project med pipelines](./vs-resource-groups-project-devops-pipelines.md) och [kontinuerlig integrering med Azure-pipelines](./resource-manager-tutorial-use-azure-pipelines.md).

* **Exporterad kod**: du kan hämta en mall för en befintlig resurs grupp genom att antingen exportera det aktuella läget för resurs gruppen eller Visa mallen som används för en viss distribution. Att granska den [exporterade mallen](export-template-portal.md) är ett bra sätt att lära sig mer om mallsyntaxen.

* **Redigerings verktyg**: du kan skapa mallar med [Visual Studio Code](resource-manager-tools-vs-code.md) och verktyget mall-tillägg. Du får IntelliSense, syntaxkontroll, direkt hjälp och många andra språk funktioner. Förutom Visual Studio Code kan du också använda [Visual Studio](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="template-file"></a>Mallfil

I mallen kan du skriva [mallar](template-expressions.md) som utökar funktionerna i JSON. Dessa uttryck använder de [funktioner](resource-group-template-functions.md) som tillhandahålls av Resource Manager.

Mallen innehåller följande avsnitt:

* [Parametrar](template-parameters.md) -ange värden under distributionen som tillåter att samma mall används med olika miljöer.

* [Variabler](template-variables.md) – definiera värden som återanvänds i dina mallar. De kan konstrueras från parameter värden.

* [Användardefinierade funktioner](template-user-defined-functions.md) – skapa anpassade funktioner som fören klar din mall.

* [Resurser](resource-group-authoring-templates.md#resources) – ange vilka resurser som ska distribueras.

* [Utdata](template-outputs.md) – returnera värden från de distribuerade resurserna.

## <a name="template-deployment-process"></a>Malldistribution process

När du distribuerar en mall konverterar Resource Manager mallen till REST API åtgärder. Till exempel när Resource Manager tar emot en mall med följande resursdefinition:

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

## <a name="template-design"></a>Mall design

Det är helt upp till dig hur du definierar mallar och resursgrupper och hur du vill hantera din lösning. Du kan till exempel distribuera programmet i tre nivåer via en enda mall till en enda resursgrupp.

![mall med tre nivåer](./media/template-deployment-overview/3-tier-template.png)

Du behöver dock inte definiera hela infrastrukturen i en enda mall. Ofta är det praktiskt att dela in dina distributionskrav i en uppsättning riktade mallar för specifika ändamål. Du kan enkelt återanvända dessa mallar för olika lösningar. Om du vill distribuera en viss lösning skapar du en huvudmall som länkar alla nödvändiga mallar. Följande bild visar hur du distribuerar en lösning i tre nivåer via en överordnad mall som innehåller tre kapslade mallar.

![mall med kapslad nivå](./media/template-deployment-overview/nested-tiers-template.png)

Om du tänker dig att dina nivåer har separata livscykler kan du distribuera dina tre nivåer till separata resursgrupper. Observera att resurserna fortfarande kan länkas till resurser i andra resursgrupper.

![nivåmall](./media/template-deployment-overview/tier-templates.png)

Mer information om kapslade mallar finns i [Använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Nästa steg

* Information om egenskaperna i mallfiler finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](resource-group-authoring-templates.md).
* Information om hur du exporterar mallar finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md).

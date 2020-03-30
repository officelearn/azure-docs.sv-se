---
title: Översikt över mallar
description: Beskriver fördelarna med Azure Resource Manager-mallar för distribution av resurser.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 4570f5471ef6baf6f3f4a920be4d93c3f5a90438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258132"
---
# <a name="what-are-arm-templates"></a>Vad är ARM-mallar?

Med flytten till molnet har många team antagit agila utvecklingsmetoder. Dessa lag itererar snabbt. De måste upprepade gånger distribuera sina lösningar till molnet och veta att deras infrastruktur är i ett tillförlitligt tillstånd. I takt med att infrastrukturen har blivit en del av iterativ processen har uppdelningen mellan drift och utveckling försvunnit. Team måste hantera infrastruktur och programkod genom en enhetlig process.

För att möta dessa utmaningar kan du automatisera distributioner och använda infrastrukturen som kod. I kod definierar du den infrastruktur som måste distribueras. Infrastrukturkoden blir en del av projektet. Precis som programkoden lagrar du infrastrukturkoden i en källdatabas och version av den. Vem som helst i teamet kan köra koden och distribuera liknande miljöer.

Om du vill implementera infrastruktur som kod för dina Azure-lösningar använder du AZURE Resource Manager (ARM) mallar. Mallen är en JSON-fil (JavaScript Object Notation) som definierar infrastrukturen och konfigurationen för projektet. Mallen använder deklarativ syntax, vilket gör att du kan ange vad du tänker distribuera utan att behöva skriva sekvensen av programmeringskommandon för att skapa den. I mallen anger du de resurser som ska distribueras och egenskaperna för dessa resurser.

## <a name="why-choose-arm-templates"></a>Varför välja ARM-mallar?

Om du försöker välja mellan att använda ARM-mallar och någon av de andra infrastrukturerna som kodtjänster bör du tänka på följande fördelar med att använda mallar:

* **Deklarativ syntax**: Med ARM-mallar kan du skapa och distribuera en hel Azure-infrastruktur deklarativt. Du kan till exempel distribuera inte bara virtuella datorer, utan även nätverksinfrastruktur, lagringssystem och andra resurser som du kan behöva.

* **Repeterbara resultat:** Distribuera din infrastruktur upprepade gånger under hela utvecklingslivscykeln och ha förtroende för att dina resurser distribueras på ett konsekvent sätt. Mallar är idempotenta, vilket innebär att du kan distribuera samma mall många gånger och få samma resurstyper i samma tillstånd. Du kan utveckla en mall som representerar önskat tillstånd, i stället för att utveckla många separata mallar för att representera uppdateringar.

* **Orkestrering:** Du behöver inte oroa dig för komplexiteten i att beställa operationer. Resurshanteraren dirigerar distributionen av resurser som är beroende av varandra så att de skapas i rätt ordning. När det är möjligt distribuerar Resource Manager resurser parallellt så att distributionerna slutförs snabbare än seriedistributioner. Du distribuerar mallen via ett kommando, i stället för genom flera tvingande kommandon.

   ![Jämförelse av malldistribution](./media/overview/template-processing.png)

* **Inbyggd validering**: Mallen distribueras först efter validering. Resource Manager kontrollerar mallen innan distributionen startas för att se till att distributionen lyckas. Din distribution är mindre benägna att stoppa i ett halvfärdigt tillstånd.

* **Modulära filer:** Du kan dela upp mallarna i mindre, återanvändbara komponenter och länka ihop dem vid distributionen. Du kan också kapsla en mall i en annan mall.

* **Skapa en Azure-resurs:** Du kan omedelbart använda nya Azure-tjänster och -funktioner i mallar. Så fort en resursprovider introducerar nya resurser kan du distribuera dessa resurser via mallar. Du behöver inte vänta på att verktyg eller moduler ska uppdateras innan du använder de nya tjänsterna.

* **Spårade distributioner**: I Azure-portalen kan du granska distributionshistoriken och få information om malldistributionen. Du kan se mallen som har distribuerats, parametervärdena skickades in och eventuella utdatavärden. Annan infrastruktur som kodtjänster spåras inte via portalen.

   ![Distributionshistorik](./media/overview/deployment-history.png)

* **Princip som kod:** [Azure Policy](../../governance/policy/overview.md) är en princip som kodramverk för att automatisera styrning. Om du använder Azure-principer utförs principsanering på icke-kompatibla resurser när du distribueras via mallar.

* **Distributionsritningar**: Du kan dra nytta av [ritningar](../../governance/blueprints/overview.md) som tillhandahålls av Microsoft för att uppfylla lagstadgade och efterlevnadsstandarder. Dessa ritningar innehåller färdiga mallar för olika arkitekturer.

* **CI/CD-integrering:** Du kan integrera mallar i dina ci/cd-verktyg (continuous integration and continuous deployment), som kan automatisera dina versionspipelor för snabba och tillförlitliga program- och infrastrukturuppdateringar. Genom att använda Azure DevOps och Resource Manager-mallaktivitet kan du använda Azure Pipelines för att kontinuerligt skapa och distribuera ARM-mallprojekt. Mer information finns i [VS-projekt med pipelines](add-template-to-azure-pipelines.md) och [kontinuerlig integrering med Azure Pipelines](template-tutorial-use-azure-pipelines.md).

* **Exportbar kod**: Du kan hämta en mall för en befintlig resursgrupp genom att antingen exportera resursgruppens aktuella tillstånd eller visa mallen som används för en viss distribution. Att granska den [exporterade mallen](export-template-portal.md) är ett bra sätt att lära sig mer om mallsyntaxen.

* **Redigeringsverktyg:** Du kan skapa mallar med [Visual Studio-kod](use-vs-code-to-create-template.md) och mallverktygstillägget. Du får intellisense, syntaxmarkering, in-line hjälp och många andra språkfunktioner. Förutom Visual Studio-kod kan du även använda [Visual Studio](create-visual-studio-deployment-project.md).

## <a name="template-file"></a>Mallfil

I mallen kan du skriva [malluttryck](template-expressions.md) som utökar JSON:s funktioner. Dessa uttryck använder sig av de [funktioner](template-functions.md) som tillhandahålls av Resource Manager.

Mallen har följande avsnitt:

* [Parametrar](template-parameters.md) - Ange värden under distributionen som gör att samma mall kan användas med olika miljöer.

* [Variabler](template-variables.md) - Definiera värden som återanvänds i mallarna. De kan konstrueras utifrån parametervärden.

* [Användardefinierade funktioner](template-user-defined-functions.md) - Skapa anpassade funktioner som förenklar mallen.

* [Resurser](template-syntax.md#resources) - Ange de resurser som ska distribueras.

* [Utdata](template-outputs.md) - Returnera värden från de distribuerade resurserna.

## <a name="template-deployment-process"></a>Distributionsprocessen för mall

När du distribuerar en mall konverterar Resource Manager mallen till REST API-åtgärder. Till exempel när Resource Manager tar emot en mall med följande resursdefinition:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
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
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>Malldesign

Det är helt upp till dig hur du definierar mallar och resursgrupper och hur du vill hantera din lösning. Du kan till exempel distribuera programmet i tre nivåer via en enda mall till en enda resursgrupp.

![mall med tre nivåer](./media/overview/3-tier-template.png)

Du behöver dock inte definiera hela infrastrukturen i en enda mall. Ofta är det praktiskt att dela in dina distributionskrav i en uppsättning riktade mallar för specifika ändamål. Du kan enkelt återanvända dessa mallar för olika lösningar. Om du vill distribuera en viss lösning skapar du en huvudmall som länkar alla nödvändiga mallar. Följande bild visar hur du distribuerar en lösning i tre nivåer via en överordnad mall som innehåller tre kapslade mallar.

![mall med kapslad nivå](./media/overview/nested-tiers-template.png)

Om du tänker dig att dina nivåer har separata livscykler kan du distribuera dina tre nivåer till separata resursgrupper. Observera att resurserna fortfarande kan länkas till resurser i andra resursgrupper.

![nivåmall](./media/overview/tier-templates.png)

Mer information om kapslade mallar finns i [Använda länkade mallar med Azure Resource Manager](linked-templates.md).

## <a name="next-steps"></a>Nästa steg

* En steg-för-steg-självstudiekurs som vägleder dig genom processen att skapa en mall finns i [Självstudiekurs: Skapa och distribuera din första ARM-mall](template-tutorial-create-first-template.md).
* Information om egenskaperna i mallfiler finns i [Förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Mer information om hur du exporterar mallar finns i [Snabbstart: Skapa och distribuera ARM-mallar med hjälp av Azure-portalen](quickstart-create-templates-use-the-portal.md).

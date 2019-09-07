---
title: Azure Resource Manager-mallar
description: Beskriver hur du använder Azure Resource Manager mallar för att distribuera resurser.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4f273a04322246a2b4112c0b5b8a062732bab555
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390716"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Med flytten till molnet har många team antagit smidiga utvecklings metoder. Dessa team itererar snabbt. De behöver flera gånger och tillförlitligt distribuera sina lösningar till molnet. Indelningen mellan drift och utveckling har försvunnit eftersom team behöver hantera infrastruktur och käll kod i en enda process.

En lösning på dessa utmaningar är att automatisera distributionen och använda infrastrukturen som kod. Du kan använda kod för att definiera vad som behöver distribueras och hantera koden genom samma process som käll koden. Du lagrar infrastrukturen som kod i käll databasen och versionen.

Med Azure Resource Manager kan du implementera infrastruktur som kod via Resource Manager-mallar. Mallen är en JavaScript Object Notation-fil (JSON) som innehåller infrastrukturen och konfigurationen för din Azure-lösning. Mallen använder deklarativ syntax, som låter dig ange vad du vill distribuera utan att du behöver skriva sekvensen med programmerings kommandon för att skapa den. I mallen anger du de resurser som ska distribueras och egenskaperna för dessa resurser.

## <a name="benefits-of-resource-manager-templates"></a>Fördelar med Resource Manager-mallar

Resource Manager-mallar ger flera fördelar. Du kan:

* Distribuera, hantera och övervaka alla resurser för din lösning som en grupp, i stället för att hantera resurserna individuellt.

* Distribuera din lösning flera gånger under utvecklings livs cykeln och få förtroende för att dina resurser distribueras i ett konsekvent tillstånd.

* Hantera din infrastruktur genom deklarativ mallar i stället för skript.

* Definiera beroenden mellan resurser så att de distribueras i rätt ordning.

* Dra nytta av nya versioner och tjänster direkt eftersom det inte finns något mellanliggande arbete som andra parter behöver.

## <a name="template-file"></a>Mallfil

I mallen kan du skriva [mallar](template-expressions.md) som utökar funktionerna i JSON. Dessa uttryck använder de [funktioner](resource-group-template-functions.md) som tillhandahålls av Resource Manager.

Mallen innehåller följande avsnitt:

* [Parametrar](template-parameters.md) -ange värden under distributionen som tillåter att samma mall används med olika miljöer.

* [Variabler](template-variables.md) – definiera värden som används i mallarna.

* [Användardefinierade funktioner](template-user-defined-functions.md) – skapa anpassade funktioner som fören klar din mall.

* [Resurser](resource-group-authoring-templates.md#resources) – ange vilka resurser som ska distribueras.

* [Utdata](template-outputs.md) – returnera värden från de distribuerade resurserna.

## <a name="dependencies"></a>Beroenden

Azure Resource Manager analyserar beroenden för att säkerställa att resurserna skapas i rätt ordning. Om en resurs bygger på ett värde från en annan resurs (till exempel en virtuell dator som behöver ett lagringskonto för diskar) anger du ett beroende. Mer information finns i [Definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md).

## <a name="conditional-deployment"></a>Villkorlig distribution

Du kan lägga till en resurs i mallen och även distribuera den. Normalt skickar du ett parameter värde som anger om resursen måste distribueras. Mer information finns i [villkorlig distribution i Resource Manager-mallar](conditional-resource-deployment.md).

## <a name="create-multiple-instances"></a>Skapa flera instanser

I stället för att upprepa block med JSON många gånger i mallen kan du använda ett kopierings element för att ange mer än en instans av en variabel, egenskap eller resurs. Mer information finns i [resurs, egenskap eller variabel iteration i Azure Resource Manager mallar](resource-group-create-multiple.md).

## <a name="export-templates"></a>Exportera mallar

Du kan hämta en mall för en befintlig resurs grupp genom att antingen exportera det aktuella läget för resurs gruppen eller Visa den mall som används för en viss distribution. Att granska den [exporterade mallen](export-template-portal.md) är ett bra sätt att lära sig mer om mallsyntaxen.

När du skapar en lösning från portalen innehåller den automatiskt en distributionsmall. Du behöver inte skapa mallen från scratch. I stället kan du börja med mallen för din lösning och anpassa den efter dina specifika behov. Ett exempel finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](./resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="template-deployment-process"></a>Malldistribution process

När du distribuerar en mall parsar Resource Manager mallen och konverterar dess syntax till REST API åtgärder. Till exempel när Resource Manager tar emot en mall med följande resursdefinition:

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

Om resursen redan finns i resurs gruppen och begäran inte innehåller några uppdateringar av egenskaperna vidtas ingen åtgärd. Om resursen finns men egenskaperna har ändrats, uppdateras den befintliga resursen. Om resursen inte finns skapas den nya resursen. Den här metoden gör det säkert att du omdistribuerar en mall och vet att resurserna är i ett konsekvent tillstånd.

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
* Om du vill komma igång med att utveckla mallar kan [du skapa Azure Resource Manager mallar i använda Visual Studio Code](resource-manager-tools-vs-code.md).



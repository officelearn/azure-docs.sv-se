---
title: Azure Resource Manager-mallar
description: Beskriver hur du använder Azure Resource Manager mallar för att distribuera resurser.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802013"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Med flytten till molnet har många team antagit smidiga utvecklings metoder. Dessa team itererar snabbt. De måste distribuera sina lösningar flera gånger till molnet och känna till att deras infrastruktur är i ett tillförlitligt tillstånd. Eftersom infrastrukturen har blivit en del av den iterativa processen har indelningen mellan drift och utveckling försvunnit. Team behöver hantera infrastruktur-och program kod genom en enhetlig process.

För att möta dessa utmaningar kan du automatisera distributioner och använda infrastrukturen som kod. I kod definierar du den infrastruktur som måste distribueras. Infrastruktur koden blir en del av projektet. Precis som program kod, lagrar du infrastruktur koden i en käll lagrings plats och version. Vilken som helst av dina team kan köra koden och distribuera liknande miljöer.

Använd Azure Resource Manager mallar om du vill implementera infrastruktur som kod för dina Azure-lösningar. Mallen är en JavaScript Object Notation-fil (JSON) som definierar infrastrukturen och konfigurationen för ditt projekt. Mallen använder deklarativ syntax, som låter dig ange vad du vill distribuera utan att du behöver skriva sekvensen med programmerings kommandon för att skapa den. I mallen anger du de resurser som ska distribueras och egenskaperna för dessa resurser.

## <a name="benefits-of-resource-manager-templates"></a>Fördelar med Resource Manager-mallar

Resource Manager-mallar ger följande fördelar:

* Distribuera, hantera och övervaka alla resurser för din lösning som en grupp, i stället för att hantera resurserna individuellt.

* Distribuera din lösning flera gånger under utvecklings livs cykeln och få förtroende för att dina resurser distribueras i ett konsekvent tillstånd.

* Hantera din infrastruktur genom deklarativ mallar i stället för skript.

Om du försöker bestämma mellan att använda Resource Manager-mallar eller någon annan infrastruktur som kod tjänster, bör du tänka på följande fördelar mallar har över dessa tjänster:

* Nya Azure-tjänster och-funktioner är omedelbart tillgängliga i mallar. Så snart en resurs leverantör inför nya resurser kan du distribuera dessa resurser via mallar. Med annan infrastruktur som kod tjänster måste du vänta på att tredje part implementerar gränssnitt för de nya resurserna.

* Mall distributioner hanteras genom en enda sändning av mallen, i stället för via flera tvingande kommandon. Resource Manager dirigerar distributionen av beroende resurser så att de skapas i rätt ordning. Den tolkar mallen och fastställer rätt distributions ordning baserat på referenser mellan resurser.

   ![Malldistribution jämförelse](./media/template-deployment-overview/template-processing.png)

* Mall distributioner spåras i Azure Portal. Du kan granska distributions historiken och få information om mall distributionen. Du kan se den mall som har distribuerats, de parameter värden som angavs i och eventuella värden. Annan infrastruktur som kod tjänster spåras inte via portalen.

   ![Distributionshistorik](./media/template-deployment-overview/deployment-history.png)

* Mall distributioner genomgår kontroll före flygning. Resource Manager kontrollerar mallen innan du påbörjar distributionen för att kontrol lera att distributionen kommer att lyckas. Distributionen är mindre troligt att stoppas i ett halv klart tillstånd.

* Om du använder [Azure-principer](../governance/policy/overview.md)görs princip reparationer på icke-kompatibla resurser när de distribueras med hjälp av mallar.

* Microsoft tillhandahåller distributions [ritningar](../governance/blueprints/overview.md) för att uppfylla regler och efterlevnad. Dessa ritningar innehåller färdiga mallar för olika arkitekturer.

## <a name="idempotent"></a>Idempotenta

Idempotenta innebär bara att du kan köra samma åtgärder flera gånger och få samma resultat. Distribution av en Resource Manager-mall är idempotenta. Du kan distribuera samma mall flera gånger och få samma resurs typ i samma tillstånd. Det här konceptet är viktigt eftersom det innebär att du får konsekventa resultat om du omdistribuerar en mall till en befintlig resurs grupp eller distribuerar en mall till en ny resurs grupp.

Låt oss anta att du har distribuerat tre resurser till en resurs grupp och sedan bestämmer dig för att lägga till en fjärde resurs. I stället för att skapa en ny mall som bara innehåller den nya resursen kan du lägga till den fjärde resursen i din befintliga mall. När du distribuerar den nya mallen till resurs gruppen som redan har tre resurser, så tar Resource Manager upp vilka åtgärder som ska vidtas.

Om resursen finns i resurs gruppen och begäran inte innehåller några uppdateringar av egenskaperna vidtas ingen åtgärd. Om resursen finns men egenskaperna har ändrats, uppdateras den befintliga resursen. Om resursen inte finns skapas den nya resursen.

Du är säker på att när distributionen är färdig är resurserna alltid i förväntat tillstånd.

## <a name="template-file"></a>Mallfil

I mallen kan du skriva [mallar](template-expressions.md) som utökar funktionerna i JSON. Dessa uttryck använder de [funktioner](resource-group-template-functions.md) som tillhandahålls av Resource Manager.

Mallen innehåller följande avsnitt:

* [Parametrar](template-parameters.md) -ange värden under distributionen som tillåter att samma mall används med olika miljöer.

* [Variabler](template-variables.md) – definiera värden som återanvänds i dina mallar. De kan konstrueras från parameter värden.

* [Användardefinierade funktioner](template-user-defined-functions.md) – skapa anpassade funktioner som fören klar din mall.

* [Resurser](resource-group-authoring-templates.md#resources) – ange vilka resurser som ska distribueras.

* [Utdata](template-outputs.md) – returnera värden från de distribuerade resurserna.

## <a name="template-features"></a>Mall funktioner

Resource Manager analyserar beroenden för att säkerställa att resurser skapas i rätt ordning. De flesta beroenden fastställs implicit. Du kan dock uttryckligen ange ett beroende för att se till att en resurs distribueras före en annan resurs. Mer information finns i [Definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md).

Du kan lägga till en resurs i mallen och även distribuera den. Normalt skickar du ett parameter värde som anger om resursen måste distribueras. Mer information finns i [villkorlig distribution i Resource Manager-mallar](conditional-resource-deployment.md).

I stället för att upprepa block med JSON många gånger i mallen kan du använda ett kopierings element för att ange mer än en instans av en variabel, egenskap eller resurs. Mer information finns i [resurs, egenskap eller variabel iteration i Azure Resource Manager mallar](resource-group-create-multiple.md).

## <a name="export-templates"></a>Exportera mallar

Du kan hämta en mall för en befintlig resurs grupp genom att antingen exportera det aktuella läget för resurs gruppen eller Visa den mall som används för en viss distribution. Att granska den [exporterade mallen](export-template-portal.md) är ett bra sätt att lära sig mer om mallsyntaxen.

När du skapar en lösning från portalen innehåller den automatiskt en distributionsmall. Du behöver inte skapa mallen från scratch. I stället kan du börja med mallen för din lösning och anpassa den efter dina specifika behov. Ett exempel finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](./resource-manager-quickstart-create-templates-use-the-portal.md).

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
* Om du vill komma igång med att utveckla mallar kan [du skapa Azure Resource Manager mallar i använda Visual Studio Code](resource-manager-tools-vs-code.md).
* En introduktion till Resource Manager-tjänsten, inklusive dess hanterings funktioner, finns i [Azure Resource Manager översikt](resource-group-overview.md).


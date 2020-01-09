---
title: Det gick inte att hitta resursen
description: Beskriver hur du löser fel när det inte går att hitta en resurs när du distribuerar med en Azure Resource Manager-mall.
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.openlocfilehash: 832dc15f81c0fd815072b9e95920a4388a94cb0b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477764"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Det gick inte att hitta fel för Azure-resurser

I den här artikeln beskrivs de fel som kan uppstå när det inte går att hitta en resurs under distributionen.

## <a name="symptom"></a>Symptom

När din mall innehåller namnet på en resurs som inte kan lösas visas ett fel som liknar följande:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Om du använder [referens](template-functions-resource.md#reference) -eller [listnycklar](template-functions-resource.md#listkeys) -funktionerna med en resurs som inte kan lösas visas följande fel meddelande:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Orsak

Resource Manager måste hämta egenskaperna för en resurs, men kan inte identifiera resursen i din prenumeration.

## <a name="solution-1---set-dependencies"></a>Lösning 1 – ange beroenden

Om du försöker distribuera den saknade resursen i mallen kontrollerar du om du behöver lägga till ett beroende. Resource Manager optimerar distributionen genom att skapa resurser parallellt, när så är möjligt. Om en resurs måste distribueras efter en annan resurs måste du använda **dependsOn** -elementet i mallen. Om du till exempel distribuerar en webbapp måste App Service plan finnas. Om du inte har angett att webbappen är beroende av App Service plan, skapar Resource Manager båda resurserna på samma gång. Du får ett fel meddelande om att det inte finns någon App Service plan resurs, eftersom den inte finns ännu vid försök att ange en egenskap för webbappen. Du undviker det här felet genom att ange beroendet i webbappen.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Men du vill undvika att ange beroenden som inte behövs. När du har onödiga beroenden kan du förlänga varaktigheten för distributionen genom att förhindra att resurser som inte är beroende av varandra distribueras parallellt. Dessutom kan du skapa cirkulära beroenden som blockerar distributionen. Funktionen [Reference](template-functions-resource.md#reference) och [list *](template-functions-resource.md#list) Functions skapar ett implicit beroende av den refererade resursen när resursen distribueras i samma mall och refereras till av sitt namn (inte resurs-ID). Det kan därför finnas fler beroenden än de beroenden som anges i egenskapen **dependsOn** . Funktionen [resourceId](template-functions-resource.md#resourceid) skapar inte ett implicit beroende eller kontrollerar att resursen finns. Funktionen [Reference](template-functions-resource.md#reference) och [list *](template-functions-resource.md#list) Functions skapar inte ett implicit beroende när resursen refereras till av resurs-ID: t. Om du vill skapa ett implicit beroende skickar du namnet på den resurs som har distribuerats i samma mall.

När du ser beroende problem måste du få insikt i ordningen på resurs distributionen. Så här visar du ordningen på distributions åtgärder:

1. Välj distributions historik för resurs gruppen.

   ![Välj distributions historik](./media/error-not-found/select-deployment.png)

2. Välj en distribution från historiken och välj **händelser**.

   ![Välj distributions händelser](./media/error-not-found/select-deployment-events.png)

3. Granska händelse ordningen för varje resurs. Var uppmärksam på status för varje åtgärd. Följande bild visar till exempel tre lagrings konton som distribueras parallellt. Observera att de tre lagrings kontona startas samtidigt.

   ![parallell distribution](./media/error-not-found/deployment-events-parallel.png)

   Nästa bild visar tre lagrings konton som inte distribueras parallellt. Det andra lagrings kontot är beroende av det första lagrings kontot och det tredje lagrings kontot är beroende av det andra lagrings kontot. Det första lagrings kontot har startats, godkänts och slutförts innan nästa påbörjas.

   ![sekventiell distribution](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Lösning 2 – Hämta resurs från en annan resurs grupp

När resursen finns i en annan resurs grupp än den som distribueras till, använder du [funktionen resourceId](template-functions-resource.md#resourceid) för att hämta det fullständigt kvalificerade namnet på resursen.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Lösning 3 – kontrol lera referens funktion

Leta efter ett uttryck som innehåller [referens](template-functions-resource.md#reference) funktionen. Värdena som du anger varierar beroende på om resursen finns i samma mall, resurs grupp och prenumeration. Dubbel kontroll av att du tillhandahåller de parameter värden som krävs för ditt scenario. Om resursen finns i en annan resurs grupp anger du det fullständiga resurs-ID: t. Om du till exempel vill referera till ett lagrings konto i en annan resurs grupp använder du:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```
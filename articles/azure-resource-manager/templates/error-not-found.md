---
title: Det gick inte att hitta resursen fel
description: Beskriver hur du löser fel när en resurs inte kan hittas när du distribuerar med en Azure Resource Manager-mall.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773250"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Lös fel hittades inte för Azure-resurser

I den här artikeln beskrivs de fel som kan visas när en resurs inte kan hittas under distributionen.

## <a name="symptom"></a>Symptom

När mallen innehåller namnet på en resurs som inte kan matchas visas ett fel som liknar:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Om du använder [funktionerna referens](template-functions-resource.md#reference) eller [listKeys](template-functions-resource.md#listkeys) med en resurs som inte kan matchas visas följande fel:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Orsak

Resource Manager måste hämta egenskaperna för en resurs, men kan inte identifiera resursen i din prenumeration.

## <a name="solution-1---set-dependencies"></a>Lösning 1 - ange beroenden

Om du försöker distribuera den resurs som saknas i mallen kontrollerar du om du behöver lägga till ett samband. Resource Manager optimerar distributionen genom att skapa resurser parallellt, när det är möjligt. Om en resurs måste distribueras efter en annan resurs måste du använda **elementet dependsOn** i mallen. När du till exempel distribuerar en webbapp måste App Service-planen finnas. Om du inte har angett att webbappen är beroende av App Service-planen skapar Resource Manager båda resurserna samtidigt. Du får ett felmeddelande om att apptjänstplanresursen inte kan hittas, eftersom den ännu inte finns när du försöker ange en egenskap i webbappen. Du förhindrar det här felet genom att ange beroendet i webbappen.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Men du vill undvika att ange beroenden som inte behövs. När du har onödiga beroenden förlänger du distributionens varaktighet genom att förhindra att resurser som inte är beroende av varandra distribueras parallellt. Dessutom kan du skapa cirkulära beroenden som blockerar distributionen. [Referensfunktionen](template-functions-resource.md#reference) och [list*-funktionerna](template-functions-resource.md#list) skapar ett implicit beroende av den refererade resursen när resursen distribueras i samma mall och refereras av dess namn (inte resurs-ID). Därför kan du ha fler beroenden än de beroenden som anges i egenskapen **dependsOn.** [Resursfunktionen](template-functions-resource.md#resourceid) skapar inte ett implicit beroende eller verifierar att resursen finns. [Referensfunktionen](template-functions-resource.md#reference) och [listfunktionerna*](template-functions-resource.md#list) skapar inte ett implicit beroende när resursen refereras till av dess resurs-ID. Om du vill skapa ett implicit samband skickar du namnet på resursen som distribueras i samma mall.

När du ser beroendeproblem måste du få insikt i ordningen för resursdistribution. Så här visar du ordningen på distributionsåtgärder:

1. Välj distributionshistorik för resursgruppen.

   ![välj distributionshistorik](./media/error-not-found/select-deployment.png)

2. Välj en distribution från historiken och välj **Händelser**.

   ![välj distributionshändelser](./media/error-not-found/select-deployment-events.png)

3. Undersök händelseförloppet för varje resurs. Var uppmärksam på statusen för varje operation. Följande bild visar till exempel tre lagringskonton som distribueras parallellt. Observera att de tre lagringskontona startas samtidigt.

   ![parallell distribution](./media/error-not-found/deployment-events-parallel.png)

   Nästa bild visar tre lagringskonton som inte distribueras parallellt. Det andra lagringskontot beror på det första lagringskontot och det tredje lagringskontot beror på det andra lagringskontot. Det första lagringskontot startas, accepteras och slutförs innan nästa startas.

   ![sekventiell distribution](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Lösning 2 - hämta resurs från olika resursgrupper

När resursen finns i en annan resursgrupp än den som distribueras till använder du [resursfunktionen](template-functions-resource.md#resourceid) För att hämta resursens fullständigt kvalificerade namn.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Lösning 3 - kontrollera referensfunktionen

Leta efter ett uttryck som innehåller [referensfunktionen.](template-functions-resource.md#reference) De värden du anger varierar beroende på om resursen finns i samma mall, resursgrupp och prenumeration. Dubbelkolla att du tillhandahåller de parametervärden som krävs för ditt scenario. Om resursen finns i en annan resursgrupp anger du det fullständiga resurs-ID:et. Om du till exempel vill referera till ett lagringskonto i en annan resursgrupp använder du:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Lösning 4 - få hanterad identitet från resurs

Om du distribuerar en resurs som implicit skapar en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md)måste du vänta tills resursen har distribuerats innan du hämtar värden på den hanterade identiteten. Om du skickar det hanterade identitetsnamnet till [referensfunktionen](template-functions-resource.md#reference) försöker Resource Manager lösa referensen innan resursen och identiteten distribueras. Skicka i stället namnet på den resurs som identiteten tillämpas på. Den här metoden säkerställer att resursen och den hanterade identiteten distribueras innan Resource Manager löser referensfunktionen.

I referensfunktionen använder `Full` du för att hämta alla egenskaper, inklusive den hanterade identiteten.

Om du till exempel vill hämta klient-ID:t för en hanterad identitet som tillämpas på en skaluppsättning för virtuella datorer använder du:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```
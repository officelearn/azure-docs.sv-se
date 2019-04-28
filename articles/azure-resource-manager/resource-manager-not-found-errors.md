---
title: Azure-resurs hittades inte-fel | Microsoft Docs
description: Beskriver hur du löser fel när en resurs inte kan hittas.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: 0dfbfa73009b5fa47dd7bb0804b612d0c2ad0a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61062300"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Lös hittas inte för Azure-resurser

Den här artikeln beskriver de fel som du kan se när en resurs går inte att hitta under distributionen.

## <a name="symptom"></a>Symtom

När mallen innehåller namnet på en resurs som inte kan matchas, visas ett fel som liknar:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Om du använder den [referens](resource-group-template-functions-resource.md#reference) eller [Listnycklar](resource-group-template-functions-resource.md#listkeys) funktioner med en resurs som inte kan matchas, du får följande fel:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Orsak

Resource Manager behöver hämta egenskaperna för en resurs, men det går inte att identifiera resursen i din prenumeration.

## <a name="solution-1---set-dependencies"></a>Lösning 1 – ange beroenden

Om du försöker distribuera den saknade resursen i mallen kan du kontrollera om du behöver lägga till ett beroende. Resource Manager optimerar distribution genom att skapa resurser parallellt vid behov. Om en resurs måste distribueras efter en annan resurs, måste du använda den **dependsOn** elementet i mallen. App Service-planen måste till exempel finnas när du distribuerar en webbapp. Om du inte har angett att webbappen är beroende av App Service-planen, skapar Resource Manager båda resurserna på samma gång. Du får ett felmeddelande om att App Service-plan resursen inte kan hittas, eftersom det inte finns ännu vid försök att ange en egenskap för webbappen. Du kan förhindra att det här felet genom att ange beroendet i webbappen.

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

Men du vill undvika att beroenden som inte behövs. När du har onödiga beroenden kan förlänga varaktigheten för distributionen genom att förhindra att resurser som inte är beroende av varandra från att distribueras parallellt. Du kan dessutom skapa cirkulärt tjänstberoende som blockerar distributionen. Den [referens](resource-group-template-functions-resource.md#reference) funktion och [lista *](resource-group-template-functions-resource.md#list) functions skapar en implicit beroende på den refererade resursen när den här resursen har distribuerats i samma mall och refereras till av sitt namn (inte resurs-ID ). Du kan därför har flera beroenden än beroenden i den **dependsOn** egenskapen. Den [resourceId](resource-group-template-functions-resource.md#resourceid) funktionen inte skapa en implicit beroende eller verifiera att resursen finns. Den [referens](resource-group-template-functions-resource.md#reference) funktion och [lista *](resource-group-template-functions-resource.md#list) funktion inte skapa beroende av ett implicit när resursen är refereras till av dess resurs-ID. Om du vill skapa beroende av ett implicit, skicka namnet på den resurs som har distribuerats i samma mall.

När du ser beroende problem, måste du få insyn i ordningen på resursdistributionen. Visa ordningen på distributionsåtgärder:

1. Välj distributionshistoriken för din resursgrupp.

   ![Välj distributionshistoriken](./media/resource-manager-not-found-errors/select-deployment.png)

2. Välj en distribution från historiken över och välj **händelser**.

   ![Välj distributionen händelser](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Granska sekvens av händelser för varje resurs. Uppmärksam på status för varje åtgärd. Följande bild visar exempelvis tre lagringskonton som har distribuerat parallellt. Observera att tre lagringskonton har startats på samma gång.

   ![parallell distribution](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   I nästa bild visas tre lagringskonton som inte är distribuerat parallellt. Andra lagringskonto beror på det första lagringskontot och det tredje storage-kontot är beroende av andra lagringskontot. Det första lagringskontot är igång, accepteras och har slutförts innan nästa startas.

   ![sekventiellt distributionen](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Lösning 2 – hämta resursen från en annan resursgrupp

När resursen finns i en annan resursgrupp än den som har distribuerats till, använder den [resourceId funktionen](resource-group-template-functions-resource.md#resourceid) att hämta det fullständigt kvalificerade namnet på resursen.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Lösning 3 – kontrollfunktionen för referens

Leta efter ett uttryck som innehåller den [referens](resource-group-template-functions-resource.md#reference) funktion. De värden som du anger varierar beroende på om resursen är i samma mall, resursgrupp och prenumeration. Kontrollera att du etablerar de obligatoriska parametervärdena för ditt scenario. Om resursen finns i en annan resursgrupp, tillhandahåller fullständiga resurs-ID. Till exempel om du vill referera till ett lagringskonto i en annan resursgrupp, använder du:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```
---
title: Azure-resurs hittades inte-fel | Microsoft Docs
description: Beskriver hur du löser problem när en resurs som inte kan hittas.
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
ms.openlocfilehash: 494526ae2084053f23bb3a096ac7d089c47a731a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823443"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Lös inte att hitta fel för Azure-resurser

Den här artikeln beskriver de fel som kan visas när en resurs kan inte hittas under distributionen.

## <a name="symptom"></a>Symtom

När mallen innehåller namnet på en resurs som inte kan matchas, visas ett felmeddelande:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Om du använder den [referens](resource-group-template-functions-resource.md#reference) eller [listKeys](resource-group-template-functions-resource.md#listkeys) funktioner med en resurs som inte kan lösas, följande felmeddelande:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Orsak

Resource Manager behöver hämta egenskaperna för en resurs, men det går inte att identifiera resursen i din prenumeration.

## <a name="solution-1---set-dependencies"></a>Lösning 1 – ange beroenden

Om du försöker distribuera resursen som saknas i mallen kan du kontrollera om du behöver lägga till ett beroende. Hanteraren för filserverresurser optimerar distribution genom att skapa resurser parallellt, när det är möjligt. Om en resurs måste distribueras efter en annan resurs, måste du använda den **dependsOn** element i mallen. App Service-plan måste till exempel finnas när du distribuerar ett webbprogram. Om du inte har angett att webbappen är beroende av App Service-plan, skapar Resource Manager båda resurserna på samma gång. Du får ett felmeddelande om att App Service plan resursen inte kan hittas, eftersom det inte finns ännu vid försök att ange en egenskap för webbappen. Du kan förhindra att det här felet genom att ange beroendet i webbprogrammet.

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

Men om du vill undvika inställningen beroenden som inte behövs. När du har onödiga beroenden förlänga varaktigheten för distributionen genom att förhindra att resurser som inte är beroende av varandra från att distribueras parallellt. Dessutom kan du skapa Cirkelberoenden som blockerar distributionen. Den [referens](resource-group-template-functions-resource.md#reference) funktion och [lista *](resource-group-template-functions-resource.md#listkeys-listsecrets-and-list) funktioner skapar en implicit beroende på den refererade resursen när resursen distribueras i samma mall och refereras till av sitt namn (inte resurs-ID ). Du kan därför ha flera beroenden än beroenden i den **dependsOn** egenskapen. Den [resourceId](resource-group-template-functions-resource.md#resourceid) funktion inte skapa en implicit beroende eller verifiera att resursen finns. Den [referens](resource-group-template-functions-resource.md#reference) funktion och [lista *](resource-group-template-functions-resource.md#listkeys-listsecrets-and-list) funktion inte skapa en implicit beroende när resursen refereras till av dess resurs-ID. Om du vill skapa en implicit beroende skicka namnet på den resurs som distribueras i samma mall.

När du ser beroende problem som du behöver få insyn i ordningen för distribution av resursen. Visa ordningen på distributionsåtgärder:

1. Välj distributionshistoriken för resursgruppen.

   ![Välj distributionshistoriken](./media/resource-manager-not-found-errors/select-deployment.png)

2. Välj en distribution från historiken och välj **händelser**.

   ![Välj distributionen händelser](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Granska sekvens av händelser för varje resurs. Vara uppmärksam på status för varje åtgärd. Följande bild visar exempelvis tre storage-konton som har distribuerats parallellt. Observera att tre storage-konton har startats på samma gång.

   ![Parallell distribution](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   Nästa bild visar tre storage-konton som inte är distribuerat parallellt. Andra lagringskontot beror på det första storage-kontot och tredje lagringskontot är beroende av andra lagringskontot. Första lagringskontot är igång, accepteras och slutförs innan nästa startas.

   ![sekventiella distribution](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Lösning 2 – hämta resurs från en annan resursgrupp

När resursen finns i en annan resursgrupp än den som distribueras för att använda den [resourceId funktionen](resource-group-template-functions-resource.md#resourceid) att hämta det fullständigt kvalificerade namnet på resursen.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Lösning 3 - referens kontrollfunktionen

Leta efter ett uttryck som innehåller den [referens](resource-group-template-functions-resource.md#reference) funktion. De värden du anger variera beroende på om resursen är i samma mall, resursgruppen och prenumeration. Kontrollera att du är att tillhandahålla parametervärdena som krävs för ditt scenario. Om resursen finns i en annan resursgrupp, ange fullständiga resurs-ID. Till exempel för att referera till ett lagringskonto i en annan resursgrupp, använder du:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```
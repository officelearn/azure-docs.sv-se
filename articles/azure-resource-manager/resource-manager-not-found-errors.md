---
title: Azure-resurs hittades inte-fel | Microsoft Docs
description: "Beskriver hur du löser problem när en resurs som inte kan hittas."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Lös inte att hitta fel för Azure-resurser

Den här artikeln beskriver de fel som kan uppstå när en resurs som inte kan hittas under distributionen. 

## <a name="symptom"></a>Symtom

När mallen innehåller namnet på en resurs som inte kan matchas, visas ett felmeddelande:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Om du försöker använda den [referens](resource-group-template-functions-resource.md#reference) eller [listKeys](resource-group-template-functions-resource.md#listkeys) funktioner med en resurs som inte kan lösas, följande felmeddelande:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Orsak

Resource Manager behöver hämta egenskaperna för en resurs, men det går inte att identifiera resursen i din prenumeration.

## <a name="solution"></a>Lösning

### <a name="solution-1"></a>Lösning 1

Om du försöker distribuera resursen som saknas i mallen kan du kontrollera om du behöver lägga till ett beroende. Hanteraren för filserverresurser optimerar distribution genom att skapa resurser parallellt, när det är möjligt. Om en resurs måste distribueras efter en annan resurs, måste du använda den **dependsOn** element i mallen för att skapa ett beroende på andra resurser. App Service-plan måste till exempel finnas när du distribuerar ett webbprogram. Om du inte har angett att webbappen är beroende av App Service-plan skapar Resource Manager båda resurserna på samma gång. Du får ett felmeddelande om att resursen App Service-plan inte kan hittas, eftersom det inte finns ännu vid försök att ange en egenskap för webbappen. Du kan förhindra att det här felet genom att ange beroendet i webbprogrammet.

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

Förslag på felsökning för beroende finns [Kontrollera distributionsordningen](resource-manager-troubleshoot-tips.md#check-deployment-sequence).

### <a name="solution-2"></a>Lösning 2

När resursen finns i en annan resursgrupp än den som distribueras för att använda den [resourceId funktionen](resource-group-template-functions-resource.md#resourceid) att hämta det fullständigt kvalificerade namnet på resursen.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>Lösning 3

Leta efter ett uttryck som innehåller den [referens](resource-group-template-functions-resource.md#reference) funktion. De värden du anger variera beroende på om resursen är i samma mall, resursgruppen och prenumeration. Kontrollera att du använder parametervärdena som krävs för ditt scenario. Om resursen finns i en annan resursgrupp, ange fullständiga resurs-ID. Till exempel för att referera till ett lagringskonto i en annan resursgrupp, använder du:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```
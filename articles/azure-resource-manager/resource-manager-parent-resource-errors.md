---
title: "Azure överordnade resurs fel | Microsoft Docs"
description: "Beskriver hur du löser problem när du arbetar med en överordnad resurs."
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
ms.openlocfilehash: e59147c4ac18f730f27b9d4aa9c008f219881065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-parent-resources"></a>Åtgärda fel för överordnade resurser

Den här artikeln beskriver de fel som kan uppstå när du distribuerar en resurs som är beroende av en överordnad resurs.

## <a name="symptom"></a>Symtom

När du distribuerar en resurs som är en underordnad till en annan resurs får du följande fel:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Orsak

När en resurs är en underordnad till en annan resurs, måste den överordnade resursen finnas innan du skapar den underordnade resursen. Namnet på den underordnade resursen innehåller namnet på överordnade. Till exempel kan en SQL-databas definieras som:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Men om du inte anger ett beroende på servern, databas-distributionen kan startas innan servern har distribuerats.

## <a name="solution"></a>Lösning

Innehåller ett beroende för att lösa det här felet.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Mer information finns i [definiera ordningen för att distribuera resurser i Azure Resource Manager-mallar](resource-group-define-dependencies.md).
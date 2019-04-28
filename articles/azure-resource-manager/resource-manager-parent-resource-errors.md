---
title: Azure överordnad resurs fel | Microsoft Docs
description: Beskriver hur du löser fel när du arbetar med en överordnad resurs.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 19642138105f3de0e2bda3f0f80e12cf74a35709
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059998"
---
# <a name="resolve-errors-for-parent-resources"></a>Åtgärda fel för överordnade resurser

Den här artikeln beskriver de fel som du kan få när du distribuerar en resurs som är beroende av en överordnad resurs.

## <a name="symptom"></a>Symtom

När du distribuerar en resurs som är underordnad till en annan resurs kan felmeddelande följande visas:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Orsak

När en resurs är en underordnad till en annan resurs, måste den överordnade resursen finnas innan du skapar den underordnade resursen. Namnet på den underordnade resursen definierar anslutningen med den överordnade resursen. Namnet på den underordnade resursen är i formatet `<parent-resource-name>/<child-resource-name>`. Till exempel kan en SQL-databas definieras som:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Om du distribuerar både servern och databasen i samma mall, men inte anger ett beroende på servern, kan databasen distributionen startar innan servern har distribuerats. 

Om den överordnade resursen redan finns och är inte distribueras i samma mall, får du det här felet när Resource Manager inte kan koppla underordnade resursen överordnade. Det här felet kan inträffa när den underordnade resursen inte finns i rätt format eller den underordnade resursen har distribuerats till en resursgrupp som skiljer sig från resursgruppen för överordnad resurs.

## <a name="solution"></a>Lösning

För att lösa det här felet när överordnade och underordnade resurser distribueras i samma mall kan du inkludera ett beroende.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

För att lösa det här felet när den överordnade resursen redan har distribuerats i en annan mall, kan du inte anger ett beroende. Distribuera underordnat till samma resursgrupp och ange namnet på den överordnade resursen istället.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

Mer information finns i [definierar ordningen för att distribuera resurser i Azure Resource Manager-mallar](resource-group-define-dependencies.md).
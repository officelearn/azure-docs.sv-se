---
title: Överordnad resursfel
description: Beskriver hur du löser fel när du arbetar med en överordnad resurs i en Azure Resource Manager-mall.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: f1847389d60ddf3c6abc70bc3309940c2246084e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154048"
---
# <a name="resolve-errors-for-parent-resources"></a>Lösa fel för överordnade resurser

I den här artikeln beskrivs de fel du kan få när du distribuerar en resurs som är beroende av en överordnad resurs.

## <a name="symptom"></a>Symptom

När du distribuerar en resurs som är underordnad till en annan resurs kan följande felmeddelande visas:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Orsak

När en resurs är underordnad till en annan resurs måste den överordnade resursen finnas innan den underordnade resursen skapas. Namnet på den underordnade resursen definierar anslutningen till den överordnade resursen. Namnet på den underordnade resursen är i formatet `<parent-resource-name>/<child-resource-name>`. En SQL-databas kan till exempel definieras som:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Om du distribuerar både servern och databasen i samma mall, men inte anger något beroende av servern, kan databasdistributionen starta innan servern har distribuerats.

Om den överordnade resursen redan finns och inte distribueras i samma mall visas det här felet när Resource Manager inte kan associera den underordnade resursen med överordnad. Det här felet kan inträffa när den underordnade resursen inte är i rätt format eller om den underordnade resursen distribueras till en resursgrupp som skiljer sig från resursgruppen för den överordnade resursen.

## <a name="solution"></a>Lösning

LÃ¶s problemet nÃ¤r överordnad och underordnad resurs distribueras i samma mall innehÃ¥ller ett beroende.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Lös det här felet när den överordnade resursen tidigare har distribuerats i en annan mall anger du inte ett beroende. Distribuera i stället underordnaden till samma resursgrupp och ange namnet på den överordnade resursen.

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
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Mer information finns i [Definiera ordern för distribution av resurser i Azure Resource Manager-mallar](define-resource-dependency.md).
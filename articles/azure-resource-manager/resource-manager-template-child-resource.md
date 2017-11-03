---
title: Definiera underordnade resursen i Azure-mall | Microsoft Docs
description: "Visar hur du ställer resurstyp och namn för underordnade resursen i en Azure Resource Manager-mall"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: tomfitz
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>Ange namn och typ för underordnade resursen i Resource Manager-mall
När du skapar en mall kan behöver du ofta innehåller en underordnad resurs som är relaterad till en överordnad resurs. Mallen kan exempelvis omfatta en SQLServer och en databas. SQLServer är den överordnade resursen och databasen är den underordnade resursen. 

Formatet för resurstypen underordnade är:`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Formatet på underordnade resursnamnet är:`{parent-resource-name}/{child-resource-name}`

Du dock ange typ och namn i en mall på olika sätt beroende på om den finns under den överordnade resursen, eller på sin egen på den översta nivån. Det här avsnittet visar hur du hanterar båda metoderna.

När man skapar en fullständiga referens till en resurs, är ordningen att kombinera segment från typ och namn inte bara en sammanfogning av två.  Använd i stället efter namnområde, en sekvens med *typnamn/* par från minst specifika mest specifika:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Exempel:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`stämmer `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` är inte korrekt

## <a name="nested-child-resource"></a>Kapslad underordnad resurs
Det enklaste sättet att definiera en underordnad resurs är att kapsla i den överordnade resursen. I följande exempel visas en SQL-databas som har kapslats i i en SQL Server.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

För den underordnade resursen typ har angetts till `databases` men dess fullständiga resurstypen är `Microsoft.Sql/servers/databases`. Du inte anger `Microsoft.Sql/servers/` eftersom det antas från den överordnade resurstypen. Namnet på underordnade resursen har angetts till `exampledatabase` men det fullständiga namnet innehåller namnet på överordnade. Du inte anger `exampleserver` eftersom det antas från den överordnade resursen.

## <a name="top-level-child-resource"></a>Översta underordnade resursen
Du kan definiera den underordnade resursen på den översta nivån. Du kan använda den här metoden om den överordnade resursen inte är distribuerat i samma mall, eller om vill använda `copy` att skapa flera underordnade resurser. Med den här metoden måste du ange fullständig resurstypen och inkludera namnet på överordnade resursen i namnet på underordnade resursen.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Databasen är en underordnad resurs till servern även om de är definierade på samma nivå i mallen.

## <a name="next-steps"></a>Nästa steg
* Rekommendationer om hur du skapar mallar finns i [bästa praxis för att skapa mallar för Azure Resource Manager](resource-manager-template-best-practices.md).
* Ett exempel på hur du skapar flera underordnade resurser, se [distribuera flera instanser av resurser i Azure Resource Manager-mallar](resource-group-create-multiple.md).

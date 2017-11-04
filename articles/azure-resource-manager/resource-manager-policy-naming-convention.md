---
title: "Principer för Azure-resurs för namnkonventioner | Microsoft Docs"
description: "Beskriver Azure Resource Manager principer för resource namngivningsregler."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: tomfitz
ms.openlocfilehash: 955b04517c3ccdbe530eb982e6aa1255b69e1edd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="apply-resource-policies-for-names-and-text"></a>Tillämpa principer för företagsresurser för namn och text
Den här artikeln visar flera [resursprinciper](resource-manager-policy.md) du kan använda för att etablera konventioner för namngivning och text. Dessa principer säkerställa konsekvens för namn och värden. 

## <a name="set-naming-convention-with-wildcard"></a>Ange namngivningskonvention med jokertecken
I följande exempel visar hur du använder jokertecken som stöds av den **som** villkor. Villkoret tillstånd som om namnet matchar det angivna mönstret (namePrefix\*nameSuffix) neka en begäran:

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>Ange namngivningskonvention med mönster

Om du vill ange att resursnamn matchar ett mönster, använder du matchar villkoret. I följande exempel kräver namn ska börja med `contoso` och innehåller ytterligare sex bokstäverna:

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>Ange datum för Taggvärdet

Att kräva ett datum användningsmönstret i två siffror, streck, tre bokstäver, bindestreck och fyra siffror:

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-multiple-naming-patterns"></a>Ange flera namngivning mönster

Om du vill ange fler än en namngivningskonvention tillåts använda de **allOf** och **inte** operatörer. I följande exempel, om det angivna namnet inte matchar mönster för antingen nekas den.

```json
{
    "if": {
        "allOf": [
            {
                "not": {
                    "field": "name",
                    "match": "contoso??????"
                }
            },
            {
                "not": {
                    "field": "name",
                    "match": "contoso-???-##"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Nästa steg
* När du definierar en regel (som visas i föregående exempel) behöver du skapar principdefinitionen och kopplar den till ett omfång. Omfattningen kan vara en prenumeration, resursgrupp eller resurs. Om du vill tilldela principer via portalen finns [Använd Azure-portalen för att tilldela och hantera resursprinciper](resource-manager-policy-portal.md). Om du vill tilldela principer via REST-API, PowerShell eller Azure CLI, se [tilldela och hantera principer via skript](resource-manager-policy-create-assign.md). 
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).


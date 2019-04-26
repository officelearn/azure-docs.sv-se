---
title: Azure storage-konto namngivningsfel | Microsoft Docs
description: Beskriver de fel som kan uppstå när du anger namnet på ett lagringskonto.
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
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: c3d4d764b1076c8705cfa64d6c0b38e3b8c1a801
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390105"
---
# <a name="resolve-errors-for-storage-account-names"></a>Åtgärda fel för lagringskontonamn

Den här artikeln beskriver namngivning fel som kan uppstå när du distribuerar ett lagringskonto.

## <a name="symptom"></a>Symtom

Om namnet på ditt lagringskonto innehåller otillåtna tecken, får du ett felmeddelande som:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Du måste ange ett namn för den resurs som är unikt i Azure för storage-konton. Om du inte anger ett unikt namn, visas ett fel som:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Om du distribuerar ett lagringskonto med samma namn som ett befintligt lagringskonto i din prenumeration, men ange en annan plats, får du ett felmeddelande om att lagringskontot finns redan i en annan plats. Antingen ta bort det befintliga lagringskontot eller ange samma plats som det befintliga lagringskontot.

## <a name="cause"></a>Orsak

Lagringskontonamn måste vara mellan 3 och 24 tecken och siffror och gemener. Namnet måste vara unikt.

## <a name="solution"></a>Lösning

Kontrollera att namnet är unikt. Du kan skapa ett unikt namn genom att sammanfoga din namngivningskonvention med resultatet från den [uniqueString](resource-group-template-functions-string.md#uniquestring) funktion.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Kontrollera att namnet på ditt lagringskonto inte överstiger 24 tecken. Den [uniqueString](resource-group-template-functions-string.md#uniquestring) funktionen returnerar 13 tecken. Om du sammanfoga ett prefix eller postfixet till den **uniqueString** resultera, ange ett värde som är 11 tecken eller mindre.

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

Kontrollera att namnet på ditt lagringskonto inte innehåller några versaler eller specialtecken.
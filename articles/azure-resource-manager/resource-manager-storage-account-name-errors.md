---
title: Azure storage-konto namngivningsfel | Microsoft Docs
description: "Beskriver de fel som kan uppstå när du anger namnet på ett lagringskonto."
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
ms.openlocfilehash: dc045827fbd38054a334ff22eb30e0db6a31bac8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-storage-account-names"></a>Åtgärda fel för lagringskontonamn

Den här artikeln beskriver namngivning fel som kan uppstå när du distribuerar ett lagringskonto.

## <a name="symptom"></a>Symtom

Om namnet på ditt lagringskonto innehåller otillåtna tecken, får ett felmeddelande som:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Du måste ange ett namn för den resurs som är unik i Azure för storage-konton. Om du inte anger ett unikt namn får ett felmeddelande som:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Om du distribuerar ett lagringskonto med samma namn som ett befintligt lagringskonto i din prenumeration, men ange en annan plats, visas ett felmeddelande om att lagringskontot finns redan i en annan plats. Ta antingen bort befintligt lagringskonto eller ange samma plats som det befintliga lagringskontot.

## <a name="cause"></a>Orsak

Lagringskontonamn måste vara mellan 3 och 24 tecken långt och innehålla siffror och gemena bokstäver. Namnet måste vara unikt.

## <a name="solution"></a>Lösning

### <a name="solution-1"></a>Lösning 1

Kontrollera att lagringskontonamn är unikt. Du kan skapa ett unikt namn genom att sammanbinda en namngivningskonvention med resultatet av den [uniqueString](resource-group-template-functions-string.md#uniquestring) funktion.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

### <a name="solution-2"></a>Lösning 2

Kontrollera att namnet på ditt lagringskonto inte överskrida 24 tecken. Den [uniqueString](resource-group-template-functions-string.md#uniquestring) funktionen returnerar 13 tecken. Om du sammanfoga ett prefix eller postfix till den **uniqueString** leda, ange ett värde som är 11 tecken eller mindre.

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

### <a name="solution-3"></a>Lösning 3

Kontrollera att namnet på ditt lagringskonto inte innehåller några versaler eller specialtecken.
---
title: Namnfel för lagringskonto
description: Beskriver de fel du kan stöta på när du anger ett lagringskontonamn.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153980"
---
# <a name="resolve-errors-for-storage-account-names"></a>Lösa fel för lagringskontonamn

I den här artikeln beskrivs namngivningsfel som kan uppstå när du distribuerar ett lagringskonto.

## <a name="symptom"></a>Symptom

Om ditt lagringskontonamn innehåller förbjudna tecken visas ett felmeddelande som:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

För lagringskonton måste du ange ett namn för den resurs som är unik i Azure. Om du inte anger ett unikt namn visas ett felmeddelande likt följande:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Om du distribuerar ett lagringskonto med samma namn som ett befintligt lagringskonto i din prenumeration, men anger en annan plats, visas ett felmeddelande om att lagringskontot redan finns på en annan plats. Ta antingen bort det befintliga lagringskontot eller ange samma plats som det befintliga lagringskontot.

## <a name="cause"></a>Orsak

Namn på lagringskonto måste vara mellan 3 och 24 tecken långa och endast med siffror och gemener. Namnet måste vara unikt.

## <a name="solution"></a>Lösning

Kontrollera att lagringskontonamnet är unikt. Du kan skapa ett unikt namn genom att sammanfoga din namngivningskonvention med resultatet av funktionen [uniqueString.](template-functions-string.md#uniquestring)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Kontrollera att ditt lagringskontonamn inte överstiger 24 tecken. Funktionen [uniqueString returnerar](template-functions-string.md#uniquestring) 13 tecken. Om du sammanfogar ett prefix eller ett postfix till **uniktStringresultat** anger du ett värde som är 11 tecken eller mindre.

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

Kontrollera att ditt lagringskontonamn inte innehåller några versaler eller specialtecken.
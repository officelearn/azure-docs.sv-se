---
title: Översikt över anpassade roll definitioner
description: Beskriver konceptet att skapa anpassade roll definitioner för hanterade program.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650765"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefakt för anpassad roll definition i Azure Managed Applications

Anpassad roll definition är en valfri artefakt i hanterade program. Den används för att avgöra vilka behörigheter som det hanterade programmet behöver för att utföra sina funktioner.

Den här artikeln innehåller en översikt över den anpassade roll definitions artefakten och dess funktioner.

## <a name="custom-role-definition-artifact"></a>Artefakt för anpassad roll definition

Du måste namnge den anpassade roll definitions definitionen artefakt customRoleDefinition. JSON. Placera den på samma nivå som createUiDefinition. JSON och mainTemplate. json i. zip-paketet som skapar en definition för ett hanterat program. Information om hur du skapar zip-paketet och publicerar en definition för hanterade program finns i [publicera en definition för ett hanterat program.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Definitions schema för anpassad roll

Filen customRoleDefinition. JSON har en `roles` egenskap på översta nivån som är en matris med roller. Dessa roller är de behörigheter som det hanterade programmet behöver för att fungera. För närvarande tillåts bara inbyggda roller, men du kan ange flera roller. En roll kan refereras av ID: t för roll definitionen eller av roll namnet.

Exempel-JSON för anpassad roll definition:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Roller

En roll består av antingen en `$.properties.roleName` eller ett `id`:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Du kan använda antingen fältet `id` eller `roleName`. Endast en krävs. De här fälten används för att leta upp den roll definition som ska användas. Om båda anges används fältet `id`.

|Egenskap|Krävs?|Beskrivning|
|---------|---------|---------|
|id|Ja|ID för den inbyggda rollen. Du kan använda det fullständiga ID: t eller bara GUID.|
|roleName|Ja|Namnet på den inbyggda rollen.|
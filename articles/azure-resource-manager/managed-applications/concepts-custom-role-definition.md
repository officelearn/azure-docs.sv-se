---
title: Översikt över anpassade rolldefinitioner
description: Beskriver konceptet att skapa anpassade rolldefinitioner för hanterade program.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391826"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Anpassad rolldefinitionsartefakt i Azure-hanterade program

Anpassad rolldefinition är en valfri artefakt i hanterade program. Den används för att avgöra vilka behörigheter det hanterade programmet behöver för att utföra sina funktioner.

Den här artikeln innehåller en översikt över den anpassade rolldefinitionsartefakten och dess funktioner.

## <a name="custom-role-definition-artifact"></a>Artefakt för anpassad rolldefinition

Du måste namnge den anpassade rolldefinitionsartefakten customRoleDefinition.json. Placera den på samma nivå som createUiDefinition.json och mainTemplate.json i ZIP-paketet som skapar en hanterad programdefinition. Mer information om hur du skapar ZIP-paketet och publicerar en hanterad programdefinition finns i [Publicera en hanterad programdefinition.](publish-service-catalog-app.md)

## <a name="custom-role-definition-schema"></a>Schema för anpassad rolldefinition

Filen customRoleDefinition.json har en `roles` egenskap på den översta nivån som är en matris med roller. Dessa roller är de behörigheter som det hanterade programmet behöver för att fungera. För närvarande är endast inbyggda roller tillåtna, men du kan ange flera roller. En roll kan refereras av ID:t för rolldefinitionen eller av rollnamnet.

Exempel på JSON för anpassad rolldefinition:

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

En roll består av `$.properties.roleName` antingen `id`a eller en:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Du kan använda `id` `roleName` antingen fältet eller. Endast en krävs. Dessa fält används för att slå upp den rolldefinition som ska tillämpas. Om båda levereras `id` används fältet.

|Egenskap|Krävs?|Beskrivning|
|---------|---------|---------|
|id|Ja|ID:et för den inbyggda rollen. Du kan använda det fullständiga ID:t eller bara GUID.You can use the full ID or just the GUID.|
|rollNamn|Ja|Namnet på den inbyggda rollen.|
---
title: Översikt över anpassade roll definitioner i Azure Managed Applications | Microsoft Docs
description: Beskriver konceptet att skapa anpassade roll definitioner för hanterade program.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: d645eebefde473e404f7760d2bc8a67c7e3e9087
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609039"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefakt för anpassad roll definition i Azure Managed Applications

Anpassad roll definition är en valfri artefakt i hanterade program. Den används för att avgöra vilka behörigheter som det hanterade programmet behöver för att utföra sina funktioner.

Den här artikeln ger en översikt över den anpassade roll definitions artefakten och dess funktioner.

## <a name="custom-role-definition-artifact"></a>Artefakt för anpassad roll definition

Den anpassade roll definitions artefakten måste ha namnet **customRoleDefinition. JSON** och placeras på samma nivå som **createUiDefinition. JSON** och **mainTemplate. JSON** i. zip-paketet som skapar en definition för hanterade program. Information om hur du skapar zip-paketet och publicerar en definition för hanterade program finns i [publicera en definition för ett hanterat program.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Definitions schema för anpassad roll

Filen **customRoleDefinition. JSON** har en egenskap på översta nivån `roles`, som är en matris med roller. Var och en av dessa roller är de behörigheter som det hanterade programmet behöver för att fungera. För närvarande tillåts bara inbyggda roller, men flera roller kan anges. Rollen kan refereras av ID: t för roll definitionen eller av roll namnet.

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

## <a name="role"></a>Roll

En roll består av antingen en `$.properties.roleName` eller `id`.

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!Note]
> Endast ett av fälten `id` eller `roleName` krävs. De här fälten används för att leta upp den roll definition som ska tillämpas. Om båda anges används fältet `id`.

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|ID|*Ja*|ID för den inbyggda rollen. Den här egenskapen kan vara det fullständiga ID: t eller bara GUID.|
|RoleName|*Ja*|Namnet på den inbyggda rollen.|
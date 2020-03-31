---
title: RBAC-roller och behörigheter
description: Använd Azure-rollbaserad åtkomstkontroll (RBAC) och IAM (Identity and Access Management) för att ge detaljerade behörigheter till resurser i ett Azure-behållarregister.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74893492"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry roller och behörigheter

Azure Container Registry-tjänsten stöder en uppsättning [inbyggda Azure-roller](../role-based-access-control/built-in-roles.md) som ger olika nivåer av behörigheter till ett Azure-behållarregister. Använd [Azure-rollbaserad åtkomstkontroll](../role-based-access-control/index.yml) (RBAC) för att tilldela specifika behörigheter till användare, tjänstobjektnamn eller andra identiteter som behöver interagera med ett register. 

| Roll/behörighet       | [Komma åt Resurshanteraren](#access-resource-manager) | [Skapa/ta bort register](#create-and-delete-registry) | [Push-bild](#push-image) | [Hämta bild](#pull-image) | [Ta bort bilddata](#delete-image-data) | [Ändra policyer](#change-policies) |   [Signera bilder](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Ägare | X | X | X | X | X | X |  |  
| Deltagare | X | X | X |  X | X | X |  |  
| Läsare | X |  |  | X |  |  |  |
| AcrPush (av en) |  |  | X | X | |  |  |  
| AcrPull (acrpull) |  |  |  | X |  |  |  |  
| AcrDelete ( |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Differentiera användare och tjänster

När behörigheter tillämpas är en metod för bästa praxis att tillhandahålla den mest begränsade uppsättningen behörigheter för en person, eller tjänst, för att utföra en uppgift. Följande behörighetsgrupper representerar en uppsättning funktioner som kan användas av människor och huvudlösa tjänster.

### <a name="cicd-solutions"></a>CI/CD-lösningar

När du `docker build` automatiserar kommandon från CI/CD-lösningar behöver `docker push` du funktioner. För dessa huvudlösa tjänstscenarier föreslår vi att rollen **AcrPush** tilldelas. Den här rollen, till skillnad från den bredare **deltagarrollen,** förhindrar att kontot utför andra registeråtgärder eller använder Azure Resource Manager.

### <a name="container-host-nodes"></a>Värdnoder för behållare

På samma sätt behöver noder som kör dina behållare rollen **AcrPull,** men bör inte kräva **reader-funktioner.**

### <a name="visual-studio-code-docker-extension"></a>Tillägg för Visual Studio-koddockning

För verktyg som Tillägget Visual Studio Code [Docker](https://code.visualstudio.com/docs/azure/docker)krävs ytterligare åtkomst till resursprovider för att lista tillgängliga Azure-behållarregister. I det här fallet ger användarna åtkomst till rollen **Läsare** eller **Deltagare.** Dessa roller `docker pull` `docker push`tillåter `az acr list` `az acr build`, , och andra funktioner. 

## <a name="access-resource-manager"></a>Komma åt Resurshanteraren

Azure Resource Manager-åtkomst krävs för Azure-portalen och registerhanteringen med [Azure CLI](/cli/azure/). Om du till exempel vill få en `az acr list` lista över register med kommandot behöver du den här behörighetsgruppen. 

## <a name="create-and-delete-registry"></a>Skapa och ta bort register

Möjligheten att skapa och ta bort Azure-behållarregister.

## <a name="push-image"></a>Push-bild

Möjligheten `docker push` att en bild, eller skicka en annan [artefakt som stöds,](container-registry-image-formats.md) till exempel ett Helm-diagram, till ett register. Kräver [autentisering](container-registry-authentication.md) med registret med den auktoriserade identiteten. 

## <a name="pull-image"></a>Hämta bild

Möjligheten `docker pull` att en icke-karantänavbildning, eller hämta en annan [artefakt som stöds,](container-registry-image-formats.md) till exempel ett Helm-diagram, från ett register. Kräver [autentisering](container-registry-authentication.md) med registret med den auktoriserade identiteten.

## <a name="delete-image-data"></a>Ta bort bilddata

Möjligheten att [ta bort behållaravbildningar](container-registry-delete.md)eller ta bort andra artefakter som [stöds,](container-registry-image-formats.md) till exempel Helm-diagram, från ett register.

## <a name="change-policies"></a>Ändra policyer

Möjligheten att konfigurera principer för ett register. Principer omfattar bildrensning, aktivera karantän och bildsignering.

## <a name="sign-images"></a>Signera bilder

Möjligheten att signera bilder, vanligtvis tilldelas en automatiserad process, som skulle använda en tjänst huvudnamn. Den här behörigheten kombineras vanligtvis med [push-avbildning](#push-image) så att du kan skicka en betrodd avbildning till ett register. Mer information finns [i Innehållsförtroende i Azure Container Registry](container-registry-content-trust.md).

## <a name="custom-roles"></a>Anpassade roller

Precis som med andra Azure-resurser kan du skapa egna [anpassade roller](../role-based-access-control/custom-roles.md) med detaljerade behörigheter till Azure Container Registry. Tilldela sedan anpassade roller till användare, tjänsthuvudnamn eller andra identiteter som behöver interagera med ett register. 

Information om vilka behörigheter som ska tillämpas på en anpassad roll finns i listan över [åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)för Microsoft.ContainerRegistry, granska de tillåtna åtgärderna för de [inbyggda ACR-rollerna](../role-based-access-control/built-in-roles.md)eller köra följande kommando:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Om du vill definiera en anpassad roll finns i [Steg för att skapa en anpassad roll](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> I en anpassad roll stöder Azure Container Registry för `Microsoft.ContainerRegistry/*` närvarande `Microsoft.ContainerRegistry/registries/*` inte jokertecken, till exempel eller som ger åtkomst till alla matchande åtgärder. Ange eventuella åtgärder som krävs individuellt i rollen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om att tilldela RBAC-roller till en Azure-identitet med hjälp av [Azure-portalen,](../role-based-access-control/role-assignments-portal.md) [Azure CLI](../role-based-access-control/role-assignments-cli.md)eller andra Azure-verktyg.

* Lär dig mer om [autentiseringsalternativ](container-registry-authentication.md) för Azure Container Registry.

* Lär dig mer om hur du aktiverar [behörigheter](container-registry-repository-scoped-permissions.md) (förhandsgranskning) i en behållare i ett behållarregister.
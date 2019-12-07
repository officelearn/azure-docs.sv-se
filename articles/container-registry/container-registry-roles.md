---
title: RBAC-roller och-behörigheter
description: Använd rollbaserad åtkomst kontroll i Azure (RBAC) och identitets-och åtkomst hantering (IAM) för att ge detaljerade behörigheter till resurser i ett Azure Container Registry.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893492"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry roller och behörigheter

Tjänsten Azure Container Registry stöder en uppsättning [inbyggda Azure-roller](../role-based-access-control/built-in-roles.md) som ger olika behörighets nivåer till ett Azure Container Registry. Använd [rollbaserad åtkomst kontroll](../role-based-access-control/index.yml) (RBAC) i Azure för att tilldela särskilda behörigheter till användare, tjänst huvud namn eller andra identiteter som behöver samverka med ett register. 

| Roll/behörighet       | [Access Resource Manager](#access-resource-manager) | [Skapa/ta bort registret](#create-and-delete-registry) | [Push-avbildning](#push-image) | [Hämta bild](#pull-image) | [Ta bort avbildnings data](#delete-image-data) | [Ändra principer](#change-policies) |   [Signera bilder](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Ägare | X | X | X | X | X | X |  |  
| Deltagare | X | X | X |  X | X | X |  |  
| Läsare | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Särskilja användare och tjänster

När du använder en viss tids period är det en bra idé att tillhandahålla den mest begränsade uppsättningen behörigheter för en person, eller tjänst, för att utföra en uppgift. Följande behörighets uppsättningar representerar en uppsättning funktioner som kan användas av människor och konsol lösa tjänster.

### <a name="cicd-solutions"></a>CI/CD-lösningar

När du automatiserar `docker build` kommandon från CI/CD-lösningar behöver du `docker push` funktioner. Vi föreslår att du tilldelar **AcrPush** -rollen för dessa scenarier för konsol lös tjänst. Den här rollen, till skillnad från rollen bredare **deltagare** , förhindrar att kontot utför andra register åtgärder eller använder Azure Resource Manager.

### <a name="container-host-nodes"></a>Noder i container värden

Noder som kör dina behållare behöver inte heller **AcrPull** -rollen, men behöver inte **läsa** in funktioner.

### <a name="visual-studio-code-docker-extension"></a>Tillägg för Visual Studio Code Docker

För verktyg som Visual Studio Code [Docker-tillägget](https://code.visualstudio.com/docs/azure/docker)krävs ytterligare resurs leverantörs åtkomst för att lista tillgängliga Azure Container register. I det här fallet ger du användarna åtkomst till **läsaren** eller **deltagar** rollen. Med dessa roller kan `docker pull`, `docker push`, `az acr list`, `az acr build`och andra funktioner. 

## <a name="access-resource-manager"></a>Åtkomst till Resource Manager

Azure Resource Manager åtkomst krävs för Azure Portal-och register hantering med [Azure CLI](/cli/azure/). Om du till exempel vill hämta en lista över register med hjälp av kommandot `az acr list` behöver du den här behörighets uppsättningen. 

## <a name="create-and-delete-registry"></a>Skapa och ta bort registret

Möjlighet att skapa och ta bort Azure-behållar register.

## <a name="push-image"></a>Push-avbildning

Möjlighet att `docker push` en bild eller skicka en annan [artefakt som stöds](container-registry-image-formats.md) , till exempel ett Helm-diagram, till ett register. Kräver [autentisering](container-registry-authentication.md) med registret med den auktoriserade identiteten. 

## <a name="pull-image"></a>Hämta bild

Möjligheten att `docker pull` en bild som inte är i karantän eller hämta en annan [artefakt som stöds](container-registry-image-formats.md) , till exempel ett Helm-diagram, från ett register. Kräver [autentisering](container-registry-authentication.md) med registret med den auktoriserade identiteten.

## <a name="delete-image-data"></a>Ta bort avbildnings data

Möjligheten att [ta bort behållar avbildningar](container-registry-delete.md)eller ta bort andra [artefakter som stöds](container-registry-image-formats.md) , till exempel Helm-diagram, från ett register.

## <a name="change-policies"></a>Ändra principer

Möjlighet att konfigurera principer i ett register. Principerna omfattar avbildnings rensning, aktivering av karantän och avbildnings signering.

## <a name="sign-images"></a>Signera bilder

Möjlighet att signera bilder, som vanligt vis tilldelas till en automatiserad process, som använder ett huvud namn för tjänsten. Den här behörigheten kombineras vanligt vis med [push-avbildning](#push-image) för att tillåta att en betrodd avbildning överförs till ett register. Mer information finns [i innehålls förtroende i Azure Container Registry](container-registry-content-trust.md).

## <a name="custom-roles"></a>Anpassade roller

Precis som med andra Azure-resurser kan du skapa egna [anpassade roller](../role-based-access-control/custom-roles.md) med detaljerade behörigheter att Azure Container Registry. Tilldela sedan de anpassade rollerna till användare, tjänstens huvud namn eller andra identiteter som behöver samverka med ett register. 

För att avgöra vilka behörigheter som ska gälla för en anpassad roll, se listan över Microsoft. ContainerRegistry- [åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry), granska de tillåtna åtgärderna för de [inbyggda ACR-rollerna](../role-based-access-control/built-in-roles.md)eller kör följande kommando:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

För att definiera en anpassad roll, se [steg för att skapa en anpassad roll](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> I en anpassad roll stöder Azure Container Registry för närvarande inte jokertecken som `Microsoft.ContainerRegistry/*` eller `Microsoft.ContainerRegistry/registries/*` som beviljar åtkomst till alla matchande åtgärder. Ange eventuella nödvändiga åtgärder individuellt i rollen.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att tilldela RBAC-roller till en Azure-identitet med hjälp av [Azure Portal](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md)eller andra Azure-verktyg.

* Lär dig mer om [autentiseringsalternativ](container-registry-authentication.md) för Azure Container Registry.

* Lär dig mer om hur du aktiverar [databasens begränsade behörigheter](container-registry-repository-scoped-permissions.md) (för hands version) i ett behållar register.
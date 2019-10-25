---
title: Azure Container Registry-roller och behörigheter
description: Använd rollbaserad åtkomst kontroll i Azure (RBAC) och identitets-och åtkomst hantering (IAM) för att ge detaljerade behörigheter till resurser i ett Azure Container Registry.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: 69104cdaeb4abfc15e2ac4209e1ddbc610656c13
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793992"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry roller och behörigheter

Tjänsten Azure Container Registry stöder en uppsättning Azure-roller som ger olika behörighets nivåer till ett Azure Container Registry. Använd [rollbaserad åtkomst kontroll](../role-based-access-control/index.yml) (RBAC) i Azure för att tilldela särskilda behörigheter till användare eller tjänst huvud namn som behöver interagera med ett register.

| Roll/behörighet       | [Åtkomst till Resource Manager](#access-resource-manager) | [Skapa/ta bort registret](#create-and-delete-registry) | [Push-avbildning](#push-image) | [Hämta bild](#pull-image) | [Ta bort avbildnings data](#delete-image-data) | [Ändra principer](#change-policies) |   [Signera bilder](#sign-images)  |
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

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att tilldela RBAC-roller till en Azure-identitet med hjälp av [Azure Portal](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md)eller andra Azure-verktyg.

* Lär dig mer om [autentiseringsalternativ](container-registry-authentication.md) för Azure Container Registry.

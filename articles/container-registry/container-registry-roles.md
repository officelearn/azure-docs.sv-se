---
title: Azure Container Registry - roller och behörigheter
description: Använda Azure rollbaserad åtkomstkontroll (RBAC) och identitets- och åtkomsthantering (IAM) för att tillhandahålla detaljerade behörigheter till resurser i ett Azure container registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: e2ec1b7ad6d1489836937d30b89d0f0f681a9bfa
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819594"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry roller och behörigheter

Azure Container Registry-tjänsten stöder en uppsättning Azure-roller som kan ger olika nivåer av behörigheter till ett Azure container registry. Använd Azure [rollbaserad åtkomstkontroll](../role-based-access-control/index.yml) (RBAC) för att tilldela specifika behörigheter till användare eller tjänsthuvudnamn som måste interagera med ett register.

| Rollbehörighet /       | Access Resource Manager| Skapa/ta bort registret | [Push-överför avbildningen](#push-image) | [Hämta avbildning](#pull-image) | Ändra principer |   [Logga avbildningar](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- |
| Ägare | X | X | X | X | X |  |  
| Deltagare | X | X | X | X | X |  |  
| Läsare | X |  |  | X |  |  | 
| AcrPush |  |  | X | X |  |  |  
| AcrPull |  |  |  | X |  |  |  
| AcrImageSigner |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Skilja mellan användare och tjänster

Alla time-behörigheter tillämpas, bästa praxis är att tillhandahålla den mest begränsad uppsättningen behörigheter för en person eller tjänst, att utföra en uppgift. Följande behörighetsuppsättningar representerar en uppsättning funktioner som kan användas av människor och fjärradministrerad tjänster.

### <a name="cicd-solutions"></a>CI/CD-lösningar

Vid automatisering av `docker build` kommandon från CI/CD-lösningar, du behöver `docker push` funktioner. Dessa scenarier med fjärradministrering service föreslår vi att tilldela den **AcrPush** roll. Den här rollen, till skillnad från den bredare **deltagare** roll, förhindrar kontot från utför andra åtgärder i registret eller åtkomst till Azure Resource Manager.

### <a name="container-host-nodes"></a>Behållarnoder för värd

På samma sätt noder som kör dina behållare måste den **AcrPull** roll, men får inte kräva **läsare** funktioner.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker-tillägg

För verktyg som Visual Studio Code [Docker-tillägg](https://code.visualstudio.com/docs/azure/docker), ytterligare resource provider åtkomst krävs för att visa de tillgängliga Azure-behållarregister. I det här fallet ger användarna åtkomst till den **läsare** eller **deltagare** roll. Dessa roller Tillåt `docker pull`, `docker push`, `az acr list`, `az acr build`, och andra funktioner. 

## <a name="access-resource-manager"></a>Access Resource Manager

Azure Resource Manager-åtkomst krävs för Azure-portalen och [Azure CLI](/cli/azure/). Till exempel för att hämta en lista över register med hjälp av den `az acr list` kommandot, du behöver den här behörigheten ange. 

## <a name="createdelete-registry"></a>Skapa/ta bort registret

Möjligheten att skapa och ta bort Azure-behållarregister.

## <a name="push-image"></a>Push-överför avbildningen

Möjligheten att `docker push` en avbildning, eller skicka en annan artefakt som stöds, till ett register. Kräver [autentisering](container-registry-authentication.md) med registret med behöriga identitet. 

## <a name="pull-image"></a>Hämta avbildning

Möjligheten att `docker pull` en icke-i karantän bild eller för att hämta en annan stöds artefakt från ett register. Kräver [autentisering](container-registry-authentication.md) med registret med behöriga identitet.

## <a name="change-policies"></a>Ändra principer

Möjligheten att konfigurera principer på ett register. Principer omfattar rensning av avbildningen, aktivera karantän och avbildning signering.

## <a name="sign-images"></a>Logga avbildningar

Möjligheten att logga avbildningar, oftast i en automatiserad process som använder ett huvudnamn för tjänsten. Den här behörigheten vanligtvis kombineras med [push bild](#push-image) att tillåta push-överföra en avbildning av en betrodd till ett register. Mer information finns i [innehåll förtroende i Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att tilldela RBAC-roller till en Azure-identitet med hjälp av den [Azure-portalen](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md), eller andra Azure-verktyg.

* Lär dig mer om [autentiseringsalternativ](container-registry-authentication.md) för Azure Container Registry.

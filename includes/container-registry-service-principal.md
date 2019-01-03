---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1a8a31b34981b0e2b68caf3f5c4952d0ee59ac8e
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2018
ms.locfileid: "53784427"
---
## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om du vill skapa ett huvudnamn för tjänsten med åtkomst till ditt behållarregister, kör du följande skript den [Azure Cloud Shell](../articles/cloud-shell/overview.md) eller en lokal installation av den [Azure CLI](/cli/azure/install-azure-cli). Skriptet formateras för Bash-gränssnittet.

Innan du kör skriptet måste du uppdatera den `ACR_NAME` variabeln med namnet på ditt behållarregister. Den `SERVICE_PRINCIPAL_NAME` värdet måste vara unikt i Azure Active Directory-klient. Om du får ett ”`'http://acr-service-principal' already exists.`” fel, ange ett annat namn för tjänstens huvudnamn.

Du kan ändra den `--role` värde i den [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] kommandot om du vill ge olika behörigheter. En fullständig lista över roller finns i [ACR roller och behörigheter](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

När du har kört skriptet anteckna tjänstens huvudnamn **ID** och **lösenord**. När du har autentiseringsuppgifterna kan konfigurera du dina program och tjänster för att autentisera till ditt behållarregister som tjänstens huvudnamn.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Använd ett befintligt huvudnamn för tjänsten

Om du vill bevilja registeråtkomst till en befintlig tjänsts huvudnamn, måste du tilldela en ny roll till tjänstens huvudnamn. Precis som med skapar en ny tjänstens huvudnamn, kan du bevilja pull, push och pull och ägaråtkomst, bland annat.

Följande skript använder den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando för att bevilja *pull* behörigheter till ett huvudnamn för tjänsten som du anger i den `SERVICE_PRINCIPAL_ID` variabeln. Justera den `--role` värde om du vill ge olika nivåer för åtkomst.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create

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
ms.openlocfilehash: fe0a4c51c4f762d866d572e0cdbd84d9e1c626b7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56247176"
---
## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om du vill skapa ett huvudnamn för tjänsten med åtkomst till ditt behållarregister, kör du följande skript den [Azure Cloud Shell](../articles/cloud-shell/overview.md) eller en lokal installation av den [Azure CLI](/cli/azure/install-azure-cli). Skriptet formateras för Bash-gränssnittet.

Innan du kör skriptet måste du uppdatera den `ACR_NAME` variabeln med namnet på ditt behållarregister. Den `SERVICE_PRINCIPAL_NAME` värdet måste vara unikt i Azure Active Directory-klient. Om du får ett ”`'http://acr-service-principal' already exists.`” fel, ange ett annat namn för tjänstens huvudnamn.

Du kan ändra den `--role` värde i kommandot [az ad sp create-for-rbac][/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac] om du vill ge olika behörigheter. En fullständig lista över roller finns i [ACR roller och behörigheter](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

När du har kört skriptet anteckna tjänstens huvudnamn **ID** och **lösenord**. När du har autentiseringsuppgifterna kan konfigurera du dina program och tjänster för att autentisera till ditt behållarregister som tjänstens huvudnamn.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Använd ett befintligt huvudnamn för tjänsten

Om du vill bevilja registeråtkomst till en befintlig tjänsts huvudnamn, måste du tilldela en ny roll till tjänstens huvudnamn. Precis som med skapar en ny tjänstens huvudnamn, kan du bevilja pull, push och pull och ägaråtkomst, bland annat.

Följande skript använder kommandot [az-rolltilldelning create][/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create] för att bevilja *pull* behörigheter till ett huvudnamn för tjänsten som du anger i `SERVICE_PRINCIPAL_ID`variabeln. Justera den `--role` värde om du vill ge olika nivåer för åtkomst.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment

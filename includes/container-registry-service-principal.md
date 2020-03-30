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
ms.openlocfilehash: 9e4f2e355240ba8682cbe9f86f2be94e7dd0d92d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70032351"
---
## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om du vill skapa ett tjänsthuvudnamn med åtkomst till behållarregistret kör du följande skript i [Azure Cloud Shell](../articles/cloud-shell/overview.md) eller en lokal installation av Azure [CLI](/cli/azure/install-azure-cli). Skriptet är formaterat för Bash-skalet.

Innan du kör skriptet uppdaterar du variabeln `ACR_NAME` med namnet på behållarregistret. Värdet `SERVICE_PRINCIPAL_NAME` måste vara unikt i din Azure Active Directory-klientorganisation. Om du får`'http://acr-service-principal' already exists.`ett " " fel anger du ett annat namn för tjänstens huvudnamn.

Du kan också `--role` ändra värdet i kommandot [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] om du vill bevilja olika behörigheter. En fullständig lista över roller finns i [ACR-roller och behörigheter](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

När du har kört skriptet bör du ta del av tjänstens huvudnamns **ID** och **lösenord**. När du har dess autentiseringsuppgifter kan du konfigurera dina program och tjänster så att de autentiseras till behållarregistret som tjänstens huvudnamn.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Använda ett befintligt tjänsthuvudnamn

Om du vill bevilja registeråtkomst till ett befintligt tjänsthuvudnamn måste du tilldela en ny roll till tjänstens huvudnamn. Som med att skapa en ny tjänst huvudman, kan du bevilja pull, push och pull, och ägare tillgång, bland annat.

Följande skript använder kommandot [az role assignment create][az-role-assignment-create] för att bevilja *pull-behörigheter* till ett tjänsthuvudnamn som du anger i variabeln. `SERVICE_PRINCIPAL_ID` Justera `--role` värdet om du vill bevilja en annan åtkomstnivå.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

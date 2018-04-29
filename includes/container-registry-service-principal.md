---
title: ta med fil
description: ta med fil
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 04/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 6ed114ea6162c9d4888b6f86998cfb422a3944e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan använda följande skript för att skapa ett huvudnamn för tjänsten åtkomst till behållaren registret. Uppdatering av `ACR_NAME` variabeln med namnet på behållaren registret, och eventuellt den `--role` värde i den [az ad sp skapa-för-rbac] [ az-ad-sp-create-for-rbac] kommando för att ge olika behörigheter. Du måste ha den [Azure CLI](/cli/azure/install-azure-cli) installerat för att kunna använda det här skriptet.

När du har kört skriptet anteckna tjänstens huvudnamn **ID** och **lösenord**. När du har dess autentiseringsuppgifter kan konfigurera du dina program och tjänster för att autentisera till behållaren registret som tjänstens huvudnamn.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Använd ett befintligt huvudnamn för tjänsten

Om du vill ge registeråtkomst till en befintlig tjänstens huvudnamn, måste du tilldela en ny roll till tjänstens huvudnamn. Du kan bevilja pull, push och pull och ägare åtkomst som skapar en ny tjänst huvudnamn.

Följande skript använder den [az rolltilldelning skapa] [ az-role-assignment-create] kommando för att ge *pull* behörigheter till ett huvudnamn för tjänsten som du anger i den `SERVICE_PRINCIPAL_ID` variabeln. Justera det `--role` värde om du vill ge olika nivåer av åtkomst.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create

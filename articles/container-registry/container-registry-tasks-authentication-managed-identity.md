---
title: Använd en hanterad identitet med Azure Container Registry uppgifter
description: Aktivera en hanterad identitet för Azure-resurser i en Azure Container Registry aktivitet för att tillåta att aktiviteten får åtkomst till andra Azure-resurser, inklusive andra privata behållar register.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: 9f7c083a079e42172a9e2865f90293fa4d6813d8
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640424"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Använd en Azure-hanterad identitet i ACR-aktiviteter 

Aktivera en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i en [ACR-uppgift](container-registry-tasks-overview.md)så att aktiviteten kan komma åt andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter. Du kan till exempel använda en hanterad identitet för att aktivera ett uppgifts steg för att hämta eller skicka behållar avbildningar till ett annat register.

I den här artikeln får du lära dig hur du använder Azure CLI för att aktivera en användardefinierad eller systemtilldelad hanterad identitet på en ACR-aktivitet. Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI. Om du vill använda det lokalt, krävs version 2.0.68 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

Scenarier för att komma åt skyddade resurser från en ACR-aktivitet med hjälp av en hanterad identitet finns i:

* [Autentisering mellan register](container-registry-tasks-cross-registry-authentication.md)
* [Få åtkomst till externa resurser med hemligheter lagrade i Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

En hanterad identitet för Azure-resurser ger utvalda Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan konfigurera en ACR-aktivitet med en hanterad identitet så att aktiviteten kan komma åt andra skyddade Azure-resurser, utan att skicka autentiseringsuppgifter i aktivitets stegen.

Hanterade identiteter är av två typer:

* *Användarspecifika identiteter*, som du kan tilldela till flera resurser och bevara så länge du vill. Användarspecifika identiteter är för närvarande en för hands version.

* En *systemtilldelad identitet*, som är unik för en speciell resurs, till exempel en ACR-aktivitet och varar för den aktuella resursens livs längd.

Du kan aktivera antingen eller båda typerna av identitet i en ACR-aktivitet. Ge identitets åtkomst till en annan resurs, precis som alla säkerhets objekt. När aktiviteten körs använder den identiteten för att få åtkomst till resursen i alla aktivitets steg som kräver åtkomst.

## <a name="steps-to-use-a-managed-identity"></a>Steg för att använda en hanterad identitet

Följ dessa övergripande steg för att använda en hanterad identitet med en ACR-uppgift.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. Valfritt Skapa en användardefinierad identitet

Om du planerar att använda en användardefinierad identitet kan du använda en befintlig identitet. Du kan också skapa identiteten med hjälp av Azure CLI eller andra Azure-verktyg. Använd till exempel kommandot [AZ Identity Create][az-identity-create] . 

Hoppa över det här steget om du endast planerar att använda en tilldelad identitet. Du kan skapa en systemtilldelad identitet när du skapar ACR-aktiviteten.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Aktivera identitet för en ACR-uppgift

När du skapar en ACR-uppgift kan du välja att aktivera en tilldelad identitet, en tilldelad identitet eller både och. Du kan till exempel skicka `--assign-identity` parametern när du kör kommandot [AZ ACR Task Create][az-acr-task-create] i Azure CLI.

Om du vill aktivera en tilldelad identitet `--assign-identity` skickar du utan värde `assign-identity [system]`eller. Följande kommando skapar en Linux-uppgift från en offentlig GitHub-lagringsplats som skapar `hello-world` avbildningen med en git commit-utlösare och med en systemtilldelad hanterad identitet:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity
```

Om du vill aktivera en användardefinierad identitet skickar `--assign-identity` du ett värde för identitetens *resurs-ID* . Följande kommando skapar en Linux-uppgift från en offentlig GitHub-lagringsplats som skapar `hello-world` avbildningen med en git commit-utlösare och med en användardefinierad hanterad identitet:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity <resourceID>
```

Du kan hämta resurs-ID för identiteten genom att köra kommandot [AZ Identity show][az-identity-show] . Resurs-ID: t för ID- *myUserAssignedIdentity* i resurs gruppen *myResourceGroup* har formatet. 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Ge identitets behörigheterna åtkomst till andra Azure-resurser

Beroende på kraven för din uppgift ger du identitets behörighet att få åtkomst till andra Azure-resurser. Exempel:

* Tilldela den hanterade identiteten en roll med pull, push och pull eller andra behörigheter till ett mål behållar register i Azure. En fullständig lista över register roller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md). 
* Tilldela den hanterade identiteten en roll för att läsa hemligheter i ett Azure Key Vault.

Använd [Azure CLI](../role-based-access-control/role-assignments-cli.md) eller andra Azure-verktyg för att hantera rollbaserad åtkomst till resurser. Du kan till exempel köra kommandot [AZ roll tilldelning Create][az-role-assignment-create] för att tilldela identiteten en roll till identiteten. 

I följande exempel tilldelas en hanterad identitet behörighet att hämta från ett behållar register. Kommandot anger tjänstens *huvud namn-ID* för identiteten och *resurs-ID: t* för mål registret.


```azurecli
az role assignment create --assignee <servicePrincipalID> --scope <registryID> --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. Valfritt Lägg till autentiseringsuppgifter för uppgiften

Om din uppgift hämtar eller push-överför avbildningar till ett annat Azure Container Registry, lägger du till autentiseringsuppgifter i uppgiften för att autentisera identiteten. Kör kommandot [AZ ACR Task Credential Add][az-acr-task-credential-add] och skicka `--use-identity` parametern för att lägga till identitetens autentiseringsuppgifter för aktiviteten. 

Om du till exempel vill lägga till autentiseringsuppgifter för en systemtilldelad identitet för att autentiseramed registret targetregistry `use-identity [system]`, pass:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Om du vill lägga till autentiseringsuppgifter för en användardefinierad identitet för att autentisera med registret *targetregistry*, `use-identity` skickar du ett värde för identitetens *klient-ID* . Exempel:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Du kan hämta klient-ID: t för identiteten genom att köra kommandot [AZ Identity show][az-identity-show] . Klient-ID är ett GUID för formuläret `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och använder en tilldelad eller systemtilldelad hanterad identitet på en ACR-aktivitet. Scenarier för att komma åt skyddade resurser från en ACR-aktivitet med hjälp av en hanterad identitet finns i:

* [Autentisering mellan register](container-registry-tasks-cross-registry-authentication.md)
* [Få åtkomst till externa resurser med hemligheter lagrade i Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli

---
title: Hanterad identitet i ACR-uppgift
description: Aktivera en hanterad identitet för Azure-resurser i en Azure Container Registry-uppgift så att aktiviteten kan komma åt andra Azure-resurser, inklusive andra privata behållarregister.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111764"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Använda en Azure-hanterad identitet i ACR-uppgifter 

Aktivera en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i en [ACR-aktivitet](container-registry-tasks-overview.md)så att aktiviteten kan komma åt andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter. Använd till exempel en hanterad identitet för att aktivera ett aktivitetssteg för att hämta eller skicka behållaravbildningar till ett annat register.

I den här artikeln får du lära dig hur du använder Azure CLI för att aktivera en användartilldelad eller systemtilldelad hanterad identitet på en ACR-uppgift. Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI. Om du vill använda den lokalt krävs version 2.0.68 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

I exempelsyfte använder exempelkommandona i den här artikeln [az acr-uppgift][az-acr-task-create] för att skapa en grundläggande avbildningsaktivitet som möjliggör en hanterad identitet. Exempel på scenarier för åtkomst till skyddade resurser från en ACR-aktivitet med hjälp av en hanterad identitet finns i:

* [Autentisering mellan register](container-registry-tasks-cross-registry-authentication.md)
* [Få tillgång till externa resurser med hemligheter som lagras i Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Varför använda en hanterad identitet?

En hanterad identitet för Azure-resurser ger valda Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan konfigurera en ACR-aktivitet med en hanterad identitet så att aktiviteten kan komma åt andra skyddade Azure-resurser, utan att skicka autentiseringsuppgifter i aktivitetsstegen.

Hanterade identiteter är av två typer:

* *Användartilldelade identiteter*, som du kan tilldela till flera resurser och kvarstår så länge du vill. Användartilldelade identiteter förhandsgranskas för närvarande.

* En *systemtilldelad identitet*, som är unik för en viss resurs, till exempel en ACR-aktivitet och varar under resursens livstid.

Du kan aktivera antingen eller båda typerna av identitet i en ACR-uppgift. Bevilja identitetsåtkomst till en annan resurs, precis som alla säkerhetsobjekt. När aktiviteten körs används identiteten för att komma åt resursen i alla aktivitetssteg som kräver åtkomst.

## <a name="steps-to-use-a-managed-identity"></a>Steg för att använda en hanterad identitet

Följ de här stegen på hög nivå om du vill använda en hanterad identitet med en ACR-uppgift.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Valfritt) Skapa en användartilldelad identitet

Om du planerar att använda en användartilldelad identitet använder du en befintlig identitet eller skapar identiteten med Azure CLI eller andra Azure-verktyg. Använd till exempel kommandot [az identity create.][az-identity-create] 

Om du planerar att bara använda en systemtilldelad identitet hoppar du över det här steget. Du skapar en systemtilldelad identitet när du skapar ACR-uppgiften.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Aktivera identitet för en ACR-uppgift

När du skapar en ACR-uppgift aktiverar du eventuellt en användartilldelad identitet, en systemtilldelad identitet eller båda. Skicka till exempel `--assign-identity` parametern när du kör kommandot [az acr task create][az-acr-task-create] i Azure CLI.

Om du vill aktivera en `--assign-identity` systemtilldelad `assign-identity [system]`identitet skickar du utan värde eller . Följande exempelkommando skapar en Linux-uppgift från en offentlig GitHub-databas som skapar avbildningen `hello-world` och aktiverar en systemtilldelad hanterad identitet:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Om du vill aktivera en `--assign-identity` användartilldelad identitet skickar du med värdet för *identitetens resurs-ID.* Följande exempelkommando skapar en Linux-uppgift från en offentlig GitHub-databas som skapar avbildningen `hello-world` och aktiverar en användartilldelad hanterad identitet:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Du kan hämta identitetens resurs-ID genom att köra kommandot [az identity show.][az-identity-show] Resurs-ID:et för ID *myUserAssignedIdentity* i resursgruppen *myResourceGroup* är av formuläret: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Bevilja identitetsbehörigheter för åtkomst till andra Azure-resurser

Beroende på kraven för din aktivitet, bevilja identitetsbehörigheter för att komma åt andra Azure-resurser. Exempel på rekommendationer:

* Tilldela den hanterade identiteten en roll med pull, push and pull eller andra behörigheter till ett målbehållareregister i Azure. En fullständig lista över registerroller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md). 
* Tilldela den hanterade identiteten en roll för att läsa hemligheter i ett Azure-nyckelvalv.

Använd [Azure CLI](../role-based-access-control/role-assignments-cli.md) eller andra Azure-verktyg för att hantera rollbaserad åtkomst till resurser. Kör till exempel kommandot [az role assignment create][az-role-assignment-create] för att tilldela identiteten en roll till resursen. 

I följande exempel tilldelas en hanterad identitet behörigheterna att hämta från ett behållarregister. Kommandot anger *huvud-ID* för aktivitetsidentiteten och *resurs-ID* för målregistret.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Valfritt) Lägg till autentiseringsuppgifter i uppgiften

Om aktiviteten behöver autentiseringsuppgifter för att hämta eller skicka avbildningar till ett annat anpassat register eller för att komma åt andra resurser lägger du till autentiseringsuppgifter i aktiviteten. Kör kommandot [az acr task-autentiseringsuppgifter][az-acr-task-credential-add] för att `--use-identity` lägga till autentiseringsuppgifter och skicka parametern för att ange att identiteten kan komma åt autentiseringsuppgifterna. 

Om du till exempel vill lägga till autentiseringsuppgifter för en systemtilldelad `use-identity [system]`identitet för att autentisera med *målregistret*för Azure-behållarregister , skicka:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Om du vill lägga till autentiseringsuppgifter för en användartilldelad identitet för att autentisera med *registermålregistret*skickar du `use-identity` ett värde för *klient-ID:n* för identiteten. Ett exempel:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Du kan hämta klient-ID för identiteten genom att köra kommandot [az identity show.][az-identity-show] Klient-ID:et är ett `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID för formuläret .

### <a name="5-run-the-task"></a>5. Kör uppgiften

När du har konfigurerat en aktivitet med en hanterad identitet kör du aktiviteten. Om du till exempel vill testa en av de uppgifter som skapas i den här artikeln utlöser du den manuellt med kommandot [az acr task run.][az-acr-task-run] Om du har konfigurerat ytterligare, automatiska uppgiftsutlösare körs aktiviteten när den utlöses automatiskt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du aktiverar och använder en användartilldelad eller systemtilldelad hanterad identitet för en ACR-uppgift. Scenarier för åtkomst till skyddade resurser från en ACR-aktivitet med hjälp av en hanterad identitet finns i:

* [Autentisering mellan register](container-registry-tasks-cross-registry-authentication.md)
* [Få tillgång till externa resurser med hemligheter som lagras i Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli

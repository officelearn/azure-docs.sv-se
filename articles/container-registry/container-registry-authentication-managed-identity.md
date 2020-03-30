---
title: Autentisera med hanterad identitet
description: Ge åtkomst till avbildningar i ditt privata behållarregister med hjälp av en användartilldelad eller systemtilldelad hanterad Azure-identitet.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9b8bed78629d3a9739ec00772ad5c8216a04c122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456488"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Använda en Azure-hanterad identitet för att autentisera till ett Azure-behållarregister 

Använd en [hanterad identitet för Azure-resurser för](../active-directory/managed-identities-azure-resources/overview.md) att autentisera till ett Azure-behållarregister från en annan Azure-resurs, utan att behöva ange eller hantera registerautentiseringsuppgifter. Du kan till exempel konfigurera en användartilldelad eller systemtilldelad hanterad identitet på en virtuell Linux-dator för att komma åt behållaravbildningar från behållarregistret, lika enkelt som du använder ett offentligt register.

I den här artikeln får du lära dig mer om hanterade identiteter och hur du:

> [!div class="checklist"]
> * Aktivera en användartilldelad eller systemtilldelad identitet på en virtuell Azure-dator
> * Bevilja identitetsåtkomst till ett Azure-behållarregister
> * Använd den hanterade identiteten för att komma åt registret och hämta en behållaravbildning 

För att skapa Azure-resurser kräver den här artikeln att du kör Azure CLI version 2.0.55 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Om du vill konfigurera ett behållarregister och skicka en behållaravbildning till det måste du också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.

## <a name="why-use-a-managed-identity"></a>Varför använda en hanterad identitet?

En hanterad identitet för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan konfigurera [vissa Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), inklusive virtuella datorer, med en hanterad identitet. Använd sedan identiteten för att komma åt andra Azure-resurser, utan att skicka autentiseringsuppgifter i kod eller skript.

Hanterade identiteter är av två typer:

* *Användartilldelade identiteter*, som du kan tilldela till flera resurser och kvarstår så länge du vill. Användartilldelade identiteter förhandsgranskas för närvarande.

* En *systemhanterad identitet*, som är unik för en viss resurs som en enda virtuell dator och varar under resursens livstid.

När du har konfigurerat en Azure-resurs med en hanterad identitet ger du identiteten den åtkomst du vill ha till en annan resurs, precis som alla säkerhetsobjekt. Tilldela till exempel en hanterad identitet en roll med pull, push and pull eller andra behörigheter till ett privat register i Azure. (En fullständig lista över registerroller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).) Du kan ge en identitetsåtkomst till en eller flera resurser.

Använd sedan identiteten för att autentisera till alla [tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), utan några autentiseringsuppgifter i koden. Om du vill använda identiteten för att komma åt ett Azure-behållarregister från en virtuell dator autentiserar du med Azure Resource Manager. Välj hur du autentiserar med den hanterade identiteten, beroende på ditt scenario:

* [Hämta en Azure AD-åtkomsttoken](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programmässigt med HTTP- eller REST-anrop

* Använda [Azure SDK:er](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Logga in på Azure CLI eller PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) med identiteten. 

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Om du inte redan har ett Azure-behållarregister skapar du ett register och skickar en exempelbehållareavbildning till det. Stegvis finns i [Snabbstart: Skapa ett privat behållarregister med Azure CLI](container-registry-get-started-azure-cli.md).

Den här artikeln förutsätter att du har behållaravbildningen `aci-helloworld:v1` lagrad i registret. Exemplen använder ett registernamn *för myContainerRegistry*. Ersätt med ditt eget register och bildnamn i senare steg.

## <a name="create-a-docker-enabled-vm"></a>Skapa en Docker-aktiverad virtuell dator

Skapa en virtuell Ubuntu-dator med Docker-aktiverad. Du måste också installera [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) på den virtuella datorn. Om du redan har en virtuell Azure-dator hoppar du över det här steget för att skapa den virtuella datorn.

Distribuera en standard virtuell Ubuntu Azure-dator med [az vm create][az-vm-create]. I följande exempel skapas en virtuell dator med namnet *myDockerVM* i en befintlig resursgrupp med namnet *myResourceGroup:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot är klart bör `publicIpAddress` du notera vad som visas av Azure CLI. Använd den här adressen om du vill skapa SSH-anslutningar till den virtuella datorn.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När den virtuella datorn har körts gör du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress* med den offentliga IP-adressen för din virtuella dator.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommando för att installera Docker på den virtuella datorn:

```bash
sudo apt install docker.io -y
```

Efter installationen kör du följande kommando för att kontrollera att Docker körs korrekt på den virtuella datorn:

```bash
sudo docker run -it hello-world
```

Resultat:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installera Azure CLI

Följ stegen i [Installera Azure CLI med apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) för att installera Azure CLI på din virtuella Ubuntu-dator. I den här artikeln kontrollerar du att du installerar version 2.0.55 eller senare.

Avsluta SSH-sessionen.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Exempel 1: Åtkomst med en användartilldelad identitet

### <a name="create-an-identity"></a>Skapa en identitet

Skapa en identitet i din prenumeration med kommandot [az identity create.](/cli/azure/identity?view=azure-cli-latest#az-identity-create) Du kan använda samma resursgrupp som du använde tidigare för att skapa behållarregistret eller den virtuella datorn, eller en annan.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Om du vill konfigurera identiteten i följande steg använder du kommandot [az identity show][az-identity-show] för att lagra identitetens resurs-ID och tjänstens huvud-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Eftersom du behöver identitetens ID i ett senare steg när du loggar in på CLI från den virtuella datorn visar du värdet:

```bash
echo $userID
```

ID:et är av lagen om ursäkt:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurera den virtuella datorn med identiteten

Följande kommando [för att tilldela azure vm-identitet][az-vm-identity-assign] konfigurerar den virtuella datorn i Docker med den användartilldelade identiteten:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Bevilja identitetsåtkomst till behållarregistret

Konfigurera nu identiteten för att komma åt behållarregistret. Använd först kommandot [az acr visa][az-acr-show] för att hämta registrets resurs-ID:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Använd kommandot [az role assignment create][az-role-assignment-create] för att tilldela rollen AcrPull till registret. Den här rollen ger [pull-behörigheter](container-registry-roles.md) till registret. Om du vill ange både pull- och pushbehörigheter tilldelar du rollen ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Använda identiteten för att komma åt registret

SSH till den virtuella Docker-datorn som är konfigurerad med identiteten. Kör följande Azure CLI-kommandon med hjälp av Azure CLI som är installerat på den virtuella datorn.

Först autentisera till Azure CLI med [az-inloggning][az-login]med hjälp av den identitet som du konfigurerat på den virtuella datorn. För `<userID>`ersätter du ID:t för den identitet som du hämtade i ett tidigare steg. 

```azurecli
az login --identity --username <userID>
```

Autentisera sedan till registret med [az acr-inloggning][az-acr-login]. När du använder det här kommandot använder CLI active `az login` directory-token som skapades när du körde för att sömlöst autentisera sessionen med behållarregistret. (Beroende på den virtuella datorns inställningar kan du behöva köra `sudo`det här kommandot och docker-kommandona med .)

```azurecli
az acr login --name myContainerRegistry
```

Du borde `Login succeeded` se ett meddelande. Du kan `docker` sedan köra kommandon utan att ange autentiseringsuppgifter. Kör till exempel [docker][docker-pull] pull `aci-helloworld:v1` för att hämta avbildningen och ange registrets inloggningsservernamn. Inloggningsserverns namn består av ditt behållarregisternamn `.azurecr.io` (alla gemener) följt av till exempel `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Exempel 2: Åtkomst med en systemtilldelad identitet

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurera den virtuella datorn med en systemhanterad identitet

Följande kommando [för azure vm-identitetstilldelar][az-vm-identity-assign] konfigurerar den virtuella docker-datorn med en systemtilldelad identitet:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Använd kommandot [az vm visa][az-vm-show] för att `principalId` ange en variabel till värdet för (tjänstens huvud-ID) för den virtuella datorns identitet, som ska användas i senare steg.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Bevilja identitetsåtkomst till behållarregistret

Konfigurera nu identiteten för att komma åt behållarregistret. Använd först kommandot [az acr visa][az-acr-show] för att hämta registrets resurs-ID:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Använd kommandot [az role assignment create][az-role-assignment-create] för att tilldela rollen AcrPull till identiteten. Den här rollen ger [pull-behörigheter](container-registry-roles.md) till registret. Om du vill ange både pull- och pushbehörigheter tilldelar du rollen ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Använda identiteten för att komma åt registret

SSH till den virtuella Docker-datorn som är konfigurerad med identiteten. Kör följande Azure CLI-kommandon med hjälp av Azure CLI som är installerat på den virtuella datorn.

Först autentisera Azure CLI med [az-inloggning][az-login]med hjälp av den systemtilldelade identiteten på den virtuella datorn.

```azurecli
az login --identity
```

Autentisera sedan till registret med [az acr-inloggning][az-acr-login]. När du använder det här kommandot använder CLI active `az login` directory-token som skapades när du körde för att sömlöst autentisera sessionen med behållarregistret. (Beroende på den virtuella datorns inställningar kan du behöva köra `sudo`det här kommandot och docker-kommandona med .)

```azurecli
az acr login --name myContainerRegistry
```

Du borde `Login succeeded` se ett meddelande. Du kan `docker` sedan köra kommandon utan att ange autentiseringsuppgifter. Kör till exempel [docker][docker-pull] pull `aci-helloworld:v1` för att hämta avbildningen och ange registrets inloggningsservernamn. Inloggningsserverns namn består av ditt behållarregisternamn `.azurecr.io` (alla gemener) följt av till exempel `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig mer om hur du använder hanterade identiteter med Azure Container Registry och hur du:

> [!div class="checklist"]
> * Aktivera en användartilldelad eller systemtilldelad identitet i en virtuell Azure-dator
> * Bevilja identitetsåtkomst till ett Azure-behållarregister
> * Använd den hanterade identiteten för att komma åt registret och hämta en behållaravbildning

* Läs mer om [hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli

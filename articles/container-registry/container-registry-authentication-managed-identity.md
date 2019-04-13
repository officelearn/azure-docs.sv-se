---
title: Azure Container Registry-autentisering med en hanterad identitet
description: Ger åtkomst till avbildningar i ditt privata behållarregister med hjälp av en Användartilldelad eller systemtilldelade hanterad Azure identitet.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/16/2019
ms.author: danlep
ms.openlocfilehash: 728a2f8cf61bbe0691350b9de45a5fab6b90cadb
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526630"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Använd en Azure-hanterad identitet för att autentisera till Azure container registry 

Använd en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) att autentisera till Azure container registry från en annan Azure-resursen, utan att du behöver ange eller hantera autentiseringsuppgifter för registret. Till exempel konfigurera en Användartilldelad eller systemtilldelade hanterad identitet på en Linux-VM till behållaravbildningar för åtkomst från ditt behållarregister lika enkelt som du använder ett offentligt register.

För den här artikeln får du lära dig mer om hanterade identiteter och hur du:

> [!div class="checklist"]
> * Aktivera en Användartilldelad eller systemtilldelad identitet på en Azure VM
> * Ge åtkomst till ett Azure container registry identitet
> * Använd hanterad identitet för att komma åt registret och hämta en behållaravbildning 

Den här artikeln för att skapa Azure-resurser, kräver att du kör Azure CLI version 2.0.55 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Om du vill konfigurera ett behållarregister och push-överföra en behållaravbildning till den, måste du också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.

## <a name="why-use-a-managed-identity"></a>Varför ska jag använda en hanterad identitet?

En hanterad identitet för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD Azure). Du kan konfigurera [vissa Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), inklusive virtuella datorer med en hanterad identitet. Använd sedan identiteten för att få åtkomst till andra Azure-resurser utan att skicka autentiseringsuppgifter i kod eller skript.

Hanterade identiteter finns två typer av:

* *Användartilldelade identiteter*, som du kan tilldela flera resurser och bevara så länge som vill ha. Användartilldelade identiteter finns för närvarande i förhandsversion.

* En *system-hanterad identitet*, som är unik för en specifik resurs, exempelvis en virtuell dator och varar i livslängden för den här resursen.

När du har konfigurerat en Azure-resurs med en hanterad identitet, ge identiteten åtkomst som du vill ha en annan resurs, precis som alla säkerhetsobjekt. Till exempel tilldela en hanterad identitet en roll med pull, push och pull eller andra behörigheter till ett privat register i Azure. (En fullständig lista över registret roller finns [Azure Container Registry roller och behörigheter](container-registry-roles.md).) Du kan ge en identity-åtkomst till en eller flera resurser.

Använd sedan identiteten för att autentisera till någon [tjänst som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), utan några autentiseringsuppgifter i din kod. Om du vill använda identiteten för att få åtkomst till ett Azure container registry från en virtuell dator måste autentisera du med Azure Resource Manager. Välj hur du autentiserar med hjälp av den hanterade identitet, beroende på ditt scenario:

* [Hämta en Azure AD-åtkomsttoken](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programmässigt med hjälp av HTTP- eller REST-anrop

* Använd den [Azure SDK: er](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Logga in på Azure CLI eller PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) med identiteten. 

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Om du inte redan har ett Azure container registry kan du skapa ett register och push-överföra en behållaravbildning för exemplet till den. Anvisningar finns i [snabbstarten: Skapa ett privat behållarregister med Azure CLI](container-registry-get-started-azure-cli.md).

Den här artikeln förutsätter att du har den `aci-helloworld:v1` behållaravbildning som lagras i registret. I exemplen används ett registernamn av *myContainerRegistry*. Ersätt med din egen registret och avbildningsnamn i senare steg.

## <a name="create-a-docker-enabled-vm"></a>Skapa en Docker-aktiverad virtuell dator

Skapa en Docker-aktiverad virtuell Ubuntu-dator. Du måste också installera den [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) på den virtuella datorn. Om du redan har en Azure virtuell dator kan du hoppa över det här steget om du vill skapa den virtuella datorn.

Distribuera en standard Ubuntu Azure-dator med [az vm skapa][az-vm-create]. I följande exempel skapas en virtuell dator med namnet *myDockerVM* i en befintlig resursgrupp med namnet *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot har slutförts kan du ta del av den `publicIpAddress` visas av Azure CLI. Använd den här adressen för att skapa SSH-anslutningar till den virtuella datorn.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När Virtuellt datorn körs, skapar du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress* med den offentliga IP-adressen för den virtuella datorn.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommando för att installera Docker på den virtuella datorn:

```bash
sudo apt install docker.io -y
```

Kör följande kommando för att verifiera att Docker körs korrekt på den virtuella datorn efter installationen:

```bash
sudo docker run -it hello-world
```

Utdata:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installera Azure CLI

Följ stegen i [installera Azure CLI med apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) att installera Azure CLI på Ubuntu-dator. Den här artikeln är att se till att du installerar version 2.0.55 eller senare.

Avsluta SSH-sessionen.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Exempel 1: Åtkomst med en Användartilldelad identitet

### <a name="create-an-identity"></a>Skapa en identitet

Skapa en identitet i din prenumeration med hjälp av den [az identitet skapa](/cli/azure/identity?view=azure-cli-latest#az-identity-create) kommando. Du kan använda samma resursgrupp som du använde tidigare för att skapa behållarregistret eller virtuell dator eller ett annat namn.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Konfigurera identitet i följande steg med den [az identitet show] [ az-identity-show] kommando för att lagra Identitetens resurs-ID och ID för tjänstens huvudnamn i variabler.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Eftersom du behöver den identitets-ID i ett senare steg när du loggar in till CLI från din virtuella dator kan du visa värdet:

```bash
echo $userID
```

ID: T är i formatet:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurera den virtuella datorn med identiteten

Följande [az identitet för virtuell dator tilldelar] [ az-vm-identity-assign] kommando konfigurerar dina virtuella datorer med Docker med Användartilldelad identitet:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Bevilja identitet åtkomst till behållarregistret

Nu ska du konfigurera identitet för att komma åt ditt behållarregister. Först använder den [az acr show] [ az-acr-show] kommando för att hämta resurs-ID för registret:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Använd den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando för att tilldela rollen AcrPull till registret. Den här rollen ger [Hämta behörigheter](container-registry-roles.md) i registret. Om du vill ge båda pull och push-behörigheter kan du tilldela rollen ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Använda identiteten för att komma åt registret

SSH till den Docker-dator som har konfigurerats med identiteten. Kör följande Azure CLI-kommandon, med hjälp av Azure CLI installerat på den virtuella datorn.

Först måste autentisera till Azure CLI med [az-inloggning][az-login], med det identitet som du har konfigurerat på den virtuella datorn. För `<userID>`, Ersätt ID för den identitet som du hämtade i föregående steg. 

```azurecli
az login --identity --username <userID>
```

Sedan kan autentisera till registret med [docker login][az-acr-login]. När du använder det här kommandot CLI använder Active Directory-token som skapas när du körde `az login` att sömlöst autentisera din session med container registry. (Beroende på den Virtuella datorns inställningar kan du behöva köra det här kommandot och docker-kommandon med `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Du bör se en `Login succeeded` meddelande. Du kan sedan köra `docker` kommandon utan att ange autentiseringsuppgifter. Till exempel [docker pull] [ docker-pull] till pull den `aci-helloworld:v1` bild, ange namnet på inloggningsservern för registret. Namnet på inloggningsservern består av din behållarregisternamnet (alla gemener) följt av `.azurecr.io` , till exempel `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Exempel 2: Åtkomst med en automatiskt genererad identitet

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurera den virtuella datorn med en system-hanterad identitet

Följande [az identitet för virtuell dator tilldelar] [ az-vm-identity-assign] kommando konfigurerar dina virtuella datorer med Docker med en automatiskt genererad identitet:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Använd den [az vm show] [ az-vm-show] kommando för att ange en variabel till värdet för `principalId` (ID för tjänstens huvudnamn) för den Virtuella datorns identitet, att använda i senare steg.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Bevilja identitet åtkomst till behållarregistret

Nu ska du konfigurera identitet för att komma åt ditt behållarregister. Först använder den [az acr show] [ az-acr-show] kommando för att hämta resurs-ID för registret:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Använd den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando för att tilldela rollen AcrPull till identiteten. Den här rollen ger [Hämta behörigheter](container-registry-roles.md) i registret. Om du vill ge båda pull och push-behörigheter kan du tilldela rollen ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Använda identiteten för att komma åt registret

SSH till den Docker-dator som har konfigurerats med identiteten. Kör följande Azure CLI-kommandon, med hjälp av Azure CLI installerat på den virtuella datorn.

Först autentisera Azure CLI med [az-inloggning][az-login], med systemtilldelade identiteten på den virtuella datorn.

```azurecli
az login --identity
```

Sedan kan autentisera till registret med [docker login][az-acr-login]. När du använder det här kommandot CLI använder Active Directory-token som skapas när du körde `az login` att sömlöst autentisera din session med container registry. (Beroende på den Virtuella datorns inställningar kan du behöva köra det här kommandot och docker-kommandon med `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Du bör se en `Login succeeded` meddelande. Du kan sedan köra `docker` kommandon utan att ange autentiseringsuppgifter. Till exempel [docker pull] [ docker-pull] till pull den `aci-helloworld:v1` bild, ange namnet på inloggningsservern för registret. Namnet på inloggningsservern består av din behållarregisternamnet (alla gemener) följt av `.azurecr.io` , till exempel `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om hur du använder hanterade identiteter med Azure Container Registry och hur du:

> [!div class="checklist"]
> * Aktivera en Användartilldelad eller systemtilldelad identitet i en Azure VM
> * Ge åtkomst till ett Azure container registry identitet
> * Använd hanterad identitet för att komma åt registret och hämta en behållaravbildning

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

---
title: Snabbstart – skapa ett privat Docker-register i Azure med Azure CLI
description: Lär dig snabbt att skapa ett privat Docker-containerregister med Azure CLI.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 57c72056b669865278fa8109cd7f4963a1f0887a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855246"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-cli"></a>Snabbstart: Skapa ett containerregister med hjälp av Azure CLI

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. I den här guiden får du lära dig att skapa en Azure Container Registry-instans med hjälp av Azure CLI, push-överföra en behållaravbildning till registret och distribuera behållaren från ditt register till Azure Container Instances (ACI).

För den här snabbstarten krävs att du kör Azure CLI version 2.0.27 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create][az-group-create]. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här snabbstarten skapar du ett *grundläggande* register. Azure Container Registry finns i flera olika SKU:er, som beskrivs kortfattat i följande tabell. Mer information om var och en finns i [SKU:er för Container Registry][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Skapa en ACR-instans med hjälp av kommandot [az acr create][az-acr-create]. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I följande exempel används *myContainerRegistry007*. Uppdatera det här till ett unikt värde.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

När registret har skapats ser utdata ut ungefär så här:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

I resten av den här snabbstarten använder du `<acrName>` som platshållare för namnet på containerregistret.

## <a name="log-in-to-acr"></a>Logga in på ACR

Innan du skickar och hämtar containeravbildningar måste du logga in på ACR-instansen. Det gör du med hjälp av kommandot [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar meddelandet `Login Succeeded` när det har slutförts.

## <a name="push-image-to-acr"></a>Push-överföra avbildning till ACR

Innan du kan push-överföra en avbildning till Azure Container Registry måste du ha en avbildning. Om du inte har några lokala containeravbildningar ännu kan du köra följande kommando för att hämta en befintlig avbildning från Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Innan du kan push-överföra en avbildning till ditt register måste du tagga den med det fullständiga namnet på din ACR-inloggningsserver. Kör följande kommando för att hämta det fullständiga namnet på inloggningsservern för ACR-instansen.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för ACR-instansen.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Använd slutligen [docker push][docker-push] för att överföra avbildningen till ACR-instansen. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för ACR-instansen.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Visa lista över containeravbildningar

I följande exempel visas lagringsplatserna i ett register:

```azurecli
az acr repository list --name <acrName> --output table
```

Resultat:

```bash
Result
----------------
aci-helloworld
```

I följande exempel visas taggarna för lagringsplatsen **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Resultat:

```bash
Result
--------
v1
```

## <a name="deploy-image-to-aci"></a>Distribuera avbildningen till ACI

Du måste ange autentiseringsuppgifterna för registret när du ska distribuera en containerinstans från registret som du skapat. I produktionsscenarier bör du använda [tjänstens huvudnamn][container-registry-auth-aci] för åtkomst till containerregistret, men aktivera administratörsanvändaren i registret med följande kommando för att hålla snabbstarten kort:

```azurecli
az acr update --name <acrName> --admin-enabled true
```

När administratören har aktiverats är användarnamnet det samma som ditt registernamn och du kan hämta lösenordet med det här kommandot:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Kör följande kommando för att distribuera containeravbildningen med 1 CPU-kärna och 1 GB minne. Ersätt `<acrName>`, `<acrLoginServer>` och `<acrPassword>` med de värden som du fick från tidigare kommandon.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Du bör få ett inledande svar från Azure Resource Manager med information om din container. Om du vill övervaka statusen för behållaren och kontrollera när den körs upprepar du kommandot [az container show][az-container-show]. Det ska ta mindre än en minut.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>Visa programmet

När distribueringen till ACI har genomförts hämtar du behållarens FQDN med kommandot [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

Exempel på utdata: `"aci-demo.eastus.azurecontainer.io"`

Gå till den offentliga IP-adressen som visas i webbläsaren om du vill se programmet som körs.

![Hello World-app i webbläsaren][aci-app-browser]

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du använda kommandot [az group delete][az-group-delete] till att ta bort resursgruppen, ACR-instansen och alla containeravbildningar.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Container Registry med Azure CLI, push-överförde en behållaravbildning till registret och startade en instans av den via Azure Container Instances. Fortsätt till självstudien om Azure Container Instances om du vill titta närmare på ACI.

> [!div class="nextstepaction"]
> [Självstudie för Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az-container-show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md

---
title: "Använda ACR med ett Azure DC/OS-kluster"
description: "Använd ett Azure-behållarregister med ett DC/OS-kluster i Azure Container Service"
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 90d449de19022b3b427e3d89d5beb18bbd36c6b4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Använd ACR med ett DC/OS-kluster till att distribuera ditt program

I den här artikeln går vi igenom hur du använder Azure Container Registry med ett DC/OS-kluster. Med ACR kan du lagra och hantera behållaravbildningar privat. Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * distribuera Azure Container Registry (om det behövs)
> * konfigurera ACR-autentisering i ett DC/OS-kluster
> * ladda upp en avbildning till Azure Container Registry
> * köra en behållaravbildning från Azure Container Registry.

Du behöver ett ACS DC/OS-klustret när du ska utföra stegen i den här självstudien. Om det behövs kan du skapa ett sådant med [detta skriptexempel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md).

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Distribuera Azure Container Registry

Om det behövs skapar du ett Azure-behållarregister med kommandot [az acr create](/cli/azure/acr#az_acr_create). 

I följande exempel skapas ett register med ett slumpmässigt genererat namn. Registret konfigureras också med ett administratörskonto med argumentet `--admin-enabled`.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

När registret har skapats ser utdata från Azure CLI ungefär så här: Anteckna `name` och `loginServer`, du använder dem i senare steg.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Hämta autentiseringsuppgifterna för behållarregistret med kommandot [az acr credential show](/cli/azure/acr/credential). Ersätt `--name` med namnet från föregående steg. Notera lösenordet, du behöver det i ett senare steg.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Mer information om Azure Container Registry finns i [Introduktion till privata Docker-behållarregister](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Hantera ACR-autentisering

I det vanliga sättet att push- och pull-överföra avbildningar från ett privat register autentiserar du först med registret. Om du vill göra det skulle du använda kommandot `docker login` från de klienter som behöver åtkomst till det privata registret. Eftersom ett DC/OS-kluster kan innehålla många noder, som alla måste autentiseras i ACR, är det bra att automatisera den processen för varje nod. 

### <a name="create-shared-storage"></a>Skapa delad lagring

I den här processen används en Azure-filresurs som har monterats vid varje nod i klustret. Om du inte redan har konfigurerat den delade lagringen kan du läsa [Konfigurera en filresurs i ett DC/OS-kluster](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Konfigurera ACR-autentisering

Hämta först FQDN för DC/OS-huvudservern och lagra det i en variabel.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Skapa en SSH-anslutning med huvudservern (eller den första huvudservern) i ditt DC/OS-baserade kluster. Uppdatera användarnamnet om du använde något annat än standardvärdet när du skapade klustret.

```azurecli-interactive
ssh azureuser@$FQDN
```

Kör sedan följande kommando för att logga in på Azure Container Registry. Ersätt `--username` med namnet på behållarregistret och `--password` med något av de angivna lösenorden. Ersätt det sista argumentet *mycontainerregistry.azurecr.io* i exemplet med namnet på inloggningsservern för behållarregistret. 

Det här kommandot lagrar autentiseringsvärdena lokalt under sökvägen `~/.docker`.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Skapa en komprimerad fil som innehåller autentiseringsvärdena för behållarregistret.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Kopiera den här filen till klustrets delade lagring. Det här steget gör filen tillgänglig vid alla noder i DC/OS-klustret.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Ladda upp avbildningen till ACR

Använd en utvecklingsdator eller något annat system där Docker installerat, skapa en avbildning och ladda upp den till Azure-behållarregistret.

Skapa en behållare från Ubuntu-avbildningen.

```azurecli-interactive
docker run ubuntu --name base-image
```

Fånga nu behållaren i en ny avbildning. Avbildningens namn måste innehålla `loginServer`-namnet för behållarregistret på formatet `loginServer/imageName`.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

Logga in på Azure-behållarregistret. Ersätt namnet med loginServer-namnet, --username med namnet på behållarregistret och --password med något av de angivna lösenorden.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Ladda slutligen upp avbildningen till ACR-registret. I det här exemplet laddas en avbildning med namnet dcos-demo upp.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Köra en avbildning från ACR

Om du vill använda en avbildning från ACR-registret skapar du en fil med namnet *acrDemo.json* och kopierar följande text till den. Ersätt avbildningsnamnet med loginServer-namnet och avbildningsnamnet för behållarregistret, till exempel `loginServer/imageName`. Anteckna egenskapen `uris`. Den här egenskapen innehåller platsen för autentiseringsfilen för behållarregistret, som i det här fallet är Azure-filresursen som är monterad vid varje nod i DC/OS-klustret.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Distribuera programmet med DC/OC CLI.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat DC/OS för att använda Azure Container Registry, och utfört följande uppgifter:

> [!div class="checklist"]
> * distribuera Azure Container Registry (om det behövs)
> * konfigurera ACR-autentisering i ett DC/OS-kluster
> * ladda upp en avbildning till Azure Container Registry
> * köra en behållaravbildning från Azure Container Registry.

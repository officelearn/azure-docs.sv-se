---
title: Självstudie – distribuera en grupp med flera behållare i Azure Container Instances - YAML
description: I de här självstudierna lär du dig att distribuera en behållargrupp med flera behållare i Azure Container Instances med hjälp av en YAML-fil med Azure CLI.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: a0a91ece4f219cf822673cd457c064c326b89478
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006184"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Självstudier: Distribuera en grupp med flera behållare med hjälp av en YAML-fil

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances stöder distribution av flera behållare till en enda värd med hjälp av en [behållargruppen](container-instances-container-groups.md). En behållargrupp är användbart när du skapar ett program sidovagn för loggning, övervakning eller en annan konfiguration där en tjänst behöver en andra anslutna process.

I de här självstudierna gör du för att köra en enkel två behållare sidovagn konfiguration genom att distribuera en YAML-fil med hjälp av Azure CLI. En YAML-fil innehåller en koncis format för att ange inställningar för instansen. Lär dig att:

> [!div class="checklist"]
> * Konfigurera en YAML-fil
> * Distribuera behållargruppen
> * Visa loggarna för behållarna

> [!NOTE]
> Grupper med flera behållare är för närvarande begränsade till Linux-behållare.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Konfigurera en YAML-fil

Distribuera en grupp med flera behållare med den [az container skapa] [ az-container-create] kommandot i Azure CLI, måste du ange konfigurationen av behållaren grupp i en YAML-fil. Skicka sedan YAML-fil som en parameter i kommandot.

Starta genom att kopiera följande YAML till en ny fil med namnet **distribuera aci.yaml**. Du kan använda Visual Studio Code i Azure Cloud Shell för att skapa filen i arbetskatalogen:

```
code deploy-aci.yaml
```

Den här YAML-filen definierar en behållargrupp med namnet ”myContainerGroup” med två behållare, en offentlig IP-adress och två portar. Behållarna distribueras från offentliga Microsoft-avbildningar. Den första behållaren i gruppen kör ett webbprogram för webbservergrupper på internet. Behållaren andra sidovagnen, skickar regelbundet HTTP-begäranden till webbprogrammet som körs i den första behållaren via det behållargruppen lokala nätverk.

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Om du vill använda ett privat behållarregister bild, lägger du till den `imageRegistryCredentials` egenskapen för behållargruppen med värden som har ändrats för din miljö:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Distribuera behållargruppen

Skapa en resursgrupp med det [az gruppen skapa] [ az-group-create] kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera behållargrupp med den [az container skapa] [ az-container-create] kommando, skicka YAML-fil som ett argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Inom några sekunder bör du få ett första svar från Azure.

## <a name="view-deployment-state"></a>Visa status för distribution

Om du vill visa statusen för distributionen, använder du följande [az container show] [ az-container-show] kommando:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Om du vill visa programmet som körs, navigerar du till dess IP-adress i webbläsaren. IP-Adressen är till exempel `52.168.26.124` i följande Exempelutdata:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Visa containerloggar

Visa loggutdata för behållaren med den [az behållarloggarna] [ az-container-logs] kommando. Den `--container-name` argumentet anger behållaren som du vill hämta loggar från. I det här exemplet på `aci-tutorial-app` behållare har angetts.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Utdata:

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Om du vill se loggar för behållaren sidovagn, kör du en liknande kommando för att ange den `aci-tutorial-sidecar` behållare.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Utdata:

```console
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Som du ser är sidovagnen regelbundet att en HTTP-förfrågan till det huvudsakliga webbprogrammet via gruppens lokala nätverk så att den körs. Det här exemplet sidovagn kan utökas för att utlösa en avisering om den har fått en HTTP-svarskoden än `200 OK`.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en YAML-fil för att distribuera en grupp med flera behållare i Azure Container Instances. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera en YAML-fil för en grupp med flera behållare
> * Distribuera behållargruppen
> * Visa loggarna för behållarna

Du kan också ange en grupp med flera behållare med hjälp av en [Resource Manager-mall](container-instances-multi-container-group.md). Resource Manager-mall kan lätt anpassas för scenarier när du behöver distribuera ytterligare Azure-tjänstresurser med behållargruppen.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

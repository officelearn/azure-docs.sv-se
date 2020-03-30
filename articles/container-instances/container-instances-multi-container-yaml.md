---
title: Självstudiekurs - Distribuera grupp med flera behållare - YAML
description: I den här självstudien får du lära dig hur du distribuerar en behållargrupp med flera behållare i Azure Container Instances med hjälp av en YAML-fil med Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cce98ec56ee1d84c087150ba486b9482515b46f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533596"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Självstudiekurs: Distribuera en grupp med flera behållare med hjälp av en YAML-fil

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances stöder distribution av flera behållare till en enda värd med hjälp av en [behållargrupp](container-instances-container-groups.md). En behållargrupp är användbar när du skapar en sidovagn för loggning, övervakning eller någon annan konfiguration där en tjänst behöver en andra kopplad process.

I den här självstudien följer du stegen för att köra en enkel sidvagnskonfiguration med två behållare genom att distribuera en [YAML-fil](container-instances-reference-yaml.md) med Hjälp av Azure CLI. En YAML-fil ger ett koncist format för att ange förekomstinställningarna. Lär dig att:

> [!div class="checklist"]
> * Konfigurera en YAML-fil
> * Distribuera behållargruppen
> * Visa behållarnas loggar

> [!NOTE]
> Multi-container grupper är för närvarande begränsade till Linux-behållare.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Konfigurera en YAML-fil

Om du vill distribuera en grupp med flera behållare med kommandot [az container create][az-container-create] i Azure CLI måste du ange konfigurationen för behållargruppen i en YAML-fil. Skicka sedan YAML-filen som en parameter till kommandot.

Börja med att kopiera följande YAML till en ny fil med namnet **deploy-aci.yaml**. I Azure Cloud Shell kan du använda Visual Studio-kod för att skapa filen i arbetskatalogen:

```
code deploy-aci.yaml
```

Den här YAML-filen definierar en behållargrupp med namnet "myContainerGroup" med två behållare, en offentlig IP-adress och två exponerade portar. Behållarna distribueras från offentliga Microsoft-avbildningar. Den första behållaren i gruppen kör ett internetvänt webbprogram. Den andra behållaren, sidovagnen, gör regelbundet HTTP-begäranden till webbprogrammet som körs i den första behållaren via behållargruppens lokala nätverk.

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

Om du vill använda ett privat `imageRegistryCredentials` avbildningsregister för behållare lägger du till egenskapen i behållargruppen med värden som ändrats för din miljö:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Distribuera behållargruppen

Skapa en resursgrupp med kommandot [az group create:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera behållargruppen med kommandot [az container create][az-container-create] och skicka YAML-filen som ett argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Inom några sekunder bör du få ett första svar från Azure.

## <a name="view-deployment-state"></a>Visa distributionstillstånd

Om du vill visa tillståndet för distributionen använder du följande kommando [för az-behållarvisning:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Om du vill visa programmet som körs navigerar du till dess IP-adress i webbläsaren. Ip är `52.168.26.124` till exempel i det här exemplet utdata:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Visa containerloggar

Visa loggutdata för en behållare med kommandot [az container logs.][az-container-logs] Argumentet `--container-name` anger den behållare som du vill hämta loggar från. I det här `aci-tutorial-app` exemplet anges behållaren.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Resultat:

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Om du vill visa loggarna för sidovagnsbehållaren `aci-tutorial-sidecar` kör du ett liknande kommando som anger behållaren.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Resultat:

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

Som du kan se gör sidovagnen regelbundet en HTTP-begäran till huvudwebbprogrammet via gruppens lokala nätverk för att säkerställa att den körs. Det här sidovagnsexemplet kan utökas för att utlösa `200 OK`en avisering om den har fått en annan HTTP-svarskod än .

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en YAML-fil för att distribuera en grupp med flera behållare i Azure Container Instances. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera en YAML-fil för en grupp med flera behållare
> * Distribuera behållargruppen
> * Visa behållarnas loggar

Du kan också ange en grupp med flera behållare med hjälp av en [Resource Manager-mall](container-instances-multi-container-group.md). En Resource Manager-mall kan enkelt anpassas för scenarier när du behöver distribuera ytterligare Azure-tjänstresurser med behållargruppen.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

---
title: Distribuera grupper med flera behållare i Azure Container Instances med Azure CLI och YAML
description: Lär dig hur du distribuerar en behållargrupp med flera behållare i Azure Container Instances med hjälp av Azure CLI och en YAML-fil.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/17/2018
ms.author: danlep
ms.openlocfilehash: ffc9cf24e686924878a752b5d9df31160328ef0a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854719"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Distribuera en behållargrupp med flera behållare med YAML

Azure Container Instances stöder distribution av flera behållare till en enda värd med hjälp av en [behållargruppen](container-instances-container-groups.md). Grupper med flera behållare behållare är användbara när du skapar ett program sidovagn för loggning, övervakning eller en annan konfiguration där en tjänst behöver en andra anslutna process.

Det finns två metoder för att distribuera grupper med flera behållare med Azure CLI:

* YAML-fildistribution (den här artikeln)
* [Resource Manager för malldistribution](container-instances-multi-container-group.md)

På grund av YAML-format kortare natur, distribution med en YAML-fil rekommenderas när distributionen omfattar *endast* behållarinstanser. Om du vill distribuera ytterligare Azure-tjänstresurser (till exempel en Azure Files-resurs) vid tidpunkten för distribution av behållarinstanser rekommenderas Resource Manager för malldistribution.

> [!NOTE]
> Grupper med flera behållare är för närvarande begränsade till Linux-behållare. Medan vi arbetar för att göra alla funktioner till Windows-behållare, du kan hitta nuvarande skillnaderna i [kvoter och regiontillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Konfigurera YAML-fil

Distribuera en behållargrupp med flera behållare med den [az container skapa] [ az-container-create] kommandot i Azure CLI, måste du ange konfigurationen av behållaren grupp i en YAML-fil och sedan skicka YAML-fil som en parameter i kommandot.

Starta genom att kopiera följande YAML till en ny fil med namnet **distribuera aci.yaml**.

Den här YAML-filen definierar en behållargrupp med namnet ”myContainerGroup” med två behållare, en offentlig IP-adress och två portar. Den första behållaren i gruppen kör ett webbprogram för webbservergrupper på internet. Behållaren andra sidovagnen, skickar regelbundet HTTP-begäranden till webbprogrammet som körs i den första behållaren via det behållargruppen lokala nätverk.

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
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
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Visa loggar

Visa loggutdata för behållaren med den [az behållarloggarna] [ az-container-logs] kommando. Den `--container-name` argumentet anger behållaren som du vill hämta loggar från. Den första behållaren har angetts i det här exemplet.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Resultat:

```console
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Kör samma kommando anger andra behållarens namn om du vill visa loggarna för behållaren sidvagn.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Resultat:

```console
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

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
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Som du ser är sidovagnen regelbundet att en HTTP-förfrågan till det huvudsakliga webbprogrammet via gruppens lokala nätverk så att den körs. Det här exemplet sidovagn kan utökas för att utlösa en avisering om den har fått en HTTP-svarskoden än 200 OK.

## <a name="deploy-from-private-registry"></a>Distribuera från privata register

För att använda ett privat behållarregister avbildning, inkluderar du följande YAML med värden som har ändrats för din miljö:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Till exempel distribuerar följande YAML en behållargrupp med en enskild behållare vars avbildningen hämtas från ett privat Azure-Behållarregister med namnet ”myregistry”:

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>Exportera behållargruppen till YAML

Du kan exportera konfigurationen av en befintlig behållargrupp till en YAML-fil med hjälp av Azure CLI-kommando [az container export][az-container-export].

Användbart om du vill bevara en behållargrupp konfiguration, exportera kan du lagra dina behållare konfigurationer i versionskontroll för ”konfiguration som kod”. Eller Använd den exporterade filen som en startpunkt när du utvecklar en ny konfiguration i YAML.

Exportera konfigurationen för behållargruppen som du skapade tidigare genom att utfärda följande [az container export] [ az-container-export] kommando:

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

Inga utdata visas om kommandot genomförs, men du kan visa innehållet i filen och se resultatet. Till exempel de första raderna med `head`:

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs de steg som krävs för att distribuera en flera behållare, Azure container-instans. En slutpunkt till slutpunkt Azure Container Instances upplevelse, inklusive via en privat Azure container registry, finns i självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Självstudie för Azure Container Instances][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

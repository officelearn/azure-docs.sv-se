---
title: Distribuera flera behållare grupper i Azure Container instanser med Azure CLI och YAML
description: Lär dig hur du distribuerar en behållare grupp med flera behållare i Azure Container instanser med Azure CLI och en YAML-fil.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: marsma
ms.openlocfilehash: 5dfee15e978d2dba0f50d1dc4b78953698389950
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851332"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Distribuera flera behållare behållaren grupp med YAML

Azure Behållarinstanser stöder distribution av flera behållare till en enda värd med hjälp av en [behållargruppen](container-instances-container-groups.md). Flera behållare behållargrupper är användbara när du skapar ett program sidovagn för loggning, övervakning eller någon annan konfiguration där en tjänst behöver en andra anslutna process.

Det finns två metoder för att distribuera flera behållare grupper med hjälp av Azure CLI:

* Distribution av YAML (den här artikeln)
* [Resource Manager för malldistribution](container-instances-multi-container-group.md)

På grund av formatet YAML kortare karaktär distribution med en YAML-fil rekommenderas om din distribution innehåller *endast* behållarinstanser. Om du behöver distribuera ytterligare Azure serviceresurser (till exempel en resurs för Azure-filer) på behållaren instans distributionen bör Resource Manager för malldistribution.

> [!NOTE]
> Flera behållare grupper är för närvarande begränsad till Linux-behållare. När vi arbetar för att göra alla funktioner till Windows-behållare, hittar du den aktuella plattformen skillnader i [kvoter och regional tillgänglighet för Azure-Behållarinstanser](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Konfigurera YAML-fil

Att distribuera flera behållare behållaren grupp med den [az behållaren skapa] [ az-container-create] kommandot i Azure CLI, måste du ange konfigurationen av grupp-behållaren i en YAML-fil och sedan lämnar YAML-filen som en Parametern för kommandot.

Starta genom att kopiera följande YAML till en ny fil med namnet **distribuera aci.yaml**.

Den här YAML-filen definierar en behållare grupp med två behållare, en offentlig IP-adress och två exponerade portar. Första behållaren i gruppen körs ett internet-riktade webbprogram. Behållaren andra sidvagn, skickar regelbundet HTTP-begäranden till webbprogrammet som körs i den första behållaren via behållargruppen lokala nätverk.

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

Distribuera behållargruppen med den [az behållaren skapa] [ az-container-create] kommando, skickar YAML-filen som ett argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --name myContainerGroup -f deploy-aci.yaml
```

Inom några sekunder bör du få ett första svar från Azure.

## <a name="view-deployment-state"></a>Visa status för distributionen

Om du vill visa status för distributionen, Använd följande [az behållaren visa] [ az-container-show] kommando:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Om du vill visa programmet som körs, navigerar du till dess IP-adress i webbläsaren. IP-Adressen är till exempel `52.168.26.124` i detta exempel på utdata:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Visa loggar

Visa loggutdata från en behållare med hjälp av den [az behållaren loggar] [ az-container-logs] kommando. Den `--container-name` argumentet anger behållaren som hämtar loggarna. Den första behållaren har angetts i det här exemplet.

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

Om du vill se loggar för behållaren sida bilen köra samma kommando anger andra behållarens namn.

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

Som du kan se göra sidovagn regelbundet en HTTP-begäran till huvudsakliga webb-applikationen via gruppens lokala nätverk så att den körs. Det här exemplet sidovagn kan expanderas för att utlösa en avisering om det tog emot en HTTP-svarskoden än 200 OK.

## <a name="deploy-from-private-registry"></a>Distribuera från privata registret

Om du vill använda ett privat behållaren image register omfatta följande YAML med värden som har ändrats för din miljö:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Till exempel distribuerar följande YAML en behållare grupp med en enskild behållare vars avbildningen hämtas från ett privat Azure-behållare register med namnet ”myregistry”:

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

Du kan exportera konfigurationen av en befintlig grupp behållare till en YAML-fil med hjälp av Azure CLI-kommando [az behållaren export][az-container-export].

Användbart om du vill bevara en behållargruppen konfiguration, exportera kan du lagra dina behållaren konfigurationer i versionskontroll för ”konfiguration som kod”. Eller Använd den exporterade filen som utgångspunkt när du utvecklar en ny konfiguration i YAML.

Exportera konfigurationen för behållargruppen som du skapade tidigare genom att utfärda följande [az behållaren export] [ az-container-export] kommando:

```azurecli-interactive
az container export --resource-group rg604 --name myContainerGroup --file deployed-aci.yaml
```

Inga utdata visas om kommandot genomförs, men du kan visa innehållet i filen för att se resultatet. Till exempel de första raderna med `head`:

```console
$ head deployed-aci.yaml
apiVersion: 2018-02-01-preview
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
      ports:
```

> [!NOTE]
> Från och med Azure CLI version 2.0.34, det finns en [kända problem] [ cli-issue-6525] i som exporterats behållargrupper anger ett äldre API-version av **2018-02-01-preview** (visas i föregående JSON exempel på utdata). Om du vill distribuera med hjälp av den exporterade filen med YAML du på ett säkert sätt kan uppdatera den `apiVersion` värde i den exporterade filen med YAML till **2018-06-01**.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs de steg som krävs för att distribuera en instans av flera behållare Azure-behållaren. Se Azure Behållarinstanser självstudierna för en slutpunkt till slutpunkt Azure Behållarinstanser upplevelse, inklusive användning av en privat Azure-behållaren i registret.

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

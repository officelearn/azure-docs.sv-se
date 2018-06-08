---
title: Distribuera flera behållare grupper i Azure Container instanser
description: Lär dig hur du distribuerar en behållare grupp med flera behållare i Azure Container instanser.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: db3f616d85c21f01c751fd82532289593a6e7e45
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850577"
---
# <a name="deploy-a-container-group"></a>Distribuera en behållare grupp

Azure Behållarinstanser stöder distribution av flera behållare till en enda värd med en [behållargruppen](container-instances-container-groups.md). Detta är användbart när du skapar ett program sidovagn för loggning, övervakning eller någon annan konfiguration där en tjänst behöver en andra anslutna process.

Det finns två metoder för att distribuera flera behållare grupper med hjälp av Azure CLI:

* Malldistribution för hanteraren för filserverresurser (den här artikeln)
* [Distribution av YAML](container-instances-multi-container-yaml.md)

Distribution med en Resource Manager-mall rekommenderas när du behöver distribuera ytterligare Azure serviceresurser (till exempel en resurs för Azure-filer) på behållaren instans distributionen. På grund av formatet YAML kortare karaktär distribution med en YAML-fil rekommenderas om din distribution innehåller *endast* behållarinstanser.

> [!NOTE]
> Flera behållare grupper är för närvarande begränsad till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-template"></a>Konfigurera mallen

Avsnitten i den här artikeln beskriver hur du kör en enkel flera behållare sidovagn konfiguration genom att distribuera en Azure Resource Manager-mall.

Börja med att skapa en fil med namnet `azuredeploy.json`, kopierar följande JSON till den.

Resource Manager-mallen definierar en behållare grupp med två behållare, en offentlig IP-adress och två exponerade portar. Första behållaren i gruppen körs ett mot internet-program. Behållaren andra sidvagn, gör en HTTP-begäran till webbprogrammet huvudsakliga via gruppens lokala nätverk.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-04-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Om du vill använda ett privat behållaren image register att lägga till ett objekt i JSON-dokumentet med följande format. Exempel på implementering av den här konfigurationen, finns det [ACI Resource Manager mallreferensen] [ template-reference] dokumentation.

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Distribuera mallen

Skapa en resursgrupp med kommandot [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med den [az distribution skapa] [ az-group-deployment-create] kommando.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
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
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Visa loggar

Visa loggutdata från en behållare med hjälp av den [az behållaren loggar] [ az-container-logs] kommando. Den `--container-name` argumentet anger behållaren som hämtar loggarna. Den första behållaren har angetts i det här exemplet.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Resultat:

```bash
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

```bash
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

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs de steg som krävs för att distribuera en instans av flera behållare Azure-behållaren. En slutpunkt till slutpunkt Azure Behållarinstanser upplevelse finns i Azure Container instanser kursen.

> [!div class="nextstepaction"]
> [Självstudie för Azure Container Instances][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

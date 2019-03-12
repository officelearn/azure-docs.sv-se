---
title: Distribuera grupper med flera behållare i Azure Container Instances
description: Lär dig hur du distribuerar en behållargrupp med flera behållare i Azure Container Instances med en Azure Resource Manager-mall.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 2dfe1bbf01b7e1fae8c07602ac4faa40ae74ecc9
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729509"
---
# <a name="deploy-a-multi-container-group-with-a-resource-manager-template"></a>Distribuera en grupp med flera behållare med en Resource Manager-mall

Azure Container Instances stöder distribution av flera behållare till en enda värd med hjälp av en [behållargruppen](container-instances-container-groups.md). Detta är användbart när du skapar ett program sidovagn för loggning, övervakning eller en annan konfiguration där en tjänst behöver en andra anslutna process.

Det finns två metoder för att distribuera grupper med flera behållare med Azure CLI:

* För malldistribution av resurshanteraren (den här artikeln)
* [YAML-fildistribution](container-instances-multi-container-yaml.md)

Distribution med en Resource Manager-mall rekommenderas när du behöver distribuera ytterligare Azure-tjänstresurser (till exempel en Azure Files-resurs) vid tidpunkten för distribution av behållarinstanser. På grund av YAML-format kortare natur, distribution med en YAML-fil rekommenderas när distributionen omfattar *endast* behållarinstanser.

> [!NOTE]
> Grupper med flera behållare är för närvarande begränsade till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

Ytterligare mallexempel finns [Azure Resource Manager-mallar för Azure Container Instances](container-instances-samples-rm.md). 

## <a name="configure-the-template"></a>Konfigurera mallen

Avsnitten i den här artikeln vägleder dig genom att köra en enkel flera behållare sidovagn konfiguration genom att distribuera en Azure Resource Manager-mall.

Börja med att skapa en fil med namnet `azuredeploy.json`, kopiera följande JSON till den.

Resource Manager-mallen definierar en behållargrupp med två behållare, en offentlig IP-adress och två portar. Den första behållaren i gruppen kör ett program för webbservergrupper på internet. Den andra behållaren, sidovagnen, gör en HTTP-förfrågan till det huvudsakliga webbprogrammet via gruppens lokala nätverk.

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

Lägga till ett objekt till JSON-dokument med följande format om du vill använda ett privat behållarregister för avbildningen. Ett exempel på en implementering av den här konfigurationen, finns det [ACI Resource Manager-mallreferensen] [ template-reference] dokumentation.

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

Distribuera mallen med den [az group deployment skapa] [ az-group-deployment-create] kommando.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
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
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Visa loggar

Visa loggutdata för behållaren med den [az behållarloggarna] [ az-container-logs] kommando. Den `--container-name` argumentet anger behållaren som du vill hämta loggar från. Den första behållaren har angetts i det här exemplet.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Utdata:

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Kör samma kommando anger andra behållarens namn om du vill visa loggarna för behållaren sidvagn.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Utdata:

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

Som du ser är sidovagnen regelbundet att en HTTP-förfrågan till det huvudsakliga webbprogrammet via gruppens lokala nätverk så att den körs. Det här exemplet sidovagn kan utökas för att utlösa en avisering om den har fått en HTTP-svarskoden än 200 OK.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs de steg som krävs för att distribuera en flera behållare, Azure container-instans. En Azure Container Instances-upplevelse för slutpunkt till slutpunkt, finns i självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Självstudie för Azure Container Instances][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

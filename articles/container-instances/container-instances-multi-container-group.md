---
title: "Distribuera flera behållare grupper i Azure Container instanser"
description: "Lär dig hur du distribuerar en behållare grupp med flera behållare i Azure Container instanser."
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5e1f23e20b001404d3f781e7e6deac87ede12684
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-a-container-group"></a>Distribuera en behållare grupp

Azure Behållarinstanser stöd för distribution av flera behållare till en enda värd med en *behållargruppen*. Detta är användbart när du skapar ett program sidovagn för loggning, övervakning eller någon annan konfiguration där en tjänst behöver en andra anslutna process.

Det här dokumentet går igenom kör en enkel flera behållare sidovagn konfiguration med en Azure Resource Manager-mall.

## <a name="configure-the-template"></a>Konfigurera mallen

Skapa en fil med namnet `azuredeploy.json` och kopierar följande json till den.

I det här exemplet definieras en behållare grupp med två behållare och en offentlig IP-adress. Första behållare för gruppen kör ett Internetriktade Internetprogram. Behållaren andra sidvagn, gör en HTTP-begäran till webbprogrammet huvudsakliga via gruppens lokala nätverk.

Det här exemplet sidovagn kan expanderas för att utlösa en avisering om det tog emot en HTTP-svarskoden än 200 OK.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
    "resources": [
      {
        "name": "myContainerGroup",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2017-08-01-preview",
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
              }
            ]
          }
        }
      }
    ],
    "outputs": {
      "containerIPv4Address": {
        "type": "string",
        "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
      }
    }
  }
```

Om du vill använda ett privat behållaren image register att lägga till ett objekt i json-dokumentet med följande format.

```json
"imageRegistryCredentials": [
    {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
    }
]
```

## <a name="deploy-the-template"></a>Distribuera mallen

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create).

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Distribuera mallen med den [az distribution skapa](/cli/azure/group/deployment#create) kommando.

```azurecli-interactive
az group deployment create --name myContainerGroup --resource-group myResourceGroup --template-file azuredeploy.json
```

Inom några sekunder får du ett första svar från Azure.

## <a name="view-deployment-state"></a>Visa status för distributionen

Du kan visa statusen för distributionen av `az container show` kommando. Detta returnerar etablerade offentliga IP-adressen som programmet kan användas.

```azurecli-interactive
az container show --name myContainerGroup --resource-group myResourceGroup -o table
```

Resultat:

```azurecli
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGrou2  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>Visa loggfiler

Visa loggutdata från en behållare med hjälp av den `az container logs` kommando. Den `--container-name` argumentet anger behållaren som hämtar loggarna. Den första behållaren har angetts i det här exemplet.

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-app --resource-group myResourceGroup
```

Resultat:

```bash
istening on port 80
::1 - - [27/Jul/2017:17:35:29 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:32 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:35 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:38 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Om du vill se loggar för behållaren sida bilen köra samma kommando anger andra behållarens namn.

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-sidecar --resource-group myResourceGroup
```

Resultat:

```bash
Every 3.0s: curl -I http://localhost                                                                                                                       Mon Jul 17 11:27:36 2017

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 1663
Content-Type: text/html; charset=utf-8
Last-Modified: Sun, 16 Jul 2017 02:08:22 GMT
Date: Mon, 17 Jul 2017 18:27:36 GMT
```

Som du kan se göra sidovagn regelbundet en HTTP-begäran till huvudsakliga webb-applikationen via gruppens lokala nätverk så att den körs.

## <a name="next-steps"></a>Nästa steg

Det här dokumentet beskrivs de steg som krävs för att distribuera en instans av flera behållare Azure-behållaren. En slutpunkt till slutpunkt Azure Behållarinstanser upplevelse finns i Azure Container instanser kursen.

> [!div class="nextstepaction"]
> [Azure Behållarinstanser kursen]:./container-instances-tutorial-prepare-app.md

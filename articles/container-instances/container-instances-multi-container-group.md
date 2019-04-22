---
title: Självstudie – distribuera en grupp med flera behållare i Azure Container Instances - mall
description: I de här självstudierna lär du dig att distribuera en behållargrupp med flera behållare i Azure Container Instances genom att använda en Azure Resource Manager-mall med Azure CLI.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f769beda1654dc9f58ecff733741fb1ab9118031
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006914"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Självstudier: Distribuera en grupp med flera behållare med en Resource Manager-mall

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances stöder distribution av flera behållare till en enda värd med hjälp av en [behållargruppen](container-instances-container-groups.md). En behållargrupp är användbart när du skapar ett program sidovagn för loggning, övervakning eller en annan konfiguration där en tjänst behöver en andra anslutna process.

I den här självstudien kan du följa steg för att köra en enkel två behållare sidovagn konfiguration genom att distribuera en Azure Resource Manager-mall med hjälp av Azure CLI. Lär dig att:

> [!div class="checklist"]
> * Konfigurera en grupp med flera behållare-mall
> * Distribuera behållargruppen
> * Visa loggarna för behållarna

Resource Manager-mall kan lätt anpassas för scenarier när du behöver distribuera ytterligare Azure-tjänstresurser (till exempel en Azure Files-resurs eller ett virtuellt nätverk) med behållargruppen. 

> [!NOTE]
> Grupper med flera behållare är för närvarande begränsade till Linux-behållare. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Konfigurera en mall

Starta genom att kopiera följande JSON till en ny fil med namnet `azuredeploy.json`. Du kan använda Visual Studio Code i Azure Cloud Shell för att skapa filen i arbetskatalogen:

```
code azuredeploy.json
```

Resource Manager-mallen definierar en behållargrupp med två behållare, en offentlig IP-adress och två portar. Den första behållaren i gruppen kör ett webbprogram för webbservergrupper på internet. Den andra behållaren, sidovagnen, gör en HTTP-förfrågan till det huvudsakliga webbprogrammet via gruppens lokala nätverk.

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
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
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

```bash
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

```bash
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

I den här självstudien använde du en Azure Resource Manager-mall för att distribuera en grupp med flera behållare i Azure Container Instances. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera en grupp med flera behållare-mall
> * Distribuera behållargruppen
> * Visa loggarna för behållarna

Ytterligare mallexempel finns [Azure Resource Manager-mallar för Azure Container Instances](container-instances-samples-rm.md).

Du kan också ange en grupp med flera behållare med hjälp av en [YAML-fil](container-instances-multi-container-yaml.md). På grund av YAML-format kortare natur är distribution med en YAML-fil ett bra val när distributionen omfattar endast behållarinstanser.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

---
title: Självstudie – distribuera grupp med flera behållare – mall
description: I den här självstudien får du lära dig hur du distribuerar en behållar grupp med flera behållare i Azure Container Instances genom att använda en Azure Resource Manager-mall med Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: d2b4e20520cad28c5d62118f6c9d10fcc43ac89e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74533627"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Självstudie: Distribuera en grupp med flera behållare med hjälp av en Resource Manager-mall

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances stöder distribution av flera behållare till en enda värd med hjälp av en [behållar grupp](container-instances-container-groups.md). En behållar grupp är användbar när du skapar en program-sidvagn för loggning, övervakning eller andra konfigurationer där en tjänst behöver en andra ansluten process.

I den här självstudien följer du steg för steg hur du kör en enkel sidvagn-konfiguration med två behållare genom att distribuera en Azure Resource Manager-mall med hjälp av Azure CLI. Lär dig att:

> [!div class="checklist"]
> * Konfigurera en grupp mal len med flera behållare
> * Distribuera behållar gruppen
> * Visa loggarna för behållarna

En Resource Manager-mall kan enkelt anpassas för scenarier när du behöver distribuera ytterligare Azure-tjänst resurser (till exempel en Azure Files-resurs eller ett virtuellt nätverk) med behållar gruppen. 

> [!NOTE]
> Grupper med flera behållare är för närvarande begränsade till Linux-behållare. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Konfigurera en mall

Börja med att kopiera följande JSON till en ny fil med `azuredeploy.json`namnet. I Azure Cloud Shell kan du använda Visual Studio Code för att skapa filen i din arbets katalog:

```
code azuredeploy.json
```

Den här Resource Manager-mallen definierar en behållar grupp med två behållare, en offentlig IP-adress och två exponerade portar. Den första behållaren i gruppen kör ett webb program som riktar sig mot Internet. Den andra behållaren, den sidvagn, skickar en HTTP-begäran till huvud webb programmet via gruppens lokala nätverk.

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

Om du vill använda ett privat behållar avbildnings register lägger du till ett objekt i JSON-dokumentet med följande format. En exempel implementering av den här konfigurationen finns i referens dokumentationen för [ACI Resource Manager-mall][template-reference] .

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

Distribuera mallen med kommandot [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Inom några sekunder bör du få ett första svar från Azure.

## <a name="view-deployment-state"></a>Visa distributions status

Om du vill visa status för distributionen använder du följande [AZ container show][az-container-show] -kommando:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Om du vill visa det program som körs, navigerar du till dess IP-adress i webbläsaren. IP-adressen är `52.168.26.124` till exempel utdata i följande exempel:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Visa containerloggar

Visa logg resultatet för en behållare med hjälp av kommandot [AZ container logs][az-container-logs] . `--container-name` Argumentet anger den behållare från vilken du vill hämta loggar. I det här exemplet anges `aci-tutorial-app` containern.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Resultat:

```bash
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Om du vill se loggarna för den sidvagn behållaren kör du ett liknande kommando `aci-tutorial-sidecar` som anger behållaren.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Resultat:

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

Som du kan se gör den sidvagn regelbundet en HTTP-begäran till huvud webb programmet via gruppens lokala nätverk för att säkerställa att det körs. Detta sidvagn-exempel kan utökas för att utlösa en avisering om den fick en HTTP-svarskod `200 OK`än.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en Azure Resource Manager-mall för att distribuera en grupp med flera behållare i Azure Container Instances. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera en grupp mal len med flera behållare
> * Distribuera behållar gruppen
> * Visa loggarna för behållarna

Ytterligare mall-exempel finns i [Azure Resource Manager mallar för Azure Container instances](container-instances-samples-rm.md).

Du kan också ange en grupp med flera behållare med hjälp av en [yaml-fil](container-instances-multi-container-yaml.md). På grund av YAML-formatet är det mer koncist att distribuera med en YAML-fil när distributionen endast innehåller behållar instanser.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

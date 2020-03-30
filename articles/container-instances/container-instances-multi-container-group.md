---
title: Självstudiekurs - Distribuera grupp med flera behållare - mall
description: I den här självstudien får du lära dig hur du distribuerar en behållargrupp med flera behållare i Azure Container Instances med hjälp av en Azure Resource Manager-mall med Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: d2b4e20520cad28c5d62118f6c9d10fcc43ac89e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533627"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Självstudiekurs: Distribuera en grupp med flera behållare med hjälp av en Resource Manager-mall

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances stöder distribution av flera behållare till en enda värd med hjälp av en [behållargrupp](container-instances-container-groups.md). En behållargrupp är användbar när du skapar en sidovagn för loggning, övervakning eller någon annan konfiguration där en tjänst behöver en andra kopplad process.

I den här självstudien följer du stegen för att köra en enkel sidvagnskonfiguration med två behållare genom att distribuera en Azure Resource Manager-mall med Hjälp av Azure CLI. Lär dig att:

> [!div class="checklist"]
> * Konfigurera en gruppmall med flera behållare
> * Distribuera behållargruppen
> * Visa behållarnas loggar

En Resource Manager-mall kan enkelt anpassas för scenarier när du behöver distribuera ytterligare Azure-tjänstresurser (till exempel en Azure Files-resurs eller ett virtuellt nätverk) med behållargruppen. 

> [!NOTE]
> Multi-container grupper är för närvarande begränsade till Linux-behållare. 

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Konfigurera en mall

Börja med att kopiera följande JSON `azuredeploy.json`till en ny fil med namnet . I Azure Cloud Shell kan du använda Visual Studio-kod för att skapa filen i arbetskatalogen:

```
code azuredeploy.json
```

Den här Resource Manager-mallen definierar en behållargrupp med två behållare, en offentlig IP-adress och två exponerade portar. Den första behållaren i gruppen kör ett internetvänt webbprogram. Den andra behållaren, sidovagnen, gör en HTTP-begäran till huvudwebbprogrammet via gruppens lokala nätverk.

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

Om du vill använda ett privat behållaravbildningsregister lägger du till ett objekt i JSON-dokumentet med följande format. En exempelimplementering av den här konfigurationen finns i [referensdokumentationen för ACI Resource Manager-mallen.][template-reference]

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

Distribuera mallen med kommandot [az group deployment create.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
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

```bash
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

Som du kan se gör sidovagnen regelbundet en HTTP-begäran till huvudwebbprogrammet via gruppens lokala nätverk för att säkerställa att den körs. Det här sidovagnsexemplet kan utökas för att utlösa `200 OK`en avisering om den har fått en annan HTTP-svarskod än .

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en Azure Resource Manager-mall för att distribuera en grupp med flera behållare i Azure Container Instances. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera en gruppmall med flera behållare
> * Distribuera behållargruppen
> * Visa behållarnas loggar

Ytterligare mallexempel finns i [Azure Resource Manager-mallar för Azure Container Instances](container-instances-samples-rm.md).

Du kan också ange en grupp med flera behållare med hjälp av en [YAML-fil](container-instances-multi-container-yaml.md). På grund av YAML-formatets mer koncisa natur är distribution med en YAML-fil ett bra val när distributionen endast innehåller behållarinstanser.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups

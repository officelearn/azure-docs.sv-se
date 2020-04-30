---
title: Snabb start – skapa en profil och en slut punkt med Resource Manager-mallar
titleSuffix: Azure Content Delivery Network
description: Lär dig hur du skapar ett Azure-innehåll leverera nätverks profil och slut punkt med Resource Manager-mallar
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: dfd7b933502b96c0952a24dbee563e9b537dcdd8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81683477"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Snabb start: skapa en Azure CDN profil och slut punkt med Resource Manager-mall

I den här snabb starten distribuerar du en Azure Resource Manager-mall med hjälp av CLI. Mallen som du skapar distribuerar en CDN-profil och CDN-slutpunkt för att placera ditt webb program.
Det bör ta cirka tio minuter att slutföra de här stegen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Prequisites

I den här snabb starten måste du ha ett webb program som ska användas som ursprung. Exempel webb programmet som används i den här snabb starten distribuerades tillhttps://cdndemo.azurewebsites.net

Mer information finns i [skapa en statisk HTML-webbapp i Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Alla resurser måste distribueras i samma resurs grupp.

Skapa resurs gruppen på den plats som du väljer. I det här exemplet visas hur du skapar en resurs grupp med namnet CDN på platsen USA, östra.

```azurecli-interactive
az group create --name cdn --location eastus
```

![Ny resursgrupp](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

I det här steget skapar du en mallfil som distribuerar resurserna.

Det här exemplet går igenom ett allmänt scenario för webbplats acceleration, men det finns många andra inställningar som kan konfigureras. De här inställningarna är tillgängliga i referensen till Azure Resource Manager mal len. Se referenser för [CDN-profil](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) och [CDN-profil slut punkt](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Observera att Microsoft CDN inte stöder ändring av listan över innehålls typer.

Spara mallen som **Resource Manager-CDN. JSON**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "cdnProfileSku": {
            "type": "string",
            "allowedValues": [
                "Standard_Microsoft",
                "Standard_Akamai",
                "Standard_Verizon",
                "Premium_Verizon"
            ]
        },
        "endpointOriginHostName": {
            "type": "string"
        }
    },
    "variables": {
        "profile": {
            "name": "[replace(toLower(parameters('cdnProfileSku')), '_', '-')]"
        },
        "endpoint": {
            "name": "[replace(toLower(parameters('endpointOriginHostName')), '.', '-')]",
            "originHostName": "[parameters('endpointOriginHostName')]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Cdn/profiles",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[variables('profile').name]",
            "sku": {
                "name": "[parameters('cdnProfileSku')]"
            }
        },
        {
            "dependsOn": [
                "[resourceId('Microsoft.Cdn/profiles', variables('profile').name)]"
            ],
            "type": "Microsoft.Cdn/profiles/endpoints",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[concat(variables('profile').name, '/', variables('endpoint').name)]",
            "properties": {
                "hostName": "[concat(variables('endpoint').name, '.azureedge.net')]",
                "originHostHeader": "[variables('endpoint').originHostName]",
                "isHttpAllowed": true,
                "isHttpsAllowed": true,
                "queryStringCachingBehavior": "IgnoreQueryString",
                "origins": [
                    {
                        "name": "[replace(variables('endpoint').originHostName, '.', '-')]",
                        "properties": {
                            "hostName": "[variables('endpoint').originHostName]",
                            "httpPort": 80,
                            "httpsPort": 443
                        }
                    }
                ],
                "contentTypesToCompress": [
                    "application/eot",
                    "application/font",
                    "application/font-sfnt",
                    "application/javascript",
                    "application/json",
                    "application/opentype",
                    "application/otf",
                    "application/pkcs7-mime",
                    "application/truetype",
                    "application/ttf",
                    "application/vnd.ms-fontobject",
                    "application/xhtml+xml",
                    "application/xml",
                    "application/xml+rss",
                    "application/x-font-opentype",
                    "application/x-font-truetype",
                    "application/x-font-ttf",
                    "application/x-httpd-cgi",
                    "application/x-javascript",
                    "application/x-mpegurl",
                    "application/x-opentype",
                    "application/x-otf",
                    "application/x-perl",
                    "application/x-ttf",
                    "font/eot",
                    "font/ttf",
                    "font/otf",
                    "font/opentype",
                    "image/svg+xml",
                    "text/css",
                    "text/csv",
                    "text/html",
                    "text/javascript",
                    "text/js",
                    "text/plain",
                    "text/richtext",
                    "text/tab-separated-values",
                    "text/xml",
                    "text/x-script",
                    "text/x-component",
                    "text/x-java-source"
                ],
                "isCompressionEnabled": true,
                "optimizationType": "GeneralWebDelivery"
            }
        }
    ],
    "outputs": {
        "cdnUrl": {
            "type": "string",
            "value": "[concat('https://', variables('endpoint').name, '.azureedge.net')]"
        }
    }
}
```

## <a name="create-the-resources"></a>Skapa resurserna

Distribuera mallen med hjälp av Azure CLI. Du blir ombedd att ange 2 indata:

**cdnProfileSku** – CDN-providern som du vill använda. Alternativen är:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** – slut punkten som ska hanteras via CDN, till exempel cdndemo.azurewebsites.net.

```azurecli-interactive
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Distribuera Resource Manager-mall](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Visa CDN-profilen

```azurecli-interactive
az cdn profile list --resource-group cdn -o table
```

![Visa CDN-profil](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Visa CDN-slutpunkten för profilen standard – Microsoft

```azurecli-interactive
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Visa CDN-slutpunkt](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Använd värd namnet för att visa innehållet. Till exempel åtkomst till https:\//cdndemo-azurewebsites-net.azureedge.net med din webbläsare.

## <a name="clean-up"></a>Rensa

Om du tar bort resurs gruppen tas alla resurser som har distribuerats bort automatiskt.

```azurecli-interactive
az group delete --name cdn
```

![Ta bort resurs grupp](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Referenser

* CDN-profil – [referens för Azure Resource Manager mall](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* CDN-slutpunkt – [referens dokumentation för Azure Resource Manager Template](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du lägger till en anpassad domän i din CDN-slutpunkt finns i följande självstudiekurs:

> [!div class="nextstepaction"]
> [Självstudier: Lägga till en anpassad domän i Azure CDN-slutpunkten](cdn-map-content-to-custom-domain.md)

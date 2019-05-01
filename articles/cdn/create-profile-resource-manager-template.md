---
title: Snabbstart – skapa en Azure Content Delivery Network-profil och slutpunkten med hjälp av Resource Manager-mallar | Microsoft Docs
description: Lär dig hur du skapar ett nätverk för Azure Innehållsleverans profilen och slutpunkten med hjälp av Resource Manager-mallar
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: c26c9021637b1b6ec2b3808db1307de9955bb660
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922398"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Snabbstart: Skapa en Azure CDN-profilen och slutpunkten med hjälp av Resource Manager-mall

I den här snabbstarten distribuerar du en Azure Resource Manager-mall med hjälp av CLI. Mallen som du skapar distribuerar en CDN-profil och CDN-slutpunkt för klientdelens ditt webbprogram.
Det bör ta ungefär tio minuter för att slutföra dessa steg.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Prequisites

För den här snabbstarten, måste du ha ett webbprogram som ska användas som ditt ursprung. Exemplet webbprogram i den här snabbstarten har distribuerats till https://cdndemo.azurewebsites.net

Mer information finns i [skapa en statisk HTML-webbapp i Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Alla resurser måste distribueras i samma resursgrupp.

Skapa resursgruppen på den plats som du väljer. Det här exemplet visar skapandet av en resursgrupp med namnet cdn platsen East USA.

```bash
az group create --name cdn --location eastus
```

![Ny resursgrupp](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

I det här steget skapar du en mallfil som distribuerar resurserna.

Det här exemplet går igenom ett scenario med allmänna webbplats Acceleration, finns men det många andra inställningar som kan konfigureras. Dessa inställningar är tillgängliga i mallreferensen för Azure Resource Manager. Se referenser för [CDN-profil](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) och [CDN-Slutpunktsprofilen](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Observera att Microsoft CDN inte stöder ändra listan.

Spara mallen som **resource-manager-cdn.json**.

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

## <a name="create-the-resources"></a>Skapa resurser

Distribuera mallen med hjälp av Azure CLI. Du uppmanas att 2 indata:

**cdnProfileSku** -CDN-leverantör som du vill använda. Alternativen är:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** -slutpunkt som hanteras via CDN, till exempel cdndemo.azurewebsites.net.

```bash
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Distribuera Resource Manager-mall](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Visa CDN-profil

```bash
az cdn profile list --resource-group cdn -o table
```

![Visa CDN-profil](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Visa CDN-slutpunkten för profilen standard-microsoft

```bash
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Visa CDN-slutpunkt](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Använd värdnamnet för att visa innehållet. Exempelvis kan komma åt https://cdndemo-azurewebsites-net.azureedge.net i webbläsaren.

## <a name="clean-up"></a>Rensa

Ta bort resursgruppen tar automatiskt bort alla resurser som har distribuerats i den.

```bash
az group delete --name cdn
```

![Ta bort resursgrupp](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Referenser

* CDN-profil - [mallreferensen för Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* CDN-slutpunkt - [mallreferensen för Azure Resource Manager-dokumentation](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du lägger till en anpassad domän i din CDN-slutpunkt finns i följande självstudiekurs:

> [!div class="nextstepaction"]
> [Självstudie: Självstudier: Lägga till en anpassad domän i Azure CDN-slutpunkten](cdn-map-content-to-custom-domain.md)

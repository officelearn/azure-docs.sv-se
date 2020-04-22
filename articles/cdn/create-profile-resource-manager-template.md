---
title: Snabbstart - Skapa en profil och slutpunkt med Resource Manager-mallar
titleSuffix: Azure Content Delivery Network
description: Lär dig hur du skapar en profil och slutpunkt för Azure Content Deliver Network med Resource Manager-mallar
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683477"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Snabbstart: Skapa en Azure CDN-profil och slutpunkt med hjälp av Resource Manager-mallen

I den här snabbstarten distribuerar du en Azure Resource Manager-mall med CLI. Mallen som du skapar distribuerar en CDN-profil och CDN-slutpunkt för att fronta webbprogrammet.
Det bör ta ungefär tio minuter att slutföra dessa steg.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Prequisites (Prequisites)

För den här snabbstarten måste du ha ett webbprogram som ska användas som ursprung. Det exempel webbprogram som användes i den här snabbstarten har distribuerats tillhttps://cdndemo.azurewebsites.net

Mer information finns [i Skapa en statisk HTML-webbapp i Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Alla resurser måste distribueras i samma resursgrupp.

Skapa resursgruppen på den plats som du väljer. I det här exemplet visas skapandet av en resursgrupp med namnet cdn på platsen i östra USA.

```azurecli-interactive
az group create --name cdn --location eastus
```

![Ny resursgrupp](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

I det här steget skapar du en mallfil som distribuerar resurserna.

Det här exemplet går igenom ett allmänt webbplatsaccelerationsscenario, men det finns många andra inställningar som kan konfigureras. Dessa inställningar är tillgängliga i mallreferensen för Azure Resource Manager. Se referenser för [CDN-profil](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) och [CDN-profilslutpunkt](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Observera att Microsoft CDN inte stöder ändring av innehållstypslistan.

Spara mallen som **resurshanterare-cdn.json**.

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

Distribuera mallen med Azure CLI. Du kommer att bli tillfrågad om 2 ingångar:

**cdnProfileSku** - cdn-providern som du vill använda. Alternativen är:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** - slutpunkten som ska visas via CDN, till exempel cdndemo.azurewebsites.net.

```azurecli-interactive
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Distribuera resurshanterarens mall](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Visa CDN-profilen

```azurecli-interactive
az cdn profile list --resource-group cdn -o table
```

![Visa CDN-profil](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Visa CDN-slutpunkten för profilen standard-microsoft

```azurecli-interactive
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Visa CDN-slutpunkt](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Använd HostName för att visa innehållet. Åtkomst till https:\//cdndemo-azurewebsites-net.azureedge.net med hjälp av din webbläsare.

## <a name="clean-up"></a>Rensa

Om du tar bort resursgruppen tas alla resurser som har distribuerats i resursgruppen automatiskt bort.

```azurecli-interactive
az group delete --name cdn
```

![Ta bort resursgrupp](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Referenser

* CDN-profil – [Mallreferens för Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* CDN-slutpunkt - [Referensdokumentation för Mallreferens för Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du lägger till en anpassad domän i din CDN-slutpunkt finns i följande självstudiekurs:

> [!div class="nextstepaction"]
> [Självstudier: Lägga till en anpassad domän i Azure CDN-slutpunkten](cdn-map-content-to-custom-domain.md)

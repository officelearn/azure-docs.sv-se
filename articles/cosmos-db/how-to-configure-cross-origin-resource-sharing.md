---
title: Cross-Origin Resource Sharing (CORS) i Azure Cosmos DB
description: Den här artikeln beskriver hur du konfigurerar Cross-Origin Resource Sharing (CORS) i Azure Cosmos DB med hjälp av Azure-portalen och Azure Resource Manager-mallar.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2018
ms.author: dech
ms.openlocfilehash: 689b0bb08e300efc7c5e93dbf346040b82a7c52d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468486"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Konfigurera Cross-Origin Resource Sharing (CORS) 

Cross-Origin Resource Sharing (CORS) är en HTTP-funktion som gör att ett webbprogram som körs i en domän att komma åt resurser i en annan domän. Webbläsare implementerar en säkerhetsbegränsning som kallas princip om samma ursprung som förhindrar att en webbsida från anropa API: er i en annan domän. Dock erbjuder CORS ett säkert sätt så att den ursprungliga domänen att anropa API: er i en annan domän. Core SQL API i Azure Cosmos DB har nu stöd för Cross-Origin Resource Sharing (CORS) med rubriken ”allowedOrigins”. När du aktiverar CORS-stöd för ditt Azure Cosmos-konto utvärderas endast autentiserade begäranden för att avgöra om de är tillåten enligt de regler som du har angett.

Du kan konfigurera den Cross-origin resource sharing (CORS)-inställningen från Azure-portalen eller från en Azure Resource Manager-mall. Core SQL API i Azure Cosmos DB stöder ett JavaScript-bibliotek som fungerar i både Node.js och webbläsarbaserade miljöer. Det här biblioteket kan nu dra nytta av CORS-stöd när du använder Gateway-läge. Det finns ingen konfiguration för klientsidan som behövs för att använda den här funktionen. Med CORS-stöd resurser från en webbläsare kan direkt åtkomst till Azure Cosmos DB via den [JavaScript-bibliotek](https://www.npmjs.com/package/@azure/cosmos) eller direkt från den [REST API](https://docs.microsoft.com/rest/api/cosmos-db/) för enkla åtgärder. 

## <a name="enable-cors-support-from-azure-portal"></a>Aktivera CORS-stöd från Azure-portalen

Använd följande steg för att aktivera Cross-Origin Resource Sharing med hjälp av Azure-portalen:

1. Navigera till ditt Azure cosmos DB-konto. Öppna den **CORS** bladet.

2. Ange en kommaavgränsad lista över ursprung som kan göra korsande ursprungsanrop till ditt Azure Cosmos DB-konto. Till exempel `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Du kan också använda jokertecken ”\*” att tillåta alla ursprung och välj **skicka**. 

   > [!NOTE]
   > För närvarande kan använda du inte jokertecken som en del av domännamnet. Till exempel `https://*.mydomain.net` format stöds inte ännu. 
   
   ![Aktivera flera ursprung resursdelning med hjälp av Azure portal](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Aktivera CORS-stöd från Resource Manager-mall

Om du vill aktivera CORS med hjälp av Resource Manager-mall lägger du till avsnittet ”cors” med ”allowedOrigins”-egenskap till en befintlig mall. Följande JSON är ett exempel på en mall som skapar ett nytt Azure Cosmos-konto med CORS aktiverat.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Med hjälp av Azure Cosmos DB JavaScript-bibliotek från en webbläsare

Idag har i Azure Cosmos DB JavaScript-biblioteket bara CommonJS-versionen av biblioteket levereras med dess paket. Du måste använda ett verktyg som samlade eller Webpack för att skapa ett kompatibelt bibliotek i webbläsaren om du vill använda det här biblioteket från webbläsaren. Vissa Node.js-bibliotek bör ha webbläsare mocks för dessa. Följande är ett exempel på en webpack config-fil som har de nödvändiga fingerad inställningarna.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Här är en [kodexempel](https://github.com/christopheranderson/cosmos-browser-sample) som använder TypeScript och Webpack med Azure Cosmos DB JavaScript SDK-biblioteket för att skapa en att göra-app som skickar uppdateringar i realtid när nya objekt skapas.
Du bör inte använda den primära nyckeln för att kommunicera med Azure Cosmos DB från webbläsaren. Använd i stället resurstokens för att kommunicera. Läs mer om resurstokens [skydda åtkomst till Azure Cosmos DB](secure-access-to-data.md#resource-tokens) artikeln.

## <a name="next-steps"></a>Nästa steg

Mer information om andra sätt att skydda ditt Azure Cosmos-konto, finns i följande artiklar:

* [Konfigurera en brandvägg för Azure Cosmos DB](how-to-configure-firewall.md) artikeln.

* [Konfigurera virtuella nätverk och undernät-baserad åtkomst för ditt Azure Cosmos DB-konto](how-to-configure-vnet-service-endpoint.md)
    


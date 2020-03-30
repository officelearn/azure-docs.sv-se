---
title: Korsdelning (Cross-Origin Resource Sharing) i Azure Cosmos DB
description: I den här artikeln beskrivs hur du konfigurerar CORS (Cross-Origin Resource Sharing) i Azure Cosmos DB med hjälp av Azure Portal- och Azure Resource Manager-mallar.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 7a487cb10965a379a0a418efaa061be88c5d10dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082989"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Konfigurera resursdelning mellan ursprungsgränser (CORS)

Cors (Cross-Origin Resource Sharing) är en HTTP-funktion som gör att ett webbprogram som körs under en domän kan komma åt resurser i en annan domän. Webbläsare implementerar en säkerhetsbegränsning som kallas samma princip som förhindrar att en webbsida anropar API:er i en annan domän. CORS är dock ett säkert sätt att tillåta ursprungsdomänen att anropa API:er i en annan domän. Core-API:et (SQL) i Azure Cosmos DB stöder nu CORS (Cross-Origin Resource Sharing) med hjälp av huvudet "allowedOrigins". När du har aktiverat CORS-supporten för ditt Azure Cosmos-konto utvärderas endast autentiserade begäranden för att avgöra om de tillåts enligt de regler som du har angett.

Du kan konfigurera inställningen Korsbeskärningsresursdelning (CORS) från Azure-portalen eller från en Azure Resource Manager-mall. För Cosmos-konton med hjälp av SQL-API:et (Core) stöder Azure Cosmos DB ett JavaScript-bibliotek som fungerar i både Node.js och webbläsarbaserade miljöer. Det här biblioteket kan nu dra nytta av CORS-stöd när du använder gateway-läge. Det finns ingen konfiguration på klientsidan som behövs för att använda den här funktionen. Med CORS-stöd kan resurser från en webbläsare direkt komma åt Azure Cosmos DB via [JavaScript-biblioteket](https://www.npmjs.com/package/@azure/cosmos) eller direkt från [REST API](https://docs.microsoft.com/rest/api/cosmos-db/) för enkla åtgärder.

> [!NOTE]
> CORS-stöd är endast tillämpligt och stöds för Azure Cosmos DB Core (SQL) API. Det är inte tillämpligt på Azure Cosmos DB API:er för Cassandra, Gremlin eller MongoDB, eftersom dessa protokoll inte använder HTTP för klientserverkommunikation.

## <a name="enable-cors-support-from-azure-portal"></a>Aktivera CORS-stöd från Azure Portal

Gör så här för att aktivera resursdelning över flera ursprung med hjälp av Azure-portalen:

1. Navigera till ditt Azure cosmos DB-konto. Öppna **CORS-bladet.**

2. Ange en kommaavgränsad lista med ursprung som kan ringa samtal med flera ursprung till ditt Azure Cosmos DB-konto. Till exempel `https://www.mydomain.com` `https://mydomain.com`, `https://api.mydomain.com`, . Du kan också använda\*ett jokertecken " " för att tillåta alla ursprung och välja **Skicka**. 

   > [!NOTE]
   > För närvarande kan du inte använda jokertecken som en del av domännamnet. Format `https://*.mydomain.net` stöds till exempel ännu inte. 

   ![Aktivera resursdelning mellan ursprung med Azure-portalen](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)

## <a name="enable-cors-support-from-resource-manager-template"></a>Aktivera CORS-stöd från Resource Manager-mall

Om du vill aktivera CORS med hjälp av en Resource Manager-mall lägger du till avsnittet "cors" med egenskapen "allowedOrigins" i en befintlig mall. Följande JSON är ett exempel på en mall som skapar ett nytt Azure Cosmos-konto med CORS aktiverat.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Använda Azure Cosmos DB JavaScript-bibliotek från en webbläsare

Idag har Azure Cosmos DB JavaScript-biblioteket bara CommonJS-versionen av biblioteket som levereras med paketet. Om du vill använda det här biblioteket från webbläsaren måste du använda ett verktyg som Samlad eller Webpack för att skapa ett webbläsarkompatibelt bibliotek. Vissa Node.js-bibliotek bör ha webbläsarhämtar för dem. Följande är ett exempel på en webpack config-fil som har de nödvändiga utkastinställningarna.

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
 
Här är ett [kodexempel](https://github.com/christopheranderson/cosmos-browser-sample) som använder TypeScript och Webpack med Azure Cosmos DB JavaScript SDK-biblioteket för att skapa en Todo-app som skickar realtidsuppdateringar när nya objekt skapas.
Använd inte primärnyckeln för att kommunicera med Azure Cosmos DB från webbläsaren. Använd i stället resurstoken för att kommunicera. Mer information om resurstoken finns i [Skydda åtkomst till Azure Cosmos DB-artikel.](secure-access-to-data.md#resource-tokens)

## <a name="next-steps"></a>Nästa steg

Mer information om andra sätt att skydda ditt Azure Cosmos-konto finns i följande artiklar:

* [Konfigurera en brandvägg för Azure Cosmos](how-to-configure-firewall.md) DB-artikel.

* [Konfigurera virtuellt nätverk och undernätsbaserad åtkomst för ditt Azure Cosmos DB-konto](how-to-configure-vnet-service-endpoint.md)

---
title: MongoDB-anslutningssträng för ett Azure Cosmos DB-konto | Microsoft Docs
description: Lär dig hur du ansluter din MongoDB-app till ett Azure Cosmos DB-konto med hjälp av en anslutningssträng för MongoDB.
keywords: mongodb-anslutningssträng
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sclyon
ms.openlocfilehash: ad8d6fe36c289c4c9e37689e1c7d755dc3bf9048
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054556"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Anslut en MongoDB-program till Azure Cosmos DB
Lär dig hur du ansluter din MongoDB-app till ett Azure Cosmos DB-konto med hjälp av en anslutningssträng för MongoDB. Du kan sedan använda ett Azure Cosmos DB-databasen som data store för din MongoDB-app. 

Den här självstudien ger dig två sätt att hämta information om anslutningssträngen:

- [Snabbstartsguide metoden](#QuickstartConnection), för användning med .NET, Node.js, MongoDB-gränssnittet, Java och Python-drivrutiner
- [Anpassad sträng anslutningsmetoden](#GetCustomConnection), för användning med andra drivrutiner

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har ett Azure-konto kan du skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) nu. 
- Ett Azure Cosmos DB-konto. Anvisningar finns i [skapa en MongoDB API-webbapp med .NET och Azure-portalen](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Hämta anslutningssträngen för MongoDB med hjälp av snabb start
1. I en webbläsare, loggar du in den [Azure-portalen](https://portal.azure.com).
2. I den **Azure Cosmos DB** bladet välj API för MongoDB-kontot. 
3. I rutan till vänster på kontobladet klickar du på **Snabbstart**. 
4. Välj din plattform (**.NET**, **Node.js**, **MongoDB-gränssnittet**, **Java**, **Python**). Om du inte ser din drivrutin eller verktyg i listan, oroa inte dig – dokumenterar vi kontinuerligt fler kodfragment. Kommentera nedan på vad du vill se. Om du vill lära dig mer om att skapa din egen anslutning, läsa [hämta information om kontots anslutningssträng](#GetCustomConnection).
5. Kopiera och klistra in kodfragmentet i MongoDB-app.

    ![Snabbstart-bladet](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Hämta anslutningssträngen för MongoDB för att anpassa
1. I en webbläsare, loggar du in den [Azure-portalen](https://portal.azure.com).
2. I den **Azure Cosmos DB** bladet välj API för MongoDB-kontot. 
3. I rutan till vänster på kontobladet klickar du på **Connection String**. 
4. Den **Connection String** blad öppnas. Den har all information som behövs för att ansluta till kontot med hjälp av en drivrutin för MongoDB, inklusive en preconstructed anslutningssträng.

    ![Bladet Anslutningssträng](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Sträng anslutningskraven
> [!Important]
> Azure Cosmos DB har stränga säkerhetskrav och standarder. Azure Cosmos DB-konton kräver autentisering och säker kommunikation via *SSL*. 
>
>

Azure Cosmos DB stöder standard MongoDB URI formatet för anslutningssträngen, med några särskilda krav: Azure Cosmos DB-konton kräver autentisering och säker kommunikation via SSL. Därför är formatet för anslutningssträngen:

    mongodb://username:password@host:port/[database]?ssl=true

Värdena för den här strängen är tillgängliga i den **Connection String** bladet som visades tidigare:

* Användarnamn (krävs): Azure Cosmos DB-kontonamn.
* Lösenord (krävs): lösenordet för Azure Cosmos DB.
* Värd (krävs): FQDN för Azure Cosmos DB-kontot.
* Port (krävs): 10255.
* Databas (valfritt): den databas som anslutningen använder. Om ingen databas har angetts och är standarddatabasen ”test”.
* SSL = true (krävs)

Anta exempelvis att kontot som visas på den **Connection String** bladet. En giltig anslutningssträng är:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda Studio 3T (MongoChef)](mongodb-mongochef.md) med ett Azure Cosmos DB-API för MongoDB-kontot.
* Utforska Azure Cosmos DB API för MongoDB genom att visa [exempel](mongodb-samples.md).

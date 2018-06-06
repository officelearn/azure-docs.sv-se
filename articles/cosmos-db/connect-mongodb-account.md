---
title: MongoDB-anslutningssträng för ett konto i Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om att ansluta appen MongoDB till ett Azure DB som Cosmos-konto med hjälp av en anslutningssträng för MongoDB.
keywords: anslutningssträngen för mongodb
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: 441f6725b96dc7a099304e06db6d46a65e7f9c81
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795966"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Ansluta ett MongoDB-program till Azure Cosmos DB
Lär dig mer om att ansluta appen MongoDB till ett Azure DB som Cosmos-konto med hjälp av en anslutningssträng för MongoDB. Du kan sedan använda en Azure Cosmos-DB-databas som data store för MongoDB-app. 

Den här kursen finns det två sätt att hämta information om anslutningssträngar:

- [Snabbstartsguide metoden](#QuickstartConnection), för användning med .NET, Node.js, MongoDB-gränssnittet, Java och Python drivrutiner
- [Anpassad sträng anslutningsmetoden](#GetCustomConnection), för användning med andra drivrutiner

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har ett Azure-konto kan du skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) nu. 
- Ett Azure Cosmos DB-konto. Instruktioner finns i [bygga en MongoDB API webbprogram med .NET och Azure portal](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Hämta MongoDB-anslutningssträngen med hjälp av Snabbstart
1. I en webbläsare, logga in på den [Azure-portalen](https://portal.azure.com).
2. I den **Azure Cosmos DB** bladet välj API för MongoDB-kontot. 
3. I den vänstra rutan på konto-bladet klickar du på **Snabbstart**. 
4. Välj din plattform (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Om du inte ser ditt drivrutin eller verktyg, oroa inte dig – dokumentera vi kontinuerligt mer kodfragment för anslutningen. Kommentera nedan på vad du skulle vilja se. Om du vill lära dig skapa dina egna anslutning, läsa [hämta kontots Anslutningssträngsinformation](#GetCustomConnection).
5. Kopiera och klistra in kodfragmentet i MongoDB-app.

    ![Snabbstart-bladet](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Hämta anslutningssträngen för MongoDB för att anpassa
1. I en webbläsare, logga in på den [Azure-portalen](https://portal.azure.com).
2. I den **Azure Cosmos DB** bladet välj API för MongoDB-kontot. 
3. I den vänstra rutan på konto-bladet klickar du på **anslutningssträngen**. 
4. Den **anslutningssträngen** blad öppnas. Den har all information som behövs för att ansluta till kontot genom att använda en drivrutin för MongoDB, inklusive en preconstructed anslutningssträng.

    ![Anslutningen sträng bladet](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Anslutningen sträng krav
> [!Important]
> Azure Cosmos-DB har stränga säkerhetskrav och standarder. Azure DB Cosmos-konton som kräver autentisering och säker kommunikation via *SSL*. 
>
>

Azure Cosmos-DB stöder det standard MongoDB-Anslutningssträngens URI formatet, med några särskilda krav: Azure Cosmos DB konton som kräver autentisering och säker kommunikation via SSL. Därför är Anslutningssträngens format:

    mongodb://username:password@host:port/[database]?ssl=true

Värdena för den här strängen är tillgängliga i den **anslutningssträngen** bladet som visades tidigare:

* Användarnamn (krävs): Azure Cosmos DB kontonamn.
* Lösenord (krävs): Azure Cosmos DB kontolösenord.
* Värd (krävs): FQDN för kontot Azure Cosmos DB.
* Port (krävs): 10255.
* Databas (valfritt): den databas som används i anslutningen. Om ingen databas är standarddatabasen ”test”.
* SSL = true (krävs)

Anta till exempel att det konto som visas i den **anslutningssträngen** bladet. Det är en giltig anslutningssträng:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda Studio 3T (MongoChef)](mongodb-mongochef.md) med en Azure Cosmos DB API för MongoDB-kontot.
* Utforska Azure Cosmos DB API för MongoDB genom att visa [exempel](mongodb-samples.md).

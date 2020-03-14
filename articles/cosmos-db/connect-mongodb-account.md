---
title: Anslut ett MongoDB-program till Azure Cosmos DB
description: Lär dig hur du ansluter en MongoDB-app till Azure Cosmos DB genom att hämta anslutnings strängen från Azure Portal
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: 0f2cd1b7228f2cc9cadb84232222f658a512a81f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246856"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Anslut ett MongoDB-program till Azure Cosmos DB
Lär dig hur du ansluter MongoDB-appen till en Azure Cosmos DB med hjälp av en MongoDB-anslutningssträng. Du kan sedan använda en Azure Cosmos-databas som datalager för din MongoDB-app. 

I den här självstudien beskrivs två sätt att hämta information om anslutningssträng:

- [Snabb starts metoden](#QuickstartConnection)för användning med .net, Node. js, MongoDB Shell-, Java-och python-drivrutiner
- [Metoden anpassad anslutnings sträng](#GetCustomConnection), som används med andra driv rutiner

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har ett Azure-konto kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) nu. 
- Ett Cosmos-konto. Instruktioner finns i [bygga en webbapp med hjälp av Azure Cosmos DBS API för MongoDB och .NET SDK](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Hämta anslutnings strängen för MongoDB med hjälp av snabb start
1. Logga in på [Azure Portal](https://portal.azure.com)i en webbläsare.
2. På bladet **Azure Cosmos DB** väljer du API: et. 
3. I det vänstra fönstret på konto bladet klickar du på **snabb start**. 
4. Välj din plattform ( **.net**, **Node. js**, **MongoDB Shell**, **Java**, **python**). Om du inte ser den driv rutin eller det verktyg som visas, behöver vi kontinuerligt dokumentera fler anslutnings kods tycken. Kommentera nedan om vad du vill se. Information om hur du kan skapa en egen anslutning finns [i Hämta kontots anslutnings sträng information](#GetCustomConnection).
5. Kopiera och klistra in kodfragmentet i MongoDB-appen.

    ![Bladet snabb start](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a>Hämta MongoDB-anslutningssträngen för att anpassa
1. Logga in på [Azure Portal](https://portal.azure.com)i en webbläsare.
2. På bladet **Azure Cosmos DB** väljer du API: et. 
3. I det vänstra fönstret på konto bladet klickar du på **anslutnings sträng**. 
4. Bladet **anslutnings sträng** öppnas. Den innehåller all information som krävs för att ansluta till kontot med hjälp av en driv rutin för MongoDB, inklusive en förkonstruerad anslutnings sträng.

    ![Bladet Anslutningssträng](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Krav för anslutnings sträng
> [!Important]
> Azure Cosmos DB har stränga säkerhetskrav och säkerhetsstandarder. Azure Cosmos DB konton kräver autentisering och säker kommunikation via *SSL*. 
>
>

Azure Cosmos DB stöder URI-formatet standard MongoDB-anslutnings sträng, med ett par särskilda krav: Azure Cosmos DB konton kräver autentisering och säker kommunikation via SSL. Formatet för anslutnings strängen är alltså:

    mongodb://username:password@host:port/[database]?ssl=true

Värdena för den här strängen är tillgängliga på bladet **anslutnings sträng** som visades tidigare:

* Användar namn (obligatoriskt): Cosmos-konto namn.
* Lösen ord (obligatoriskt): Cosmos-kontots lösen ord.
* Värd (obligatoriskt): FQDN för Cosmos-kontot.
* Port (krävs): 10255.
* Databas (valfritt): databasen som anslutningen använder. Om ingen databas anges är standard databasen "test".
* SSL = true (krävs)

Överväg till exempel kontot som visas på bladet **anslutnings sträng** . En giltig anslutnings sträng är:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

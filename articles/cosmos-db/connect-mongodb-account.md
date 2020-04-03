---
title: Ansluta ett MongoDB-program till Azure Cosmos DB
description: Lär dig hur du ansluter en MongoDB-app till Azure Cosmos DB genom att hämta anslutningssträngen från Azure Portal
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/19/2020
ms.reviewer: sngun
ms.openlocfilehash: cba16d79b6506f9809a76f0128938a68afd15c92
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617038"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Ansluta ett MongoDB-program till Azure Cosmos DB

Lär dig hur du ansluter MongoDB-appen till en Azure Cosmos DB med hjälp av en MongoDB-anslutningssträng. Du kan sedan använda en Azure Cosmos-databas som datalager för din MongoDB-app.

I den här självstudien beskrivs två sätt att hämta information om anslutningssträng:

- [Snabbstartsmetoden](#get-the-mongodb-connection-string-by-using-the-quick-start), för användning med .NET, Node.js, MongoDB Shell,Java och Python-drivrutiner
- [Metoden med anpassad anslutningssträng](#get-the-mongodb-connection-string-to-customize), för användning med andra drivrutiner

## <a name="prerequisites"></a>Krav

- Ett Azure-konto. Om du inte har ett Azure-konto skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) nu.
- Ett Cosmos-konto. Instruktioner finns i [Skapa en webbapp med Azure Cosmos DB:s API för MongoDB och .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Hämta Anslutningssträngen för MongoDB med hjälp av snabbstarten

1. Logga in på [Azure-portalen](https://portal.azure.com)i en webbläsare .
2. Välj API:et i **Azure Cosmos** DB-bladet.
3. Klicka på **Snabbstart**i den vänstra rutan på kontobladet .
4. Välj din plattform (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Om du inte ser din drivrutin eller ditt verktyg i listan, oroa dig inte – vi dokumenterar kontinuerligt fler anslutningskodavsnitt. Kommentera nedan vad du vill se. Om du vill veta hur du skapar en egen anslutning läser [du Hämta kontots anslutningsstränginformation](#get-the-mongodb-connection-string-to-customize).
5. Kopiera och klistra in kodavsnittet i mongoDb-appen.

    ![Snabbstartsblad](./media/connect-mongodb-account/QuickStartBlade.png)

## <a name="get-the-mongodb-connection-string-to-customize"></a>Hämta Anslutningssträngen för MongoDB för att anpassa

1. Logga in på [Azure-portalen](https://portal.azure.com)i en webbläsare .
2. Välj API:et i **Azure Cosmos** DB-bladet.
3. Klicka på **Anslutningssträng**i den vänstra rutan på kontobladet .
4. **Bladet Anslutningssträng** öppnas. Den har all information som behövs för att ansluta till kontot med hjälp av en drivrutin för MongoDB, inklusive en förkonstruerad anslutningssträng.

   [![Bladet](./media/connect-mongodb-account/ConnectionStringBlade.png) Anslutningssträng](./media/connect-mongodb-account/ConnectionStringBlade.png#lightbox)

## <a name="connection-string-requirements"></a>Krav på anslutningssträng

> [!Important]
> Azure Cosmos DB har stränga säkerhetskrav och säkerhetsstandarder. Azure Cosmos DB-konton kräver autentisering och säker kommunikation via *TLS*. 
>
>

Azure Cosmos DB stöder uri-standardsträngen MongoDB-anslutningssträngen, med ett par specifika krav: Azure Cosmos DB-konton kräver autentisering och säker kommunikation via TLS. Så är anslutningssträngformatet:

    mongodb://username:password@host:port/[database]?ssl=true

Värdena för den här strängen är tillgängliga i **bladet Anslutningssträng** som visas tidigare:

* Användarnamn (obligatoriskt): Cosmos-kontonamn.
* Lösenord (obligatoriskt): Cosmos-kontolösenord.
* Värd (obligatoriskt): FQDN för Cosmos-kontot.
* Port (obligatorisk): 10255.
* Databas (valfritt): Databasen som anslutningen använder. Om ingen databas tillhandahålls är standarddatabasen "test".
* ssl=true (obligatoriskt)

Tänk dig till exempel kontot som visas i **bladet Anslutningssträng.** En giltig anslutningssträng är:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

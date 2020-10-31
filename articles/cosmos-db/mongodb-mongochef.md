---
title: Använd Studio 3T för att ansluta till Azure Cosmos DB s API för MongoDB
description: Lär dig hur du ansluter till en Azure Cosmos DBs API för MongoDB med Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: a02aaadf8c774557eb182acf041b6f19337a0de8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096528"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Ansluta till ett Azure Cosmos-konto med Studio 3T
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

För att ansluta till en Azure Cosmos DBs API för MongoDB med Studio 3T måste du:

* Hämta och installera [Studio 3T](https://studio3t.com/).
* Få information om [anslutnings strängen](connect-mongodb-account.md) för ditt Azure Cosmos-konto.

## <a name="create-the-connection-in-studio-3t"></a>Skapa anslutningen i Studio 3T

Använd följande steg för att lägga till ditt Azure Cosmos-konto i anslutnings hanteraren för Studio 3T:

1. Hämta anslutnings informationen för din Azure Cosmos DBs API för MongoDB-konto med hjälp av anvisningarna i artikeln [Anslut ett MongoDB program till Azure Cosmos DB](connect-mongodb-account.md) .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Skärm bild av sidan anslutnings sträng":::

2. Klicka på **Anslut** för att öppna anslutnings hanteraren och klicka sedan på **ny anslutning**

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Skärm bild av sidan anslutnings sträng":::
3. I fönstret **ny anslutning** går du till fliken **Server** och anger värd (FQDN) för Azure Cosmos-kontot och porten.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Skärm bild av sidan anslutnings sträng":::
4. I fönstret **ny anslutning** på fliken **autentisering** väljer du autentiseringsläge **Basic (MONGODB-CR eller SCARM-SHA-1)** och anger användar namn och lösen ord.  Godkänn standard databasen för autentisering (admin) eller ange ett eget värde.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Skärm bild av sidan anslutnings sträng":::
5. I fönstret **ny anslutning** går du till fliken **SSL** och markerar kryss rutan **Använd SSL-protokoll för att ansluta** och alternativ knappen **acceptera serverns självsignerade SSL-certifikat** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Skärm bild av sidan anslutnings sträng":::
6. Klicka på knappen **Testa anslutning** för att kontrol lera anslutnings informationen, klicka på **OK** för att återgå till fönstret ny anslutning och klicka sedan på **Spara** .

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Skärm bild av sidan anslutnings sträng":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Använd Studio 3T för att skapa en databas, samling och dokument
Utför följande steg för att skapa en databas, samling och dokument med Studio 3T:

1. I **anslutnings hanteraren** markerar du anslutningen och klickar på **Anslut** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Skärm bild av sidan anslutnings sträng":::
2. Högerklicka på värden och välj **Lägg till databas** .  Ange ett databas namn och klicka på **OK** .

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Skärm bild av sidan anslutnings sträng":::
3. Högerklicka på databasen och välj **Lägg till samling** .  Ange ett samlings namn och klicka på **skapa** .

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Skärm bild av sidan anslutnings sträng":::
4. Klicka på meny alternativet **samling** och klicka sedan på **Lägg till dokument** .

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Skärm bild av sidan anslutnings sträng" och returnera fälten föräldrar och State.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Skärm bild av sidan anslutnings sträng":::

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

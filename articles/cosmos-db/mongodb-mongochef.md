---
title: Ansluta till Azure Cosmos DB s API för MongoDB med Studio 3T
titleSuffix: Azure Cosmos DB
description: Lär dig hur du ansluter till Cosmos DB med Studio 3T och Azure Cosmos DB s API för MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 46cac6720bbef133d6b3f7a269cf97a81cddd0c5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062113"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Ansluta till Cosmos-kontot med Studio 3T

Om du vill ansluta till ett Cosmos-konto med hjälp av Azure Cosmos DB s API för MongoDB, måste du:

* Hämta och installera [Studio 3T](https://studio3t.com/)
* Ha information om Cosmos DB- [anslutningssträng](connect-mongodb-account.md)

> [!NOTE]
> För närvarande stöds Robo 3T v 1.2 och lägre med Cosmos DB s API för MongoDB. 

## <a name="create-the-connection-in-studio-3t"></a>Skapa anslutningen i Studio 3T
Gör så här om du vill lägga till ditt Cosmos-konto i anslutnings hanteraren för Studio 3T:

1. Hämta anslutnings informationen för ditt Cosmos-konto som kon figurer ATS med Azure Cosmos DB s API för MongoDB med hjälp av anvisningarna i artikeln [Anslut ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md) .

    ![Skärm bild av sidan anslutnings sträng](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Klicka på **Connect** för att öppna hanteraren för anslutning, klicka sedan på **ny anslutning**

    ![Skärm bild av anslutnings hanteraren för Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. I den **ny anslutning** fönstret på den **Server** ange värden (FQDN) för Azure Cosmos DB-kontot och PORT.

    ![Skärm bild av 3T för anslutnings hanteraren i Studio-servern](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. I den **ny anslutning** fönstret på den **autentisering** fliken, välja autentiseringsläge **Basic (MONGODB-CR eller SCARM-SHA-1)** och ange användarnamn och lösenord.  Acceptera standardvärdet autentisering db (admin) eller ange ett eget värde.

    ![Skärm bild av fliken för 3T i anslutnings hanteraren för Studio](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. I den **ny anslutning** fönstret på den **SSL** fliken, kontrollera den **Använd SSL-protokollet för att ansluta** markerar du kryssrutan och **accepterar server självsignerat SSL-certifikat**  alternativknappen.

    ![Skärm bild av fliken SSL 3T Connection Manager SSL](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klicka på den **Testanslutningen** knappen för att verifiera anslutningsinformationen och klickar på **OK** tillbaka till fönstret Ny anslutning och klicka sedan på **spara**.

    ![Skärm bild av fönstret Studio 3T Test Connection](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Använda Studio 3T för att skapa en databas, samling och dokument
Om du vill skapa en databas, samling och dokument med hjälp av Studio 3T, utför du följande steg:

1. I **Anslutningshanteraren**, markera anslutningen och klicka på **Connect**.

    ![Skärm bild av anslutnings hanteraren för Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Högerklicka på värden och välj **lägga till databas**.  Ange ett databasnamn och klicka på **OK**.

    ![Skärm bild av alternativet för att lägga till databaser i Studio-3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Högerklicka på databasen och välj **Lägg till samling**.  Ange ett samlingsnamn och klicka på **skapa**.

    ![Skärm bild av alternativet för att lägga till samlingar i Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Klicka på den **samling** menyn, klicka sedan på **Lägg till dokument**.

    ![Skärm bild av meny alternativet Lägg till dokument på Studio-3T](./media/mongodb-mongochef/AddDocument1.png)
5. Klistra in följande i dialogrutan Lägg till dokument och klicka sedan på **Lägg till dokument**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Lägg till ett annat dokument, den här gången med följande innehåll:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Kör en exempelfråga. Till exempel söka efter familjer med efternamn ”Andersen” och returnera föräldrar och statusfält.

    ![Skärm bild av frågeresultatet Mongo chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

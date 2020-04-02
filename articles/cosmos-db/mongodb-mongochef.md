---
title: Använd Studio 3T för att ansluta till Azure Cosmos DB:s API för MongoDB
description: Lär dig hur du ansluter till en Azure Cosmos DB:s API för MongoDB med Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 84b703cceeb130b177b8ab32281ef616b1ec632b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548825"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Ansluta till ett Azure Cosmos-konto med Studio 3T

Om du vill ansluta till ett Azure Cosmos DB:s API för MongoDB med Studio 3T måste du:

* Ladda ner och installera [Studio 3T](https://studio3t.com/).
* Ha information om ditt Azure Cosmos-kontos [anslutningssträng.](connect-mongodb-account.md)

## <a name="create-the-connection-in-studio-3t"></a>Skapa anslutningen i Studio 3T

Så här lägger du till ditt Azure Cosmos-konto i Studio 3T-anslutningshanteraren:

1. Hämta anslutningsinformationen för ditt Azure Cosmos DB:s API för MongoDB-konto med hjälp av instruktionerna i artikeln [Anslut ett MongoDB-program till Azure Cosmos DB.](connect-mongodb-account.md)

    ![Skärmbild av anslutningssträngsidan](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Klicka på **Anslut** för att öppna Anslutningshanteraren och klicka sedan på **Ny anslutning**

    ![Skärmbild av Studio 3T-anslutningshanteraren](./media/mongodb-mongochef/ConnectionManager.png)
3. I fönstret **Ny anslutning** på fliken **Server** anger du HOST (FQDN) för Azure Cosmos-kontot och PORT.

    ![Skärmbild av fliken Studio 3T-anslutningshanterare](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Välj Basic för **autentiseringsläge** **(MONGODB-CR eller SCARM-SHA-1)** på fliken Autentisering i fönstret **Ny anslutning** och ange ANVÄNDARNAMN och LÖSENORD.  Acceptera standardautentisering db (admin) eller ange ditt eget värde.

    ![Skärmbild av autentiseringsfliken studio 3T-anslutningshanterare](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. I fönstret **Ny anslutning** på fliken **SSL** markerar du kryssrutan **Använd SSL-protokoll för att ansluta** och alternativknappen Acceptera **serverns självsignerade SSL-certifikat.**

    ![Skärmbild av SSL-fliken Studio 3T-anslutningshanterare](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klicka på knappen **Testa anslutning** om du vill validera anslutningsinformationen, klicka på **OK** för att återgå till fönstret Ny anslutning och klicka sedan på **Spara**.

    ![Skärmbild av fönstret för studio 3T-testanslutning](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Använda Studio 3T för att skapa en databas, samling och dokument
Så här skapar du en databas, samling och dokument med Studio 3T:

1. Markera anslutningen i **Anslutningshanteraren**och klicka på **Anslut**.

    ![Skärmbild av Studio 3T-anslutningshanteraren](./media/mongodb-mongochef/ConnectToAccount.png)
2. Högerklicka på värden och välj **Lägg till databas**.  Ange ett databasnamn och klicka på **OK**.

    ![Skärmbild av alternativet Studio 3T Add Database](./media/mongodb-mongochef/AddDatabase1.png)
3. Högerklicka på databasen och välj **Lägg till samling**.  Ange ett samlingsnamn och klicka på **Skapa**.

    ![Skärmbild av alternativet Studio 3T Lägg till samling](./media/mongodb-mongochef/AddCollection.png)
4. Klicka på menyalternativet **Samling** och sedan **på Lägg till dokument**.

    ![Skärmbild av menyalternativet Studio 3T Lägg till dokument](./media/mongodb-mongochef/AddDocument1.png)
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
7. Kör en exempelfråga. Sök till exempel efter familjer med efternamnet "Andersen" och returnera föräldrarnas och statens fält.

    ![Skärmbild av Mongo Chef-frågeresultat](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

---
title: Använd Studio 3T (MongoChef) med Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om Studio 3T med ett konto i Azure Cosmos DB MongoDB API
keywords: mongochef studio 3T
services: cosmos-db
author: AndrewHoh
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: anhoh
ms.openlocfilehash: 98f1a83ad2470d4e133167b6c4d140a0aa34e114
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Azure Cosmos DB: Använd Studio 3T med ett konto med MongoDB-API

För att ansluta till ett Azure Cosmos DB MongoDB API-konto, måste du:

* Hämta och installera [Studio 3T](https://studio3t.com/) (kallades tidigare MongoChef)
* Har Azure Cosmos-DB [anslutningssträngen](connect-mongodb-account.md) information för ditt konto för MongoDB

## <a name="create-the-connection-in-studio-3t"></a>Skapa anslutningen i Studio 3T
Utför följande steg för att lägga till ditt konto i Azure Cosmos DB Studio 3T connection manager:

1. Hämta Azure Cosmos DB-anslutningsinformationen för ditt konto för MongoDB-API med hjälp av anvisningarna i den [ansluta ett MongoDB-program till Azure Cosmos DB](connect-mongodb-account.md) artikel.

    ![Skärmbild av sidan sträng](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Klicka på **Anslut** öppna Connection Manager och sedan klicka på **ny anslutning**

    ![Skärmbild av Anslutningshanteraren för Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. I den **ny anslutning** fönstret på den **Server** ange värden (FQDN) för kontot som Azure Cosmos DB och PORT.

    ![Skärmbild som visar fliken Studio 3T anslutning manager server](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. I den **ny anslutning** fönstret på den **autentisering** väljer autentiseringsläge **Basic (CR MONGODB eller SCARM-SHA-1)** och ange användarnamn och lösenord.  Acceptera standardvärdet autentisering db (admin) eller ange ett eget värde.

    ![Skärmbild som visar fliken Studio 3T connection manager autentisering](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. I den **ny anslutning** fönstret på den **SSL** markerar den **Använd SSL-protokollet för att ansluta** kryssrutan och **accepterar server självsignerade SSL-certifikat** knappen.

    ![Skärmbild av fliken Studio 3T connection manager SSL](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klicka på den **Testa anslutning** för att verifiera informationen klickar du på **OK** återgå till fönstret Ny anslutning och klicka sedan på **spara**.

    ![Skärmdump av fönstret Studio 3T Testa anslutning](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Använd Studio 3T för att skapa en databas, samling och dokument
Om du vill skapa en databas, samling och dokument med hjälp av Studio 3T, utför du följande steg:

1. I **Connection Manager**, markera anslutningen och klicka på **Anslut**.

    ![Skärmbild av Anslutningshanteraren för Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Högerklicka på värden och välj **lägga till databas**.  Ange ett namn och klicka på **OK**.

    ![Skärmdump av alternativet Studio 3T lägga till databas](./media/mongodb-mongochef/AddDatabase1.png)
3. Högerklicka på databasen och välj **lägga till samlingen**.  Ange ett samlingsnamn och klicka på **skapa**.

    ![Skärmdump av alternativet Studio 3T lägga till samlingen](./media/mongodb-mongochef/AddCollection.png)
4. Klicka på den **samling** menyn, klicka på **Lägg till dokument**.

    ![Skärmbild som visar menyalternativet Studio 3T Lägg till dokument](./media/mongodb-mongochef/AddDocument1.png)
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
6. Lägg till ett annat dokument nu med följande innehåll:

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
7. Köra en exempelfråga. Till exempel söka efter familjer med efternamn 'Andersen' och returnerar det överordnade och tillstånd.

    ![Skärmbild av Mongo Chef frågeresultat](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Nästa steg
* Utforska Azure Cosmos DB MongoDB API [exempel](mongodb-samples.md).

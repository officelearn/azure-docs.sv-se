---
title: "Använd MongoChef för Azure Cosmos DB | Microsoft Docs"
description: "Lär dig hur du använder MongoChef med en Azure-Cosmos-DB: API för MongoDB-konto"
keywords: mongochef
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 54c9799bd646b827f602e2ea2f9a15a4fc853f00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-mongochef-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Använda MongoChef med en Azure-Cosmos-DB: API för MongoDB-konto

Att ansluta till en Azure-Cosmos-DB: API för MongoDB-konto, måste du:

* Hämta och installera [MongoChef](http://3t.io/mongochef)
* Har Azure Cosmos-DB: API för MongoDB konto [anslutningssträngen](connect-mongodb-account.md) information

## <a name="create-the-connection-in-mongochef"></a>Skapa anslutningen i MongoChef
Att lägga till Azure Cosmos-DB: API MongoDB-kontot Anslutningshanteraren MongoChef utför följande steg.

1. Hämta Azure Cosmos-DB: API: et för MongoDB anslutningsinformationen med instruktioner [här](connect-mongodb-account.md).

    ![Skärmbild av bladet anslutning sträng](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Klicka på **Anslut** öppna Connection Manager och sedan klicka på **ny anslutning**

    ![Skärmbild av MongoChef Anslutningshanteraren](./media/mongodb-mongochef/ConnectionManager.png)
3. I den **ny anslutning** fönstret på den **Server** ange värden (FQDN) på Azure Cosmos DB: API för MongoDB-kontot och PORT.

    ![Skärmbild av fliken MongoChef anslutning manager server](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. I den **ny anslutning** fönstret på den **autentisering** väljer autentiseringsläge **Standard (CR MONGODB eller SCARM-SHA-1)** och ange användarnamn och lösenord.  Acceptera standardvärdet autentisering db (admin) eller ange ett eget värde.

    ![Skärmbild av fliken MongoChef connection manager autentisering](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. I den **ny anslutning** fönstret på den **SSL** markerar den **Använd SSL-protokollet för att ansluta** kryssrutan och **accepterar server självsignerade SSL-certifikat** knappen.

    ![Skärmbild av fliken MongoChef connection manager SSL](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klicka på den **Testa anslutning** för att verifiera informationen klickar du på **OK** återgå till fönstret Ny anslutning och klicka sedan på **spara**.

    ![Skärmdump av fönstret MongoChef Testa anslutning](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>Använd MongoChef för att skapa en databas, samling och dokument
Utför följande steg för att skapa en databas, samling och dokument med hjälp av MongoChef.

1. I **Connection Manager**, markera anslutningen och klicka på **Anslut**.

    ![Skärmbild av MongoChef Anslutningshanteraren](./media/mongodb-mongochef/ConnectToAccount.png)
2. Högerklicka på värden och välj **lägga till databas**.  Ange ett namn och klicka på **OK**.

    ![Skärmdump av alternativet MongoChef lägga till databas](./media/mongodb-mongochef/AddDatabase1.png)
3. Högerklicka på databasen och välj **lägga till samlingen**.  Ange ett samlingsnamn och klicka på **skapa**.

    ![Skärmdump av alternativet MongoChef lägga till samlingen](./media/mongodb-mongochef/AddCollection.png)
4. Klicka på den **samling** menyn, klicka på **Lägg till dokument**.

    ![Skärmbild som visar menyalternativet MongoChef Lägg till dokument](./media/mongodb-mongochef/AddDocument1.png)
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
6. Lägg till ett annat dokument nu med följande innehåll.

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
* Utforska Azure Cosmos DB: API för MongoDB [exempel](mongodb-samples.md).

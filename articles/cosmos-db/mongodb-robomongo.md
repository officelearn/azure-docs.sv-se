---
title: Använda Robomongo för Azure Cosmos DB
description: 'Lär dig hur du använder Robomongo med ett Azure Cosmos DB: API för MongoDB-kontot'
keywords: robomongo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: 78f0158c9a80a60717b81b4788531c7efd979111
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863813"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Använda Robomongo med ett Azure Cosmos DB: API för MongoDB-kontot
Att ansluta till ett Azure Cosmos DB: API för MongoDB-kontot med hjälp av Robomongo, måste du:

* Ladda ned och installera [Robomongo](https://robomongo.org/)
* Har Azure Cosmos DB: API för MongoDB-kontot [anslutningssträngen](connect-mongodb-account.md) information

## <a name="connect-using-robomongo"></a>Ansluta med Robomongo
Att lägga till Azure Cosmos DB: API för MongoDB-kontot till Robomongo MongoDB-klientanslutningar, utför följande steg.

1. Hämta Azure Cosmos DB: API för anslutningsinformationen för MongoDB-konto med hjälp av anvisningarna [här](connect-mongodb-account.md).

    ![Skärmbild av bladet för anslutningssträng](./media/mongodb-robomongo/connectionstringblade.png)
2. Kör *Robomongo.exe*

3. Klicka på anslutningsknappen under **filen** att hantera dina anslutningar. Klicka sedan på **skapa** i den **MongoDB klientanslutningar** fönstret som öppnas den **anslutningsinställningar** fönster.

4. I den **anslutningsinställningar** fönstret, Välj ett namn. Leta sedan reda på **värden** och **Port** från anslutningsinformation i steg 1 och ange dem till **adress** och **Port**respektive.

    ![Skärmbild som visar Robomongo hantera anslutningar](./media/mongodb-robomongo/manageconnections.png)
5. På den **autentisering** fliken **utför autentisering**. Ange sedan databasen (standardvärdet är *Admin*), **användarnamn** och **lösenord**.
Båda **användarnamn** och **lösenord** finns i informationen i steg 1.

    ![Skärmbild som visar fliken Robomongo autentisering](./media/mongodb-robomongo/authentication.png)
6. På den **SSL** fliken Kontrollera **Använd SSL-protokollet**, ändra den **autentiseringsmetod** till **självsignerat certifikat**.

    ![Skärmbild som visar fliken Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Klicka slutligen på **Test** att kontrollera att du kan sedan ansluta **spara**.

## <a name="next-steps"></a>Nästa steg
* Utforska Azure Cosmos DB: API för MongoDB [exempel](mongodb-samples.md).

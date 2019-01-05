---
title: Använda Robo 3T för att ansluta till Azure Cosmos DB
description: Lär dig hur du ansluter till Azure Cosmos DB med Robo 3T och Azure Cosmos DB API för MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5696c376ad64df01d7f9d43ff59c87402c334c52
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034819"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Använda Robo 3T med Azure Cosmos DB: s API för MongoDB

För att ansluta till Cosmos-konto med Robo 3T, måste du:

* Ladda ned och installera [Robo 3T](https://robomongo.org/)
* Har ditt Cosmos DB [anslutningssträngen](connect-mongodb-account.md) information

## <a name="connect-using-robo-3t"></a>Ansluta med hjälp av Robo 3T
Lägg till ditt Cosmos till Anslutningshanteraren för Robo 3T, utför du följande steg:

1. Hämta anslutningsinformationen för ditt Cosmos-konto som har konfigurerats med Azure Cosmos DB API MongoDB med hjälp av anvisningarna [här](connect-mongodb-account.md).

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

- Lär dig hur du [använda Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB-API för MongoDB.
- Utforska MongoDB [exempel](mongodb-samples.md) med Azure Cosmos DB-API för MongoDB.

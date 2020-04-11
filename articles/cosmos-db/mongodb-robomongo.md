---
title: Använd Robo 3T för att ansluta till Azure Cosmos DB
description: Lär dig hur du ansluter till Azure Cosmos DB med Robo 3T och Azure Cosmos DB:s API för MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 627849acb902a721c80b98adbd16103b4729591b
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114195"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Använda Robo 3T med Azure Cosmos DB-API:et för MongoDB

Om du vill ansluta till Cosmos-kontot med Robo 3T måste du:

* Ladda ner och installera [Robo 3T](https://robomongo.org/)
* Har information om cosmos [DB-anslutningssträng](connect-mongodb-account.md)

> [!NOTE]
> För närvarande stöds Robo 3T v1.2 och lägre versioner med Cosmos DB:s API för MongoDB.

## <a name="connect-using-robo-3t"></a>Ansluta med Robo 3T

Så här lägger du till ditt Cosmos-konto i Robo 3T-anslutningshanteraren:

1. Hämta anslutningsinformationen för ditt Cosmos-konto som konfigurerats med Azure Cosmos DB:s API MongoDB med hjälp av instruktionerna [här](connect-mongodb-account.md).

    ![Skärmbild av anslutningssträngbladet](./media/mongodb-robomongo/connectionstringblade.png)
2. Kör *Robomongo-programmet.*

3. Klicka på anslutningsknappen under **Arkiv** för att hantera dina anslutningar. Klicka sedan på **Skapa** i fönstret **MongoDB-anslutningar,** som öppnar fönstret **Anslutningsinställningar.**

4. Välj ett namn i fönstret **Anslutningsinställningar.** Leta sedan reda på **värden** och **porten** från din anslutningsinformation i steg 1 och ange dem i **Adress** respektive **Port.**

    ![Skärmbild av Robomongo Hantera anslutningar](./media/mongodb-robomongo/manageconnections.png)
5. Klicka på Utför **autentisering**på fliken **Autentisering** . Ange sedan databasen (standard är *Admin),* **Användarnamn** och **Lösenord**.
Både **användarnamn** och **lösenord** finns i din anslutningsinformation i steg 1.

    ![Skärmbild av fliken Autentisering av Robomongo](./media/mongodb-robomongo/authentication.png)
6. Kontrollera **Använd SSL-protokollet**på fliken **SSL** och ändra sedan **autentiseringsmetoden** till **självsignerat certifikat**.

    ![Skärmbild av fliken Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Klicka slutligen på **Testa** för att kontrollera att du kan ansluta och sedan **spara**.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

---
title: Ansluta till Azure Cosmos DB med kompass
description: Lär dig hur du använder MongoDB kompass för att lagra och hantera data i Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 06/24/2019
author: roaror
ms.author: roaror
ms.openlocfilehash: 102d3fdc2e36f812e9a86286383a06f9930a1947
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67665916"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Använd MongoDB kompass för att ansluta till Azure Cosmos DB API för MongoDB 

Den här självstudien visar hur du använder [MongoDB kompass](https://www.mongodb.com/products/compass) när lagring och/eller hantera data i Cosmos DB. Vi använder Azure Cosmos DB: s API för MongoDB för den här genomgången. För dem som inte vet är kompass ett grafiskt användargränssnitt för MongoDB. Det används ofta för att visualisera dina data genom att köra ad hoc-frågor, tillsammans med hantering av dina data. 

Cosmos DB är Microsofts globalt distribuerade databas för flera modeller tjänst. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Cosmos DB.


## <a name="pre-requisites"></a>Förutsättningar 
För att ansluta till ditt Cosmos DB-konto med Robo 3T, måste du:

* Ladda ned och installera [kompass](https://www.mongodb.com/download-center/compass?jmp=hero)
* Har ditt Cosmos DB [anslutningssträngen](connect-mongodb-account.md) information

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Anslut till Cosmos DB: s API för MongoDB 
För att ansluta ditt Cosmos DB-konto till kompass, kan du följa den stegen nedan:

1. Hämta anslutningsinformationen för ditt Cosmos-konto som har konfigurerats med Azure Cosmos DB API MongoDB med hjälp av anvisningarna [här](connect-mongodb-account.md).

    ![Skärmbild av bladet för anslutningssträng](./media/mongodb-compass/mongodb-compass-connection.png)

2. Klicka på knappen som säger **kopiera till Urklipp** bredvid din **primär/sekundär anslutningssträng** i Cosmos DB. Den här knappen kommer att kopiera hela anslutningssträngen till Urklipp. 

    ![Skärmbild av den knappen kopieringen till Urklipp](./media/mongodb-compass/mongodb-connection-copy.png)

3. Öppna kompass på desktop/datorn och klicka på **Connect** och sedan **ansluta till...** . 

4. Kompass identifierar automatiskt en anslutning sträng i Urklipp och så uppmanas du för att fråga om du vill använda den för att ansluta. Klicka på **Ja** som visas i skärmbilden nedan.

    ![Skärmbild av kompass-prompten för att ansluta](./media/mongodb-compass/mongodb-compass-detect.png)

5. När du klickar på **Ja** i steget ovan din information från anslutningssträngen automatiskt fylls i. Ta bort värdet fylls automatiskt i den **ange namn** fält så som är tomt. 

    ![Skärmbild av kompass-prompten för att ansluta](./media/mongodb-compass/mongodb-compass-replica.png)

6. Klicka på **Connect** längst ned på sidan. Ska nu visas i MongoDB kompass ditt Cosmos DB-konto och databaser.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.
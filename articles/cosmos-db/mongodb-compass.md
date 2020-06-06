---
title: Ansluta till Azure Cosmos DB med hjälp av kompass
description: Lär dig hur du använder MongoDB kompass för att lagra och hantera data i Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: 74822211ae834bc4705552bd3291299cf14b54d0
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465736"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Använd MongoDB kompass för att ansluta till Azure Cosmos DB s API för MongoDB

Den här självstudien visar hur du använder [MongoDB kompass](https://www.mongodb.com/products/compass) när du lagrar och/eller hanterar data i Cosmos dB. Vi använder Azure Cosmos DBs API för MongoDB för den här genom gången. För dem som inte är välkända är kompass ett GUI för MongoDB. Den används ofta för att visualisera dina data, köra Ad hoc-frågor, tillsammans med hantera dina data.

Cosmos DB är Microsofts globalt distribuerade databas tjänst för flera modeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Cosmos DB.

## <a name="pre-requisites"></a>Förutsättningar

För att ansluta till ditt Cosmos DB-konto med MongoDB kompass måste du:

* Hämta och installera [kompass](https://www.mongodb.com/download-center/compass?jmp=hero)
* Ha information om Cosmos DB- [anslutningssträng](connect-mongodb-account.md)

> [!NOTE]
> För närvarande stöds Azure Cosmos DBs-API för MongoDB Server version 3,2 med den senaste versionen av MongoDB kompass. MongoDB kompass stöds inte ännu med 3,6-Server versionen. 

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Ansluta till Cosmos DB s API för MongoDB

Om du vill ansluta ditt Cosmos DB konto till kompass kan du följa stegen nedan:

1. Hämta anslutnings informationen för ditt Cosmos-konto som kon figurer ATS med Azure Cosmos DB API-MongoDB med hjälp av instruktionerna [här](connect-mongodb-account.md).

    ![Skärm bild av bladet anslutnings sträng](./media/mongodb-compass/mongodb-compass-connection.png)

2. Klicka på knappen med texten **Kopiera till Urklipp** bredvid din **primära/sekundära anslutnings sträng** i Cosmos dB. Om du klickar på den här knappen kopieras hela anslutnings strängen till Urklipp.

    ![Skärm bild av knappen Kopiera till Urklipp](./media/mongodb-compass/mongodb-connection-copy.png)

3. Öppna kompassen på Skriv bordet/datorn och klicka på **Anslut** och **Anslut sedan till.**...

4. Kompass identifierar automatiskt en anslutnings sträng i Urklipp och frågar om du vill använda den för att ansluta. Klicka på **Ja** så som visas på skärm bilden nedan.

    ![Skärm bild av kompass-prompten för att ansluta](./media/mongodb-compass/mongodb-compass-detect.png)

5. När du klickar på **Ja** i ovanstående steg fylls informationen från anslutnings strängen i automatiskt. Ta bort värdet som fylls i automatiskt i fältet **namn på replik uppsättning** för att se till att det lämnas tomt.

    ![Skärm bild av kompass-prompten för att ansluta](./media/mongodb-compass/mongodb-compass-replica.png)

6. Klicka på **Anslut** längst ned på sidan. Ditt Cosmos DB konto och databaser bör nu vara synligt i MongoDB kompass.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.

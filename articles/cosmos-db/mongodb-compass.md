---
title: Ansluta till Azure Cosmos DB med kompass
description: Lär dig hur du använder MongoDB Compass för att lagra och hantera data i Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: 5a3ec79b27231f781b5e4104922993de38c7fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063647"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Använd MongoDB Compass för att ansluta till Azure Cosmos DB:s API för MongoDB

Den här självstudien visar hur du använder [MongoDB Compass](https://www.mongodb.com/products/compass) när du lagrar och/eller hanterar data i Cosmos DB. Vi använder Azure Cosmos DB:s API för MongoDB för den här genomsökningen. För er som inte är obekanta är Compass ett GUI för MongoDB. Det används ofta för att visualisera dina data, köra ad hoc-frågor, tillsammans med hantering av dina data.

Cosmos DB är Microsofts globalt distribuerade databastjänst för flera modeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Cosmos DB.

## <a name="pre-requisites"></a>Förutsättningar

Om du vill ansluta till ditt Cosmos DB-konto med Robo 3T måste du:

* Ladda ner och installera [Compass](https://www.mongodb.com/download-center/compass?jmp=hero)
* Har information om cosmos [DB-anslutningssträng](connect-mongodb-account.md)

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Anslut till Cosmos DB:s API för MongoDB

Om du vill ansluta ditt Cosmos DB-konto till Compass kan du följa stegen nedan:

1. Hämta anslutningsinformationen för ditt Cosmos-konto som konfigurerats med Azure Cosmos DB:s API MongoDB med hjälp av instruktionerna [här](connect-mongodb-account.md).

    ![Skärmbild av anslutningssträngbladet](./media/mongodb-compass/mongodb-compass-connection.png)

2. Klicka på knappen som säger **Kopiera till Urklipp** bredvid din **primära / sekundära anslutningssträng** i Cosmos DB. Om du klickar på den här knappen kopieras hela anslutningssträngen till Urklipp.

    ![Skärmbild av knappen Kopiera till Urklipp](./media/mongodb-compass/mongodb-connection-copy.png)

3. Öppna Kompass på skrivbordet/maskinen och klicka på **Anslut** och anslut sedan **till...**.

4. Kompassen identifierar automatiskt en anslutningssträng i Urklipp och frågar om du vill använda den för att ansluta. Klicka på **Ja** som visas i skärmdumpen nedan.

    ![Skärmbild av kompassprompten för att ansluta](./media/mongodb-compass/mongodb-compass-detect.png)

5. När du klickar på **Ja** i ovanstående steg fylls dina uppgifter från anslutningssträngen i automatiskt. Ta bort värdet i automatiskt i fältet **Replikuppsättningsnamn** för att säkerställa att det lämnas tomt.

    ![Skärmbild av kompassprompten för att ansluta](./media/mongodb-compass/mongodb-compass-replica.png)

6. Klicka på **Anslut** längst ner på sidan. Ditt Cosmos DB-konto och databaser bör nu vara synliga i MongoDB Compass.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.
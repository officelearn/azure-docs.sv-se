---
title: Uppgradera Mongo-versionen av din Azure Cosmos DBs API för MongoDB-konto
description: Så här uppgraderar du MongoDB Wire-Protocol-versionen för din befintliga Azure Cosmos DBs API för MongoDB-konton sömlöst
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: chrande
ms.openlocfilehash: 9ce444e41d19ece984071d0f62e705a09d5f23c9
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356475"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>Uppgradera MongoDB Wire Protocol-versionen av din Azure Cosmos DBs API för MongoDB-konto
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

I den här artikeln beskrivs hur du uppgraderar överförings protokoll versionen av din Azure Cosmos DBs API för MongoDB-konto. När du har uppgraderat Wire Protocol-versionen kan du använda de senaste funktionerna i Azure Cosmos DBs API för MongoDB. Uppgraderings processen avbryter inte tillgängligheten för ditt konto och den förbrukar inte RU/s eller minskar kapaciteten för databasen när som helst. Inga befintliga data eller index påverkas av den här processen.

>[!Note]
> För närvarande kan endast kvalificerade konton som använder Server version 3,2 uppgraderas till version 3,6. Om ditt konto inte visar uppgraderings alternativet ska du skicka in [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-from-version-32-to-36"></a>Uppgradera från version 3,2 till 3,6

### <a name="benefits-of-upgrading-to-version-36"></a>Fördelar med att uppgradera till version 3.6

Följande är de nya funktionerna som ingår i version 3,6:
- Bättre prestanda och stabilitet
- Stöd för nya databaskommandon
- Stöd för sammansättningspipeline som standard samt nya aggregeringsfaser
- Stöd för ändrings strömmar
- Stöd för sammansatta index
- Stöd för att korsa partitioner för följande åtgärder: uppdatera, ta bort, räkna och sortera
- Bättre prestanda för följande mängd åtgärder: $count, $skip, $limit och $group
- Indexering med jokertecken stöds nu

### <a name="changes-from-version-32"></a>Ändringar från version 3,2

- **RequestRateIsLarge-fel har tagits bort**. Begär Anden från klient programmet kommer inte att returnera 16500-fel längre. I stället kommer förfrågningar att återupptas tills de har slutfört eller uppfyllt tids gränsen.
- Tids gränsen för begäran har angetts till 60 sekunder.
- MongoDB-samlingar som skapats i den nya versionen av överförings protokollet har `_id` som standard inte egenskapen indexerad.

### <a name="action-required"></a>Åtgärd som krävs

För uppgraderingen till version 3,6 kommer databas kontots slut punkts suffix att uppdateras till följande format:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Du måste ersätta den befintliga slut punkten i dina program och driv rutiner som ansluter till det här databas kontot. **Endast anslutningar som använder den nya slut punkten får åtkomst till funktionerna i MongoDB version 3,6**. Den tidigare slut punkten ska ha suffixet `.documents.azure.com` .

>[!Note]
> Den här slut punkten kan ha små skillnader om ditt konto har skapats i en suverän, myndighets eller begränsad Azure-moln.

### <a name="how-to-upgrade"></a>Så här uppgraderar du

1. Först går du till Azure Portal och navigerar till bladet Azure Cosmos DB-API för MongoDB-konto. Kontrol lera att Server versionen är `3.2` . 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Översikt över Azure Portal med MongoDB-konto" border="false":::

2. Välj bladet från alternativen till vänster `Features` . Detta visar de konto nivå funktioner som är tillgängliga för ditt databas konto.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure Portal med MongoDB konto översikt med bladet funktioner markerat" border="false":::

3. Klicka på `Upgrade to Mongo server version 3.6` raden. Om du inte ser det här alternativet kanske kontot inte är giltigt för den här uppgraderingen. Ange [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om så är fallet.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Funktions blad med alternativ." border="false":::

4. Granska informationen som visas om den här uppgraderingen. Observera att uppgraderingen endast slutförs förrän dina program använder den uppdaterade slut punkten, som du ser i det här avsnittet. Klicka på `Enable` så snart du är redo att starta processen.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Utökad uppgraderings vägledning." border="false":::

5. När processen har startats visas `Features` uppgraderingens status i menyn. Statusen skickas från till till `Pending` `In Progress` `Upgraded` . Den här processen påverkar inte befintliga funktioner eller åtgärder för databas kontot.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Uppgraderings status efter initiering." border="false":::

6. När uppgraderingen har slutförts visas statusen som `Upgraded` . Klicka på den för att lära dig mer om nästa steg och åtgärder som du behöver utföra för att slutföra processen. [Kontakta supporten](https://azure.microsoft.com/en-us/support/create-ticket/) om du har problem med att bearbeta din begäran.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Status för uppgraderat konto." border="false":::

7. **Om du vill börja använda den uppgraderade versionen av ditt databas konto** går du tillbaka till `Overview` bladet och kopierar den nya anslutnings strängen som ska användas i ditt program. Programmen kommer att börja använda den uppgraderade versionen så fort de ansluter till den nya slut punkten. Befintliga anslutningar avbryts inte och kan uppdateras när du vill. För att säkerställa en konsekvent upplevelse måste alla program använda den nya slut punkten.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Bladet ny översikt." border="false":::

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om funktionerna som stöds och som inte stöds [i MongoDB version 3,6](mongodb-feature-support-36.md).
- Mer information finns i [Mongo 3,6 versions funktioner](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)

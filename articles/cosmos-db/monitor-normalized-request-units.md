---
title: Övervaka normaliserade RU/s för en Azure Cosmos-behållare eller ett konto
description: Lär dig hur du övervakar den normaliserade enheten för begäran om användning av en åtgärd i Azure Cosmos DB. Ägare av ett Azure Cosmos DB konto kan förstå vilka åtgärder som förbrukar fler enheter för programbegäran.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 05/10/2020
ms.openlocfilehash: 23001bdaab0732dbeb088ebadefa90a27e622b19
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118847"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Övervaka normaliserade RU/s för en Azure Cosmos-behållare eller ett konto

Azure Monitor för Azure Cosmos DB ger en mått vy för att övervaka ditt konto och skapa instrument paneler. De Azure Cosmos DB måtten samlas in som standard, men den här funktionen kräver inte att du aktiverar eller konfigurerar något explicit.

Det **normaliserade ru-förbruknings** måttet används för att se hur bra mätta är replikerna som stämmer inte med till enhetens användning av begär ande enheter över partitionernas nyckel intervall. Azure Cosmos DB distribuerar data flödet jämnt över alla fysiska partitioner. Det här måttet ger en vy av den högsta data flödes användningen i en replik uppsättning. Om du använder det här måttet, bör du öka data flödet för att uppfylla arbets Belastningens behov, om du ser hög procent andel av enhets användningen för enheter.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Vad som ska förväntas och göra när normaliserade RU/s är högre

När den normaliserade RU/s-förbrukningen når 100%, tar klienten emot hastighets begränsnings fel. Klienten bör respektera vänte tiden och försöka igen. Om det finns en kort insamling som når 100% användning innebär det att data flödet på repliken nått sin maximala prestanda gräns. En enskild åtgärd som till exempel en lagrad procedur som använder alla RU/s på en replik kommer att leda till en kort insamling i normaliserad RU/s-förbrukning. I sådana fall kommer det inte att finnas några omedelbara hastighets begränsnings fel om begär ande frekvensen är låg. Det beror på att Azure Cosmos DB tillåter begär Anden att debitera mer än etablerade RU/s för den särskilda begäran och andra begär Anden inom den tids perioden är begränsad.

Azure Monitor måtten hjälper dig att hitta åtgärderna per status kod med hjälp av måttet **Totalt antal begär Anden** . Senare kan du filtrera efter dessa förfrågningar med status koden 429 och dela upp dem efter **Åtgärds typ**.

Det rekommenderade sättet är att hämta den här informationen via diagnostikloggar för att hitta de begär Anden som är begränsade.

Om det finns kontinuerlig hög belastning på 100% normaliserade RU/s-förbrukning eller nära 100% rekommenderar vi att du ökar data flödet. Du kan ta reda på vilka åtgärder som är stora och deras högsta användning genom att använda Azure Monitor-mått och Azure Monitor-loggar.

Det **normaliserade ru-förbruknings** måttet används också för att se vilka nyckel intervall som är mer varma i användnings villkoren. därmed får du skevingen av data flödet mot ett nyckel intervall. Senare kan du följa upp för att se **PartitionKeyRUConsumption** -loggen i Azure Monitor loggar för att få information om vilka logiska partitionsnyckel som är frekventa när det gäller användningen.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Visa den normaliserade förbruknings måttet för begär ande enheter

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **övervaka** i det vänstra navigerings fältet och välj **mått**.

   ![Mått fönstret i Azure Monitor](./media/monitor-normalized-request-units/monitor-metrics-blade.png)

3. I fönstret **mått** > väljer du **en resurs** > väljer den nödvändiga **prenumerationen**och **resurs gruppen**. För **resurs typen**väljer du **Azure Cosmos DB konton**, väljer något av dina befintliga Azure Cosmos-konton och väljer **Använd**.

   ![Välj ett Azure Cosmos-konto om du vill visa mått](./media/monitor-normalized-request-units/select-cosmos-db-account.png)

4. Sedan kan du välja ett mått i listan över tillgängliga mått. Du kan välja mått som är speciella för att begära enheter, lagring, svars tid, tillgänglighet, Cassandra och andra. Mer information om alla tillgängliga mått i den här listan finns i artikeln [mått per kategori](monitor-cosmos-db-reference.md) . I det här exemplet väljer vi **normaliserat ru-förbruknings** mått och **Max** som agg regerings värde.

   Förutom dessa uppgifter kan du också välja **tidsintervallet** och **tids kornig het** för måtten. Som Max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på ditt filter.

   ![Välj ett mått från Azure Portal](./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png)

### <a name="filters-for-normalized-request-unit-consumption"></a>Filter för normaliserad förbrukning av begär ande enheter

Du kan också filtrera mått och diagrammet som visas av en speciell **samlings**-, **databasename**-, **PartitionKeyRangeID**-och **region**. Om du vill filtrera måtten väljer du **Lägg till filter** och väljer den obligatoriska egenskapen, till exempel **samlings** namn och motsvarande värde som du är intresse rad av. Diagrammet visar sedan de normaliserade RU-konsumtions enheter som förbrukas för behållaren för den valda perioden.  

Du kan gruppera mått med hjälp av alternativet **Använd delning** .  

Den normaliserade förbruknings måttet för begäran för varje behållare visas enligt följande bild:

![Använd filter för normaliserat förbruknings mått för begär ande enhet](./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png)

## <a name="next-steps"></a>Nästa steg

* Övervaka Azure Cosmos DB data med hjälp av [diagnostiska inställningar](cosmosdb-monitor-resource-logs.md) i Azure.
* [Granska Azure Cosmos DB kontroll Plans åtgärder](audit-control-plane-logs.md)
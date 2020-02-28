---
title: Övervaka Azure Cosmos DB med Azure Monitor för Cosmos DB (förhands granskning) | Microsoft Docs
description: I den här artikeln beskrivs Azure Monitor för Cosmos DB-funktionen som ger Cosmos DB ägare en snabb förståelse för prestanda-och användnings problem med sina CosmosDB-konton.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: d28db9907094a651835078f4459a985b9d15e589
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657392"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Utforska Azure Monitor för Azure Cosmos DB (förhands granskning)

Azure Monitor för Azure Cosmos DB (för hands version) ger en överblick över den övergripande prestandan, fel, kapacitet och drifts hälsa för alla dina Azure Cosmos DB resurser i en enhetlig interaktiv upplevelse. Den här artikeln hjälper dig att förstå fördelarna med den nya övervaknings miljön och hur du kan ändra och anpassa upplevelsen så att den passar organisationens unika behov.   

## <a name="introduction"></a>Introduktion

Innan du simhopp i upplevelsen bör du förstå hur den visar och visualiserar information. 

Den ger:

* I **skalnings perspektiv** för dina Azure Cosmos DB resurser över alla dina prenumerationer på en enda plats, med möjlighet att selektivt begränsa till de prenumerationer och resurser som du är intresse rad av att utvärdera.

* **Nedbrytnings analys** av en viss Azure CosmosDB-resurs för att hjälpa till att diagnostisera problem eller utföra detaljerad analys efter kategori användning, fel, kapacitet och åtgärder. Om du väljer något av dessa alternativ visas en djupgående vy över relevanta Azure Cosmos DB mått.  

* **Anpassningsbar** – den här upplevelsen bygger på Azure Monitor mallar för arbets böcker som gör att du kan ändra vilka mått som visas, ändra eller ange tröskelvärden som överensstämmer med dina gränser och sedan spara dem i en anpassad arbets bok. Diagram i arbets böckerna kan sedan fästas på Azure-instrumentpaneler.  

Den här funktionen kräver inte att du aktiverar eller konfigurerar något, dessa Azure Cosmos DB mått samlas in som standard.

>[!NOTE]
>Det kostar inget att få åtkomst till den här funktionen och du debiteras bara för Azure Monitor viktiga funktioner som du konfigurerar eller aktiverar, enligt beskrivningen på sidan [pris information för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Visa mått på åtgärds nivå för Azure Cosmos DB

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **övervaka** i det vänstra navigerings fältet och välj **mått**.

   ![Mått fönstret i Azure Monitor](./media/cosmosdb-insights-overview/monitor-metrics-blade.png)

1. I fönstret **mått** > väljer du **en resurs** > väljer den nödvändiga **prenumerationen**och **resurs gruppen**. För **resurs typen**väljer du **Azure Cosmos DB konton**, väljer något av dina befintliga Azure Cosmos-konton och väljer **Använd**.

   ![Välj ett Cosmos DB konto om du vill visa mått](./media/cosmosdb-insights-overview/select-cosmosdb-account.png)

1. Sedan kan du välja ett mått i listan över tillgängliga mått. Du kan välja mått som är speciella för att begära enheter, lagring, svars tid, tillgänglighet, Cassandra och andra. Mer information om alla tillgängliga mått i den här listan finns i artikeln [mått per kategori](../../cosmos-db/monitor-cosmos-db-reference.md) . I det här exemplet ska vi välja **enheter för programbegäran** och **AVG** som agg regerings värde.

   Förutom dessa uppgifter kan du också välja **tidsintervallet** och **tids kornig het** för måtten. Som Max kan du visa mått för de senaste 30 dagarna.  När du har tillämpat filtret visas ett diagram baserat på ditt filter. Du kan se det genomsnittliga antalet förbrukade enheter för programbegäran per minut för den valda perioden.  

   ![Välj ett mått från Azure Portal](./media/cosmosdb-insights-overview/metric-types.png)

### <a name="add-filters-to-metrics"></a>Lägg till filter till mått

Du kan också filtrera mått och diagrammet som visas av en speciell **samlings**-, **databasename**-, **OperationType**-, **region**-och **StatusCode**-värde. Om du vill filtrera måtten väljer du **Lägg till filter** och väljer önskad egenskap som **OperationType** och väljer ett värde, till exempel **fråga**. Diagrammet visar sedan de enheter för programbegäran som för bruk ATS för den valda perioden. De åtgärder som utförs via den lagrade proceduren loggas inte, så de är inte tillgängliga i OperationType-måttet.

![Lägg till ett filter för att välja mått kornig het](./media/cosmosdb-insights-overview/add-metrics-filter.png)

Du kan gruppera mått med hjälp av alternativet **Använd delning** . Du kan till exempel gruppera enheter för programbegäran per åtgärds typ och Visa grafen för alla åtgärder samtidigt som visas i följande bild:

![Lägg till Använd delnings filter](./media/cosmosdb-insights-overview/apply-metrics-splitting.png)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Visa användnings-och prestanda mått för Azure Cosmos DB

Utför följande steg för att visa användning och prestanda för dina lagrings konton i alla dina prenumerationer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **övervakare** och välj **övervaka**.

    ![Sökrutan med ordet "Monitor" och en listruta som säger tjänster "Monitor" med en hastighets format bild](./media/cosmosdb-insights-overview/search-monitor.png)

3. Välj **Cosmos dB (för hands version)** .

    ![Skärm bild av Cosmos DB översikt över arbets bok](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Översikt

I **Översikt**visar tabellen interaktiva Azure Cosmos DB mått. Du kan filtrera resultaten baserat på de alternativ du väljer i följande List rutor:

* **Prenumerationer – endast** prenumerationer som har en Azure Cosmos DB resurs visas.  

* **Cosmos DB** – du kan välja alla, en delmängd eller en enskild Azure Cosmos DB resurs.

* **Tidsintervall** – som standard visar de senaste 4 timmarna med information baserat på motsvarande val som gjorts.

Panelen räknare under List rutor slår upp det totala antalet Azure Cosmos DB resurser finns i de valda prenumerationerna. Det finns en villkorsstyrd färg kodning eller termiska kartor för kolumner i arbets boken som rapporterar transaktions mått. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena. 

Om du väljer en nedrullningsbar pil bredvid någon av Azure Cosmos DB resurserna visas en uppdelning av prestanda måtten på den enskilda databas behållar nivån:

![Expanderad listruta för att visa enskilda databas behållare och associerad prestanda analys](./media/cosmosdb-insights-overview/container-view.png)

Om du markerar Azure Cosmos DB resurs namn som marker ATS i blått tas du till standard **översikten** för det associerade Azure Cosmos DB-kontot. 

### <a name="failures"></a>Fel

Välj **felen** överst på sidan och **felen** i arbets bokens mall öppnas. Det visar totalt antal begär Anden med distributionen av svar som utgör dessa förfrågningar:

![Skärm bild av problem med nedbrytning efter HTTP-begäran](./media/cosmosdb-insights-overview/failures.png)

| Kod      |  Beskrivning       | 
|-----------|:--------------------|
| `200 OK`  | En av följande REST-åtgärder lyckades: </br>– Hämta på en resurs. </br> – PUBLICERA på en resurs. </br> – PUBLICERA på en resurs. </br> – PUBLICERA på en lagrad procedur resurs för att köra den lagrade proceduren.|
| `201 Created` | En POST-åtgärd för att skapa en resurs har slutförts. |
| `404 Not Found` | Åtgärden försöker vidta en resurs som inte längre finns. Till exempel kanske resursen redan har tagits bort. |

En fullständig lista över status koder finns i artikeln om [status kod för Azure Cosmos DB http](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Kapacitet

Välj **kapacitet** överst på sidan och **kapacitets** delen i arbets boks mal len öppnas. Det visar hur många dokument du har, din dokument tillväxt över tid, data användning och den totala mängden tillgängligt lagrings utrymme som du har kvar.  Detta kan användas för att identifiera eventuella problem med lagring och data användning.

![Arbets bok för kapacitet](./media/cosmosdb-insights-overview/capacity.png) 

Precis som med översikts arbets boken kan du välja den nedrullningsbara List rutan bredvid en Azure Cosmos DB-resurs i kolumnen **prenumeration** för att visa en uppdelning av de enskilda behållare som utgör databasen.

### <a name="operations"></a>Åtgärder 

Välj **åtgärder** överst på sidan och **drift** delen av arbets bokens mall öppnas. Det ger dig möjlighet att se dina begär Anden uppdelade efter den typ av förfrågningar som görs. 

I exemplet nedan ser du att `eastus-billingint` i huvudsak tar emot Läs begär Anden, men med ett litet antal upsert-och Create-begäranden. `westeurope-billingint` är skrivskyddad från ett begär ande perspektiv, minst de senaste fyra timmarna som arbets boken för närvarande är begränsad till via dess tids intervalls parameter.

![Arbets bok](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Fäst, exportera och expandera

Du kan fästa något av mått avsnitten på en Azure- [instrumentpanel](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) genom att välja kartnål-ikonen längst upp till höger i avsnittet.

![Mått avsnitt fäst vid instrument panelen exempel](./media/cosmosdb-insights-overview/pin.png)

Om du vill exportera data till Excel-formatet väljer du nedåtpilen till vänster om ikonen kartnål.

![Ikonen Exportera arbets bok](./media/cosmosdb-insights-overview/export.png)

Om du vill visa eller dölja alla nedrullningsbara List rutor i arbets boken väljer du ikonen Expandera till vänster om export ikonen:

![Ikonen Visa arbets bok](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Anpassa Azure Monitor för Azure Cosmos DB (förhands granskning)

Eftersom den här upplevelsen bygger på Azure Monitor arbetsboksmall, kan du **anpassa** > **Redigera** och **Spara** en kopia av den ändrade versionen i en anpassad arbets bok. 

![Anpassa stapel](./media/cosmosdb-insights-overview/customize.png)

Arbets böcker sparas i en resurs grupp, antingen i avsnittet **Mina rapporter** som är privat för dig eller i avsnittet **delade rapporter** som är tillgängliga för alla som har åtkomst till resurs gruppen. När du har sparat den anpassade arbets boken måste du gå till arbets boks galleriet för att starta den.

![Starta arbets boks galleriet från kommando fältet](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Nästa steg

* Konfigurera [mått aviseringar](../platform/alerts-metric.md) och [meddelanden om tjänst hälsa](../../service-health/alerts-activity-log-service-notifications.md) för att ställa in automatiserad avisering som hjälper till att identifiera problem.

* Lär dig mer om arbets böckerna i scenarier är utformade för att stödja, hur du skapar nya och anpassar befintliga rapporter och mer genom att granska [skapa interaktiva rapporter med Azure Monitor arbets böcker](../app/usage-workbooks.md).

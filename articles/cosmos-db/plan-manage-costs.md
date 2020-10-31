---
title: Planera och hantera kostnader för Azure Cosmos DB
description: Lär dig att planera för och hantera kostnader för Azure Cosmos DB genom att använda kostnads analys i Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 3a1bb36910b09d24c3328c8fc8ae94e1e3321642
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080710"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planera och hantera kostnader för Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

I den här artikeln beskrivs hur du kan planera och hantera kostnader för Azure Cosmos DB:

- Uppskatta vad som är din kostnad innan du skapar några resurser
- Granska de uppskattade kostnaderna när du börjar använda dina resurser
- Använd funktionerna för kostnads hantering för att ställa in budgetar och övervaka kostnader
- Granska de prognostiserade kostnaderna och identifiera utgifts trender för att visa områden där du kanske vill handla

Ta reda på att kostnaderna för Azure Cosmos DB bara är en del av månads kostnaderna på din Azure-faktura. Om du använder andra Azure-tjänster debiteras du för alla Azure-tjänster och-resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part. Den här artikeln beskriver hur du planerar för och hanterar kostnader för Azure Cosmos DB. När du är bekant med att hantera kostnader för Azure Cosmos DB kan du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

## <a name="prerequisites"></a>Krav

Kostnadsanalys stöder en mängd olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="provisioned-throughput-or-serverless"></a>Allokerat data flöde eller Server lös

Azure Cosmos DB stöder två typer av kapacitets lägen: [etablerade data flöden](set-throughput.md) och [Server](serverless.md)lös. Hur du debiteras för Azure Cosmos DB användningen varierar mycket mellan dessa två lägen, så det är viktigt att välja det som fungerar bäst för din arbets belastning. Mer information och rekommendationer om hur du gör detta finns i artikeln om [hur du väljer mellan etablerade data flöde och](throughput-serverless.md) en artikel utan server.

## <a name="estimating-provisioned-throughput-costs-with-capacity-calculator"></a>Beräkna etablerade data flödes kostnader med kapacitets kalkylatorn

Om du planerar att använda Azure Cosmos DB i det etablerade data flödes läget använder du [Azure Cosmos DB kapacitets kalkylatorn](https://cosmos.azure.com/capacitycalculator/) för att beräkna kostnader innan du skapar resurserna i ett Azure Cosmos-konto. Kapacitets kalkylatorn används för att få en uppskattning av det nödvändiga data flödet och kostnaden för din arbets belastning. Det är viktigt att konfigurera dina Azure Cosmos-databaser och behållare med rätt mängd etablerade data flöde eller [enheter för programbegäran (ru/s)](request-units.md)för din arbets belastning för att optimera kostnaderna och prestandan. Du måste ange information som t. ex. API-typ, antal regioner, objekt storlek, Läs-/skriv förfrågningar per sekund, totalt antal data som lagras för att få en kostnads uppskattning. Mer information om kapacitets kalkylatorn finns i artikeln om [uppskattning](estimate-ru-with-capacity-planner.md) .

Följande skärm bild visar data flödet och kostnads uppskattningen med hjälp av kapacitets Kalkylatorn:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Kostnads uppskattning i Azure Cosmos DB kapacitets kalkylator":::

## <a name="estimating-serverless-costs"></a>Uppskatta kostnader utan Server

Om du planerar att använda Azure Cosmos DB i Server lös läge måste du uppskatta hur många enheter för [programbegäran](request-units.md) och GB lagrings utrymme som du kan använda varje månad. Du kan beräkna den nödvändiga mängden enheter för programbegäran genom att utvärdera antalet databas åtgärder som ska utfärdas under en månad och multiplicera deras belopp med motsvarande RU-kostnad. I följande tabell visas de uppskattade RU-avgifterna för vanliga databas åtgärder:

| Åtgärd | Beräknad kostnad | Kommentarer |
| --- | --- | --- |
| Skapa ett objekt | 5 ru: er | Genomsnittlig kostnad för ett 1 KB-objekt med färre än 5 egenskaper att indexera |
| Uppdatera ett objekt | 10 RU:er | Genomsnittlig kostnad för ett 1 KB-objekt med färre än 5 egenskaper att indexera |
| Läsa ett enskilt objekt med dess ID och partitionsnyckel (punkt-Läs) | 1 RU | Genomsnittlig kostnad för 1 KB-artikel |
| Ta bort ett objekt | 5 ru: er | |
| Köra en fråga | 10 RU:er | Genomsnittlig kostnad för en fråga som tar full nytta av [indexering](index-overview.md) och returnerar 100 resultat eller mindre |

> [!IMPORTANT] 
> Var uppmärksam på anteckningarna från tabellen ovan. För en mer exakt uppskattning av de faktiska kostnaderna för dina åtgärder kan du använda [Azure Cosmos-emulatorn](local-emulator.md) och [mäta den exakta ru-kostnaden för dina åtgärder](find-request-unit-charge.md). Även om Azure Cosmos-emulatorn inte stöder Server lös rapporteras en standard-avgift för databas åtgärder och kan användas för denna uppskattning.

När du har beräknat det totala antalet enheter för programbegäran och GB lagrings utrymme som du sannolikt kommer att förbruka under en månad, returnerar följande formel din kostnads uppskattning: **([antal enheter för programbegäran]/1 000 000 * $0,25) + ([GB lagrings utrymme] * $0,25)** .

> [!NOTE]
> Kostnaderna som visas i föregående exempel är endast i demonstrations syfte. På [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) finns den senaste pris informationen.

## <a name="review-estimated-costs-from-the-azure-portal"></a>Granska uppskattade kostnader från Azure Portal

När du börjar använda Azure Cosmos DB resurser från Azure Portal kan du se de uppskattade kostnaderna. Använd följande steg för att granska kostnads uppskattningen:

1. Logga in på Azure Portal och gå till ditt Azure Cosmos-konto.
1. Gå till **översikts** avsnittet.
1. Kontrol lera **kostnads** diagrammet längst ned. Det här diagrammet visar en uppskattning av din aktuella kostnad under en konfigurerbar tids period:
1. Skapa en ny behållare, till exempel en graf-behållare.
1. Mata in det data flöde som krävs för din arbets belastning, till exempel 400 RU/s. När du har matat in data flöde svärdet kan du se pris beräkningen så som visas på följande skärm bild:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Kostnads uppskattning i Azure Cosmos DB kapacitets kalkylator":::

## <a name="use-budgets-and-cost-alerts"></a>Använda budgetar och kostnadsaviseringar

Du kan skapa [budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md) för att hantera kostnader och skapa aviseringar som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. De kan dock ha begränsade funktioner för att hantera enskilda kostnader för Azure-tjänster, till exempel kostnaden för Azure Cosmos DB eftersom de är utformade för att spåra kostnader på en högre nivå.

Om din Azure-prenumeration har en utgifts gräns hindrar Azure dig från att behöva spendera på ditt kredit belopp. När du skapar och använder Azure-resurser används dina krediter. När du når din kredit gräns inaktive ras de resurser som du har distribuerat för resten av fakturerings perioden. Du kan inte ändra kredit gränsen, men du kan ta bort den. Mer information om utgifts gränser finns i [utgifts gränsen för Azure](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder resurser med Azure Cosmos DB debiteras du kostnader. Kostnader för resursanvändning varierar beroende på tidsintervaller (sekunder, minuter, timmar och dagar) eller genom att använda enhets användning. Så snart som användningen av Azure Cosmos DB startar kan kostnader uppstå och du kan se dem i fönstret [kostnads analys](../cost-management-billing/costs/quick-acm-cost-analysis.md) i Azure Portal.

När du använder kostnads analys kan du Visa Azure Cosmos DB kostnader i grafer och tabeller i olika tidsintervall. Några exempel är per dag, aktuell, föregående månad och år. Du kan också Visa kostnader för budgetar och prognostiserade kostnader. Om du växlar till längre vyer över tid kan du identifiera utgifts trender och se var överförbrukningen kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de överskreds. Så här visar du Azure Cosmos DB kostnader i kostnads analys:

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Öppna fönstret **Cost Management + fakturering** , Välj **kostnads hantering** på menyn och välj sedan **kostnads analys** . Du kan sedan ändra omfånget för en speciell prenumeration från List rutan **omfattning** .

1. Som standard visas kostnaden för alla tjänster i det första Ring diagrammet. Markera ytan i diagrammet med etiketten "Azure Cosmos DB".

1. Om du vill begränsa kostnaderna för en enskild tjänst, till exempel Azure Cosmos DB, väljer du **Lägg till filter** och väljer sedan **tjänst namn** . Välj sedan **Azure Cosmos DB** i listan. Här är ett exempel som visar kostnader för just Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Kostnads uppskattning i Azure Cosmos DB kapacitets kalkylator":::

I föregående exempel visas den aktuella kostnaden för Azure Cosmos DB för månadens månad. Diagrammen innehåller också Azure Cosmos DB kostnader per plats och per resurs grupp.

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns mer information om hur priser fungerar i Azure Cosmos DB:

* [Prismodell i Azure Cosmos DB](how-pricing-works.md)
* [Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnaden för frågor i Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Optimera lagrings kostnaden i Azure Cosmos DB](optimize-cost-storage.md)
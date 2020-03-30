---
title: Planera och hantera kostnader för Azure Cosmos DB
description: Lär dig hur du planerar och hanterar kostnader för Azure Cosmos DB med hjälp av kostnadsanalys i Azure-portalen.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152592"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planera och hantera kostnader för Azure Cosmos DB

I den här artikeln beskrivs hur du kan planera och hantera kostnader för Azure Cosmos DB. Först använder du Azure Cosmos DB-kapacitetskalkylatorn för att planera för kostnader innan du lägger till några resurser. Därefter, när du lägger till Azure-resurser, kan du granska de uppskattade kostnaderna. När du har börjat använda Azure Cosmos DB-resurser använder du kostnadshanteringsfunktionerna för att ange budgetar och övervaka kostnader. Du kan också granska de prognostiserade kostnaderna och identifiera utgiftstrender för att identifiera områden där du kanske vill agera.

Förstå att kostnaderna för Azure Cosmos DB bara är en del av de månatliga kostnaderna i din Azure-faktura. Om du använder andra Azure-tjänster debiteras du för alla Azure-tjänster och resurser som används i din Azure-prenumeration, inklusive tjänster från tredje part. I den här artikeln beskrivs hur du planerar för och hanterar kostnader för Azure Cosmos DB. När du är bekant med att hantera kostnader för Azure Cosmos DB kan du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

## <a name="prerequisites"></a>Krav

Kostnadsanalys stöder en mängd olika typer av Azure-konton. Om du vill visa en fullständig lista över typer av stöds kan du läsa [Förstå Cost Management-data](../cost-management-billing/costs/understand-cost-mgt-data.md). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Granska uppskattade kostnader med kapacitetskalkylator

Använd [Azure Cosmos DB-kapacitetskalkylatorn](https://cosmos.azure.com/capacitycalculator/) för att uppskatta kostnaderna innan du skapar resurserna i ett Azure Cosmos-konto. Kapacitetskalkylatorn används för att få en uppskattning av det dataflöde och kostnaden för din arbetsbelastning. Konfigurera dina Azure Cosmos-databaser och behållare med rätt mängd etablerat dataflöde, eller [Begär enheter (RU/s)](request-units.md), för din arbetsbelastning är viktigt för att optimera kostnaden och prestanda. Du måste mata in information som API-typ, antal regioner, artikelstorlek, läs-/skrivbegäranden per sekund, totala data som lagras för att få en kostnadsuppskattning. Mer information om kapacitetskalkylatorn finns i uppskattningsartikeln. [estimate](estimate-ru-with-capacity-planner.md)

Följande skärmbild visar dataflödet och kostnadsuppskattningen med hjälp av kapacitetskalkylatorn:

![Kostnadsuppskattning i Azure Cosmos DB-kapacitetskalkylator](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Granska uppskattade kostnader från Azure-portalen

När du skapar Azure Cosmos DB-resurser från Azure Portal kan du se de uppskattade kostnaderna. Gör så här för att granska kostnadsuppskattningen:

1. Logga in på Azure-portalen och navigera till ditt Azure Cosmos-konto.
1. Gå till **Data Explorer**.
1. Skapa en ny behållare, till exempel en diagrambehållare.
1. Mata in det dataflöde som krävs för din arbetsbelastning, till exempel 400 RU/s. När du har angett dataflödesvärdet kan du se prisuppskattningen enligt följande skärmbild:

   ![Kostnadsuppskattning i Azure-portalen](./media/plan-manage-costs/cost-estimate-portal.png)

Om din Azure-prenumeration har en utgiftsgräns hindrar Azure dig från att spendera över ditt kreditbelopp. När du skapar och använder Azure-resurser används dina krediter. När du når kreditgränsen inaktiveras de resurser som du har distribuerat för resten av faktureringsperioden. Du kan inte ändra kreditgränsen, men du kan ta bort den. Mer information om utgiftsgränser finns i [Azure-utgiftsgräns](../billing/billing-spending-limit.md).

## <a name="use-budgets-and-cost-alerts"></a>Använda budgetar och kostnadsaviseringar

Du kan skapa [budgetar](../cost-management/tutorial-acm-create-budgets.md) för att hantera kostnader och skapa aviseringar som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resursgrupper, så de är användbara som en del av en övergripande kostnadsövervakningsstrategi. De kan dock ha begränsad funktionalitet för att hantera enskilda Azure-tjänstkostnader som kostnaden för Azure Cosmos DB eftersom de är utformade för att spåra kostnader på en högre nivå.

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder resurser med Azure Cosmos DB ådrar du dig kostnader. Kostnaderna för resursanvändningsenhet varierar beroende på tidsintervall (sekunder, minuter, timmar och dagar) eller efter enhetsanvändning för begäran. Så fort användningen av Azure Cosmos DB startar uppstår kostnader och du kan se dem i [kostnadsanalysfönstret](../cost-management/quick-acm-cost-analysis.md) i Azure-portalen.

När du använder kostnadsanalys kan du visa Azure Cosmos DB-kostnaderna i diagram och tabeller för olika tidsintervall. Några exempel är per dag, aktuell, föregående månad och år. Du kan också visa kostnader mot budgetar och prognostiserade kostnader. Om du växlar till längre vyer över tid kan du identifiera utgiftstrender och se var överutnyttjande kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de har överskridits. Så här visar du Azure Cosmos DB-kostnader i kostnadsanalys:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Öppna fönstret **Kostnadshantering + Fakturering,** välj **Kostnadshantering** på menyn och välj sedan **Kostnadsanalys**. Du kan sedan ändra omfånget för en viss prenumeration från listrutan **Scope.**

1. Som standard visas kostnaden för alla tjänster i det första donutdiagrammet. Välj området i diagrammet "Azure Cosmos DB".

1. Om du vill begränsa kostnaderna för en enskild tjänst, till exempel Azure Cosmos DB, väljer **du Lägg till filter** och väljer sedan **Tjänstnamn**. Välj sedan **Azure Cosmos DB** i listan. Här är ett exempel som visar kostnader för bara Azure Cosmos DB:
 
   ![Övervaka kostnader med fönstret Kostnadsanalys](./media/plan-manage-costs/cost-analysis-pane.png)

I föregående exempel ser du den aktuella kostnaden för Azure Cosmos DB för februari månad. Diagrammen innehåller också Azure Cosmos DB-kostnader per plats och resursgrupp.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om du vill veta mer om hur prissättning fungerar i Azure Cosmos DB:

* [Prismodell i Azure Cosmos DB](how-pricing-works.md)
* [Optimera kostnaden för etablerat dataflöde i Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimera kostnaden för frågor i Azure Cosmos DB](optimize-cost-queries.md)
* [Optimera lagringskostnaden i Azure Cosmos DB](optimize-cost-storage.md)
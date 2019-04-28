---
title: Självstudie – Granska användningskostnader med Cloudyn i Azure | Microsoft Docs
description: I den här självstudien granskar du användning och kostnader för att spåra trender, identifiera ineffektivitet och skapa aviseringar.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: bb5a4b3a3019007948a9996ed6715e755f284f9e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127318"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Självstudie: Granska användning och kostnader

I Cloudyn visas användning och kostnader så att du kan spåra trender, identifiera ineffektivitet och skapa aviseringar. Alla data om användning och kostnader visas på instrumentpaneler och i rapporter i Cloudyn. I exemplen i den här självstudien får du lära dig hur du granskar användning och kostnader med instrumentpaneler och rapporter.

Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Mer information finns i [Azure Cost Management](overview-cost-mgt.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Spårar trender inom användning och kostnader
> * Identifierar ineffektivitet i användningen
> * Skapar aviseringar för onormalt höga kostnader
> * Exportera data

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha ett Azure-konto.
- Du måste antingen ha en utvärderingsregistrering eller en betald prenumeration för Cloudyn.

## <a name="open-the-cloudyn-portal"></a>Öppna Cloudyn-portalen

Du kan granska all användning och alla kostnader i Cloudyn-portalen. Öppna Cloudyn-portalen från Azure Portal eller gå till https://azure.cloudyn.com och logga in.

## <a name="track-usage-and-cost-trends"></a>Spårar trender inom användning och kostnader

Du kan spåra faktiska utgifter för användning och kostnader med rapporter över tid där du kan identifiera trender. Om du vill börja leta efter trender kan du använda rapporten Actual Cost Over Time (Faktisk kostnad över tid). Längst upp till vänster i portalen klickar du på **Kostnader** > **Kostnadsanalys** > **Faktisk kostnad över tid**. När du först öppnar rapporten används inga grupper eller filter.

Här är en exempelrapport:

![Exempel på rapport över faktisk kostnad över tid](./media/tutorial-review-usage/actual-cost01.png)

I rapporten visas alla utgifter de senaste 30 dagarna. Om du bara vill visa utgifter för Azure-tjänster använder du gruppen Service och filtrerar på alla Azure-tjänster. I följande bild visas de filtrerade tjänsterna.

![Exempel som visar filtrerade Azure-tjänster](./media/tutorial-review-usage/actual-cost02.png)

I föregående exempel spenderades mindre pengar från 2018-10-29. Men det kan vara svårt att se en uppenbar trend om du har för många kolumner. Du kan ändra rapportvyn till ett linje- eller områdesdiagram för att se data i andra vyer. I följande bild ser du trenden tydligare.

![Exemplet visar en sjunkande kostnadstrend för Azure VM](./media/tutorial-review-usage/actual-cost03.png)

Om du fortsätter med exemplet kan du se att kostnaden för virtuell Azure-dator har gått ned. Kostnader för andra Azure-tjänster började också gå ned den dagen. Vad orsakade då de minskade utgifterna? I det här exemplet slutfördes ett stort arbetsprojekt så förbrukningen av många Azure-tjänster gick också ned.

Om du vill se en självstudievideo om att spåra användnings- och kostnadstrender går du till [Analysera molnfaktureringsdata över tid med Cloudyn](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>Identifierar ineffektivitet i användningen

Med optimeringsrapporter kan du förbättra effektiviteten, optimera användningen och identifiera sätt att spara pengar på dina molnresurser. Rekommendationerna kring kostnadseffektiv storleksändring är avsedda att minska antalet inaktiva eller dyra virtuella datorer.

Ett vanligt problem som påverkar organisationer när de ursprungligen flyttar resurser till molnet är deras virtualiseringsstrategi. De använder ofta en metod som liknar den som användes till att skapa virtuella datorer i den lokala virtualiseringsmiljön. De antar även att de kan minska kostnaderna genom att flytta de lokala virtuella datorerna till molnet oförändrade. Den här metoden kommer dock förmodligen inte att minska kostnaderna.

Problemet är att den befintliga infrastrukturen redan är betald. Användarna kunde skapa stora virtuella datorer och låta dem vara igång om de ville, inaktiva eller inte, utan att det gjorde något. Om stora eller inaktiva virtuella datorer flyttas till molnet kommer kostnaderna förmodligen att *öka*. Det är viktigt med kostnadsallokering för resurser när du tecknar avtal med en molntjänstprovider. Du måste betala för det du begär oavsett om du använder resursen fullt ut eller inte.

I rapporten Cost Effective Sizing Recommendations (Rekommendationer kring kostnadseffektiv storleksändring) identifieras potentiella årliga besparingar genom att jämföra kapaciteten hos olika typer av VM-instanser med historiska data kring användning av processor och minne.  

I menyn längst upp i portalen klickar du på **Optimizer** (Optimerare)  > **Sizing Optimization** (Prisstorlek)  > **Cost Effective Sizing Recommendations** (Kostnadseffektiv storleksrekommendation). Om det är användbart kan du använda ett filter för att minska resultatet. Här är en exempelbild.

![Rapporten med rekommendationer om kostnadseffektiv storlek för virtuella Azure-datorer](./media/tutorial-review-usage/sizing01.png)

I det här exemplet kan du spara 2 382 USD genom att följa rekommendationerna för att ändra typerna av VM-instanser. Klicka på plustecknet (+) under **Details** (Detaljer) för den första rekommendationen. Här är lite information om den första rekommendationen.

![Exempel som visar information om rekommendation](./media/tutorial-review-usage/sizing02.png)

Visa ID:n för VM-instanser genom att klicka på plustecknet bredvid **List of Candidates** (Lista med kandidater).

![Exemplet visar en lista över VM-kandidater att ändra storlek på](./media/tutorial-review-usage/sizing03.png)

Om du vill se en självstudievideo om att identifiera ineffektivitet i användningen kan du titta på [Optimera VM-storlek i Cloudyn](https://youtu.be/1xaZBNmV704).

Azure Cost Management ger även kostnadsbesparande rekommendationer för Azure-tjänster. Mer information finns i [Självstudie: Optimera kostnader från rekommendationer](tutorial-acm-opt-recommendations.md).

## <a name="create-alerts-for-unusual-spending"></a>Skapa aviseringar för onormalt höga kostnader

Aviseringar gör att du automatiskt kan meddela intressenter om avvikelser i utgifter och risk för höga utgifter. Du kan skapa aviseringar med hjälp av rapporter som har stöd för aviseringar baserat på tröskelvärden för budgetar och kostnader.

I det här exemplet används rapporten **Actual Cost Over Time** (Faktisk kostnad över tid) till att skicka ett meddelande när dina Azure VM-utgifter närmar sig din totala budget. I det här scenariot har du en total budget på 20 000 dollar, och du vill få ett meddelande när kostnaderna närmar sig hälften av budgeten – 9 000 dollar – och ytterligare ett meddelande när kostnaderna når 10 000 dollar.

1. På menyn längst upp i Cloudyn-portalen väljer du **Kostnader** > **Kostnadsanalys** > **Faktisk kostnad över tid**.
2. Ställ in **Groups** (Grupper) på **Service** och sätt **Filter on the service** (Filtrera efter tjänsten) till **Azure/VM**.
3. Längst upp till höger i rapporten väljer du **Åtgärder** och sedan **Schemalägg rapport**.
4. Om du vill skicka e-post med rapporten till dig själv vid schemalagt intervall väljer du fliken **Schemaläggning** i rapportdialogrutan **Spara eller schemalägg den här**. Välj **Skicka via e-post**. Eventuella taggar samt den gruppering och filtrering du använde ingår i den rapport som du får via e-post.
5. Välj fliken **Tröskel** och sedan **Actual Cost vs. Threshold** (Faktisk kostnad jämfört med tröskelvärde).
   1. I tröskelrutan **Röd avisering** anger du 10000.
   2. I tröskelrutan **Gul avisering** anger du 9000.
   3. I rutan **Antal på varandra följande aviseringar** anger du det antal på varandra följande aviseringar som ska tas emot. När du har fått det antal aviseringar som du angett, skickas inga fler aviseringar.
6. Välj **Spara**.

![Exemplet visar röda och gula aviseringar baserat på tröskelvärden för utgifter](./media/tutorial-review-usage/schedule-alert01.png)

Du kan även välja tröskelvärdesmåttet **Cost Percentage vs. Budget** (Kostnadernas procentandel av budgeten) till att skapa aviseringar. På så sätt kan du ange tröskelvärden som procentandelar av budgeten i stället för valutavärden.

## <a name="export-data"></a>Exportera data

På ungefär samma sätt som du skapar aviseringar för rapporter som kan du även exportera data från valfri rapport. Till exempel kanske du vill exportera en lista med Cloudyn-konton eller andra användardata. Om du vill exportera en rapport öppnar du den och klickar på **Åtgärder** uppe till höger i rapporten. En användbar åtgärd är att **Exportera alla rapportdata** så att du kan ladda ned eller skriva ut informationen. Du kan också välja **Schemalägg rapport** om du vill schemalägga rapporten och få den via e-post.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Spårar trender inom användning och kostnader
> * Identifierar ineffektivitet i användningen
> * Skapar aviseringar för onormalt höga kostnader
> * Exportera data


Gå vidare till nästa självstudie om du vill lära dig att göra prognoser för utgifter med hjälp av historiska data.

> [!div class="nextstepaction"]
> [Prognostisera framtida utgifter](tutorial-forecast-spending.md)

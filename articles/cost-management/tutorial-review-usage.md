---
title: Självstudie – Granska användningskostnader med Cloudyn i Azure | Microsoft Docs
description: I den här självstudien granskar du användning och kostnader för att spåra trender, identifiera ineffektivitet och skapa aviseringar.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 72eaa6f085581f34b696a946e2168eceaa21a849
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987721"
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

![exempelrapport](./media/tutorial-review-usage/actual-cost01.png)

I rapporten visas alla utgifter de senaste 30 dagarna. Om du bara vill visa utgifter för Azure-tjänster använder du gruppen Service och filtrerar på alla Azure-tjänster. I följande bild visas de filtrerade tjänsterna.

![filtrerade tjänster](./media/tutorial-review-usage/actual-cost02.png)

I föregående exempel spenderades mindre pengar från 2017-08-31 än innan. Den kostnadstrenden fortsatte för de olika tjänsterna i ungefär nio dagar. Sedan återgick kostnaderna till det normala. Det kan dock vara svårt att se en uppenbar trend om du har för många kolumner. Du kan ändra rapportvyn till ett linje- eller områdesdiagram för att se data i andra vyer. I följande bild ser du trenden tydligare.

![trend i rapport](./media/tutorial-review-usage/actual-cost03.png)

I det här exemplet ser du tydligt att kostnaden för Azure Storage minskade 2017-08-31 medan utgifterna för andra Azure-tjänster låg kvar på samma nivå. Vad orsakade då de minskade utgifterna? I det här exemplet var några medarbetare på semester och använde inte Storage-tjänsten.

Om du vill se en självstudievideo om att spåra användnings- och kostnadstrender går du till [Analysera molnfaktureringsdata över tid med Cloudyn](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>Identifierar ineffektivitet i användningen

Med optimeringsrapporter kan du förbättra effektiviteten, optimera användningen och identifiera sätt att spara pengar på dina molnresurser. Rekommendationerna kring kostnadseffektiv storleksändring är avsedda att minska antalet inaktiva eller dyra virtuella datorer.

Ett vanligt problem som påverkar organisationer när de ursprungligen flyttar resurser till molnet är deras virtualiseringsstrategi. De använder ofta en metod som liknar den som användes till att skapa virtuella datorer i den lokala virtualiseringsmiljön. De antar även att de kan minska kostnaderna genom att flytta de lokala virtuella datorerna till molnet oförändrade. Den här metoden kommer dock förmodligen inte att minska kostnaderna.

Problemet är att den befintliga infrastrukturen redan är betald. Användarna kunde skapa stora virtuella datorer och låta dem vara igång om de ville, inaktiva eller inte, utan att det gjorde något. Om stora eller inaktiva virtuella datorer flyttas till molnet kommer kostnaderna förmodligen att *öka*. Det är viktigt med kostnadsallokering för resurser när du tecknar avtal med en molntjänstprovider. Du måste betala för det du begär oavsett om du använder resursen fullt ut eller inte.

I rapporten Cost Effective Sizing Recommendations (Rekommendationer kring kostnadseffektiv storleksändring) identifieras potentiella årliga besparingar genom att jämföra kapaciteten hos olika typer av VM-instanser med historiska data kring användning av processor och minne.  

I menyn längst upp i portalen klickar du på **Optimizer** (Optimerare)  > **Sizing Optimization** (Prisstorlek)  > **Cost Effective Sizing Recommendations** (Kostnadseffektiv storleksrekommendation). Filtrera providern som Azure för att endast titta på virtuella Azure-datorer. Här är en exempelbild.

![Virtuella Azure-datorer](./media/tutorial-review-usage/sizing01.png)

I det här exemplet kan du spara 3 114 USD genom att följa rekommendationerna för att ändra typerna av VM-instanser. Klicka på plustecknet (+) under **Details** (Detaljer) för den första rekommendationen. Här är lite information om den första rekommendationen.

![information om rekommendation](./media/tutorial-review-usage/sizing02.png)

Visa ID:n för VM-instanser genom att klicka på plustecknet bredvid **List of Candidates** (Lista med kandidater).

![Lista med kandidater](./media/tutorial-review-usage/sizing03.png)

Om du vill se en självstudievideo om att identifiera ineffektivitet i användningen kan du titta på [Optimera VM-storlek i Cloudyn](https://youtu.be/1xaZBNmV704).

## <a name="create-alerts-for-unusual-spending"></a>Skapa aviseringar för onormalt höga kostnader

Du kan automatiskt meddela intressenter vid avvikelser i utgifterna och vid risk för höga utgifter. Du kan snabbt och enkelt skapa aviseringar med hjälp av rapporter som har stöd för aviseringar baserat på tröskelvärden för budgetar och kostnader.

Du kan skapa en avisering för alla typer av utgifter från valfri kostnadsrapport. I det här exemplet använder du rapporten Actual Cost Over Time till att meddela dig när Azure VM-utgifterna närmar sig din totala budget. Alla följande steg krävs för att skapa aviseringen. I menyn längst upp i portalen klickar du på **Kostnader** > **Kostnadsanalys** > **Faktisk kostnad över tid**. Ställ in **Groups** (Grupper) på **Service** och sätt **Filter on the service** (Filtrera efter tjänsten) till **Azure/VM**. Klicka på **Actions** (Åtgärder) uppe till höger i rapporten och välj **Schedule report** (Schemalägg rapport).

På fliken **Schemaläggning** i rutan Spara eller schemalägg den här rapporten kan du ange att du vill få ett e-postmeddelande med rapporten med valfri frekvens. Välj **Skicka via e-post**. De taggar samt den gruppering och filtrering du använde ingår i rapporten du får via e-post. Klicka på fliken **Threshold** (Tröskelvärde) och välj **Actual Cost vs. Threshold** (Faktisk kostnad jämfört med tröskelvärde). Om du har en total budget på 500 000 USD och vill få en avisering när kostnaderna är ungefär hälften kan du skapa en **röd avisering** vid 250 000 USD och en **gul avisering** vid 240 000 USD. Inkludera inte kommatecken i de värden som du anger. Välj sedan antalet på varandra följande aviseringar. När du har fått det antal aviseringar som du angett, skickas inga fler aviseringar. Spara den schemalagda rapporten.

![exempelrapport](./media/tutorial-review-usage/schedule-alert01.png)

Du kan också välja tröskelvärdesmåttet Cost Percentage vs. Budget (Kostnadernas procentandel av budgeten) till att skapa aviseringar. Då kan du använda procentandelar av budgeten i stället för faktiska valutavärden.

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

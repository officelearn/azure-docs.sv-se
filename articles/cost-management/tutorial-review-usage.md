---
title: "Granska användning och kostnader i Azure kostnaden Management | Microsoft Docs"
description: "Granska användning och kostnader för att spåra trender, identifiera ineffektivitet och skapa varningar."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 36ebffb41211e443cc1619df46f50247945cc57c
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="review-usage-and-costs"></a>Granska användning och kostnader

Azure kostnaden hanteringen av Cloudyn visas användning och kostnader så att du kan spåra trender, identifiera ineffektivitet och skapa aviseringar. Alla data för användning och kostnader visas i Cloudyn instrumentpaneler och rapporter. Exemplen i den här kursen får du om granska användning och kostnader med instrumentpaneler och rapporter. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Spåra användning och kostnader trender
> * Identifiera användning ineffektiviteter
> * Skapa aviseringar för ovanliga utgifter eller överskridanden



## <a name="open-the-cloudyn-portal"></a>Öppna Cloudyn-portalen

Du kan granska alla användning och kostnader i Cloudyn-portalen. Öppna Cloudyn portalen från Azure-portalen eller navigera till https://app.cloudyn.com och logga in.

## <a name="track-usage-and-cost-trends"></a>Spåra användning och kostnader trender

Du kan spåra faktiska utgifter för användning och kostnader med rapporter över tid att identifiera trender. Om du vill börja söka vid trender, kan du använda rapporten faktiska kostnaden över tid. På menyn rapporter överst i portalen **kostnaden** > **kostnaden Analysis** > **faktiska kostnaden över tid**. När du först öppnar rapporten som inga grupper eller filter tillämpas på den.

Här är en exempelrapport:

![exempelrapport](./media/tutorial-review-usage/actual-cost01.png)

Rapporten visar alla utgiftsgränsen under de senaste 30 dagarna. Om du vill visa endast utgifter för Azure-tjänster, tillämpa tjänstgruppen och filtrera sedan för alla Azure-tjänster. Följande bild visar de filtrerade tjänsterna.

![filtrerade tjänster](./media/tutorial-review-usage/actual-cost02.png)

I föregående exempel kördes mindre pengar för att starta på 2017-08-31 än innan. Den kostnadstrend fortsätter för olika tjänster för ungefär nio dagar. Sedan fortsätter ytterligare utgifter som tidigare. För många kolumner kan dock dölja en uppenbara trend. Du kan ändra rapporten till en rad eller område diagrammet för att se data som visas i andra vyer. Följande bild visar trenden tydligare.

![Trend i rapporten](./media/tutorial-review-usage/actual-cost03.png)

I det här exemplet kan se du tydligt att Azure Storage kosta ignorerade startar på 2017-08-31 medan utgifter på andra Azure-tjänster låg nivå. Så, vad som orsakade att minskning i utgifter? I det här exemplet vissa medarbetare var på semester från arbete och använde inte Storage-tjänst.

Om du vill se en video som självstudiekurs om att spåra användning och kostnader trender, se [analysera ditt moln fakturering data jämfört med Azure kostnaden Management med Cloudyn](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>Identifiera användning ineffektiviteter

Förbättra effektiviteten optimering rapporter, optimera användningen och identifiera sätt att spara pengar åt för molnresurser. De är särskilt användbart med kostnadseffektiv sizing rekommendationer är avsedda för att minska inaktiv eller dyr virtuella datorer.

Ett vanligt problem som påverkar organisationer när de ursprungligen flyttar resurser i till molnet är deras virtualiseringsstrategi för. De använder ofta en metod som liknar den som de används för att skapa virtuella datorer för lokala virtualiseringsmiljön. Och de förutsätter att minskas kostnaderna genom att flytta sina lokala virtuella datorer till molnet, som-är. Denna metod är inte troligt att minska kostnaderna.

Problemet är att den befintliga infrastrukturen har betalats. Användare kan skapa och spara stora virtuella datorer som körs om de tyckte om, inaktiv eller inte och lite följd. Flytta stora eller inaktiv virtuella datorer till molnet är troligt att *öka* kostnader. Tilldelning av kostnad för resurser som är viktigt när du anger avtal med molntjänst-providers. Du måste betala för vad du gör om du använder resursen fullständigt eller inte.

Rapporten kostnadseffektiv Sizing rekommendationer identifierar potentiella årliga besparingar genom att jämföra VM-instans typen kapacitet till deras historiska CPU och minne användningsdata.  

Klicka på menyn rapporter överst i portalen **optimering** > **priser optimering** > **kostnadseffektiv Sizing rekommendationer**. Filtrera providern till Azure för att titta på endast virtuella Azure-datorer. Här är en exempelbild.

![Virtuella Azure-datorer](./media/tutorial-review-usage/sizing01.png)

I det här exemplet kunde $3,114 sparas genom att följa rekommendationerna för att ändra typer för VM-instans. Klicka på plustecknet (+) under **information** för första rekommendationen. Här följer information om den första rekommendationen.

![rekommendation information](./media/tutorial-review-usage/sizing02.png)

Visa VM-instans-ID: N genom att klicka på plustecknet bredvid **listan över kandidater**.

![Listan över kandidater](./media/tutorial-review-usage/sizing03.png)

Om du vill se en självstudiekurs video upptäcka användning ineffektiviteter, se [optimera VM-storlek i Azure kostnaden Management med Cloudyn](https://youtu.be/1xaZBNmV704).

## <a name="create-alerts-for-unusual-spending"></a>Skapa aviseringar om onormal utgifter

Du kan meddela berörda personer automatiskt för utgiftsgräns avvikelser och överskridandet risker. Du kan snabbt och enkelt skapa aviseringar med hjälp av rapporter att support aviseringar baserat på budget och kostnaden tröskelvärden.

Du skapar en avisering för alla utgiftsgränsen med kostnaden rapport. I det här exemplet rapporten den faktiska kostnaden över tid för att meddela dig när Azure VM utgifter närmar sig din totala budget. På menyn rapporter överst i portalen **kostnaden** > **kostnaden Analysis** > **faktiska kostnaden över tid**. Ange **grupper** till **Service** och ange **Filter på tjänsten** till **Virtuella och Azure**. I övre högra i rapporten klickar du på **åtgärder** och välj sedan **schemalägga rapport**.

Använd den **schemaläggning** att skicka dig ett e-postmeddelande för rapporten med den frekvens som du vill använda. Alla taggar, gruppering och filtrering som du använde ingår i rapporten via e-post. Klicka på den **tröskelvärdet** väljer du fliken och välj **verklig kostnad vs. Tröskelvärde för**. Om du har en total budget 500 000 kr och du vill att avisering när kostnader nära ungefär hälften, skapa en **Red avisering** på 250 000 $ och en **gul avisering** på $240,000. Välj antal på varandra följande aviseringar. När du tar emot totalt antal aviseringar som du angav skickas ingen ytterligare aviseringar. Spara den schemalagda rapporten.

![exempelrapport](./media/tutorial-review-usage/schedule-alert01.png)

Du kan också välja procentsats kostnaden vs. Budget tröskelvärdet mått för att skapa aviseringar. Du kan använda budget procenttal i stället för värden med hjälp av den måtten.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Spåra användning och kostnader trender
> * Identifiera användning ineffektiviteter
> * Skapa aviseringar för ovanliga utgifter eller överskridanden


Gå vidare till nästa kurs att lära dig hur du styr åtkomst till data.

> [!div class="nextstepaction"]
> [Kontrollera åtkomsten till data](tutorial-user-access.md)

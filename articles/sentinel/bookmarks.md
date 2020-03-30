---
title: Använda jaktbokmärken för dataundersökningar i Azure Sentinel
description: I den här artikeln beskrivs hur du använder Azure Sentinel-jaktbokmärkena för att hålla reda på data.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588696"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Håll reda på data under jakt med Azure Sentinel

Hotjakt kräver vanligtvis att granska berg av loggdata letar efter bevis på skadligt beteende. Under denna process, utredare hitta händelser som de vill komma ihåg, återkomma och analysera som en del av att validera potentiella hypoteser och förstå hela historien om en kompromiss.

Jaktbokmärken i Azure Sentinel hjälper dig att göra detta genom att bevara de frågor som du körde i **Azure Sentinel - Loggar**, tillsammans med frågeresultat som du anser vara relevanta. Du kan också spela in kontextuella observationer och referera till dina resultat genom att lägga till anteckningar och taggar. Bokmärkta data är synliga för dig och dina lagkamrater för enkelt samarbete.

Du kan när som helst gå tillbaka till dina bokmärkta data på fliken **Bokmärken** i **jaktfönstret.** Du kan använda filtrerings- och sökalternativ för att snabbt hitta specifika data för din aktuella undersökning. Du kan också visa dina bokmärkta data direkt i **tabellen HuntingBookmark** på arbetsytan Log Analytics. Ett exempel:

> [!div class="mx-imgBorder"]
> ![visa HuntingBookmark-tabell](./media/bookmarks/bookmark-table.png)

Genom att visa bokmärken från tabellen kan du filtrera, sammanfatta och koppla bokmärkta data till andra datakällor, vilket gör det enkelt att söka efter bekräftande bevis.

För närvarande i förhandsgranskning, om du hittar något som snabbt måste åtgärdas under jakt i dina loggar, i ett par klick, kan du skapa ett bokmärke och marknadsföra det till en incident, eller lägga till bokmärket till en befintlig incident. Mer information om incidenter finns i [Självstudiekurs: Undersöka incidenter med Azure Sentinel](tutorial-investigate-cases.md). 

Även i förhandsgranskningen kan du visualisera dina bokmärkta data genom att klicka på **Undersök** från bokmärkets information. Detta startar undersökningsupplevelsen där du kan visa, undersöka och visuellt kommunicera dina resultat med hjälp av ett interaktivt entitetsdiagram och tidslinje.

## <a name="add-a-bookmark"></a>Lägga till ett bokmärke

1. I Azure-portalen navigerar du till **Sentinel** > **Threat management** > **Hunting** för att köra frågor om misstänkt och avvikande beteende.

2. Välj en av jaktfrågorna och till höger väljer **du Kör fråga**i jaktfrågadetaljer i jaktfrågadetaljer . 

3. Välj **Visa frågeresultat**. Ett exempel:
    
    > [!div class="mx-imgBorder"]
    > ![visa frågeresultat från Azure Sentinel-jakt](./media/bookmarks/new-processes-observed-example.png)
    
    Den här åtgärden öppnar frågeresultaten i **fönstret Loggar.**

4. I listan över loggfrågeresultat använder du kryssrutorna för att markera en eller flera rader som innehåller den information som du tycker är intressant.

5. Välj **Lägg till bokmärke:**
    
    > [!div class="mx-imgBorder"]
    > ![Lägga till jaktbokmärke i fråga](./media/bookmarks/add-hunting-bookmark.png)

6. Till höger, i fönstret **Lägg till bokmärke,** kan du uppdatera bokmärkesnamnet, lägga till taggar och anteckningar som hjälper dig att identifiera vad som var intressant med objektet.

7. I avsnittet **Frågeinformation** använder du listrutorna för att extrahera information från frågeresultaten för entiteten **Konto,** **Värd**och IP-adressentitetstyper. **IP address** Den här åtgärden mappar den valda entitetstypen till en viss kolumn från frågeresultatet. Ett exempel:
    
    > [!div class="mx-imgBorder"]
    > ![Kartenhetstyper för jaktbokmärke](./media/bookmarks/map-entity-types-bookmark.png)
    
    Om du vill visa bokmärket i undersökningsdiagrammet (för närvarande i förhandsgranskning) måste du mappa minst en entitetstyp som är **antingen Konto,** **Värd**eller **IP-adress**. 

5. Klicka på **Spara** om du vill genomföra ändringarna och lägga till bokmärket. Alla bokmärkta data delas med andra utredare och är ett första steg mot en samarbetsundersökning.

 
> [!NOTE]
> Loggfrågans resultat stöder bokmärken när den här rutan öppnas från Azure Sentinel. Du kan till exempel välja **Allmänna** > loggar från**navigeringsfältet,** välja händelselänkar i undersökningsdiagrammet eller välja ett varnings-ID från fullständig information om en incident (för närvarande i förhandsgranskning). Du kan inte skapa bokmärken när **loggfönstret** öppnas från andra platser, till exempel direkt från Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Visa och uppdatera bokmärken 

1. I Azure-portalen navigerar du till **Sentinel** > **Threat management** > **Hunting**. 

2. Välj fliken **Bokmärken** om du vill visa listan med bokmärken.

3. Använd sökrutan eller filteralternativen för att hitta ett visst bokmärke.

4. Välj enskilda bokmärken och visa bokmärkesinformationen i informationsfönstret till höger.

5. Gör ändringarna efter behov, som sparas automatiskt.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Utforska bokmärken i undersökningsdiagrammet

> [!IMPORTANT]
> Utforska bokmärken i undersökningsdiagrammet och själva undersökningsdiagrammet är för närvarande i offentlig förhandsversion.
> Dessa funktioner tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. I Azure-portalen navigerar du till fliken**Hunting** > **Jaktbokmärken** för**sentinelhothantering** >  **Sentinel** > och väljer det bokmärke eller bokmärken som du vill undersöka.

2. Kontrollera att minst en entitet är mappad i bokmärkesdetaljerna. För **entiteter**visas till exempel transaktioner för **IP,** **Dator**eller **Konto**.

3. Klicka på **Undersök** om du vill visa bokmärket i undersökningsdiagrammet.

Instruktioner för hur du använder undersökningsdiagrammet finns i [Använd undersökningsdiagrammet för djupdykning](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Lägga till bokmärken i en ny eller befintlig incident

> [!IMPORTANT]
> Att lägga till bokmärken i en ny eller befintlig incident är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. I Azure-portalen navigerar du till fliken**Hunting** > **Jaktbokmärken** för**sentinelhothantering** >  **Sentinel** > och väljer det bokmärke eller bokmärken som du vill lägga till i en incident.

2. Välj **Incidentåtgärder (förhandsgranskning)** i kommandofältet:
    
    > [!div class="mx-imgBorder"]
    > ![Lägga till bokmärken i incidenten](./media/bookmarks/incident-actions.png)

3. Välj antingen **Skapa ny incident** eller Lägg till befintlig **incident**, efter behov. Sedan:
    
    - För en ny incident: Uppdatera information om incidenten och välj sedan **Skapa**.
    - För att lägga till ett bokmärke i en befintlig incident: Välj en incident och välj sedan **Lägg till**. 

Så här visar du bokmärket i incidenten: Navigera till **Sentinel** > **Threat management** > **Incidents** och välj incidenten med ditt bokmärke. Välj **Visa fullständig information**och välj sedan fliken **Bokmärken.**

> [!TIP]
> Som ett alternativ till alternativet **Incidentåtgärder (Förhandsgranska)** i kommandofältet kan du använda snabbmenyn (**...**) för ett eller flera bokmärken för att välja alternativ för att **skapa ny incident,** **Lägg till i befintlig incident**och Ta bort **från incidenten**. 

## <a name="view-bookmarked-data-in-logs"></a>Visa bokmärkta data i loggar

Om du vill visa bokmärkta frågor, resultat eller deras historik markerar du bokmärket på fliken > **Jaktbokmärken** och använder länkarna i informationsfönstret: **Hunting** 

- **Visa källfrågan** om du vill visa källfrågan i **fönstret Loggar.**

- **Visa bokmärkesloggar** för att se alla bokmärkesmetadata, vilket inkluderar vem som gjorde uppdateringen, de uppdaterade värdena och den tidpunkt då uppdateringen inträffade.

Du kan också visa råbokmärkesdata för alla bokmärken genom att välja **Bokmärkesloggar** i kommandofältet på fliken > **Jaktbokmärken:** **Hunting**

> [!div class="mx-imgBorder"]
> ![Bokmärkesloggar](./media/bookmarks/bookmark-logs.png)

I den här vyn visas alla dina bokmärken med associerade metadata. Du kan använda KQL-frågor [(Keyword Query Language)](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) för att filtrera ned till den senaste versionen av det specifika bokmärke du letar efter.

> [!NOTE]
> Det kan finnas en betydande fördröjning (mätt i minuter) mellan den tidpunkt då du skapar ett bokmärke och när det visas på fliken **Bokmärken.**

## <a name="delete-a-bookmark"></a>Ta bort ett bokmärke
 
1.  I Azure-portalen navigerar du till fliken**Hunting** > **Jaktbokmärken** för**sentinelhothantering** >  **Sentinel** > och väljer det bokmärke eller bokmärken som du vill ta bort. 

2. Högerklicka på dina val och välj alternativet att ta bort bokmärket eller bokmärkena. Ta till exempel **bort bokmärke** om du bara har markerat ett bokmärke och **Ta bort två bokmärken** om du har markerat två bokmärken.
    
Om du tar bort bokmärket tas bokmärket bort från listan på fliken **Bokmärke.** **Tabellen HuntingBookmark** för din Log Analytics-arbetsyta fortsätter att innehålla tidigare bokmärkesposter, men den senaste posten ändrar **SoftDelete-värdet** till true, vilket gör det enkelt att filtrera bort gamla bokmärken. Om du tar bort ett bokmärke tas inga entiteter bort från undersökningsupplevelsen som är associerade med andra bokmärken eller aviseringar. 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kör en jaktundersökning med bokmärken i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:


- [Proaktivt jakt efter hot](hunting.md)
- [Använda anteckningsböcker för att köra automatiserade jaktkampanjer](notebooks.md)

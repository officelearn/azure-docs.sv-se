---
title: "Förstå kostnadsrapporter i Azure kostnaden Management | Microsoft Docs"
description: "Den här artikeln hjälper dig att förstå Cloudyn rapporter grundläggande struktur och funktioner."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/27/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: df2108a6e2a01195340a09eacf1c56f9d738c923
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="understanding-cost-reports"></a>Förstå kostnad rapporter

Den här artikeln hjälper dig att förstå Cloudyn rapporter grundläggande struktur och funktioner. De flesta Cloudyn rapporter är intuitiva och har ett enhetligt utseende. När du har läst den här artikeln är redo att använda alla rapporter. Många funktioner som standard är tillgängliga under olika rapporter, så att du kan navigera i rapporter utan problem. Rapporter kan anpassas och du kan välja mellan flera alternativ för att beräkna och visa resultat.

## <a name="report-fields-and-options"></a>Rapportfält och alternativ

Här är en titt på ett exempel på rapporten kostnaden över tid. De flesta Cloudyn rapporter har en liknande layout.

![exempelrapport](./media/understanding-cost-reports/sample-report.png)

Varje numrerade område i föregående bild beskrivs i detalj i följande information:

1. **Datumintervall**

    Använd listan datumintervall för att definiera ett tidsintervall för rapporten med hjälp av en förinställning eller anpassad.
2. **Sparat Filter**

    Använd listan Spara Filter för att spara aktuella grupper och filter som tillämpas på rapporten. Sparade filter finns i kostnad och prestanda rapporter, till exempel:

      - Kostnad analys
      - Allokering
      - Tillgångshantering
      - Optimering

  Ange ett filternamn och klicka på **spara**.

3. **Taggar**

    Använd området taggar till gruppen med taggkategorier. Taggar visas på menyn Azure avdelning eller kostnaden center taggar eller de Cloudyns kostnaden entiteten och prenumeration taggar. Välj att filtrera resultat. Du kan också skriva ett taggnamn (nyckelord) för att filtrera resultat.

    ![Välj alternativ](./media/understanding-cost-reports/select-options.png)

    Klicka på **Lägg till** att lägga till ett nytt filter.

    ![Lägg till filter](./media/understanding-cost-reports/add-filter.png)

    Taggen gruppering eller filtrering är inte relaterad till Azure-resurser eller resource group-taggarna.

    Kostnad allokering taggen gruppering och filtrering är tillgängliga i den **grupper** menyalternativet.

4. **Grupper i rapporter**

    Använda grupper i kostnadsanalys detaljerade rapporter för att visa standard kategorier från fakturering data i rapporten.  Dock visa grupper i kostnadsallokering rapporter visa tagg-baserade kategorier. Taggen-baserade kategorier har definierats i kostnaden allokering modellen och standard specificerade kategorier från faktureringsinformation.

    ![grupper taggar](./media/understanding-cost-reports/groups-tags01.png)

    ![grupper taggar](./media/understanding-cost-reports/groups-tags02.png)

    Grupper i taggen grupper kategorier i kostnadsrapporter, kan innehålla:
      - Taggar
      - gruppen resurstaggar
      - Cloudyn kostnad entitetstaggar
      - Prenumerationen taggkategorier för kostnad allokering

  Exempel kan vara:
     - Kostnadsställe
     - Avdelning
     - Program
     - Miljö
     - Kostnad kod

5. **Filter**

    Använda enkla eller välja flera filter för att ange intervall för valda värden. Ange ett filter, klicka på **Lägg till** och välj sedan filterkategorier och värden.

6. **Kostnad modellen**

    Använd kostnaden modellen för att välja en modell för kostnad som du skapade tidigare med kostnaden allokering 360. Du kan ha flera Cloudyn kostnaden modeller beroende på dina krav för fördelning av kostnaden. Vissa av din organisations team kan ha cost allokering krav som skiljer sig från andra. Varje team kan ha sina egna dedikerade kostnaden modellen.

    Information om hur du skapar en definition av kostnader allokering modellen finns [Använd anpassade taggar när du vill fördela kostnader](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amorteringen**

    Använd amorteringen i kostnad rapporter för att visa ej användning baserat serviceavgifter eller en betalning kostnader och utspridda kostnaderna på jämnt under sin livslängd. Exempel på en avgifter kan:
    - Årliga supportavgifter
    - Årliga komponenter avgifter för säkerhet
    - Reserverade instanser köpa avgifter
    - Vissa Azure Marketplace-objekt.

  Välj under amortering, **amorteras kostnaden** eller **verklig kostnad**.

8. **Lösning**

    Använd lösning för att välja tid upplösning inom det valda datumintervallet. Tid-upplösning avgör hur enheter visas i rapporten och kan vara:
    - Dagligen
    - Varje vecka
    - Månadsvis
    - Varje kvartal
    - Årlig

9. **Tilldelningsregler för**

    Använda tilldelningsregler att tillämpa eller inaktivera kostnaden allokering kostnaden omberäkning. Du kan aktivera eller inaktivera kostnaden allokering omberäkningen för faktureringsinformation. Omberäkningen gäller för de valda kategorierna i rapporten. På så sätt kan du utvärdera kostnaden allokering omberäkning effekten mot rådata faktureringsinformation.

10. **Ej kategoriserade**

    Använd Ej kategoriserade för att inkludera eller exkludera Ej kategoriserade kostnader i rapporten.

11. **Visa/dölj fält**

    Visa/Dölj alternativet har inte någon effekt i rapporter.

12.   **Format**

    Använd format för att välja olika vyer av diagrammet eller tabellen.

    ![format](./media/understanding-cost-reports/display-formats.png)

13. **Flera färg**

    Använda flera färg för att ange färgen på diagram i rapporten.

14. **Åtgärder**

    Använd åtgärder för att spara, exportera eller schemalägger rapporten.

## <a name="next-steps"></a>Nästa steg

- Om du inte har redan slutförts första självstudierna för hantering av kostnader, läsa den på [granska användning och kostnader](tutorial-review-usage.md).

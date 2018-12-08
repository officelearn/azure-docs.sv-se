---
title: Förstå Cloudyn kostnad rapporter i Azure | Microsoft Docs
description: Den här artikeln hjälper dig att förstå grundläggande struktur för Cloudyn cost management-rapporter och funktioner.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 10a8d434df376749b76e2cf90212d872f705b209
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103429"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Förstå Cloudyn kostnad rapporter

Den här artikeln hjälper dig att förstå grundläggande struktur för Cloudyn cost management-rapporter och funktioner. De flesta Cloudyn-rapporter är intuitivt och har ett enhetligt utseende. När du har läst den här artikeln är du redo att använda alla kostnadshanteringsrapporter. Många standard-funktionerna är tillgängliga i hela olika rapporter, så att du kan navigera rapporter enkelt. Rapporter kan anpassas och du kan välja mellan flera alternativ för att beräkna och visa resultat.

## <a name="report-fields-and-options"></a>Rapportfält och alternativ

Här är en titt på ett exempel på rapport för kostnad över tid. De flesta Cloudyn-rapporter har en liknande layout.

![Exempel på Cost Over Time-rapport med numrerade områden som motsvarar beskrivningar](./media/understanding-cost-reports/sample-report.png)

Varje numrerad område i den föregående bilden beskrivs i detalj i följande information:

1. **Datumintervall**

    Använd datumintervall listan för att definiera ett tidsintervall för rapporten med hjälp av en förinställning eller anpassat.
2. **Sparat Filter**

    Använd listan sparade Filter för att spara aktuella grupper och filter som tillämpas på rapporten. Sparade filter finns tillgängliga och kostnad och prestanda rapporter, till exempel:

      - Kostnadsanalys
      - Allokering
      - Tillgångshantering
      - Optimering

  Skriv namnet på ett filter och klicka på **spara**.

3. **Taggar**

    Använd området taggar till gruppen efter taggkategorier. Taggar som visas på menyn är Azure-avdelning eller kostnaden center taggar eller om de är Cloudyns kostnaden entitets- och prenumeration taggar. Välj taggar för att filtrera resultat. Du kan också skriva ett taggnamn (nyckelord) för att filtrera resultaten.

    ![Exempel på en lista över taggar om du vill filtrera resultaten efter](./media/understanding-cost-reports/select-options.png)

    Klicka på **Lägg till** att lägga till ett nytt filter.

    ![Lägg till filter-ruta som visar alternativ och villkor för att filtrera efter](./media/understanding-cost-reports/add-filter.png)

    Taggen gruppering och filtrering rör inte Azure-resurser eller grupp resurstaggar.

    Kostnadsallokering taggen gruppering och filtrering är tillgängliga i den **grupper** menyalternativ.

4. **Grupper i rapporter**

    Använda grupper i kostnadsanalys rapporter för att visa standard, uppdelat kategorier från faktureringsdata i rapporten.  Grupper i kostnadsallokering visas Visa dock kodspråk kategorier. Tagg-baserade kategorier har definierats i modellen för kostnadsallokering och standard specificerade kategorier från faktureringsinformation.

    ![Den första exemplet lista över taggar som du kan gruppera efter](./media/understanding-cost-reports/groups-tags01.png)

    ![Andra exempel lista över taggar som du kan gruppera efter](./media/understanding-cost-reports/groups-tags02.png)

    Grupper i tagg-baserade kategorierna i Cost Allocation rapporter kan innehålla:
      - Taggar
      - gruppen resurstaggar
      - Cloudyn kostnad entitetstaggar
      - Prenumeration taggkategorier för cost allocation

  Exempel kan vara:
     - Kostnadsställe
     - Avdelning
     - Program
     - Miljö
     - Kostnad kod

    Här är en lista över inbyggda grupper i rapporter:

    - **Kostnadstyp**
      - Välj en kostnadstyp eller flera kostnadstyper eller markera alla. Kostnadstyper är:
        - Avgift
        - Support
        - Förbrukningskostnad
    - **Kunden**
        - Välj en viss kund, flera kunder, eller alla kunder.
    - **Kontonamn**
        - Namnet på konto eller prenumeration. I Azure är det namnet på den Azure-prenumerationen.
    - **Konto Nej**
        - Välj ett konto, flera konton eller alla konton. I Azure är det Azure-prenumerations-GUID.
    - **Överordnat konto**
        - Välj det överordnade kontot, flera konton eller välj.
    - **Tjänst**
        - Välj en tjänst, flera tjänster, eller alla tjänster.
    - **Leverantör**
        - Den molnleverantören där tillgångar och utgifter som är associerade.
    - **Region**
        - Regionen där resursen finns.
    - **Tillgänglighetszon**
        - AWS separat platser inom en region.
    - **Resurstyp**
        - Typ av resurs som används.
    - **Undertyp**
        - Välj undertyp.
    - **Åtgärd**
        - Markera åtgärden eller **visa alla**.
    - **Prismodell**
        - Alla förskott
        - Ingen startavgift
        - Partiellt förskott
        - På begäran
        - Reservation
        - Plats
    - **Tilläggstyp**
        - Markera som är negativa eller positiva tilläggstyp eller båda.
    - **Innehavare**
        - Om en dator körs som en dedikerad dator.
    -   **Användningstyp**
          - Användningstyp kan vara engångsavgifter eller återkommande avgifter.

5. **Filter**

    Använd enkel eller välja flera filter för att ange intervall till valda värden. Om du vill ange ett filter klickar du på **Lägg till** och markera sedan filtret kategorier och värden.

6. **Kostnad modell**

    Använd kostnadsmodell för att välja en kostnadsmodell som du skapade tidigare med Cost Allocation 360. Du kan ha flera Cloudyn kostnadsmodeller, beroende på dina krav för allokering av kostnader. Några av dina organisationens team kan kosta allokering krav som skiljer sig från andra. Varje team kan ha sina egna dedikerade kostnadsmodell.

    Information om hur du skapar en cost allocation modellen definition finns i [använder anpassade taggar för allokering av kostnader](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amorteringen**

    Använd amorteringen i kostnadsallokering rapporter att visa icke-usage baserat serviceavgifter eller betalas engångskostnad och sprida kostnad över tid jämnt under deras livslängd. Exempel på engångsavgifter kan innehålla:
    - Årlig supportavgifter
    - Årlig security komponenter avgifter
    - Avgifter för köp av reserverade instanser
    - Vissa Azure Marketplace-objekt.

  Välj under amortering, **Amorterad kostnad** eller **verklig kostnad**.

8. **Lösning**

    Använd lösning för att välja tid lösning inom det valda datumintervallet. Time-lösningen avgör hur enheter visas i rapporten och kan vara:
    - Dagligen
    - Varje vecka
    - Månadsvis
    - Varje kvartal
    - Årlig

9. **Allokeringsregler**

    Använd allokeringsregler för att tillämpa eller inaktivera kostnadsfördelning kostnaden beräknas. Du kan aktivera eller inaktivera cost allocation omberäkningen för faktureringsinformation. Omberäkningen gäller för de valda kategorierna i rapporten. Det kan du utvärdera kostnaden allokering omberäkning effekten mot fakturering rådata.

10. **Ej kategoriserade**

    Använd Ej kategoriserade att inkludera eller exkludera Ej kategoriserade kostnader i rapporten.

11. **Visa/dölj fält**

    Visa/Dölj-alternativet har inte någon effekt i rapporter.

12.   **Visningsformat**

    Använd format för att välja olika vyer för diagrammet eller tabellen.

    ![Symbolerna i format som du kan välja](./media/understanding-cost-reports/display-formats.png)

13. **Flera färg**

    Använda flera färg för att ange färgen på diagram i rapporten.

14. **Åtgärder**

    Använda åtgärder för att spara, exportera eller schemalägger rapporten.

15. **Princip**

    Även om inte visas i bilden, innehålla vissa rapporter en princip för beräkning av beräknade kostnaden. I dessa rapporter på **konsoliderade** princip visar rekommendationer för alla konton och prenumerationer under den aktuella entiteten, till exempel Microsoft registrering eller AWS betalare. Den **fristående** princip visar rekommendationer för ett konto eller -prenumeration som om det finns inga andra prenumerationer. Den princip som du väljer beror på den optimering strategi som används av din organisation. Kostnad projektioner baseras på de senaste 30 dagarna användning.

## <a name="save-and-schedule-reports"></a>Spara och schemalägga rapporter

När du skapar en rapport kan spara du den för framtida användning. Sparade rapporter är tillgängliga i **Mina verktyg** > **Mina rapporter**. Om du gör ändringar i en befintlig rapport och spara den sparas rapporten som en ny version. Eller så kan du spara den som en ny rapport.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Spara en rapport i Cloudyn-portalen

När du visar en rapport klickar du på **åtgärder** och välj sedan **spara till Mina rapporter**. Namnge rapporten och sedan antingen lägga till en din egen URL eller Använd den automatiskt skapade URL: en. Du kan eventuellt **dela** rapporten offentligt med andra i din organisation eller du kan dela den i din entitet. Om du inte delar rapporten, den är en personlig rapport och att endast du kan visa. Spara rapporten.


### <a name="save-a-report-to-cloud-provider-storage"></a>Spara en rapport till molnlagring av provider

För att spara en rapport i din molntjänstleverantör, måste du redan ha konfigurerat en storage-konto. När du visar en rapport klickar du på **åtgärder** och välj sedan **Schemalägg rapport**. Namnge rapporten och sedan antingen lägga till en din egen URL eller Använd den automatiskt skapade URL: en. Välj **spara i storage** och välj lagringskonto eller lägga till en ny. Ange ett prefix som kommer att läggas till filnamnet för rapporten. Välj en CSV eller JSON-filformat och sedan spara rapporten.

### <a name="schedule-a-report"></a>Schemalägg en rapport

Du kan köra rapporter med schemalagda intervall och det går att skicka dem till en mottagare lista eller cloud service provider storage-konto. När du visar en rapport klickar du på **åtgärder** och välj sedan **Schemalägg rapport**. Du kan skicka rapporten via e-post och spara till ett lagringskonto. Under **schema**, Välj intervall (varje dag, vecka eller månad). Välj dag eller datum att leverera och väljer du tid för varje vecka och per månad. Spara den schemalagda rapporten. Om du väljer formatet Excel-rapporten skickas rapporten som en bifogad fil. När du väljer e-innehållsformat levereras rapportresultat som visas i diagramformat som ett diagram.

### <a name="export-a-report-as-a-csv-file"></a>Exportera en rapport som en CSV-fil

När du visar en rapport klickar du på **åtgärder** och välj sedan **exportera alla rapportdata**. Ett popup-fönster visas en CSV-fil har överförts.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de rapporter som ingår i Cloudyn på [Använd Cloudyn rapporterar](use-reports.md).
- Mer information om hur du använder rapporter för att skapa [instrumentpaneler](dashboards.md).

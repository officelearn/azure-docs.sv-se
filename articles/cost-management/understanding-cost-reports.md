---
title: Förstå kostnadsrapporter i Azure kostnaden Management | Microsoft Docs
description: Den här artikeln hjälper dig att förstå Cloudyn kostnaden management rapporter grundläggande struktur och funktioner.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b2f33a8dce441312e74688ca766ab65c028451d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167907"
---
# <a name="understanding-cost-management-reports"></a>Förstå kostnadsrapporter

Den här artikeln hjälper dig att förstå Cloudyn kostnaden management rapporter grundläggande struktur och funktioner. De flesta Cloudyn rapporter är intuitiva och har ett enhetligt utseende. När du har läst den här artikeln är redo att använda alla rapporter för kostnad-hantering. Många funktioner som standard är tillgängliga under olika rapporter, så att du kan navigera i rapporter utan problem. Rapporter kan anpassas och du kan välja mellan flera alternativ för att beräkna och visa resultat.

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

    Här är en lista över inbyggda grupper i rapporter:

    - **Kostnadstyp**
      - Välj en kostnadstyp eller flera kostnadstyper, eller alla. Kostnad typer är:
        - En avgift
        - Support
        - Kostnaden för användning
    - **Kunden**
        - Välj en viss kund flera kunder, eller alla kunder.
    - **Kontonamn**
        - Namnet på kontot eller prenumeration. I Azure är det namnet på Azure-prenumerationen.
    - **Kontot Nej**
        - Välj ett konto, flera konton eller alla konton. I Azure är det den Azure-prenumeration GUID.
    - **Överordnat konto**
        - Välj det överordnade kontot, flera konton eller välj.
    - **Tjänst**
        - Välj en tjänst, flera tjänster eller alla tjänster.
    - **Leverantör**
        - Molnleverantören där tillgångar och kostnader som är kopplade.
    - **Region**
        - Region där resursen finns.
    - **Tillgänglighet zon**
        - AWS separat platser inom en region.
    - **Resurstyp**
        - Typ av resurs som används.
    - **Undertyp**
        - Välj undertypen.
    - **Åtgärd**
        - Markera åtgärden eller **visa alla**.
    - **Prismodell**
        - Alla förskott
        - Ingen förskott
        - Partiell förskott
        - På begäran
        - Reservation
        - Plats
    - **Tilläggstyp**
        - Markera negativa eller positiva tilläggstyp eller båda.
    - **Organisationer i samma installation**
        - Om en dator körs som en särskild dator.
    -   **Användningstyp**
          - Användningstyp kan vara en avgifter eller återkommande avgifter.

5. **filter**

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

12.   **format**

    Använd format för att välja olika vyer av diagrammet eller tabellen.

    ![format](./media/understanding-cost-reports/display-formats.png)

13. **Flera färg**

    Använda flera färg för att ange färgen på diagram i rapporten.

14. **Åtgärder**

    Använd åtgärder för att spara, exportera eller schemalägger rapporten.

## <a name="save-and-schedule-reports"></a>Spara och schemalägga rapporter

När du har skapat en rapport kan du spara den för framtida användning. Sparade rapporter är tillgängliga i **Mina verktyg** > **Mina rapporter**. Om du gör ändringar i en befintlig rapport och spara den sparade rapporten som en ny version. Eller så kan du spara den som en ny rapport.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Spara en rapport i Cloudyn-portal

När du visar en rapport klickar du på **åtgärder** och välj sedan **spara Mina rapporter**. Namn på rapporten och sedan Lägg antingen till en egna URL eller Använd den automatiskt skapade URL: en. Du kan alternativt **dela** rapporten offentligt med andra i din organisation eller du kan dela till din enhet. Om du inte delar rapporten förblir en personlig rapport och som bara kan du visa. Spara rapporten.


### <a name="save-a-report-to-cloud-provider-storage"></a>Spara en rapport till providern molnlagring

För att spara en rapport i din molntjänstleverantör, måste du redan har konfigurerat ett lagringskonto. När du visar en rapport klickar du på **åtgärder** och välj sedan **schemalägga rapport**. Namn på rapporten och sedan Lägg antingen till en egna URL eller Använd den automatiskt skapade URL: en. Välj **spara till lagring** och välja lagringskontot eller lägga till ett nytt. Ange ett prefix som kommer att läggas till filnamnet för rapporten. Välj en CSV- eller JSON-filformat och sedan spara rapporten.

### <a name="schedule-a-report"></a>Schemalägg en rapport

Du kan köra rapporter med schemalagda intervall och du kan skickas dem till ett mottagande lista eller cloud service provider storage-konto. När du visar en rapport klickar du på **åtgärder** och välj sedan **schemalägga rapport**. Du kan skicka rapporten via e-post och spara till ett lagringskonto. Under **schema**, ett intervall (varje dag, vecka eller månad). Välj för varje vecka och månad, dag eller datum att leverera och väljer du tid. Spara den schemalagda rapporten. Om du väljer formatet Excel-rapport skickas rapporten som en bifogad fil. När du väljer e-innehållsformat levereras rapportresultat som visas i diagrammet format som ett diagram.

### <a name="export-a-report-as-a-csv-file"></a>Exportera en rapport som en CSV-fil

När du visar en rapport klickar du på **åtgärder** och välj sedan **exportera alla rapportdata**. Ett popup-fönster visas en CSV-fil har överförts.

## <a name="next-steps"></a>Nästa steg

- Om du inte har redan slutförts första självstudierna för hantering av kostnader, läsa den på [granska användning och kostnader](tutorial-review-usage.md).

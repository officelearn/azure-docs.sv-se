---
title: Förstå Cloudyn-kostnadshanteringsrapporter i Azure
description: Den här artikeln hjälper dig att förstå den grundläggande strukturen och funktionerna i Cloudyn-kostnadshanteringsrapporter.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 9f42359cc843a934cf1258576d13eec8af2279cc
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687662"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Förstå Cloudyn-kostnadshanteringsrapporter

Den här artikeln hjälper dig att förstå den grundläggande strukturen och funktionerna i Cloudyn-kostnadshanteringsrapporter. De flesta Cloudyn-rapporter är intuitiva och har ett enhetligt utseende. När du har läst den här artikeln är du redo att använda alla kostnadshanteringsrapporter. Eftersom många av standardfunktionerna är tillgängliga i de olika rapporterna kan du enkelt navigera i rapporterna. Rapporter är anpassningsbara, och det finns flera alternativ att välja bland för att beräkna och visa resultat.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-fields-and-options"></a>Rapportfält och alternativ

Här är ett exempel på rapporten för kostnad över tid. De flesta Cloudyn-rapporter har en liknande layout.

![Exempel på rapporten för kostnad över tid med numrerade områden som motsvarar beskrivningar](./media/understanding-cost-reports/sample-report.png)

Varje numrerat område på föregående bild beskrivs i detalj i följande information:

1. **Datumintervall**

    Använd listan Datumintervall för att definiera ett tidsintervall för rapporten med en för förinställning eller anpassad inställning.
2. **Sparat filter**

    Använd listan med sparade filter för att spara de aktuella grupper och filter som används i rapporten. Sparade filter är tillgängliga för kostnads- och resultatrapporter, inklusive:

      - Kostnadsanalys
      - Allokering
      - Tillgångshantering
      - Optimering

   Skriv ett filternamn och klicka på **Spara**.

3. **Taggar**

    Gruppera efter taggkategorier i området Taggar. Taggarna på menyn är taggar för Azure-avdelningar eller Azure-kostnadsställen eller taggar för Cloudyns kostnadsenhet och prenumeration. Du kan filtrera resultat genom att välja taggar. Du kan också filtrera resultat genom att ange ett taggnamn (nyckelord).

    ![Exempel på en lista med taggar för att filtrera resultat](./media/understanding-cost-reports/select-options.png)

    Klicka på **Lägg till** om du vill lägga till ett nytt filter.

    ![Rutan Lägg till filter som visar alternativ och villkor för filtreringen](./media/understanding-cost-reports/add-filter.png)

    Gruppering eller filtrering med taggar har inget att göra med taggar för Azure-resurser eller resursgrupper.

    Gruppering och filtrering av kostnadsallokering är tillgängligt i menyalternativet **Grupper**.

4. **Grupper i rapporter**

    Du kan använda grupper i kostnadsanalysrapporter för att visa standardkategorier från faktureringsdata i rapporten.  Men grupperna i kostnadsallokeringsrapporter visar taggbaserade kategorier. Taggbaserade kategorier definieras i kostnadsallokeringsmodellen och standardkategorier från faktureringsdata.

    ![Första exempellistan över taggar som du kan gruppera efter](./media/understanding-cost-reports/groups-tags01.png)

    ![Andra exempellistan över taggar som du kan gruppera efter](./media/understanding-cost-reports/groups-tags02.png)

    Grupper i taggbaserade gruppkategorier kan innefatta följande i kostnadsallokeringsrapporter:
      - Taggar
      - taggar för resursgrupp
      - taggar för Cloudy-kostnadsenhet
      - Prenumerationstaggkategorier för kostnadsallokeringsändamål

   Exempel:
   - Kostnadsställe
   - Avdelning
   - Program
   - Miljö
   - Kostnadskod

     Här är en lista över inbyggda grupper som är tillgängliga i rapporter:

     - **Kostnadstyp**
     - Välj en kostnadstyp, flera kostnadstyper eller alla. Kostnadstyper omfattar:
       - Engångsavgift
       - Support
       - Användningskostnad
     - **Kund**
       - Välj en specifik kund, flera kunder eller alla kunder.
     - **Kontonamn**
       - Kontonamnet eller prenumerationsnamnet. I Azure är detta namnet på Azure-prenumerationen.
     - **Kontonr**
       - Välj ett konto, flera konton eller alla konton. I Azure är detta GUID för Azure-prenumerationen.
     - **Överordnat konto**
       - Välj det överordnade kontot, flera konton eller alla.
     - **Tjänst**
       - Välj en tjänst, flera tjänster eller alla tjänster.
     - **Leverantör**
       - Molnleverantören där tillgångar och utgifter associeras.
     - **Region**
       - Den region där resursen finns.
     - **Tillgänglighetszon**
       - AWS isolerade platser inom en region.
     - **Resurstyp**
       - Den typ av resurs som används.
     - **Undertyp**
       - Välj undertyp.
     - **Åtgärd**
       - Välj åtgärden eller **Visa alla**.
     - **Prismodell**
       - Allt i förskott
       - Ingenting i förskott
       - En del i förskott
       - På begäran
       - Reservation
       - Spot
     - **Kostnadstyp**
       - Välj negativ eller positiv kostnadstyp eller båda.
     - **Innehav**
       - Om en dator körs som en dedikerad dator.
     - **Användningstyp**
       - Användningstypen kan vara engångsavgifter eller återkommande avgifter.

5. **Filter**

    Använd filter med ett eller flera val för att ange intervall till valda värden. Om du vill ställa in ett filter klickar du på **Lägg till** och väljer filterkategorier och värden.

6. **Kostnadsmodell**

    Du kan använda Kostnadsmodell om du vill välja en kostnadsmodell som du tidigare har skapat med Kostnadsallokering 360. Du kan ha flera Cloudyn-kostnadsmodeller beroende på dina kostnadsallokeringsbehov. Olika team i organisationen kan ha olika kostnadsallokeringsbehov. Varje team kan ha en egen dedikerad kostnadsmodell.

    Information om hur du skapar en definition för en kostnadsallokeringsmodell finns i [Använda anpassade taggar för allokering av kostnader](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortering**

    Du kan använda Amortering i kostnadsallokeringsrapporter om du vill visa ej användningsbaserade avgifter eller engångsavgifter och sprida kostnaderna jämnt över tiden under livslängden. Exempel på engångsavgifter kan vara:
    - Årliga supportavgifter
    - Årliga avgifter för säkerhetskomponenter
    - Avgifter för köp av reserverade instanser
    - Vissa Azure Marketplace-produkter.

   Under Amortering väljer du **Amorterad kostnad** eller **Faktisk kostnad**.

8. **Upplösning**

    Du kan använda Upplösning för att välja tidsupplösning inom det valda datumintervallet. Tidsupplösningen bestämmer hur enheter visas i rapporten. Till exempel:
    - Varje dag
    - Varje vecka
    - Månadsvis
    - Varje kvartal
    - Årsvis

9. **Allokeringsregler**

    Använd allokeringsregler för att tillämpa eller inaktivera omberäkningen av kostnadsallokering. Du kan aktivera eller inaktivera omberäkningen av kostnadsallokering för faktureringsdata. Omberäkningen gäller för de valda kategorierna i rapporten. Det gör att du kan utvärdera hur omberäkningen av kostnadsallokering påverkar faktureringsdata (rådata).

10. **Ej kategoriserade**

    Använd Ej kategoriserade om du vill ta med eller undanta ej kategoriserade kostnader i rapporten.

11. **Visa/dölj fält**

    Alternativet Visa/dölj har ingen effekt i rapporterna.

12. **Visningsformat**

    Använd visningsformat för att välja olika diagram- eller tabellvyer.

    ![Symboler för visningsformat som du kan välja](./media/understanding-cost-reports/display-formats.png)

13. **Flera färger**

    Använd flera färger för att välja färg på diagram i rapporten.

14. **Åtgärder**

    Använd åtgärder för att spara, exportera eller schemalägga rapporten.

15. **Princip**

    Även om de inte är avbildade, innehåller vissa rapporter en beräkningsprincip för planerad kostnad. I dessa rapporter visar **Consolidated** (Konsoliderat) rekommendationer för alla konton och prenumerationer under den aktuella entiteten, till exempel Microsoft-registrering eller AWS-betalare. Principen **Standalone** (Fristående) visar rekommendationer för ett konto eller en prenumeration som om det inte finns några andra prenumerationer. Vilken princip du väljer beror på vilken optimeringsstrategi din organisation har. Kostnadsberäkningar baseras på användningen de senaste 30 dagarna.

## <a name="save-and-schedule-reports"></a>Spara och schemalägga rapporter

När du har skapat en rapport kan du spara den för framtida användning. Sparade rapporter är tillgängliga i **Mina verktyg** > **Mina rapporter**. Om du gör ändringar i en befintlig rapport och sparar den, sparas rapporten som en ny version. Eller så kan du spara den som en ny rapport.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Spara en rapport till Cloudyn-portalen

När du visar en rapport kan du klicka på **Actions** (Åtgärder) och sedan välja**Save to my reports** (Spara till mina rapporter). Ge rapporten ett namn och lägg sedan till din egen URL eller använd den URL som skapats automatiskt. Du kan också **dela** rapporten offentligt med andra i organisationen eller dela den med din entitet. Om du inte delar rapporten förblir den en personlig rapport som bara du kan se. Spara rapporten.


### <a name="save-a-report-to-cloud-provider-storage"></a>Spara en rapport i molnleverantörslagringen

För att kunna spara en rapport till molntjänstleverantören måste du redan ha konfigurerat ett lagringskonto. När du visar en rapport klickar du på **Actions** (Åtgärder) och väljer **Schedule report** (Schemalägg rapport). Ge rapporten ett namn och lägg sedan till din egen URL eller använd den URL som skapats automatiskt. Välj **Save to storage** (Spara till lagring) och välj sedan lagringskontot, eller lägg till ett nytt. Ange ett prefix som läggs till i rapportfilnamnet. Välj CSV eller JSON som filformat och spara sedan rapporten.

### <a name="schedule-a-report"></a>Schemalägga en rapport

Du kan köra rapporter med schemalagda intervall och du kan skicka dem till en mottagarlista eller en molntjänstleverantörs lagringskonto. När du visar en rapport klickar du på **Actions** (Åtgärder) och väljer **Schedule report** (Schemalägg rapport). Du kan skicka rapporten via e-post och spara den till ett lagringskonto. Under **Schemalägg** väljer du intervallet (varje dag, varje vecka eller varje månad). För varje vecka och varje månad väljer du den dag eller de datum då leveransen ska ske och en tid. Spara den schemalagda rapporten. Om du väljer Excel-rapportformatet skickas rapporten som en bifogad fil. När du väljer format för e-postinnehåll levereras rapportresultat som visas i diagramformat som en graf.

### <a name="export-a-report-as-a-csv-file"></a>Exportera en rapport som en CSV-fil

När du visar en rapport klickar du på **Actions** (Åtgärder) och väljer **Export all report data** (Exportera alla rapportdata). Ett popup-fönster visas och en CSV-fil laddas ned.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de rapporter som ingår i Cloudyn på [Använda Cloudyn-rapporter](../../cost-management/use-reports.md).
- Lär dig mer om hur du använder rapporter för att skapa [instrumentpaneler](../../cost-management/dashboards.md).

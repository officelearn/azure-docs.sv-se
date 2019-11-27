---
title: Använda Cloudyn-rapporter i Azure | Microsoft Docs
description: Den här artikeln beskriver syftet med Cloudyn-rapporter som ingår i Cloudyn-portalen när du använder dem effektivt.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: f838091f4b4cdcb0535f58926a724074ef080463
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218860"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Rapporter som är tillgängliga i Cloudyn-portalen

Den här artikeln beskriver syftet med Cloudyn-rapporter som ingår i Cloudyn-portalen. Här beskrivs också hur du effektivt kan använda rapporterna. De flesta rapporter är intuitivt och har ett enhetligt utseende. De flesta av de åtgärder som du kan göra i en rapport, kan du också göra i andra rapporter. En översikt över hur du använder Cloudyn-rapporter, inklusive hur du anpassar och sparar eller schemalägger rapporter, finns i [förstå kostnads rapporter](understanding-cost-reports.md).

Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Mer information finns i [Azure Cost Management](overview-cost-mgt.md).

## <a name="report-types"></a>Rapporttyper

Det finns tre typer av Cloudyn-rapporter:

- Tidrapporter över. Till exempel rapporten Cost Over Time. Tidrapporter över visar en tidsserie med data under en vald period med en fördefinierad upplösning och visa en veckovis upplösning för de senaste två månaderna. Du kan använda gruppering och filtrering för att zooma in på olika datapunkter.
  - Tidrapporter över kan du visa trender och identifiera toppar och avvikelser.
- Analysrapporter. Till exempel rapporten kostnads analys. De här rapporterna visar aggregerade data under en period som du definierar och Tillåt gruppering och filtrering av data.
  - Med hjälp av rapporter kan du visa toppar och fastställa rotorsaker för avvikelseidentifiering Analysis och att visa dig en detaljerad kraschar av dina data.
- Tabular rapporter. Du kan visa en rapport som en tabell, men vissa rapporter visas endast som en tabell. De här rapporterna ger dig detaljerad listor med objekt.
  - Rekommendationer är tabular rapporter – det finns inga visualiseringar rekommendationer. Dock kan du visualisera rekommendation resultat. Till exempel besparingar över tid.
  - Tabular rapporter är användbara som listor över åtgärder eller för export av data för vidare bearbetning. Till exempel en chargeback-rapporten.

Kostnads rapporter visar antingen _faktiska_ eller _periodiserade_ kostnader.

Verklig kostnad-rapporter visar betalningar under den valda tidsperioden. Till exempel visas alla engångsavgifter som köp av reserverade instanser (RI) i verklig kostnad-rapporter som toppar i kostnad.

Amorterad kostnadsrapporter sprider engångsavgifter under en period som de gäller. Engångsavgifter för RI-köp är fördelade över reservationsperioden och visas inte som en topp. Den amorterade vyn är det enda sättet att visa SANT trender och göra kostnadsprognoser.

I vissa fall kan visas amorteringen som en separat rapport. Exempel är kostnadsanalys och Amorterad kostnadsanalys rapporter. I andra fall är amorteringen en princip för rapporten, till exempel kostnadsallokering och Cost Analysis-rapporter.

Du kan schemalägga en rapport för periodiska leverans. Kostnad rapporter kan ange ett tröskelvärde, så att de kan användas för aviseringar.

## <a name="cost-analysis-vs-cost-allocation"></a>Kostnadsanalys jämfört med kostnadsallokering

_Kostnads analys_ rapporter visar fakturerings data från dina moln leverantörer. Med rapporterna kan du gruppera och öka detaljnivån i olika datasegment uppdelat från filen fakturering. Rapporterna möjliggör detaljerad kostnaden navigering i molnet leverantörens fakturering rådata.

Vissa _kostnads analys_ rapporter grupperar inte kostnader efter resurs taggar. Och tagg-baserad fakturerings information visas bara i rapporter efter att du har allokerat kostnader genom att skapa en kostnads modell med hjälp av [kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

_Kostnads fördelnings_ rapporter är tillgängliga när du har skapat en kostnads modell med hjälp av [kostnads fördelning 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Cloudyn bearbetar kostnader och fakturerings data och _matchar_ data med användnings-och märkes data för dina moln konton. Om du vill matcha data kräver åtkomst till användningsdata i Cloudyn. Om du har konton som saknar autentiseringsuppgifter är de märkta som _Okategoriserade-resurser_.

## <a name="dashboards"></a>Instrumentpaneler

Instrumentpaneler i Cloudy ger en översikt över rapporter. Instrumentpaneler består av widgetar och varje widget är i stort sett miniatyrbilden för en rapport. När du [anpassar rapporter](understanding-cost-reports.md#save-and-schedule-reports)sparar du dem till Mina rapporter och de läggs till på instrument panelen. Mer information om instrument paneler finns i [Visa viktiga kostnads mått med instrument paneler](dashboards.md).

## <a name="budget-information-in-reports"></a>Budgetinformation i rapporter

Många Cloudyn-rapporter visar budgetinformation när du har skapat en manuellt. Så att rapporter inte visar budgetinformation förrän du har skapat en budget. Mer information finns i [Inställningar för budget hantering](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Rapporter och rapporteringsfunktioner

Cloudyn innehåller följande rapporter och rapporterings funktioner.

### <a name="cost-navigator-report"></a>Kostnad Navigator-rapport

Kostnaden Navigator rapporten är ett snabbt sätt att visa dina fakturering användningen med hjälp av en instrumentpanelsvy. Den har en delmängd av filter och grundläggande vyer som ska visas omedelbart en sammanfattande vy över organisationens kostnader. Kostnader visas efter datum. Eftersom rapporten är avsett som en inledande vy över dina kostnader, det är inte lika flexibla eller så omfattande som många andra rapporter eller anpassade instrumentpaneler som du skapar själv.

Som standard visar större vyer i rapporten:

- Kostnad över tid som visar en arbetsvecka liggande diagramvy. Du kan ändra datum **intervallet** för att ändra stapeldiagram för datum intervall.
- Utgifter av tjänsten, använda ett cirkeldiagram.
- Resursen kategorisering efter taggar, använda ett cirkeldiagram.
- Utgifter av kostnadsenheter, använda ett cirkeldiagram.
- Kostnad Totalt per datum i en listvy.

### <a name="cost-analysis-report"></a>Cost Analysis-rapport

Cost Analysis-rapporten är en beräkning av showback och chargeback, baserat på din princip. Sammanställer den dina molnförbrukning under en vald tidsperiod efter att ha gjort alla allokeringsregler till dina kostnader. Exempelvis den beräknas kostnader efter taggar, omtilldelas kostnaderna för ej taggade resurser och allokerar du kan också användningen av reserverade instanser.

De principer som angetts i [kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) används i kostnads analys rapporten och resultaten kombineras sedan med information från din moln leverantörs rå data.

Hur beräknas den här rapporten? Cloudyn-tjänsten säkerställer att allokeringen behåller integriteten för varje länkat konto genom att använda _konto tillhörighet_. Det innebär ett konto som inte använder en specifik tjänst inte har några kostnader för den här tjänsten som allokeras till den. Kostnader som ackumuleras i att kontot finns kvar i det kontot och beräknas inte av allokeringsprinciper för. Du kan till exempel ha fem länkade konton. Om det bara tre av dem använder storage-tjänster kan tilldelas endast kostnaden för lagringstjänster över taggar i de tre kontona.

Använda Cost Analysis-rapporten för att:

- Beräkna din organisation återbetalning/showback
- Kategorisera dina kostnader
- Visa en sammansatt vy av hela distributionen för en viss tidsram.
- Visa kostnaderna genom att tagga kategorier baserat på principer som skapas i kostnadsmodellen.

Använda Cost Analysis-rapport:

1. Välj ett datumintervall.
2. Lägg till taggar, efter behov.
3. Lägg till grupper.
4. Välj en kostnadsmodell du skapade tidigare.

### <a name="cost-over-time-report"></a>Rapport för kostnad över tid

Kostnad över tid rapporten visas resultatet av en kostnadsallokering som tidsserier. Det kan du se trender och identifiera fel i din distribution. Den visar i stort sett kostnader fördelas på en definierad period. Rapporten innehåller dina viktigaste kostnadsfaktorer inklusive löpande kostnader och avgifter för en reserverad instans som används under en vald tidsperiod. Principer som angetts i [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) används i den här rapporten.

Du kan använda rapporten Cost Over Time till:

- Se ändringar över tid och vilken påverkan ändras från en dag (eller ett intervall) till nästa.
- Analysera kostnaderna med tiden för en specifik instans.
- Förstå varför ökade kostnader för en specifik instans.

Använda Cost Over Time-rapport:

1. Välj ett datumintervall.
2. Lägg till taggar, efter behov.
3. Lägg till grupper.
4. Välj en kostnadsmodell du skapade tidigare.
5. Välj faktiska kostnader eller amorterade kostnader.
6. Välj om du vill tillämpa allokeringsregler på Visa rådata fakturering datavyn eller att omberäknas kostnaden vy.

### <a name="actual-cost-analysis-report"></a>Faktiska Cost Analysis-rapporten

Rapporten analys av faktiska kostnader visar providern kostnaderna utan några ändringar. Den visar dina huvudsakliga kostnadsfaktorer, inklusive löpande kostnader och engångsavgifter.

Du kan använda rapporten för att visa information om kostnaden för dina prenumerationer. I rapporten visas Azure-prenumerationer som **konto namn** och **konto nummer**. **Länkade konton** visar AWS-prenumerationer. Om du vill visa per prenumerations kostnad, en uppdelning för varje konto under **grupper**, väljer du den typ av prenumeration du har.

Använd rapporten analys av faktiska kostnader för att:

- Analysera och övervaka raw provider-kostnader som har använt under en angiven tidsperiod.
- Schemalägga en avisering om tröskelvärdet.
- Analysera oförändrad kostnader dina konton och entiteter.

### <a name="actual-cost-over-time-report"></a>Faktisk kostnad över tid-rapport

Rapporten Actual Cost Over Time är en standard kostnaden analysrapporten distribuerar kostnad över en angiven tidpunkt-lösning. Rapporten visar utgifter över tid att se trender och identifiera utgiftsgränsen oegentligheter. Den här rapporten visar dina huvudsakliga kostnadsfaktorer inklusive löpande kostnader och avgifter för en reserverad instans som används under en vald tidsperiod.

Du kan använda rapporten Actual Cost Over Time till:

- Se kostnadstrender över tid.
- Hitta oegentligheter i kostnad.
- Hitta alla cost-relaterade frågor som rör molnleverantörer.

### <a name="amortized-cost-reports"></a>Amorterad kostnad-rapporter

Den här uppsättningen amorterad kostnad rapporter visar linjär icke-användningen baserat serviceavgifter eller betalas engångskostnad och sprida kostnad över tid jämnt under deras livslängd. Till exempel kan engångsavgifter innehålla:

- Årlig supportavgifter
- Årlig security komponenten avgifter
- Avgifter för köp av reserverade instanser
- Vissa Azure Marketplace-objekt

I filen fakturering engångsavgifter kännetecknas när tjänsteförbrukning börja och sluta datum (tidsstämpel) har samma värden. Cloudyn-tjänsten identifierar dem sedan som engångsavgifter som är amorterad. Andra förbrukningsbaserad tjänster med kostnader för på begäran är inte amorterad.

Amorterad kostnadsrapporter är:

- Amorterad kostnadsanalys
- Amorterad kostnad över tid

### <a name="cost-analysis-report"></a>Cost Analysis-rapport

Rapporten Cost Analysis ger inblick i dina molnförbrukning och utgifter under en vald tidsperiod. De principer som angetts i [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) används i rapporten kostnads analys.

Hur beräknar den här rapporten i Cloudyn?

Cloudyn säkerställer att fördelningen behåller integriteten för varje länkat konto genom att använda _konto tillhörighet_. Det innebär också inte har några kostnader för den här tjänsten som allokeras till den i ett konto som inte använder en specifik tjänst. Kostnader som ackumuleras i att kontot finns kvar i det kontot och inte beräknas genom att allokeringsprinciper. Du kan till exempel ha fem länkade konton. Om det bara tre av dem använder storage-tjänster kan tilldelas endast kostnaden för lagringstjänster över taggar i de tre kontona.

Använda Cost Analysis-rapporten för att:

- Visa en sammansatt vy av hela distributionen för en viss tidsram.
- Visa kostnaderna genom att tagga kategorier baserat på principer som skapas i kostnadsmodellen.

### <a name="cost-over-time-report"></a>Rapport för kostnad över tid

Rapport för kostnad över tid visar utgifter över tid så att du kan upptäcka trender och Observera fel i distributionen. Den visar i stort sett kostnader fördelas på en definierad period. Rapporten innehåller dina viktigaste kostnadsfaktorer inklusive löpande kostnader och avgifter för en reserverad instans som används under en vald tidsperiod. Principer som angetts i [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) används i den här rapporten.

Du kan använda rapporten Cost Over Time till:

- Se ändringar över tid och vilken påverkan ändras från en dag (eller ett intervall) till nästa.
- Analysera kostnaderna med tiden för en specifik instans.
- Förstå varför ökade kostnader för en specifik instans.

### <a name="custom-charges-report"></a>Anpassad avgifter rapport

Företags- och CSP användarna ständigt ofta tillhandahåller har lagts till tjänster till sina externa eller interna kunder, förutom sina egna molnet resursförbrukning. Du kan definiera anpassade avgifter för tjänster som har lagts till eller rabatter som läggs till kundens fakturerings- eller chargeback-rapporter som anpassade radobjekt.

Anpassade tjänstavgifter återspeglar tjänster som normalt inte visas i en faktura. De anpassa tillägg som du har skapat visas sedan i kostnadsrapporter.

*Anpassade avgifter är inte anpassade priser*. Listan över anpassade avgifter visar inte olika hastigheter att du kan debitering. Till exempel visas AWS fakturering avgifter precis som de debiteras.

Skapa en anpassad kostnad:

1. I **anpassade kostnader**klickar du på **Lägg till ny**. Dialog rutan _Lägg till ny anpassad avgift_ visas.
2. I **Providernamn**anger du namnet på providern.
3. I **tjänst namn**anger du typ av tjänst.
4. I **Beskrivning**lägger du till en beskrivning för den anpassade avgiften.
5. I **typ**, anger du List rutan Välj **procent** och sedan i list rutan tjänster väljer du de tjänster som ska inkluderas som anpassade kostnader i kostnads rapporterna.
6. Under **betalning**väljer du om avgiften är en engångs avgift eller en återkommande avgift. Om tillägget är en återkommande avgift Välj Amortized om du vill att kostnaden för att vara amorterad och Välj antalet månader.
7. Om en engångs avgift har valts i **datum**, anger du det datum då avgiften betalas ut i **gällande datum**. Om återkommande avgift väljs, kan du ange datumintervallet inklusive startdatum och slutdatum för tillägget.
8. I **trädet entiteter**väljer du de entiteter som du vill tillämpa avgiften på och väljer sedan **på**.

_När avgifter tilldelas till en entitet kan användarna inte ändra dem. Avgifter som läggs till av en administratör i en överordnad entitet är skrivskyddade._

Visa anpassade avgifter:

Anpassade avgifter visas i kostnadsrapporter. Öppna till exempel rapporten verklig kostnads analys och välj **fristående**under **utökade filter**. Filtrera sedan för att visa **anpassade kostnader**.

### <a name="cost-allocation-360"></a>Kostnadsallokering 360

Du kan använda Cost Allocation 360 för att skapa anpassade allokering modeller för att tilldela kostnader till förbrukade molnresurser. Många rapporter visar information från anpassade modeller som du har skapat med anpassade modeller. Och vissa rapporter visar bara information när du har skapat en anpassad kostnadsmodell med kostnadsallokering.

Mer information om hur du skapar anpassade kostnads modeller finns i [Självstudier: hantera kostnader med hjälp av Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Rapporten kostnad kontra budget över tid

Med rapporten kostnad kontra budget över tid kan du jämföra de främsta kostnads bidragen mot din budget. Tilldelade budgeten visas i rapporten så att du kan visa förbrukning (över/under/par) budget över tid. Med Visa/dölj fält överst i rapporten kan välja du att visa kostnader, budget, ackumulerade kostnaden och total budget.

### <a name="current-month-projected-cost-report"></a>Den aktuella Month Projected Cost rapporten

Rapporten Current Month Projected Cost ger inblick i den aktuella månad hittills kostnaden sammanfattning. Den här rapporten visar kostnaderna från början av månaden, från den föregående månaden, och det totala antalet beräknad kostnad för den aktuella månaden. Den aktuella månad projected kostnaden beräknas som summan av den uppdatera månadskostnaden och en projektion baserat på vägens kostnad som övervakas i de senaste 30 dagarna.

Du kan använda rapporten Current Month Projected Cost till:

- Projektet månatliga kostnader efter tjänst
- Projektet månatliga kostnader per konto

### <a name="annual-projected-cost-report"></a>I rapporten Annual Projected Cost

Rapporten årliga beräknade kostnader kan du visa årliga beräknade kostnader baserat på föregående utgiftstrender. Den visar de närmsta 12 månaderna av övergripande beräknade kostnader. Projektionerna görs med hjälp av en trend funktion extrapolerade kommande 12 månader, utifrån kostnaderna knutna till de senaste 30 dagarna användning.

### <a name="budget-management-settings"></a>Inställningar för budget

Budgetstyrning kan du ange en budget för räkenskapsåret.

Lägga till en budget i en entitet:

1. På sidan budget hantering under **entiteter**väljer du den entitet där du vill skapa budgeten.
2. I året, väljer du det året där du vill skapa budgeten.
3. I varje månad ställer du in din budget och klickar sedan på **Spara**.

Importera en fil för årlig budget:

1. Under **åtgärder**väljer du **Exportera** för att ladda ned en tom CSV-mall som ska användas som grund för budgeten.
2. Fyll i CSV-filen med din budgetposter och spara den lokalt.
3. Under **åtgärder**väljer du **Importera**.
4. Välj den sparade filen och klicka sedan på **OK**.

Om du vill exportera din slutförda budget som en CSV-fil väljer du **Exportera** i **åtgärder**för att ladda ned filen.

När du är klar visas din budget i Cost Analysis-rapporter och i rapporten kostnad kontra budget över tid. Du kan även schemalägga rapporter baserat på budget tröskelvärden.

### <a name="azure-resource-explorer-report"></a>Azure Resource Explorer-rapport

Azure Resource Explorer rapporten visar en massinläsning lista över alla Azure-resurser tillgängliga i Cloudyn. För att effektivt använda rapporten, måste dina Azure-konton har utökat mått som är aktiverad. Utökade mått ge Cloudyn-åtkomst till virtuella datorer i Azure. Mer information finns i [lägga till utökade mått för virtuella Azure-datorer](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Rapport för Azure-resurser över tid

Azure-resurser över tid rapporten visar en sammanfattning av alla resurser som körs under en viss period. För att effektivt använda rapporten, måste dina Azure-konton har utökat mått som är aktiverad. Utökade mått ge Cloudyn-åtkomst till virtuella datorer i Azure. Mer information finns i [lägga till utökade mått för virtuella Azure-datorer](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Instans Explorer rapport

Instans Explorer rapporten används för att visa olika mått för tillgångar för dina virtuella datorer. Du kan gå till specifika instanser om du vill visa information som:
- -Instans som körs intervall
- Livscykel i den valda perioden
- CPU-användning
- Indata för nätverk
- Utgående trafik
- Aktiva diskar

Instans Explorer rapporten samlar in alla pågående intervall definierade intervallet och sammanställer data i enlighet med detta. Om du vill visa var och en av de pågående intervallen datumintervallet, expandera instansen. Kostnaden för varje instans beräknas för intervallet valda baserat på AWS och priser för Azure lista datumet. Inga rabatter. Du kan lägga till ytterligare fält i rapporten med hjälp av Visa/dölj fält.

Använd instans Explorer rapporten för att:

- Beräkna den uppskattade kostnaden per dator.
- Skapa en fullständig lista, inklusive aggregeras timmar, körning av alla datorer som var aktiva under ett tidsintervall.
- Skapa en lista med molntjänstleverantören eller -konto.
- Visa datorer som skapas eller upp under ett tidsintervall.
- Visa alla datorer som för närvarande stoppats.
- Visa taggar för varje dator.

### <a name="instances-over-time-report"></a>Instanser över tid rapport

Med rapporten instanser över tid kan visa du det maximala antalet datorer som var aktiva var under det valda tidsintervallet. Om den definierade upplösningen är per vecka eller månad, är resultatet det maximala antalet datorer som är aktiva på en viss dag under den månaden. Välj ett datumintervall att välja de filter som du vill visa i rapporten.

### <a name="instance-utilization-over-time-report"></a>Rapport för instans-användning över tid

Den här rapporten visar en sammanfattning av CPU eller minne används med tiden för alla instanser.

### <a name="compute-power-cost-over-time-report"></a>Compute Power Cost Over Time-rapport

Compute Power över tid-rapporten innehåller en detaljerad analys av beräkningskraft över ett visst datumintervall. Andra rapporter visas antalet körda datorer eller runtime-timmar, visar den här rapporten kärntimmar, under eller GB RAM-timmar.

Använd rapporten för att:

- Kontrollera beräkningskraft inom ett visst datumintervall.
- Visa beräkna tider baserat på tilldelning modeller.

Den här rapporten är länkad till din [kostnads fördelning 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) -policys, så resultatet visas baserat på de definierade taggarna och principerna den valda kostnads principen. När du inte har en princip som skapas kan visas inte resultat.

### <a name="compute-power-average-cost-over-time-report"></a>Compute Power genomsnittlig kostnad över tid rapport

Du kan använda rapporten Compute Power genomsnittlig kostnad över tid för att visa mer än bara kostnaden för varje dator som körs. Rapporten visar dina genomsnittlig kostnad instans timme, kärntimme, enhetstimme för beräkning och GB RAM-timme. Rapporten ger inblick i effektiviteten för din distribution.

Den här rapporten är länkad till din [kostnads fördelning 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) -policys, så resultatet visas baserat på de definierade taggarna och principerna den valda kostnads principen. När du inte har en princip som skapas kan visas inte resultat.

### <a name="s3-cost-over-time-report"></a>S3 Cost Over Time-rapport

S3 Cost Over Time-rapporten innehåller en detaljerad analys av kostnader för Amazon Simple Storage Service (S3) per bucket med tiden för en angiven tid. Rapporten hjälper dig att hitta de behållare som är ditt huvudsakliga kostnaden drivrutiner och den visar trender i dina S3 användning och utgifter.

### <a name="s3-distribution-of-cost-report"></a>S3 Distribution av kostnadsrapporten

Du kan använda rapporten för att analysera S3-kostnaden för den senaste månaden av bucket och storage-klassen. Du kan använda vyn cirkeldiagram för att ange tröskelvärde för insyn. Eller så kan du använda tabellvyn för att visa delsummor.

### <a name="s3-bucket-properties-report"></a>Rapport över egenskaper för S3-Bucket

Du kan använda rapporten för att visa egenskaperna för S3-bucket. Du kan använda vyn cirkeldiagram för att ange tröskelvärde för insyn. Eller så kan du använda tabellvyn för att visa delsummor.

### <a name="rds-instances-over-time-report"></a>Rapporten RDS instanser över tid

Du kan använda rapporten för att visa en sammanställning av alla Amazon Relational Database Service (RDS)-instanser som körs under den angivna perioden.

### <a name="rds-active-instances-report"></a>RDS aktiva instanser rapport

Använd rapporten för att analysera RDS aktiva instanser. Expandera radobjekt för att visa ytterligare information i rapporten.

### <a name="azure-reserved-instances-report"></a>Rapport för Azure reserverade instanser

Reserverade instanser för Azure-rapporten ger en enda vy över alla dina Azure reserverade instanser. Den här rapporten visar varje köp som har sin egen radobjekt. Rapporten visar även information om det köpet, till exempel det konto som har köpt den typ av köp och instanstyp, dagar kvar och så vidare. Du kan visa eller Dölj rapportdata med hjälp av Visa/dölj fält.

Använd reserverade instanser för Azure-rapporten för att visa:

- En lista över alla reservationer efter köp datum.
- Tid kvar tills RI upphör att gälla.
- Engångsavgifter.
- Det konto som har köpt reserverade instanser, och när.

### <a name="aws-reserved-instances-report"></a>Reserverade AWS-instanser rapport

Reserverade AWS-instanser rapporten ger dig med en enda vy över alla AWS reserverade instanser. Den här rapporten visar varje köp som är en egen radobjekt och information om det köpet, till exempel det konto som har köpt den typ av köp och instanstyp, dagar kvar och så vidare. Du kan visa eller Dölj rapportdata med hjälp av Visa/dölj fält.

Du kan använda rapporten reserverade AWS-instanser för att visa:

- En lista över alla reservationer efter köp datum.
- Tid kvar tills RI upphör att gälla.
- Engångsavgifter.
- Ursprungliga inköps-ID (reservations-ID).
- Det konto som har köpt reserverade instanser och när.

### <a name="ec2-ri-buying-recommendations-report"></a>EC2 RI Buying Recommendations rapport

Grunden för molnet resursförbrukning är den på begäran-modellen, där resurser resultera i kostnader när de används. Det finns inga direkta åtaganden – du betalar bara för det du använder, när du använder den.

AWS som ett alternativ prismodellen för dess elastiska Compute (EC2) molntjänster – reserverade instanser (RI). Den här prismodellen garanterar användare kapaciteten när de behöver den för hela den reserverade VM-instansen. Den reserverade VM-Instansen erbjuder betydande prisrabatter över priser på begäran. I utbyte kan göra användare ett krav på åtagande för användning av en virtuell instans. Åtagandet är bunden till en specifik familj, storlek, tillgänglighetszon (AZ) och operativsystem, under åtagande (ett eller tre år). Den reserverade VM-Instansen kan AWS att effektivt planera framtida kapaciteten, samt att få kunden åtagande till dess tjänster.

Tre betalningsalternativ för Fjärrinstallationstjänster som är alla krav:

- Massinläsning summan i dag 0, erbjuder de högsta rabatterna
- Inte förskott - erbjuder de lägsta rabatterna i som kostnaden för RI ställs i månaden under den reserverade VM-instansen.
- Partiellt startavgift, vilka ¼ - ½ av priset betalas direkt, och övriga månatliga avbetalningar med en diskonteringsränta som är lägre, Stäng men genom att betala för alla frekvensen

Cloudyn utvärderar drifttid för varje dator under de senaste 30 dagarna. Cloudyn rekommenderar att köpa reserverade instanser när det är mer kostnadseffektivt att köra datorn med en RI på den aktuella nivån drifttid.

Rapporten visar anledningen för dess rekommendationer för att spara de flesta pengar under året. Rekommendationerna föreslår ersätta på begäran-instanser med reserverade VM-instanser. Du kan köpa reserverade instanser direkt från rapporten.

Varje flik öppnas som en fullständig rapport. Viktiga avsnitt på flikarna finns:

- **EC2 RI-köp påverkan** – det här avsnittet innehåller en simulering av skillnaden mellan på begäran och reserverade instanser. Klicka på **Zooma in**för att se hela EC2 RI-rapporten med de filter som redan definierats för din rekommendation. Den här rapporten visar köp effekten av alla potentiella RI-köp. Du kan justera den förväntade tillgänglighet för att se potentialen sparas när du köper reserverade EC2-instanser.

- **Spara analys** – det här avsnittet innehåller de potentiella besparingarna och den månad som besparingarna är verkliga vid följande Cloudyn-rekommendationer. Faktiska besparingar och procent sparas i rött.

- **EC2 RI-typ jämförelse** – det här avsnittet betonar ROI-högdagrarna för Cloudyns rekommenderade distribution, inklusive alla relevanta alternativ. Resultaten i den här rapporten förutsätter att datorn körs på 100% drifttid. Klicka på **Zooma in** för att öppna den detaljerade rapporten.

- **Instanser över tid** – det här avsnittet visar en analys av alla instanser som är associerade med rekommendationen, OnDemand, reserverade instanser och dekor. Klicka på **Zooma in** för att öppna den detaljerade rapporten.
- **Breakeven Points** – i det här avsnittet visas en tabell med alla möjliga rekommenderade distributioner och ROI och månad då ROI uppstår. Klicka på **Zooma in** för att öppna den detaljerade rapporten.

### <a name="ec2-reservations-over-time-report"></a>Rapporten EC2 reservationer över tid

Rapporten EC2 reservationer över tid spårar statusen för din användning av dina köpta reserverade EC2-instanser. Du kan ange av lösningen på rapporten till timme, dag eller vecka.

Använd rapporten för att:

- Visa reservationer som har köpt som används och inte används.
- Öka detaljnivån till lösningen per timme att visa RI-användning per timme.

### <a name="savings-over-time-report"></a>Besparingar över tid rapport

Rapporten besparingar över tid används för att visa besparing som uppnås med hjälp av reserverade instanser samt upptäcka instanser. Rapporten visar ROI uppnås med tiden som härrör från RI-köp.

Om du vill visa besparingar från Fjärrinstallationstjänster grupperar du resultaten efter **pris modell** och väljer **reservation**. Om du vill visa RI-besparingar uppnåddes av ett visst konto eller instanstyp, lägger du till relevanta gruppering och filter för typ av konto eller instansen.

Om du vill se besparingar från användning av en dekor instans filtrerar du **pris modellen** till **dekor**. Standardfiltret för den här rapporten är RI och Spot-instanser.

### <a name="rds-ri-buying-recommendations-report"></a>RDS RI Buying Recommendations rapport

RDS RI Buying Recommendations rapporten rekommenderar när du ska använda RDS reserverade instanser i stället för på begäran-instanser.

Varje flik öppnas som en fullständig rapport. Viktiga avsnitt på flikarna finns:

- **Fjärran RI-köp påverkan** – det här avsnittet innehåller en simulering av skillnaden mellan på begäran jämfört med reserverade instanser. Klicka på **Zooma in** för att se hela rapporten fjärr skrivbords tjänster för inköps påverkan med de filter som redan definierats för din rekommendation. Den här rapporten kan du se köp effekten av alla potentiella RI-köp.  Du kan justera förväntade genomsnittliga drifttid och se potentialen sparar genom att köpa reserverade instanser.
- **Spara analys** – det här avsnittet innehåller de potentiella besparingarna och den månad som besparingarna är verkliga vid följande Cloudyn-rekommendationer. Faktiska besparingar och procent sparas i rött.

- **RDS-typ jämförelse** – det här avsnittet betonar ROI-högdagrarna för den rekommenderade distributionen, inklusive alla relevanta alternativ. Resultaten i den här rapporten förutsätter att datorn körs på 100% drifttid. Klicka på **Zooma in** för att öppna den detaljerade rapporten för den valda datorn.
- **Instanser över tid** – i det här avsnittet visas en analys av alla instanser som är associerade med rekommendationen, OnDemand, reserverade instanser och dekor. Klicka på **Zooma in** för att öppna den detaljerade rapporten.

- **Breakeven Points** – i det här avsnittet visas en tabell med alla möjliga rekommenderade distributioner och ROI och månaden när ROI uppstår. Klicka på **Zooma in** för att öppna den detaljerade rapporten.

### <a name="rds-reservations-over-time-report"></a>Rapporten RDS reservationer över tid

Rapporten RDS Reservation över tid används för att visa en detaljerad analys av båda dina används och oanvända reservationer under den angivna perioden.

### <a name="reserved-instance-purchase-impact-report"></a>Reserverad instans effektrapporten

Rapporten EC2 RI Purchase Impact kan du simulera kostnad för reserverad instans och på begäran kostnad över tid. Det kan hjälpa dig fatta bättre inköp. Justera filter som genomsnittliga körningstiden, termen, plattform och andra för att fatta välgrundade beslut när du ska välja RI-köp.

### <a name="cost-effective-sizing-recommendations-report"></a>Kostnadseffektiv Storleksrekommendationer rapport

Kostnadseffektiv Storleksrekommendationer-rapporten innehåller resultat för AWS och Azure. Dina RI-köp beaktas för AWS-användare, och resultaten omfattar inte datorer som körs som reserverade VM-Instanser. Den här rapporten innehåller en lista över underutnyttjade instanser som lämpar sig lågsäsong. Rekommendationerna baseras på användning och prestanda data från de senaste 30 dagarna. I varje rekommendation är en lista med kandidater lågsäsong anledningen lågsäsong och en länk för att visa fullständig information och prestandamått för instansen. Och när du ändrar till nyare generation instanstyper rekommenderar att relevanta rekommendationer.

Du kan inte hämta listan över instans-ID som rekommenderas lågsäsong i den här rapporten. Använda rapporten alla Storleksrekommendationer för att hämta instans-ID: N.

Överväg följande downsizing exempel:

Du har sex m3.xlarge instanser som körs. Cloudyn analys visar att fem av dem har låg CPU-användning. Överväg att downsizing dem.

I inverkan på kostnaden beräknas kostnaden effekten. I det här exemplet kan genom att expandera radartikeln, du se aktuella priset för en m3.xlarge instans (Linux/Unix) kostar $0.266 per timme och en m3.large instanskostnader (Linux/Unix) $0.133 per timme. Därför är årlig kostnad $11,651 för fem m3.xlarge instanser som körs på 100% användning. Årlig kostnad är $5,825 för fem m3.large instanser som körs på 100% användning. Potentiella besparingar är $5,825.

Klicka på om du vill visa kostnadseffektiv storleksändring berättiganden + för att expandera raden. I **information**:

- Avsnittet om **rekommendations justering** visar den aktuella distributionen och antalet instanser som rekommenderas att downsize.
- I avsnittet **kostnads påverkan** visas beräkningen som används för att fastställa potentiella besparingar.
- I avsnittet **möjliga årliga besparingar** visas de potentiella årliga besparingarna när downsizing per Cloudyn är rekommendationer.

### <a name="all-sizing-recommendations-report"></a>Alla Storleksrekommendationer rapport

Den här rapporten innehåller en lista över underutnyttjade instanser som lämpar sig lågsäsong. Rekommendationerna baseras på användning och prestanda data från de senaste 30 dagarna. Du kan visa fullständig information och prestandamått för instansen i varje rekommendation.

Om du har köpt reserverade AWS-instanser i den här rapporten innehåller resultat för alla instanser som körs, inklusive instanser som körs som reserverade instanser.

Använd alla Storleksrekommendationer rapporten för att:

- Se en lista över alla instanser som lämpar sig lågsäsong.
- Exportera en rapportlista som innehåller namn och ID: N.

Om du vill visa rekommendations information för en angiven instans klickar du på **+** för att expandera detaljerna. Information om rekommendationen-avsnittet innehåller en översikt över rekommendationen.

Avsnittet **taggar** innehåller en lista över taggarnas nycklar och värden för den valda instansen. Använd taggar i det vänstra fönstret för att filtrera avsnittet.

Avsnittet **processor användning** tillhandahåller processor användningen för instansen under den senaste månaden, per dag.

Klicka på diagrammet för att öka detaljnivån och öppna instans CPU över tid rapporten för att se en specifikation av instanserna.

- Använd **Visa/Dölj fält** för att lägga till eller ta bort fält: tidsstämpel, AVG CPU, min CPU, Max CPU.
- Använd **datum intervall** för att ange ett datum eller datum intervall och öka detalj nivån i ett visst InstanceID.
- Använd **utökade filter** för att visa alla eller ett angivet instans-ID
- Öppna rapporten CPU-användning genom att klicka på **Zooma in**

Om instansen inte har övervakats i 30 dagar, visas ofullständiga data.

Avsnittet **minnes användning (GB)** innehåller information om det minne som används. För AWS-användare minne mått är inte automatiskt tillgängliga och måste läggas per instans via AWS. AWS debiterar du kan aktivera mätvärden för minne för EC2-instanser.

I avsnittet **minnes användning (%)** visas procent andelen minne som används.

I avsnittet **trafik för nätverks** trafik visas en ögonblicks bild över tiden för nätverks trafiken, genomsnittet och Max värdet för den valda instansen. Hovra över raderna som ska se vilket datum och högsta trafik för den tiden. Klicka på **Zooma in** för att öppna rapporten trafik för nätverks inström.

Avsnittet **nätverks utgående trafik** visar en ögonblicks bild av nätverks utgående trafik för den valda instansen. Hovra över raderna som ska se vilket datum och högsta trafik för den tiden. Klicka på **Zooma in** för att öppna rapporten utgående trafik i nätverket.

### <a name="instance-metrics-explorer-report"></a>Instans Metrics Explorer-rapport

Instans Metrics Explorer-rapporten innehåller molnöverskridande prestandamått per instans. Använd rapporten för att visa instanser som är över- eller outnyttjade baserat på processor, minne och nätverk tröskelmått.

Visa molnöverskridande prestanda per instans:

1. I **datum intervall**väljer du ett datum intervall som du vill visa prestanda för.
2. I **taggar**väljer du de taggar som du vill visa.
3. I **filter**väljer du de filter som du vill visa i rapporten.
4. I **utökade filter**justerar du rapport tröskelvärdena för:
    - Genomsnittlig CPU
    - Max CPU
    - Genomsnittlig minne
    - Maximalt minne
5. I **utökade filter**klickar du på **Visa** och väljer sedan vilken typ av instanser som ska visas.

Visa mått för en specifik instans över tid:

- Gå till instansen Metrics Explorer rapporten och klicka på **+** för att visa information.

### <a name="rds-sizing-recommendations-report"></a>Storleksrekommendationer för RDS-rapport

Storleksrekommendationer för RDS-rapporten innehåller Fjärrskrivbordstjänster som ändrar storlek på rekommendationer för att optimera din molnanvändning. Den innehåller en lista över underutnyttjade instanser som lämpar sig lågsäsong. Cloudyn rekommendationer baserat på data för användning och prestanda för de senaste 30 dagarna. Du kan filtrera rekommendationer efter kontonamn, Region, instanstyp och Status.

### <a name="sizing-threshold-manager-report"></a>Sizing tröskelvärdet Manager-rapport

Cloudyns inbyggda storleksrekommendationer beräknas med en avancerad algoritm för att tillhandahålla korrekta storlek förslag. Du kan justera tröskelvärdena för downsizing rekommendationer.

Justera tröskelvärdet storleksrekommendationer manuellt:

1. I storlek tröskelvärdet Manager justera följande tröskelvärden som du vill:
    - Genomsnittlig CPU %
    - Maximal processoranvändning i procent
    - Genomsnittligt minne %
    - Högsta mängd minne %
3. Klicka på **tillämpa** för att spara ändringarna.
4. Ändringarna direkt för alla rekommendationer.

Så här återställer standardtröskelvärden:

- I storleks tröskel hanterare klickar du på **Återställ standardvärden**.

### <a name="compute-instance-types-report"></a>Compute Instanstyper rapport

Använd Instanstyper rapporten för att:

- Vytyper instans av tjänsten, familj, API-namn och namn.
- Visa information om till exempel processor, ECU, RAM och nätverk.

Du kan använda **Sök** för att hitta specifika rad objekt.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du använder rapporter, inklusive hur du anpassar eller sparar och schemalägger rapporter, finns i [förstå kostnads rapporter](understanding-cost-reports.md).
- Lär dig mer om de instrument paneler som ingår i Cloudyn och hur du skapar dina egna anpassade instrument paneler finns i [Visa viktiga kostnads mått med instrument paneler](dashboards.md).

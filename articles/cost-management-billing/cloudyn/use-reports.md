---
title: Använda Cloudyn-rapporter i Azure
description: I den här artikeln beskrivs syftet med de Cloudyn-rapporter som ingår i Cloudyn-portalen för att hjälpa dig att effektivt använda dem.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 4bc68e851c4f3ba85f8ea18a91d86906e1f87887
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685229"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Rapporter som är tillgängliga i Cloudyn-portalen

I den här artikeln beskrivs syftet med de Cloudyn-rapporter som ingår i Cloudyn-portalen. Där beskrivs även hur du använder rapporterna på ett effektivt sätt. De flesta rapporter är intuitiva och har ett enhetligt utseende. De flesta av de åtgärder som du kan utföra i en rapport kan du även utföra i andra rapporter. En översikt av hur du använder Cloudyn-rapporter, inklusive hur du anpassar och sparar eller schemalägger rapporter, finns i [Förstå kostnadsrapporter](understanding-cost-reports.md).

Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Mer information finns i [Azure Cost Management](../cost-management-billing-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-types"></a>Rapporttyper

Det finns tre typer av Cloudyn-rapporter:

- Över tid-rapporter. Ett exempel är rapporter för kostnad över tid. Över tid-rapporter visar en tidsserie med data över ett valt intervall med en fördefinierad upplösning och visar en veckovis upplösning för de senaste två månaderna. Du kan använda gruppering och filtrering för att zooma in på olika datapunkter.
  - Över tid-rapporter kan hjälpa dig att se trender och upptäcka toppar eller avvikelser.
- Analysrapporter. Ett exempel är kostnadsanalysrapporter. Dessa rapporter visar aggregerade data under en period som du definierar och möjliggör gruppering och filtrering av data.
  - Analysrapporter kan hjälpa dig att se toppar och fastställa rotorsaker till avvikelser samt visa en detaljerad uppdelning av dina data.
- Tabellrapporter. Du kan visa valfri rapport som en tabell, men vissa rapporter visas endast som en tabell. De här rapporterna innehåller detaljerade listor över objekt.
  - Rekommendationer är tabellrapporter – det finns inga visualiseringar för rekommendationer. Du kan dock visualisera rekommendationsresultat. Det kan till exempel gälla besparingar över tid.
  - Tabellrapporter är användbara som listor över åtgärder eller för dataexport för vidare bearbetning. Ett exempel är återbetalningsrapporter.

Kostnadsrapporter visar antingen _faktiska_ eller _upplupna_ kostnader.

Rapporter över faktiska kostnader visar de betalningar som gjorts under den valda tidsramen. Till exempel visas alla engångsavgifter såsom RI-köp (reserverade instanser) i rapporter över faktiska kostnader som toppar i kostnad.

Rapporter över upplupna kostnader sprider engångsavgifter över en period som de gäller för. Till exempel sprids engångsavgifter för RI-inköp över reservationsperioden och visas inte som en topp. Den upplupna vyn är det enda sättet att se riktiga trender och göra kostnadsprognoser.

I vissa fall visas upplupna kostnader som en separat rapport. Exempel på detta är rapporterna för kostnadsanalys och analys av upplupna kostnader. I andra fall är upplupna kostnader en rapportprincip såsom rapporterna för kostnadsallokering och kostnadsanalys.

Du kan schemalägga valfri rapport för periodisk leverans. Kostnadsrapporter gör det möjligt att ange ett tröskelvärde och är därför användbara för aviseringar.

## <a name="cost-analysis-vs-cost-allocation"></a>Kostnadsanalys jämfört med kostnadsallokering

Rapporter för _kostnadsanalys_ visar faktureringsinformation från dina molnleverantörer. Med dessa rapporter kan du gruppera och detaljgranska i olika datasegment som redovisas från faktureringsfilen. Rapporterna möjliggör detaljerad kostnadsnavigering i din molnleverantörs råa faktureringsdata.

Vissa rapporter för _kostnadsanalys_ grupperar inte kostnader efter resurstaggar. Och taggbaserad faktureringsinformation visas endast i rapporter när du har allokerat kostnader genom att skapa en kostnadsmodell med hjälp av [Kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Rapporter för _kostnadsallokering_ är tillgängliga när du har skapat en kostnadsmodell med hjälp av [Kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Cloudyn bearbetar kostnader och faktureringsdata och _matchar_ data till användnings- och taggdata för dina molnkonton. För att matcha data behöver Cloudyn åtkomst till dina användningsdata. Om du har konton som saknar autentiseringsuppgifter är de märkta som _okategoriserade resurser_.

## <a name="dashboards"></a>Instrumentpaneler

Instrumentpaneler i Cloudyn ger en överblick av rapporter på hög nivå. Instrumentpaneler består av widgetar. Varje widget är i princip en rapportminiatyr. När du [anpassar rapporter](understanding-cost-reports.md#save-and-schedule-reports) sparar du dem till Mina rapporter, och de läggs till på instrumentpanelen. Mer information om instrumentpaneler finns i [Visa viktiga kostnadsmått med instrumentpaneler](dashboards.md).

## <a name="budget-information-in-reports"></a>Budgetinformation i rapporter

Många Cloudyn-rapporter visar budgetinformation efter att du har skapat en manuellt. Rapporter visar alltså inte budgetinformation förrän du har skapat en budget. Mer information finns i [inställningar för budgethantering](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Rapporter och rapporteringsfunktioner

Cloudyn innehåller följande rapporter och rapporteringsfunktioner.

### <a name="cost-navigator-report"></a>Rapport för Kostnadsnavigatorn

Rapporten för Kostnadsnavigatorn är ett snabbt sätt att visa din faktureringsförbrukning med hjälp av en instrumentpanelsvy. Den har en delmängd med filter och grundläggande vyer där du omedelbart kan visa en sammanfattad vy av organisationens kostnader. Kostnader visas efter datum. Eftersom rapporten är avsedd som en inledande vy av dina kostnader är den inte lika flexibel eller omfattande som många andra rapporter eller anpassade instrumentpaneler som du skapar själv.

Som standard visar större vyer i rapporten följande:

- Kostnad över tid som visar en vy med stapeldiagram för arbetsveckan. Du kan ändra **datumintervallet** för att ändra stapeldiagrammet för datumintervall.
- Utgifter per tjänst med hjälp av ett cirkeldiagram.
- Resurskategorisering efter taggar med hjälp av ett cirkeldiagram.
- Utgifter per kostnadsentitet med hjälp av ett cirkeldiagram.
- Total kostnad per datum i en listvy.

### <a name="cost-analysis-report"></a>Cost Analysis-rapport

Rapporten för kostnadsanalys är en beräkning av showback och återbetalning baserat på din princip. Den aggregerar din molnförbrukning under en vald tidsram efter tillämpning av alla allokeringsregler på din kostnad. Den beräknar till exempel kostnaderna efter taggar, tilldelar om kostnaderna för otaggade resurser och kan eventuellt allokera utnyttjandet av reserverade instanser.

De principer som anges i [Kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) används i rapporten för kostnadsanalys, och resultatet kombineras sedan med information från din molnleverantörs rådata.

Hur beräknas den här rapporten? Cloudyn-tjänsten säkerställer att allokeringen behåller integriteten för varje länkat konto genom att tillämpa _kontotillhörighet_. Tillhörighet säkerställer att ett konto som inte använder en specifik tjänst inte allokeras några kostnader för den tjänsten. De kostnader som påförs i det kontot finns kvar i det kontot och beräknas inte av allokeringsreglerna. Till exempel har du kanske fem länkade konton. Om endast tre av dessa använder lagringstjänster allokeras kostnaden för lagringstjänster endast bland taggar i de tre kontona.

Använd kostnadsanalysrapporten för att:

- Beräkna din organisations återbetalning/showback
- Kategorisera alla dina kostnader
- Visa en aggregerad vy av hela distributionen för en angiven tidsram.
- Visa kostnader efter taggkategorier baserat på principer som skapats i kostnadsmodellen.

Så här använder du kostnadsanalysrapporten:

1. Välj ett datumintervall.
2. Lägg till taggar efter behov.
3. Lägg till grupper.
4. Välj en kostnadsmodell som du skapade tidigare.

### <a name="cost-over-time-report"></a>Rapport för kostnad över tid

Rapport för kostnad över tid visar resultatet av kostnadsallokering som tidsserie. Den gör att du kan följa trender och upptäcka oregelbundenheter i distributionen. Den visar i huvudsak kostnader som är fördelade över en definierad period. Rapporten innehåller dina huvudsakliga kostnadsfaktorer, inklusive pågående kostnader och engångsavgifter för reserverad instans som spenderas under en vald tidsram. Principer som anges i [Kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) används i den här rapporten.

Använd rapporter för kostnad över tid för att:

- Se ändringar över tid som påverkar ändring från en dag (eller ett datumintervall) till nästa.
- Analysera kostnader över tid för en specifik instans.
- Ta reda på varför det skedde en kostnadsökning för en specifik instans.

Så här använder du rapporten för kostnad över tid:

1. Välj ett datumintervall.
2. Lägg till taggar efter behov.
3. Lägg till grupper.
4. Välj en kostnadsmodell som du skapade tidigare.
5. Välj faktiska kostnader eller upplupna kostnader.
6. Välj om du vill tillämpa allokeringsregler för att visa råa faktureringsdata eller för att beräkna om kostnadsvyn.

### <a name="actual-cost-analysis-report"></a>Rapport för analys av faktiska kostnader

Rapporten för analys av faktiska kostnader visar leverantörskostnader utan ändringar. Den visar dina huvudsakliga kostnadsfaktorer, inklusive löpande kostnader och engångsavgifter.

Du kan använda rapporten för att visa kostnadsinformation för dina prenumerationer. I rapporten visas Azure-prenumerationer som **kontonamn** och **kontonummer**. **Länkade konton** visar AWS-prenumerationer. Om du vill visa kostnader per prenumeration, en uppdelning för varje konto, väljer du under **Grupper** den typ av prenumeration du har.

Använd rapporten för analys av faktiska kostnader för att:

- Analysera och övervaka råa leverantörskostnader som spenderats under en angiven tidsram.
- Schemalägga en tröskelvarning.
- Analysera ej ändrade kostnader som tillkommer i dina konton och entiteter.

### <a name="actual-cost-over-time-report"></a>Rapport för faktiska kostnader över tid

Rapporten för faktiska kostnader över tid är en kostnadsanalysrapport som fördelar kostnader över en definierad tidsupplösning. Rapporten visar utgifter över tid så att du kan följa trender och upptäcka eventuella oregelbundenheter i utgifter. Den här rapporten visar dina huvudsakliga kostnadsfaktorer, inklusive pågående kostnader och engångsavgifter för reserverad instans som spenderas under en vald tidsram.

Använd rapporten för faktiska kostnader över tid för att:

- Se kostnadstrender över tid.
- Hitta oregelbundenheter i kostnader.
- Hitta alla kostnadsrelaterade frågor som rör molnleverantörer.

### <a name="amortized-cost-reports"></a>Rapporter för upplupna kostnader

Den här uppsättningen med rapporter för upplupna kostnader visar linjäriserade icke-användningsbaserade tjänstavgifter eller engångsavgifter och sprider kostnaderna jämnt över tiden under livslängden. Engångsavgifter kan till exempel vara:

- Årliga supportavgifter
- Årliga avgifter för säkerhetskomponenter
- Avgifter för köp av reserverade instanser
- Vissa Azure Marketplace-produkter

I faktureringsfilen karakteriseras engångsavgifter när startdatum och slutdatum (tidsstämplar) för tjänstförbrukning har lika värden. Cloudyn-tjänsten känner sedan igen dem som engångsavgifter som är upplupna. Andra förbrukningsbaserade tjänster med kostnader för användning på begäran är inte upplupna.

Rapporter för upplupna kostnader inkluderar:

- Analys av upplupna kostnader
- Upplupna kostnader över tid

### <a name="cost-analysis-report"></a>Cost Analysis-rapport

Rapporten för kostnadsanalys ger inblick i din molnförbrukning och dina utgifter under en vald tidsram. De principer som angetts i [Kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) används i rapporten för kostnadsanalys.

Hur beräknar Cloudyn den här rapporten?

Cloudyn säkerställer att allokeringen behåller integriteten för varje länkat konto genom att tillämpa _kontotillhörighet_. Tillhörighet säkerställer att ett konto som inte använder en specifik tjänst heller inte allokeras några kostnader för den tjänsten. De kostnader som påförs i det kontot finns kvar i det kontot och beräknas inte av allokeringsreglerna. Till exempel har du kanske fem länkade konton. Om endast tre av dessa använder lagringstjänster allokeras kostnaden för lagringstjänster endast bland taggar i de tre kontona.

Använd kostnadsanalysrapporten för att:

- Visa en aggregerad vy av hela distributionen för en angiven tidsram.
- Visa kostnader efter taggkategorier baserat på principer som skapats i kostnadsmodellen.

### <a name="cost-over-time-report"></a>Rapport för kostnad över tid

Rapporten för kostnad över tid visar utgifter över tid så att du kan upptäcka trender och uppmärksamma oregelbundenheter i distributionen. Den visar i huvudsak kostnader som är fördelade över en definierad period. Rapporten innehåller dina huvudsakliga kostnadsfaktorer, inklusive pågående kostnader och engångsavgifter för reserverad instans som spenderas under en vald tidsram. Principer som anges i [Kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) används i den här rapporten.

Använd rapporter för kostnad över tid för att:

- Se ändringar över tid som påverkar ändring från en dag (eller ett datumintervall) till nästa.
- Analysera kostnader över tid för en specifik instans.
- Ta reda på varför det skedde en kostnadsökning för en specifik instans.

### <a name="custom-charges-report"></a>Rapporten för anpassade avgifter

Företags- och CSP-användare brukar ofta tillhandahålla extra tjänster till sina externa eller interna kunder, utöver den egna förbrukningen av molnresurser. Du definierar anpassade avgifter för tillagda tjänster eller rabatter som läggs till i kundens fakturerings- eller återbetalningsrapporter som egna radobjekt.

Anpassade tjänstavgifter speglar tjänster som vanligtvis inte visas på en faktura. De anpassade avgifter som du skapar visas sedan i kostnadsrapporter.

*Anpassade avgifter är inte anpassad prissättning*. Listan med anpassade avgifter visar inte de olika priser som du kan debitera. Till exempel visas AWS-faktureringsavgifter precis såsom de debiteras.

Så här skapar du en anpassad avgift:

1. I **Anpassade avgifter** klickar du på **Lägg till ny**. Dialogrutan _Lägg till ny anpassad avgift_ visas.
2. I **Leverantörens namn** anger du namnet på leverantören.
3. I **Tjänstnamn** anger du typen av tjänst.
4. I **Beskrivning** lägger du till en beskrivning för den anpassade avgiften.
5. I **Typ** anger du vald **Procent**. I listrutan Tjänster väljer du sedan de tjänster som ska inkluderas som anpassade avgifter i kostnadsrapporterna.
6. I **Betalning** väljer du om avgiften är en engångsavgift eller en återkommande avgift. Om avgiften är en återkommande avgift väljer du Upplupen om du vill att avgiften ska vara upplupen och väljer antalet månader.
7. Om en engångsavgift har valts anger du under **Dates** det datum då avgiften betalas i **Giltighetsdatum**. Om Återkommande avgift har valts anger du datumintervallet inklusive startdatum och slutdatum för avgiften.
8. I **trädet Entiteter** väljer du de entiteter som du vill tillämpa avgiften på och väljer sedan **På**.

_När avgifter tilldelas till en entitet kan användarna inte ändra dem. Avgifter som läggs till av en administratör i en överordnad entitet är skrivskyddade._

Så här visar du anpassade avgifter:

Anpassade avgifter visas i kostnadsrapporter. Öppna till exempel rapporten för analys av verkliga kostnader. Under **Utökade filter** väljer du sedan **Fristående**. Filtrera sedan för att visa **Anpassade avgifter**.

### <a name="cost-allocation-360"></a>Kostnadsallokering 360

Du använder Kostnadsallokering 360 för att skapa anpassade modeller för kostnadsallokering för att tilldela kostnader till förbrukade molnresurser. Många rapporter visar information från anpassade kostnadsmodeller som du har skapat med anpassade kostnadsmodeller. Och vissa rapporter visar bara information efter att du har skapat en anpassad kostnadsmodell med kostnadsallokering.

Mer information om hur du skapar anpassade kostnadsmodeller finns i [Självstudie: Hantera kostnader med hjälp av Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Rapport för kostnad jämfört med budget över tid

I rapporten för kostnad jämfört med budget över tid kan du jämföra de största kostnadsfaktorerna med din budget. Den tilldelade budgeten syns i rapporten så att du kan visa din budgetförbrukning (över/under/vid) över tid. Med hjälp av Visa/dölj fält längst upp i rapporten kan du välja att se kostnad, budget, ackumulerad kostnad och total budget.

### <a name="current-month-projected-cost-report"></a>Rapport för beräknad kostnad aktuell månad

Rapporten för beräknad kostnad aktuell månad ger inblick i din aktuella kostnadssammanfattning för månaden fram till aktuellt datum. I den här rapporten visas kostnaderna från början av månaden, från föregående månad och den totala beräknade kostnaden för aktuell månad. Den aktuella månadens beräknade kostnad beräknas som summan av månadskostnaden fram till aktuellt datum samt en beräkning baserat på den kostnad som övervakas under de senaste 30 dagarna.

Använd rapporten för beräknad kostnad aktuell månad för att:

- Beräkna månadskostnader per tjänst
- Beräkna månadskostnader per konto

### <a name="annual-projected-cost-report"></a>Rapporten för årlig beräknad kostnad

Med rapporten för årlig beräknad kostnad kan du visa årliga beräknade kostnader baserat på tidigare utgiftstrender. Den visar de kommande 12 månadernas totala beräknade kostnader. Beräkningarna görs med hjälp av en trendfunktion som extrapolerats för de närmaste 12 månaderna baserat på de kostnader som är kopplade till de senaste 30 dagarnas användning.

### <a name="budget-management-settings"></a>Inställningar för budgethantering

Med budgethantering kan du ange en budget för ditt räkenskapsår.

Så här lägger du till en budget i en entitet:

1. På sidan Budgethantering väljer du under **Entiteter** den entitet där du vill skapa budgeten.
2. Som budgetår väljer du det år som du vill skapa budgeten för.
3. I varje månad anger du budgeten och klickar på **Spara**.

Så här importerar du en fil för årsbudgeten:

1. Under **Åtgärder** väljer du **Exportera** för att ladda ned en tom CSV-mall som ska användas som grund för budgeten.
2. Fyll i CSV-filen med dina budgetposter och spara den lokalt.
3. Under **Åtgärder** väljer du **Importera**.
4. Välj den sparade filen och klicka sedan på **OK**.

Om du vill exportera din slutförda budget som en CSV-fil går du till **Åtgärder** och väljer **Exportera** för att ladda ned filen.

När det är klart visas budgeten i rapporterna för kostnadsanalys samt i rapporten för kostnad jämfört med Budget över tid. Du kan även schemalägga rapporter baserat på budgettrösklar.

### <a name="azure-resource-explorer-report"></a>Azure Resource Explorer-rapporten

Azure Resource Explorer-rapporten visar en samlingslista med alla tillgängliga Azure-resurser i Cloudyn. För att effektivt kunna använda rapporten ska dina Azure-konton ha utökade mått aktiverat. Utökade mått ger Cloudyn åtkomst till dina virtuella Azure-datorer. Mer information finns i [Lägga till utökade mått för virtuella Azure-datorer](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Rapporten för Azure-resurser över tid

Rapporten för Azure-resurser över tid visar en analys av alla resurser som körs under en viss period. För att effektivt kunna använda rapporten ska dina Azure-konton ha utökade mått aktiverat. Utökade mått ger Cloudyn åtkomst till dina virtuella Azure-datorer. Mer information finns i [Lägga till utökade mått för virtuella Azure-datorer](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Instance Explorer-rapporten

Instance Explorer-rapporten används för att visa olika mått för dina virtuella datorers tillgångar. Du kan öka detaljnivån till specifika instanser för att visa information såsom:
- Intervaller som kör instanser
- Livscykel i den valda perioden
- CPU-användning
- Nätverksindata
- Utgående trafik
- Aktiva diskar

Instance Explorer-rapporten samlar in alla intervall som körs inom det definierade datumintervallet och aggregerar data därefter. Om du vill visa var och en av de intervall som körs under datumintervallet expanderar du instansen. Kostnaden för varje instans beräknas för det datumintervall som väljs utifrån AWS- och Azure-listpriser. Inga rabatter tillämpas. Du kan lägga till ytterligare fält i rapporten med hjälp av Visa/dölj fält.

Använd Instance Explorer-rapporten för att:

- Beräkna den uppskattade kostnaden per dator.
- Skapa en fullständig lista, inklusive aggregerade körningstimmar, för alla datorer som var aktiva under ett tidsintervall.
- Skapa en lista efter molntjänstleverantör eller konto.
- Visa datorer som skapats eller avslutats under ett tidsintervall.
- Visa alla datorer som för närvarande har stoppats.
- Visa taggarna för varje dator.

### <a name="instances-over-time-report"></a>Rapport för instanser över tid

Med rapporten för instanser över tid kan du se det maximala antalet datorer som var och en var aktiva under det valda tidsintervallet. Om den definierade upplösningen är per vecka eller månad är resultatet det maximala antalet datorer som var aktiva en viss dag under den månaden. Välj ett datumintervall för att välja de filter som du vill ska visas i rapporten.

### <a name="instance-utilization-over-time-report"></a>Rapport för instansutnyttjande över tid

Den här rapporten visar en analys av CPU- eller minnesanvändning över tid för alla dina instanser.

### <a name="compute-power-cost-over-time-report"></a>Rapport för kostnad för beräkningsenergiåtgång över tid

Med rapporten för kostnad för beräkningsenergiåtgång över tid får du en analys av beräkningsenergiåtgång under ett angivet datumintervall. Även om andra rapporter visar det antal datorer som körs eller körningstimmarna visar den här rapporten kärntimmar, beräkningsenhetstimmar eller GB RAM-timmar.

Använd rapporten för att:

- Kontrollera beräkningsenergiåtgången inom ett angivet datumintervall.
- Visa beräkningstider baserat på kostnadsallokeringsmodeller.

Den här rapporten är länkad till dina [Kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-principer. Därför visas resultat baserat på definierad taggning och definierade principer för din valda kostnadsprincip. När du inte har skapat någon princip visas inte resultat.

### <a name="compute-power-average-cost-over-time-report"></a>Rapport för genomsnittlig kostnad för beräkningsenergiåtgång över tid

Du kan använda rapporten för genomsnittlig kostnad för beräkningsenergiåtgång över tid till att visa mer än bara kostnaden för varje dator som körs. Rapporten visar den genomsnittliga kostnaden per instanstimme, kärntimme, beräkningsenhetstimme och GB RAM-timme. Rapporten ger en inblick i effektiviteten i din distribution.

Den här rapporten är länkad till dina [Kostnadsallokering 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-principer. Därför visas resultat baserat på definierad taggning och definierade principer för din valda kostnadsprincip. När du inte har skapat någon princip visas inte resultat.

### <a name="s3-cost-over-time-report"></a>Rapport för S3-kostnad över tid

Rapporten för S3-kostnad över tid ger en analys av kostnaderna för Amazon Simple Storage Service (S3) per bucket över tid för en angiven tidsram. Rapporten hjälper dig att hitta de buckets som är dina huvudsakliga kostnadsfaktorer och visar trender i din S3-användning och dina utgifter.

### <a name="s3-distribution-of-cost-report"></a>Rapport för S3-distribution av kostnad

Använd rapporten för att analysera din S3-kostnad för den senaste månaden efter bucket och lagringsklass. Du kan använda cirkeldiagramsvyn för att ange tröskelvärdet för synlighet. Eller så kan du använda tabellvyn för att visa delsummor.

### <a name="s3-bucket-properties-report"></a>Rapport för S3-bucketegenskaper

Använd rapporten för att visa S3-bucketegenskaper. Du kan använda cirkeldiagramsvyn för att ange tröskelvärdet för synlighet. Eller så kan du använda tabellvyn för att visa delsummor.

### <a name="rds-instances-over-time-report"></a>Rapport för RDS-instanser över tid

Använd rapporten för att visa en analys av alla Amazon Relational Database Service-instanser (RDS) som körs under den angivna perioden.

### <a name="rds-active-instances-report"></a>Rapport över aktiva RDS-instanser

Använd rapporten för att analysera aktiva RDS-instanser. I rapporten expanderar du objektet om du vill visa mer information.

### <a name="azure-reserved-instances-report"></a>Rapport för reserverade Azure-instanser

I rapporten för reserverade Azure-instanser får du en enskild vy över alla dina reserverade Azure-instanser. I den här rapporten visas varje inköp som ett eget radobjekt. Rapporten innehåller även information om det inköpet, till exempel det konto som köpte det, typen av inköp och instanstyp, återstående dagar och så vidare. Du kan visa eller dölja rapportdata med hjälp av Visa/dölj fält.

Använd rapporten för reserverade Azure-instanser för att visa:

- En lista över alla reservationer efter inköpsdatum.
- Återstående tid tills RI upphör att gälla.
- Engångsavgifter.
- Det konto som köpte RI och när.

### <a name="aws-reserved-instances-report"></a>Rapport för reserverade AWS-instanser

I rapporten för reserverade AWS-instanser får du en enskild vy över alla reserverade AWS-instanser. Rapporten visar även varje köp som ett eget radobjekt samt information om det inköpet, till exempel det konto som köpte det, typen av inköp och instanstyp, återstående dagar och så vidare. Du kan visa eller dölja rapportdata med hjälp av Visa/dölj fält.

Använd rapporten för reserverade AWS-instanser för att visa:

- En lista över alla reservationer efter inköpsdatum.
- Återstående tid tills RI upphör att gälla.
- Engångsavgifter.
- Ursprungligt inköps-ID (reservations-ID).
- Det konto som köpte RI och när.

### <a name="ec2-ri-buying-recommendations-report"></a>Rapport för köprekommendationer för EC2 RI

Grunden för förbrukning av molnresurser är på begäran-modellen, där resurser endast medför kostnader när de används. Det finns inga åtaganden i förskott – du betalar bara för det du använder, när du använder det.

AWS erbjuder en alternativ prismodell för sina Elastic Compute Cloud-tjänster (EC2) – den reserverade instansen (RI). Den här prismodellen garanterar användare kapacitet när de behöver den under hela varaktigheten för RI. RI erbjuder betydande rabatter för på begäran-priser. I gengäld gör användarna ett åtagande i förskott för användningen av en virtuell instans. Åtagandet är kopplat till en viss familj, storlek, tillgänglighetszon (AZ) och operativsystem under åtagandeperioden (ett eller tre år). RI gör att AWS effektivt kan planera framtida kapacitet samt få kunders åtagande att använda dess tjänster.

Tre betalningsalternativ för RI som är gäller i förskott:

- Hela beloppet dag 0, vilket ger den högsta rabatten
- Ingen förskottsbetalning, där kostnaden för RI betalas månatligen under varaktigheten för RI, vilket ger den lägsta rabatten
- Delvis förskottsbetalning, en fjärdedel till hälften av priset betalas i förskott och resten månatligen med en rabatt som är lägre än men nära priset för betalning av hela beloppet i förväg

Cloudyn utvärderar drifttiden för varje dator under de senaste 30 dagarna. Cloudyn rekommenderar köp av RI när det är mer kostnadseffektivt att köra datorn med en RI vid den aktuella drifttidsnivån.

Rapporten visar motiveringen för dess rekommendationer för att spara mest pengar under året. Rekommendationerna rekommenderar att på begäran-instanser ersätts med RI. Du kan köpa RI direkt från rapporten.

Varje flik öppnas som en fullständig rapport. Viktiga avsnitt på flikarna är:

- **EC2 Köpeffekt för RI** – det här avsnittet innehåller en simulering av skillnaden mellan på begäran jämfört med reserverade instanser. Klicka på **Zooma in** för att se hela rapporten för EC2 RI-köpeffekt med filtren fördefinierade enligt din rekommendation. Den här rapporten visar köpeffekten för alla potentiella RI-inköp. Du kan justera den förväntade genomsnittliga drifttiden för att se potentiella besparingar när du köper reserverade EC2-instanser.

- **Besparingsanalys** – det här avsnittet innehåller de potentiella besparingarna och den månad då besparingarna förverkligas när Cloudyn-rekommendationer följs. De faktiska besparingarna och den sparade procentandelen är rödmarkerade.

- **EC2 RI-typjämförelse** – det här avsnittet framhäver ROI-fördelarna med Cloudyns rekommenderade distribution, inklusive alla relevanta alternativ. Resultatet i den här rapporten förutsätter att datorn körs med 100 % drifttid. Klicka på **Zooma in** för att öppnade den detaljerade rapporten.

- **Instanser över tid** – det här avsnittet visar en analys av alla instanser som är associerade med rekommendationen, OnDemand, reserverade instanser och punkt. Klicka på **Zooma in** för att öppnade den detaljerade rapporten.
- **Lönsamhetspunkter** – det här avsnittet innehåller en tabell med alla möjliga rekommenderade distributioner samt ROI och den månad då ROI sker. Klicka på **Zooma in** för att öppnade den detaljerade rapporten.

### <a name="ec2-reservations-over-time-report"></a>Rapport för EC2-reservationer över tid

Rapporten för EC2-reservationer över tid spårar statusen för din användning av din köpta EC2-RI. Du kan ange en upplösning för rapporten till timme, dag eller vecka.

Använd rapporten för att:

- Visa köpta reservationer som används och sådana som inte används.
- Öka detaljnivån till upplösningen per timme för att se RI-användning per timme.

### <a name="savings-over-time-report"></a>Rapport för besparingar över tid

Använd rapporten för besparingar över tid för att visa de besparingar som uppnås med reserverade instanser samt punktinstanser. Rapporten visar den ROI som uppnås över tid från RI-inköp.

Om du vill visa besparingar från RI grupperar du resultatet efter **Prismodell** och väljer **Reservationer**. Om du vill visa RI-besparingar som uppnås av ett specifikt konto eller en instanstyp lägger du till relevant gruppering och filter till konto- eller instanstypen.

Om du vill se besparingar från användning av punktinstanser filtrerar du **Prismodell** till **Punkt**. Standardfiltret för den här rapporten är RI och punktinstanser.

### <a name="rds-ri-buying-recommendations-report"></a>Rapport för köprekommendationer för RDS RI

Rapporten för köprekommendationer för RDS RI rekommenderar när du bör använda RDS-RI i stället för på begäran-instanser.

Varje flik öppnas som en fullständig rapport. Viktiga avsnitt på flikarna är:

- **RDS Köpeffekt för RI** – det här avsnittet innehåller en simulering av skillnaden mellan på begäran jämfört med reserverade instanser. Klicka på **Zooma in** för att se hela rapporten för RDS RI-köpeffekt med filtren fördefinierade enligt din rekommendation. Med den här rapporten kan du se köpeffekten för alla potentiella RI-inköp.  Du kan justera den förväntade genomsnittliga drifttiden och se potentiella besparingar från köp av RI.
- **Besparingsanalys** – det här avsnittet innehåller de potentiella besparingarna och den månad då besparingarna förverkligas när Cloudyn-rekommendationer följs. De faktiska besparingarna och den sparade procentandelen är rödmarkerade.

- **RDS RI-typjämförelse** – det här avsnittet framhäver ROI-fördelarna med den rekommenderade distributionen, inklusive alla relevanta alternativ. Resultatet i den här rapporten förutsätter att datorn körs med 100 % drifttid. Klicka på **Zooma in** för att öppnade den detaljerade rapporten för den valda datorn.
- **Instanser över tid** – det här avsnittet visar en analys av alla instanser som är associerade med rekommendationen, OnDemand, reserverade instanser och punkt. Klicka på **Zooma in** för att öppnade den detaljerade rapporten.

- **Lönsamhetspunkter** – det här avsnittet innehåller en tabell med alla möjliga rekommenderade distributioner samt ROI och den månad då ROI sker. Klicka på **Zooma in** för att öppnade den detaljerade rapporten.

### <a name="rds-reservations-over-time-report"></a>Rapport för RDS-reservationer över tid

Använd rapporten för RDS-reservationer över tid för att visa en analys av både dina använda och oanvända reservationer under den angivna perioden.

### <a name="reserved-instance-purchase-impact-report"></a>Rapport för köpeffekt för reserverad instans

Med rapporten för köpeffekt för EC2-RI kan du simulera kostnaden för reserverad instans jämfört med på begäran-kostnad över tid. Den kan hjälpa dig att fatta bättre inköpsbeslut. Justera filter såsom genomsnittlig körningstid, period, plattform och andra för att fatta välgrundade beslut när du överväger RI-inköp.

### <a name="cost-effective-sizing-recommendations-report"></a>Rapport för rekommendationer om kostnadseffektiv storlek

Rapporten för rekommendationer om kostnadseffektiv storlek ger resultat för AWS och Azure. För AWS-användare beaktas dina RI-inköp, och resultatet omfattar inte datorer som körs som RI. Den här rapporten innehåller en lista över underutnyttjade instanser som är kandidater för nedskalning. Rekommendationerna baseras på dina användnings- och prestandadata från de senaste 30 dagarna. I varje rekommendation finns en lista över kandidater för nedskalning, motiveringen till nedskalningen samt en länk för att visa fullständig information och prestandamått för instansen. Det visas även när relevanta rekommendationer ger rådet att byta till instanstyper från en nyare generation.

Du kan inte ladda ned listan med instans-ID:n som rekommenderas för nedskalning från den här rapporten. Om du vill ladda ned instans-ID:n använder du rapporten för alla storleksrekommendationer.

Ta följande exempel på nedskalning:

Du har sex m3.xlarge-instanser som körs. Cloudyn-analys visar att fem av dem har låg CPU-användning. Överväg att skala ned dem.

I Kostnadspåverkan beräknas kostnadspåverkan. I det här exemplet kan du genom att expandera radobjektet se att det aktuella priset för en m3.xlarge-instans (Linux/Unix) är 0,266 USD per timme och att priset för en m3.large-instans (Linux/Unix) är 0,133 USD per timme. Därför är den årliga kostnaden 11 651 USD för fem m3.xlarge-instanser som körs med 100 % utnyttjande. Den årliga kostnaden är 5 825 USD för fem m3.large-instanser som körs med 100 % utnyttjande. Den potentiella besparingen är 5 825 USD.

Om du vill visa motiveringar till kostnadseffektiv storlek klickar du på + för att expandera radobjektet. I **Information**:

- I avsnittet **Rekommendationsjustering** visas den aktuella distributionen och det antal instanser som rekommenderas för nedskalning.
- I avsnittet **Kostnadspåverkan** visas den beräkning som används för att fastställa potentiella besparingar.
- I avsnittet **Potentiella årliga besparingar** visas de potentiella årliga besparingarna vid nedskalning enligt Cloudyns rekommendationer.

### <a name="all-sizing-recommendations-report"></a>Rapport för alla storleksrekommendationer

Den här rapporten innehåller en lista över underutnyttjade instanser som är kandidater för nedskalning. Rekommendationerna baseras på dina användnings- och prestandadata från de senaste 30 dagarna. I varje rekommendation kan du visa fullständig information och prestandamått för instansen.

Om du har köpt reserverade AWS-instanser innehåller den här rapporten resultat för alla instanser som körs, inklusive instanser som körs som RI.

Använd rapporten för alla storleksrekommendationer för att:

- Se en lista över alla dina instanser som är kandidater för nedskalning.
- Exportera en rapportlista som innehåller instansnamn och ID.

Om du vill visa rekommendationsinformation för en specifik instans klickar du på **+** för att expandera informationen. Avsnittet Rekommendationsinformation innehåller en översikt av rekommendationen.

Avsnittet **Taggar** innehåller listan över taggnycklarna och värdena för den valda instansen. Använd Taggar i det vänstra fönstret för att filtrera avsnittet.

Avsnittet **CPU-användning** visar CPU-användningen för instanser under den senaste månaden efter dag.

Klicka på grafen för att öka detaljnivån och öppna rapporten för instans-CPU över tid för att se en analys av instanserna.

- Använd **Visa/dölj fält** för att lägga till eller ta bort fält: Tidsstämpel, genomsnittlig CPU, min CPU, maximal CPU.
- Använd **Datumintervall** för att ange ett datum eller datumintervall och öka detaljnivån för ett visst InstanceID.
- Använd **Utökade filter** för att visa alla eller ett specifikt instans-ID
- Klicka på **Zooma in** för att öppna rapporten för CPU-användning

Om instansen inte har övervakats i 30 dagar visas ofullständiga data.

Avsnittet **Minnesanvändning (GB)** innehåller information om använt minne. För AWS-användare är minnesmått inte automatiskt tillgängliga och måste läggas till per instans via AWS. AWS debiterar dog för aktivering av minnesmått för EC2-instanser.

I avsnittet **Minnesanvändning (%)** visas den procentandel minne som används.

I avsnittet **Inkommande nätverkstrafik** visas en ögonblicksbild över tiden för nätverkstrafiken, genomsnittet och maxvärdet för den valda instansen. Hovra över linjerna för att se datum och maximal trafik för den tiden. Klicka på **Zooma in** för att öppna rapporten för inkommande nätverkstrafik.

I avsnittet **Utgående nätverkstrafik** visas en ögonblicksbild för utgående nätverkstrafik för den valda instansen. Hovra över linjerna för att se datum och maximal trafik för den tiden. Klicka på **Zooma in** för att öppna rapporten för utgående nätverkstrafik.

### <a name="instance-metrics-explorer-report"></a>Rapport för Metrics Explorer för instanser

Rapporten för Metrics Explorer för instanser visar prestandamått mellan moln per instans. Använd rapporten om du vill visa instanser som är över- eller underutnyttjade baserat på tröskelvärden för CPU, minne och nätverk.

Så här visar du prestanda mellan moln per instans:

1. I **Datumintervall** väljer du ett datumintervall som du vill visa prestanda för.
2. I **Taggar** väljer du eventuella taggar som du vill visa.
3. I **Filter** väljer du de filter som du vill visa i rapporten.
4. I **Utökade filter** justerar du rapporttröskelvärdena för:
    - Genomsnittlig CPU
    - Maximal CPU
    - Genomsnittligt minne
    - Maximalt minne
5. I **Utökade filter** klickar du på **Visa** och väljer sedan vilken typ av instanser som ska visas.

Så här visar du en specifik instans mått över tid:

- Gå till rapporten för Metrics Explorer för instanser och klicka på **+** för att visa information.

### <a name="rds-sizing-recommendations-report"></a>Rapport för RDS-storleksrekommendationer

Rapporten för RDS-storleksrekommendationer ger RDS-storleksrekommendationer för optimering av din molnanvändning. Den innehåller en lista över underutnyttjade instanser som är kandidater för nedskalning. Cloudyn-rekommendationer baseras på användnings- och prestandadata från de senaste 30 dagarna. Du kan filtrera rekommendationer efter kontonamn, region, instanstyp och status.

### <a name="sizing-threshold-manager-report"></a>Rapport för tröskelhanterare för storlek

Cloudyns inbyggda storleksrekommendationer beräknas med hjälp av en komplex algoritm för noggranna storleksförslag. Du kan justera tröskelvärdena för rekommendationer om nedskalning.

Så här justerar du rekommendationer om tröskelstorlek manuellt:

1. I tröskelhanteraren för storlek justerar du följande tröskelvärden enligt önskemål:
    - Genomsnittlig CPU %
    - Maximal CPU %
    - Genomsnittligt minne %
    - Maximalt minne %
3. Klicka på **Tillämpa** för att spara ändringarna.
4. Ändringarna tillämpas omedelbart på alla dina rekommendationer.

Så här återställer du standardtröskelvärdena:

- I tröskelhanteraren för storlek klickar du på **Återställ standardvärden**.

### <a name="compute-instance-types-report"></a>Rapport för beräkningsinstanstyper

Använd rapporten för instanstyper för att:

- Visa instanstyper per tjänst, familj, API-namn och namn.
- Visa information såsom CPU, ECU, RAM och nätverk.

Du kan använda **Sök** för att hitta specifika radobjekt.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder rapporter, däribland hur du anpassar eller sparar och schemalägger rapporter. Se [Förstå kostnadsrapporter](understanding-cost-reports.md).
- Lär dig mer om de instrumentpaneler som ingår i Cloudyn och hur du skapar dina egna anpassade instrumentpaneler. Se [Visa viktiga kostnadsmått med instrumentpaneler](dashboards.md).

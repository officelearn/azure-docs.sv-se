---
title: Visa viktiga mått med Cloudyn-instrumentpaneler i Azure
description: Den här artikeln beskriver hur du kan visa viktiga mått med instrumentpaneler i Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 03d5e3959b85a2b0bd9ae30744051895ff026b77
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691232"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Visa viktiga kostnadsmått med instrumentpaneler

Instrumentpaneler i Cloudyn ger en överblick av rapporter på hög nivå. Med instrumentpaneler kan du visa viktiga kostnadsmått i en enskild vy. De ger även affärstrender som hjälper dig att fatta viktiga affärsbeslut.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Instrumentpaneler används även för att skapa vyer för personer med olika ansvarsområden i din organisation, vilket kan innefatta:

- Styrekonom
- Program- eller projektägare
- DevOps-utvecklare
- Chefer

Instrumentpaneler består av widgetar. Varje widget är i princip en rapportminiatyr. Klicka på widgeten för att öppna dess rapport. När du anpassar rapporter sparar du dem till Mina rapporter, och de läggs till på instrumentpanelen.

Instrumentpanelsversioner är olika för Management- (MSP), Enterprise- och Premium Cloudyn-användare. Skillnaderna avgörs av åtkomstnivåer för entiteter. Mer information om åtkomstnivåer finns i [Åtkomstnivåer för entiteter](tutorial-user-access.md#entity-access-levels).

Tillgängligheten för instrumentpaneler beror på vilken typ av molntjänstleverantörskonto som används vid visning av instrumentpaneler. Den typ av information som är tillgänglig och samlas in av Cloudyn påverkar rapporter i instrumentpaneler. Om du till exempel inte har ett AWS-konto ser du inte instrumentpanelen för S3-spårare. Och om du inte aktiverar Azure Resource Manager-åtkomst till Cloudyn ser du inte någon Azure-specifik information i Optimizer-instrumentpanelswidgetar.

Du kan använda valfri fördefinierad instrumentpanel eller skapa en egen instrumentpanel med anpassade rapporter. Om du inte är bekant med Cloudyn-rapporter kan du läsa mer i [Använda Cloudyn-rapporter](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Skapa en anpassad instrumentpanel

För att snabbt komma igång med en anpassad instrumentpanel kan du duplicera en befintlig för att använda dess egenskaper. Sedan kan du ändra den så att den passar dina behov. På den instrumentpanel som du vill kopiera klickar du på **Spara som**. Du kan bara duplicera anpassade instrumentpaneler – du kan inte duplicera de instrumentpaneler som ingår i Cloudyn.

Så här skapar du en anpassad instrumentpanel:

1. På startsidan klickar du på **Lägg till ny +**. Sidan Min instrumentpanel visas.  
    ![Sidan Min instrumentpanel där du lägger till nya rapporter](./media/dashboards/my-dashboard.png)
2. Klicka på **Lägg till ny rapport**. Rutan Lägg till rapport visas.
3. Välj den rapport som du vill lägga till i instrumentpanelswidgeten. Widgeten läggs till på instrumentpanelen.
4. Upprepa föregående steg tills instrumentpanelen är klar.
5. Om du vill ändra namnet på instrumentpanelen klickar du på instrumentpanelens namn på instrumentpanelens startsida och skriver det nya namnet.

## <a name="modify-a-custom-dashboard"></a>Ändra en anpassad instrumentpanel

Precis som när du skapar en anpassad instrumentpanel kan du inte ändra de instrumentpaneler som ingår i Cloudyn. Så här ändrar du en anpassad instrumentpanelsrapport:

1. På instrumentpanelen letar du upp den rapport som du vill ändra och klickar på **Redigera**. Rapporten visas.
2. Gör de ändringar du vill i rapporten och klicka på **Spara**. Rapporten uppdateras och visar dina ändringar.

## <a name="share-a-custom-dashboard"></a>Dela en anpassad instrumentpanel

Du kan dela en anpassad instrumentpanel med andra till _Offentlig_ eller _Min entitet_. När du delar till Offentlig kan alla användare visa instrumentpanelen. Endast användare med åtkomst till den aktuella entiteten kan visa instrumentpanelen när du delar till Min entitet. Stegen för att dela en anpassad instrumentpanel med Offentlig och Min entitet liknar varandra.

Så här delar du en anpassad instrumentpanel till Offentlig:

1. På en instrumentpanel klickar du på **Inställningar för instrumentpanelen**. Rutan Inställningar för instrumentpanelen visas.  
    ![instrumentpanelsinställningar för en anpassad instrumentpanel](./media/dashboards/dashboard-options.png)
2. I rutan Inställningar för instrumentpanelen klickar du på pilen och sedan på **Offentlig**. Dialogrutan för bekräftelse av offentlig instrumentpanel visas.
3. Klicka på **Ja**. Instrumentpanelen är nu tillgänglig för andra.

## <a name="delete-a-custom-dashboard-report"></a>Ta bort en anpassad instrumentpanelsrapport

Du kan ta bort en anpassad rapportkomponent från instrumentpanelen. Om rapporten tas bort från instrumentpanelen tas rapporten inte bort från rapportlistan. Rapporten tas i stället endast bort från instrumentpanelen.

Om du vill ta bort en instrumentpanelskomponent klickar du på **Ta bort** på instrumentpanelskomponenten. Om du klickar på **Ta bort** tas instrumentpanelskomponenten bort omedelbart.

## <a name="share-a-dashboard-enterprise"></a>Dela en instrumentpanel (Enterprise)

Du kan dela anpassade instrumentpaneler med alla användare i din organisation eller med användare av den aktuella entiteten. När du delar en instrumentpanel kan andra få en snabb vy av din KPI på hög nivå. När du delar en instrumentpanel replikeras den automatiskt till alla dina Cloudyn-entiteter/-kunder. Ändringar av den delade instrumentpanelen uppdateras automatiskt.

Så här delar du en instrumentpanel med alla användare, inklusive underentiteter:

1. På instrumentpanelens startsida klickar du på **Redigera**.
2. Klicka på **Dela** och välj sedan **Offentlig**.
3. Bekräftelserutan för global offentlig instrumentpanel visas.
4. Klicka på **Ja** för att ange instrumentpanelen som en global offentlig instrumentpanel.

Så här delar du en instrumentpanel med alla användare för en aktuell entitet:

1. På instrumentpanelens startsida klickar du på **Redigera**.
2. Klicka på **Dela** och välj sedan **Min entitet**.
3. Klicka på **Ja** för att ange instrumentpanelen som en offentlig instrumentpanel.

## <a name="duplicate-a-custom-dashboard"></a>Duplicera en anpassad instrumentpanel

När du skapar en ny instrumentpanel vill du kanske använda liknande egenskaper från en befintlig instrumentpanel. Du kan duplicera instrumentpanelen för att skapa en ny.

Du kan endast duplicera anpassade instrumentpaneler. Du kan inte duplicera standardinstrumentpaneler.

Så här duplicerar (klonar) du en anpassad instrumentpanel:

1. På den instrumentpanel som du vill duplicera klickar du på **Spara som**. En ny instrumentpanel öppnas med samma namn och ett nummer.
2. Byt namn på den duplicerade instrumentpanelen och ändra den som du vill.

Eller

1. I Inställningar för instrumentpanelen klickar du på **Spara som** på raden för den instrumentpanel som du vill duplicera.
2. Den duplicerade instrumentpanelen öppnas.
3. Byt namn på instrumentpanelen och ändra den som du vill.

## <a name="set-a-default-dashboard"></a>Ange en standardinstrumentpanel

Du kan ange valfri instrumentpanel som standard. Om du anger den till standard visas den som fliken längst till vänster i listan över instrumentpanelsflikar. Standardinstrumentpanelen visas när du öppnar Cloudyn-portalen.

- Klicka på den instrumentpanelsflik som du vill ange som standard och klicka sedan på **Standard** till höger.

Eller

1. Klicka på **Inställningar för instrumentpanelen** om du vill se en lista över tillgängliga instrumentpaneler och välj den instrumentpanel som du vill ange som standard.  
    ![instrumentpanelsalternativ för en standardinstrumentpanel](./media/dashboards/dashboard-options.png)
2. Klicka på **Standard** på raden för instrumentpanelen. Bekräftelserutan för standardinstrumentpanel visas.
3. Klicka på **Ja**. Instrumentpanelen anges som standard.

## <a name="management-dashboard"></a>Hanteringspanelen
Instrumentpanelen för hantering (eller MSP-instrumentpanelen för MSP-användare) belyser de huvudsakliga rapporttyperna.  
![Instrumentpanel för hantering som visar olika rapporter](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Sammanfattning av kostnadsenhet (endast Enterprise)
Den här widgeten sammanfattar de hanterade kostnadsentiteterna, inklusive antalet entiteter och antalet konton.
- Klicka på widgeten för att öppna rapporten för företagsinformation.

### <a name="cost-over-time"></a>Kostnad över tid
Den här widgeten kan hjälpa dig att upptäcka kostnadstrender. Den framhäver kostnaden för den senaste dagen baserat på trenden för de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten för faktisk kostnad över tid, där du kan visa och filtrera ytterligare information.

### <a name="asset-controller"></a>Tillgångskontrollant
Den här widgeten visar det antal instanser som körs från föregående dag över användningstrenden under de senaste 30 dagarna.
- Klicka på widgeten för att öppna instrumentpanelen för tillgångskontrollant.

### <a name="unused-ri-detector"></a>Detektor för oanvänd RI
Den här widgeten visar antalet oanvända Amazon EC2-reservationer.
- Klicka på widgeten för att öppna rapporten för aktuella oanvända reservationer, där du kan visa de oanvända reservationer som du kan ändra.

### <a name="cost-by-service"></a>Kostnad efter tjänst
Den här widgeten visar upplupna kostnader per tjänst för de senaste 30 dagarna. Hovra över cirkeldiagrammet för att se kostnaderna per tjänst.
- Klicka på widgeten för att öppna rapporten för analys av faktisk kostnad.

### <a name="potential-savings"></a>Potentiella besparingar
Den här widgeten visar prisrekommendationer för instanstyp för Amazon EC2 och Amazon RDS.
- Klicka på widgeten för att öppna rapporten för besparingsanalys. Den visar dina kostnader efter instanstyper med potentiella besparingar.

### <a name="compute-instances---daily-trend"></a>Beräkningsinstanser – daglig trend
Den här widgeten visar aktiva instanser efter typ för de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten för instanser över tid, där du kan visa en analys av alla instanser som körs under de senaste 30 dagarna.

### <a name="storage-by-department"></a>Lagring per avdelning
Den här widgeten visar de lagringstjänster som används av avdelningar. Hovra över cirkeldiagrammet för att se din lagringsförbrukning per avdelning.
- Klicka på widgeten för att öppna instrumentpanelen för S3-spårare.

## <a name="cost-controller-dashboard"></a>Instrumentpanel för kostnadskontrollant
Instrumentpanelen för kostnadskontrollant visar förkonfigurerade markeringar för kostnadsallokering.  
![Instrumentpanel för kostnadskontrollant som visar olika rapporter](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Kostnad över tid
Den här widgeten hjälper dig att upptäcka kostnadstrender. Den framhäver kostnaden för den senaste dagen baserat på trenden för de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten för faktisk kostnad över tid, där du kan visa och filtrera ytterligare information.

### <a name="monthly-cost-trends"></a>Månatliga kostnadstrender
Den här widgeten visar planerade upplupna utgifter och dina faktiska utgifter sedan början av månaden.
- Klicka på widgeten för att öppna rapporten för beräknad kostnad aktuell månad, som innehåller en sammanfattning av kostnad under månaden fram till dagens datum.

I den här rapporten visas kostnaden från början av månaden, kostnaden för föregående månad samt beräknad kostnad för aktuell månad. Den aktuella månadens beräknade kostnad beräknas genom addition av kostnad hittills under månaden och beräknad kostnad. Beräknad kostnad baseras på den kostnad som övervakas under de senaste 30 dagarna.

### <a name="12-month-planner"></a>12-månadsplanerare
Den här widgeten visar de beräknade kostnaderna under de kommande 12 månaderna samt potentiella besparingar.
- Klicka på widgeten för att öppna rapporten för årlig beräknad kostnad.

### <a name="cost-by-service"></a>Kostnad efter tjänst
Den här widgeten visar upplupna kostnader per tjänst för de senaste 30 dagarna.
- Hovra över cirkeldiagrammet för att se kostnaderna per tjänst.
- Klicka på widgeten för att öppna rapporten för analys av faktisk kostnad.

### <a name="cost-by-account"></a>Kostnad per konto
Den här widgeten visar upplupna kostnader per konto för de senaste 30 dagarna.
- Hovra över cirkeldiagrammet för att se kostnaderna per konto.
- Klicka på widgeten för att öppna rapporten för analys av faktisk kostnad.

### <a name="cost-trend-by-day"></a>Kostnadstrend per dag
Den här widgeten visar utgifter under de senaste 30 dagarna.
- Hovra över stapeldiagrammet för att se kostnader per dag.
- Klicka på widgeten för att öppna rapporten för faktisk kostnad över tid.

### <a name="cost-trend-by-month---last-6-months"></a>Kostnadstrend per månad – senaste 6 månaderna

Den här widgeten visar utgifter under de senaste sex månaderna.
- Hovra över stapeldiagrammet för att se kostnader per månad.
- Klicka på widgeten för att öppna rapporten för faktisk kostnad över tid.

## <a name="asset-controller-dashboard"></a>Instrumentpanel för tillgångskontrollant

Den här instrumentpanelen visar det antal instanser som körs, tillgängliga och använda diskar, distribution av instanstyper samt lagringsinformation.  
![Instrumentpanel för tillgångskontrollant som visar olika rapporter](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Beräkningsinstanser
Den här widgeten visar det antal instanser som körs baserat på användningstrenden under de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten för instanser över tid.

### <a name="disks"></a>Diskar
Den här widgeten visar det totala antal samt den volym av diskar som används och är tillgängliga.
- Klicka på widgeten för att öppna rapporten för aktiva diskar.

### <a name="instance-type-distribution"></a>Distribution av instanstyp
Den här widgeten visar instanstyperna i ett cirkeldiagram.
- Klicka på widgeten för att öppna rapporten för instansdistribution, som innehåller en analys av dina aktiva instanser efter den valda aggregeringen.

### <a name="compute-instances---daily-trend"></a>Beräkningsinstanser – daglig trend
Den här widgeten visar beräkningsinstanserna (punkt, reserverade samt på begäran) per dag under de senaste 30 dagarna.
- Hovra över grafen för att visa antalet beräkningsinstanser per typ per dag.
- Klicka på widgeten för att öppna rapporten för instanser över tid.

### <a name="all-buckets-s3"></a>Alla buckets (S3)
Den här widgeten visar den totala S3-lagringen samt det antal objekt som lagras.
- Klicka på widgeten för att öppna instrumentpanelen för S3-spårare. Instrumentpanelen hjälper dig att hitta, analysera och visa din nuvarande lagringsanvändning samt dina trender.

### <a name="sql-db-instances-rds"></a>SQL DB-instanser (RDS)
Den här widgeten visar det antal Amazon RDS-instanser som körs baserat på trenden för de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten för RDS-instanser över tid.

## <a name="optimizer-dashboard"></a>Optimizer-instrumentpanelen
Den här instrumentpanelen visar rekommendationer för nedskalning, oanvända resurser och potentiella besparingar.  
![Optimizer-instrumentpanelen som visar olika rapporter](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI-kalkylator
Den här widgeten visar antalet RI-köprekommendationer och potentiella årliga besparingar.
- Klicka på widgeten för att öppna kalkylatorn för reserverad instans, där du kan avgöra när du bör använda planer på begäran respektive reserverade priser.

### <a name="sizing"></a>Storlekar
Den här widgeten visar rekommenderad storlek och potentiella besparingar om detta har implementerats.
- Klicka på widgeten för att öppna rapporten för kostnadseffektiv EC2-storlek.

### <a name="unused-ri-detector"></a>Detektor för oanvänd RI
Den här widgeten visar antalet oanvända Amazon EC2-reservationer.
- Klicka på widgeten för att öppna rapporten för aktuella oanvända reservationer, där du kan visa de oanvända reservationer som du kan ändra.

###  <a name="available-disks"></a>Tillgängliga diskar
Den här widgeten visar antalet ej anslutna diskar i din distribution.
- Klicka på widgeten för att öppna rapporten för ej anslutna diskar.

### <a name="rds-ri-calculator"></a>RDS RI-kalkylator
Den här widgeten visar antalet reservationsrekommendationer för dina Amazon RDS-instanser samt potentiella besparingar.
- Klicka på widgeten för att öppna rapporten för RDS RI-köprekommendationer, där du kan se Cloudyn-rekommendationer om att använda reserverade instanser i stället för instanser på begäran.

### <a name="rds-sizing"></a>RDS-storlek
Den här widgeten visar antalet storleksrekommendationer och potentiella besparingar.
- Klicka på widgeten för att öppna rapporten för RDS-storleksrekommendationer, som visar detaljerade rekommendationer för Amazon RDS-storlek.

Optimeringsrekommendationerna baseras på användnings- och prestandadata som övervakas under den senaste månaden.

## <a name="s3-tracker-dashboard"></a>Instrumentpanelen för S3-spårare
Instrumentpanelen för S3-spårare hjälper dig att hitta, analysera och visa din nuvarande lagringsanvändning samt dina trender.  
![Instrumentpanelen för S3-spårare som visar olika rapporter](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Alla buckets
Den här widgeten visar den totala storleken på alla dina buckets i GB samt det totala antalet objekt i dina buckets.
- Klicka på widgeten för att öppna rapporten för distribution av S3-storlek. Rapporten hjälper dig att analysera S3-storleken efter bucket, mapp på toppnivå, lagringsklass och versionstillstånd.

### <a name="bucket-properties"></a>Bucketegenskaper
Den här widgeten visar det totala antalet lagringsbuckets.
- Klicka på widgeten för att visa rapporten för S3-bucketegenskaper.

### <a name="scan-status"></a>Genomsökningsstatus
Den här widgeten visar när den senaste S3-genomsökningen utfördes och när nästa kommer att starta.
- Klicka på widgeten för att öppna rapporten för S3-genomsökningsstatus.

### <a name="storage-by-bucket"></a>Lagring per bucket
Den här widgeten visar den procentandel som varje bucketlagringsklass använder.
- Klicka på widgeten för att öppna rapporten för distribution av S3-storlek. Rapporten hjälper dig att analysera S3-storleken efter bucket, mapp på toppnivå, lagringsklass och versionstillstånd.

### <a name="number-of-objects-by-bucket"></a>Antal objekt per bucket
Den här widgeten visar antalet objekt per bucket som faktiskt antal och som procentandel. Hovra över bucketen om du vill se det totala antalet objekt.
- Klicka på widgeten för att öppna rapporten för distribution av S3-storlek (genomsökningsbaserad).

## <a name="cloud-comparison-dashboard"></a>Instrumentpanel för molnjämförelse
Instrumentpanelen för molnjämförelse hjälper dig att jämföra kostnader från olika molnleverantörer utifrån prissättning, CPU-typ och RAM-storlek.  
![Instrumentpanel för molnjämförelse som visar olika rapporter](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2-kostnad i Azure efter instanstyp
Den här widgeten visar de senaste 30 dagarnas användning med priser på begäran. Den jämför kostnaden med den aktuella Amazon EC2-kostnaden kontra den potentiella kostnaden i Azure.
- Hovra över staplarna för att jämföra kostnader per instanstyp.
- Klicka på widgeten för att öppna rapporten för portning av din distribution – kostnadsanalys.

### <a name="ec2-cost-in-azure"></a>EC2-kostnad i Azure
Den här widgeten visar dina aktuella Amazon EC2-kostnader och jämför dem med Azure. Jämförelsen baseras på de senaste 30 dagarnas användning med priser på begäran.
- Klicka på widgeten för att öppna rapporten för portning av din distribution – kostnadsanalys.

### <a name="ec2azure-instance-type-mapping"></a>EC2/Azure-instanstypmappning
Den här widgeten visar den bästa mappningen av elastiska beräkningsenheter mellan Amazon EC2 och Azure.
- Klicka på widgeten för att öppna rapporten för instanstypmappning.

## <a name="next-steps"></a>Nästa steg
- Läs mer om rapporter i artikeln [Använda Cloudyn-rapporter](use-reports.md).

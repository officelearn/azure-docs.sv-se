---
title: Visa viktiga mått med Cloudyn instrumentpaneler i Azure | Microsoft Docs
description: Den här artikeln beskrivs hur du visar viktiga mått med instrumentpaneler i Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 7ea7cbf4526528eb481b6125b006d0c29d86deb2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58001071"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Visa nyckeln kostnad mått med instrumentpaneler

Instrumentpaneler i Cloudyn ger en översikt över rapporter. Instrumentpaneler kan du visa viktiga kostnadsmått i en enda vy. Du får även att företag trend markeringar för att hjälpa dig att fatta viktiga affärsbeslut.

Instrumentpaneler också för att skapa vyer för personer med olika ansvarsområden i din organisation, vilket kan innebära:

- Finansiella controller
- Program eller projekt ägare
- DevOps-tekniker
- Chefer

Instrumentpaneler består av widgetar och varje widget är i stort sett miniatyrbilden för en rapport. Klicka på en widget för att öppna dess rapport. När du har ändrat rapporter kan du spara dem i Mina rapporter och de läggs till på instrumentpanelen.

Instrumentpanelen versioner skiljer sig för hantering (MSP), Enterprise och Premium Cloudyn användare. Skillnaderna bestäms av åtkomstnivåer för entiteten. Läs mer om åtkomstnivåer [entitet åtkomstnivåer](tutorial-user-access.md#entity-access-levels).

Instrumentpanelen tillgänglighet beror på vilken typ av cloud service providerkonto som används när du visar instrumentpaneler. Vilken typ av information tillgänglig och som samlats in av Cloudyn påverkar rapporter i instrumentpaneler. Till exempel om du inte har ett AWS-konto visas sedan inte spårningsverktyget för S3-instrumentpanelen. På samma sätt, om du inte aktiverar Azure Resource Manager-åtkomst till Cloudyn sedan visas inte någon Azure-specifik information i optimering instrumentpanelswidgetar.

Du kan använda någon av de färdiga instrumentpanelerna eller du kan skapa din egen instrumentpanel med anpassade rapporter. Om du är bekant med Cloudyn-rapporter finns i [Använd Cloudyn rapporterar](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Skapa en anpassad instrumentpanel

Om du vill komma igång snabbt med en anpassad instrumentpanel kan duplicera du en befintlig för att använda dess egenskaper. Sedan kan du ändra den så att den passar dina behov. Klicka på den instrumentpanel som du vill kopiera **Spara som**. Du kan bara kopiera anpassade instrumentpaneler – du kan inte duplicera instrumentpaneler som ingår i Cloudyn.

Skapa en anpassad instrumentpanel:

1. På startsidan klickar du på **Lägg till ny +**. Sidan Min instrumentpanel visas.  
    ![Min instrumentpanelssida där du lägger till nya rapporter](./media/dashboards/my-dashboard.png)
2. Klicka på **Lägg till ny rapport**. Rutan Lägg till rapport visas.
3. Välj den rapport som du vill lägga till en instrumentpanelwidget. Widgeten har lagts till på instrumentpanelen.
4. Upprepa föregående steg tills instrumentpanelen är klar.
5. Om du vill ändra namnet på instrumentpanelen, klicka på namnet på instrumentpanelen på startsidan för instrumentpanelen och Skriv det nya namnet.

## <a name="modify-a-custom-dashboard"></a>Ändra en anpassad instrumentpanel

Du kan inte ändra de instrumentpaneler som ingår i Cloudyn som att skapa en anpassad instrumentpanel. Ändra en anpassad instrumentpanel för rapport:

1. I instrumentpanelen för att hitta den rapport som du vill ändra och klickar på **redigera**. Rapporten visas.
2. Göra några ändringar som du vill att rapporten och klicka på **spara**. Rapporten uppdateras och visar dina ändringar.

## <a name="share-a-custom-dashboard"></a>Dela en anpassad instrumentpanel

Du kan dela en anpassad instrumentpanel med andra att _offentliga_ eller _Mina entitet_. När du delar till offentlig kan kan alla användare visa instrumentpanelen. Endast användare med åtkomst till den aktuella entiteten kan visa instrumentpanelen när du delar till min entitet. Stegen för att dela en anpassad instrumentpanel med offentliga och min enhet är liknande.

Dela en anpassad instrumentpanel till offentligt:

1. I en instrumentpanel, klickar du på **inställningar för instrumentpanelen**. Rutan inställningar för instrumentpanelen visas.  
    ![inställningar för instrumentpanelen för en anpassad instrumentpanel](./media/dashboards/dashboard-options.png)
2. I rutan inställningar för instrumentpanelen klickar du på symbolen pilen och klickar sedan på **offentliga**. Den offentliga instrumentpanelen bekräftande dialogrutan visas.
3. Klicka på **Ja**. Instrumentpanelen är nu tillgänglig för andra.

## <a name="delete-a-custom-dashboard-report"></a>Ta bort en anpassad instrumentpanel-rapport

Du kan ta bort en anpassad rapport-komponent från instrumentpanelen. Tar bort rapporten från instrumentpanelen bort inte rapporten från rapportlistan över. I stället tar du bort rapporten tas den bort från instrumentpanelen endast.

Om du vill ta bort en instrumentpanel-komponent på komponenten instrumentpanelen klickar du på **ta bort**. Klicka på **ta bort** raderar komponenten instrumentpanel direkt.

## <a name="share-a-dashboard-enterprise"></a>Dela en instrumentpanel (företag)

Du kan dela anpassade instrumentpaneler för alla användare i din organisation eller med användare i den aktuella entiteten. Dela en instrumentpanel kan ge andra användare en snabb översikt över din KPI. När du delar en instrumentpanel, replikerar automatiskt instrumentpanelen till alla dina Cloudyn entiteter/kunder. Ändringar i den delade instrumentpanelen uppdateras automatiskt.

Att dela en instrumentpanel med alla användare, inklusive underentiteter:

1. På startsidan instrumentpanelen klickar du på **redigera**.
2. Klicka på **resursen** och välj sedan **offentliga**.
3. Dialogrutan Global offentliga instrumentpanelen visas.
4. Klicka på **Ja** ange instrumentpanelen som en global offentliga instrumentpanel.

Dela en instrumentpanel med alla användare av en aktuell entitet:

1. Startsidan instrumentpanelen klickar du på **redigera**.
2. Klicka på **resursen** och välj sedan **Mina entitet**.
3. Klicka på **Ja** ange instrumentpanelen som en offentlig instrumentpanel.

## <a name="duplicate-a-custom-dashboard"></a>Duplicera en anpassad instrumentpanel

När du skapar en ny instrumentpanel, kanske du vill använda liknande egenskaper från en befintlig instrumentpanel. Du kan duplicera instrumentpanelen som du vill skapa en ny.

Du kan endast duplicera anpassade instrumentpaneler. Du kan inte duplicera standard instrumentpaneler.

Att duplicera (klonar) en anpassad instrumentpanel:

1. Klicka på den instrumentpanel som du vill duplicera **Spara som**. En ny instrumentpanel öppnas med samma namn och ett nummer.
2. Byt namn på duplicerade instrumentpanelen och ändra det som du vill.

Eller

1. I inställningar för instrumentpanelen, klickar du på **Spara som** på raden i den instrumentpanel som du vill duplicera.
2. Duplicerade instrumentpanelen öppnas.
3. Byt namn på instrumentpanelen och ändra det som du vill.

## <a name="set-a-default-dashboard"></a>Ange en standardinstrumentpanel

Du kan ange vilken instrumentpanel som standard. Ange den till din standard gör det visas som fliken vänster i listan instrumentpanelen. Standardinstrumentpanelen visar när öppna Cloudyn-portalen.

- Klicka på fliken instrumentpanel du vill ange som standard och klicka sedan på **standard** till höger.

Eller

1. Klicka på **inställningar för instrumentpanelen** att se en lista över tillgängliga instrumentpaneler och välj den instrumentpanel som du vill ange som standard.  
    ![instrumentpanelen alternativ för en standardinstrumentpanel](./media/dashboards/dashboard-options.png)
2. Klicka på **standard** på raden i instrumentpanelen. Dialogrutan standard instrumentpanelen visas.
3. Klicka på **Ja**. Instrumentpanelen är inställd på standardvärdet.

## <a name="management-dashboard"></a>Hanteringspanelen
Hanteringen (eller MSP-instrumentpanel för MSP-användare) instrumentpanel innehåller höjdpunkter i huvudrapporten-typer.  
![Instrumentpanel för hantering som visar olika rapporter](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Kostnadsenhet (endast Enterprise)
Den här widgeten sammanfattas hanterade kostnaden-entiteter, t.ex. antal entiteter och antalet konton.
- Klicka på widgeten för att öppna rapporten information om Enterprise.

### <a name="cost-over-time"></a>Kostnad över tid
Den här widgeten kan hjälpa dig att upptäcka kostnadstrender. Den går igenom kostnaden för den sista dagen, baserat på trend de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten Actual Cost Over Time för att visa och filtrera ytterligare information.

### <a name="asset-controller"></a>Tillgångskontrollant
Widgenten visar antalet instanser som körs från föregående dag, ovan användningstrend under de senaste 30 dagarna.
- Klicka på widgeten för att öppna Tillgångskontrollant-instrumentpanelen.

### <a name="unused-ri-detector"></a>Oanvända RI detektor
Widgenten visar antalet Amazon EC2 oanvända reservationer.
- Klicka på widgeten för att öppna rapporten för närvarande oanvända reservationer där du kan visa den oanvända reservationer som du kan ändra.

### <a name="cost-by-service"></a>Kostnad per tjänst
Den här widgeten markeras amorterade kostnader genom tjänsten under de senaste 30 dagarna. Hovra över cirkeldiagrammet för att visa kostnaderna per tjänst.
- Klicka på widgeten för att öppna rapporten analys av faktiska kostnader.

### <a name="potential-savings"></a>Potentiella besparingar
Widgenten visar instanstyp priser rekommendationer för Amazon EC2 och Amazon Fjärrskrivbordstjänster.
- Klicka på widgeten öppna besparingar analysrapporten. Den visar dina kostnader med instanstyper med potentiella besparingar.

### <a name="compute-instances---daily-trend"></a>Beräkningsinstanser – daglig Trend
Widgenten visar aktiva instanser efter typ går under de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten instanser över tid där du kan visa en sammanställning av alla instanser som körs under de senaste 30 dagarna.

### <a name="storage-by-department"></a>Lagring per avdelning
Widgenten visar storage-tjänsterna som används av avdelningar. Hovra över cirkeldiagrammet för att se din lagringsanvändning per avdelning.
- Klicka på widgeten för att öppna spårningsverktyget för S3-instrumentpanelen.

## <a name="cost-controller-dashboard"></a>Kostnad instrumentpanel för kontrollenhet
Kostnaden Controller instrumentpanelen visar förinställda cost allocation höjdpunkter.  
![Cost kontrollanten instrumentpanelen som visar olika rapporter](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Kostnad över tid
Den här widgeten hjälper dig att upptäcka kostnadstrender. Den går igenom kostnaden för den sista dagen, baserat på trend de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten Actual Cost Over Time för att visa och filtrera ytterligare information.

### <a name="monthly-cost-trends"></a>Månatlig kostnad trender
Widgenten visar beräknade amorterade utgifter och den faktiska utgifter sedan i början av månaden.
- Klicka på widgeten för att öppna rapporten Current Month Projected Cost, vilket ger en sammanfattning av månad hittills kostnad.

Den här rapporten visar kostnaden från början av månaden och kostnaden för föregående månad aktuell månad projected cost. Den aktuella månaden beräknade kostnaden beräknas genom att lägga till den uppdaterade månadskostnaden och projektion. Projektionen som baseras på kostnaden övervakas under de senaste 30 dagarna.

### <a name="12-month-planner"></a>12 månaders Planner
Widgenten visar beräknade kostnader över kommande 12 månader och potentiella besparingar.
- Klicka på widgeten för att öppna rapporten Annual Projected Cost.

### <a name="cost-by-service"></a>Kostnad per tjänst
Den här widgeten markeras amorterade kostnader genom tjänsten under de senaste 30 dagarna.
- Hovra över cirkeldiagrammet för att visa kostnaderna per tjänst.
- Klicka på widgeten för att öppna rapporten analys av faktiska kostnader.

### <a name="cost-by-account"></a>Kostnad per konto
Widgenten visar amorterade kostnader per konto under de senaste 30 dagarna.
- Hovra över cirkeldiagrammet för att visa kostnaderna per konto.
- Klicka på widgeten för att öppna rapporten analys av faktiska kostnader.

### <a name="cost-trend-by-day"></a>Kostnadstrend per dag
Den här widgeten höjdpunkter spendera under de senaste 30 dagarna.
- Hovra över stapeldiagrammet för att visa kostnaderna per dag.
- Klicka på widgeten för att öppna rapporten Actual Cost Over Time.

### <a name="cost-trend-by-month---last-6-months"></a>Kostnadstrend per månad – senaste 6 månaderna

Den här widgeten höjdpunkter spendera under de senaste sex månaderna.
- Hovra över stapeldiagrammet för att se kostnader per månad.
- Klicka på widgeten för att öppna rapporten Actual Cost Over Time.

## <a name="asset-controller-dashboard"></a>Instrumentpanel för tillgången kontrollenhet

Den här instrumentpanelen visar antalet instanser, tillgänglig och i användning diskar, distribution av instanstyper och lagringsinformation som körs.  
![Instrumentpanel för kontrollenhet tillgången som visar olika rapporter](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Instanser
Widgenten visar antalet instanser baserat på den informationen om användningstrenden under de senaste 30 dagarna som körs.
- Klicka på widgeten för att öppna rapporten instanser över tid.

### <a name="disks"></a>Diskar
Widgenten visar totalt antal och volym av diskar som används och är tillgängliga.
- Klicka på widgeten för att öppna rapporten Active diskar.

### <a name="instance-type-distribution"></a>Instans typ av Distribution
Widgenten visar instanstyper i ett cirkeldiagram.
- Klicka på widgeten att öppna rapporten instans Distribution, vilket ger en detaljerad analys av din aktiva instanser av den valda aggregeringen.

### <a name="compute-instances---daily-trend"></a>Beräkningsinstanser – daglig Trend
Widgenten visar beräkningsinstanser (upptäcka reserverade och på begäran) per dag under de senaste 30 dagarna.
- Hovra över graph för att visa antalet beräkningsinstanser per typ per dag.
- Klicka på widgeten för att öppna rapporten instanser över tid.

### <a name="all-buckets-s3"></a>Alla Buckets (S3)
Widgenten visar den totala S3 lagring och antalet objekt som lagras.
- Klicka på widgeten för att öppna instrumentpanelen för S3-spåraren. Med instrumentpanelen kan du hitta, analysera och visa dina aktuella lagringsanvändningen och trender.

### <a name="sql-db-instances-rds"></a>SQL-Databasinstanser (RDS)
Widgenten visar antalet körda Amazon RDS-instanser baserat på trend de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten RDS-instansen över tid.

## <a name="optimizer-dashboard"></a>Instrumentpanel för optimering
Den här instrumentpanelen visar downsizing rekommendationer, oanvända resurser och potentiella besparingar.  
![Optimering instrumentpanelen som visar olika rapporter](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI-kalkylator
Widgenten visar antalet köprekommendationer för RI och visar den potentiella årliga besparingar.
- Klicka på widgeten för att öppna den reserverade instansen Kalkylatorn där du kan bestämma när du ska använda på begäran jämfört med reserverade prissättningar.

### <a name="sizing"></a>Storleksändring
Den här widgeten illustrerar storlek rekommenderas och potentiella besparingar om implementerat.
- Klicka på widgeten för att öppna rapporten EC2 Cost Effective Sizing Recommendations.

### <a name="unused-ri-detector"></a>Oanvända RI detektor
Widgenten visar antalet Amazon EC2 oanvända reservationer.
- Klicka på widgeten för att öppna rapporten för närvarande oanvända reservationer där du kan visa de oanvända reservationer som du kan ändra.

###  <a name="available-disks"></a>Tillgängliga diskar
Widgenten visar antalet ej anslutna diskar i distributionen.
- Klicka på widgeten för att öppna rapporten icke anslutna diskar.

### <a name="rds-ri-calculator"></a>RDS RI-kalkylator
Widgenten visar antalet reserverade rekommendationer för Amazon RDS-instanser och potentiella besparingar.
- Klicka på widgeten för att öppna rapporten RDS RI Buying Recommendations där du kan se Cloudyn rekommendationer för att använda reserverade instanser i stället för på begäran-instanser.

### <a name="rds-sizing"></a>RDS-storlek
Widgenten visar antalet storleksrekommendationer och potentiella besparingar.
- Klicka på widgeten för att öppna rapporten RDS Storleksrekommendationer som visar detaljerad Amazon RDS Bedömningsrekommendationer.

Rekommendationer för optimering baseras på användning och prestanda övervakas den senaste månaden.

## <a name="s3-tracker-dashboard"></a>S3-spåraren instrumentpanel
Spårningsverktyget för S3-instrumentpanelen kan du hitta, analysera och visa dina aktuella lagringsanvändningen och trender.  
![S3-spåraren instrumentpanelen som visar olika rapporter](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Alla Buckets
Widgenten visar den totala storleken på alla dina buckets, i GB, och det totala antalet objekt i din buckets.
- Klicka på widgeten för att öppna rapporten för Distribution av S3 storlek. Rapporten kan du analysera storleken på din S3 genom bucket, toppnivåmapp, lagringsklass och versionstillstånd.

### <a name="bucket-properties"></a>Bucket-egenskaper
Widgenten visar det totala antalet buckets för lagring.
- Klicka på widgeten om du vill visa egenskaper för S3-Bucket-rapporten.

### <a name="scan-status"></a>Genomsökningsstatus
Widgenten visar när den senaste S3-genomsökningen gjordes och när nästa kommando startar.
- Klicka på widgeten för att öppna rapporten S3 skanna Status.

### <a name="storage-by-bucket"></a>Lagring av Bucket
Widgenten visar procentandelen som varje bucket lagringsklass använder.
- Klicka på widgeten för att öppna rapporten för Distribution av S3 storlek. Rapporten kan du analysera storleken på din S3 genom bucket, toppnivåmapp, lagringsklass och versionstillstånd.

### <a name="number-of-objects-by-bucket"></a>Antal objekt efter Bucket
Widgenten visar antal objekt per bucket i faktiska antal och procentandel. Hovra över en bucket för att se det totala antalet objekten.
- Klicka på widgeten för att öppna rapporten för Distribution av S3 storlek (sökning baserat).

## <a name="cloud-comparison-dashboard"></a>Instrumentpanel för cloud-jämförelse
Med en jämförelse av Molnapps-instrumentpanelen kan du jämföra kostnader från olika molnleverantörer baserat på priser, processortyp och RAM-storleken.  
![Jämförelse av instrumentpanelen som visar olika rapporter i molnet](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Kostnaden i Azure genom instanstyp
Widgenten visar de senaste 30 dagarna av användning i priserna för på begäran. Jämför kostnaderna med de aktuella Amazon EC2 kostnaden jämfört med potentialen kostnaden i Azure.
- Hovra över staplarna jämföra kostnader per Instanstypen.
- Klicka på widgeten för att öppna porta din distributionen – Cost Analysis-rapporten.

### <a name="ec2-cost-in-azure"></a>EC2 Kostnaden i Azure
Widgenten visar dina nuvarande Amazon EC2-kostnader och jämför dem med Azure. Jämförelsen baseras på de senaste 30 dagarna av användning i priserna för på begäran.
- Klicka på widgeten för att öppna den porta din distribution – Cost Analysis-rapporten.

### <a name="ec2azure-instance-type-mapping"></a>EC2/Azure-instans Typmappning
Widgenten visar bästa mappningen av elastiska beräkningsenheter mellan Amazon EC2 och Azure.
- Klicka på widgeten för att öppna rapporten instanser typ mappning.

## <a name="next-steps"></a>Nästa steg
- Läs den [Använd Cloudyn rapporterar](use-reports.md) du lär dig mer om rapporter.

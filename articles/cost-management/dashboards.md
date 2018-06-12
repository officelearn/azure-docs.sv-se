---
title: Visar viktiga mått i Azure kostnaden Management instrumentpaneler | Microsoft Docs
description: Den här artikeln beskriver hur visar viktiga mått med instrumentpaneler i Azure kostnaden Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b1dc2e2eca900ca0ae72329c3c373b2d24f1b2e0
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304099"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Visa nyckeln kostnad mått med instrumentpaneler

Instrumentpaneler i Cloudyn ger en övergripande bild av rapporter. Instrumentpaneler kan du visa viktiga kostnadsmått i en enda vy. De ger också business trend markeras som hjälper dig att fatta viktiga affärsbeslut.

Instrumentpaneler används också för att skapa vyer för personer med olika ansvarsområdena i din organisation, vilket kan innebära:

- Finansiella domänkontrollant
- Program eller projekt ägare
- DevOps-tekniker
- Chefer

Instrumentpaneler består av widgetar och varje widget är i grunden en miniatyrbild för rapporten. Klicka på en widget om du vill öppna rapporten. När du anpassar rapporter kan du spara dem i Mina rapporter och de har lagts till på instrumentpanelen.

Instrumentpanelen versioner skiljer sig för hantering (MSP), Enterprise och Premium Cloudyn användare. Skillnaderna bestäms av entiteten åtkomstnivåer. Läs mer om åtkomstnivåer [entitet åtkomstnivåer](tutorial-user-access.md#entity-access-levels).

Instrumentpanelen tillgängliga beror på vilken typ av cloud service provider-konto som används när du visar instrumentpaneler. Typ av information som är tillgänglig och som samlats in av Cloudyn påverkar rapporter i instrumentpaneler. Till exempel om du inte har ett konto för AWS visas sedan inte S3 spåraren instrumentpanelen. På samma sätt om du inte aktiverar Azure Resource Manager åtkomst till Cloudyn visas sedan inte någon Azure-specifik information i instrumentpanelswidgetarna för optimering.

Du kan använda någon av färdiga instrumentpaneler eller du kan skapa en egen instrumentpanel med anpassade rapporter. Om du inte är bekant med Cloudyn rapporter, se [Använd kostnad rapporter](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Skapa en anpassad instrumentpanel

Du kan duplicera en befintlig om du vill använda dess egenskaper för att snabbt komma igång med en anpassad instrumentpanel. Sedan kan du ändra den så att den passar dina behov. Klicka på den instrumentpanel du vill kopiera, **Spara som**. Du kan bara kopiera anpassade instrumentpaneler – du kan inte duplicera instrumentpaneler som ingår i Cloudyn.

Skapa en anpassad instrumentpanel:

1. Klicka på startsidan, **Lägg till ny +**. Min instrumentpanel sidan visas.  
    ![Min instrumentpanel](./media/dashboards/my-dashboard.png)
2. Klicka på **Lägg till ny rapport**. Kryssrutan Lägg till rapport visas.
3. Välj den rapport som du vill lägga till instrumentpanelwidgeten. Widgeten läggs till på instrumentpanelen.
4. Upprepa ovanstående steg tills instrumentpanelen är klar.
5. Om du vill ändra namnet på instrumentpanelen, klicka på namnet på instrumentpanelen på startsidan för instrumentpanelen och Skriv det nya namnet.

## <a name="modify-a-custom-dashboard"></a>Ändra en anpassad instrumentpanel

Du kan inte ändra instrumentpaneler som ingår i Cloudyn som skapar en anpassad instrumentpanel. Ändra en anpassade instrumentpanel rapport:

1. I instrumentpanelen för att hitta den rapport som du vill ändra och klicka på **redigera**. Rapporten visas.
2. Gör de ändringar som du vill använda i rapporten och klicka på **spara**. Rapporten uppdateras och visar dina ändringar.

## <a name="share-a-custom-dashboard"></a>Dela en anpassade instrumentpanel

Du kan dela en anpassade instrumentpanel med andra _offentliga_ eller _Mina entiteten_. När du delar på offentlig kan kan alla användare visa instrumentpanelen. Endast användare med åtkomst till den aktuella enheten kan visa instrumentpanelen när du delar till min enhet. Stegen för att dela en anpassade instrumentpanel med offentliga och Mina entiteten är liknande.

Så här delar en anpassade instrumentpanel till offentligt:

1. Klicka på en instrumentpanel, **inställningar för instrumentpanelen**. Dialogrutan Inställningar för instrumentpanelen visas.  
    ![instrumentpanelen alternativ](./media/dashboards/dashboard-options.png)
2. I dialogrutan Inställningar för instrumentpanelen klickar du på pilen symbol och klicka sedan på **offentliga**. Offentliga instrumentpanelen bekräftelsedialogrutan visas.
3. Klicka på **Ja**. Instrumentpanelen är nu tillgänglig för andra.

## <a name="delete-a-custom-dashboard-report"></a>Ta bort en anpassade instrumentpanel-rapport

Du kan ta bort en anpassad rapport komponent från instrumentpanelen. Tar bort rapporten från instrumentpanelen bort inte rapporten från rapportlistan över. I stället om du tar bort rapporten tas den bort från instrumentpanelen endast.

Ta bort en instrumentpanel-komponent på komponenten instrumentpanelen, klicka på **ta bort**. Klicka på **ta bort** omedelbart tar bort komponenten instrumentpanelen.

## <a name="share-a-dashboard-enterprise"></a>Dela instrumentpanelen (Enterprise)

Du kan dela anpassade instrumentpaneler för alla användare i din organisation eller med användare i den aktuella entiteten. Dela en instrumentpanel kan ge andra en snabb översikt över för KPI: N. När du delar en instrumentpanel, replikerar automatiskt instrumentpanelen till alla dina Cloudyn entiteter/kunder. Ändringar i delad instrumentpanelen uppdateras automatiskt.

Dela en instrumentpanel med alla användare, inklusive underentiteter:

1. På startsidan för instrumentpanelen klickar du på **redigera**.
2. Klicka på **resursen** och välj sedan **offentliga**.
3. Dialogrutan Bekräfta globala offentliga instrumentpanelen visas.
4. Klicka på **Ja** att ställa in instrumentpanelen som en global offentliga instrumentpanel.

Dela en instrumentpanel med alla användare av en aktuell enhet:

1. Klicka på startsidan för instrumentpanelen **redigera**.
2. Klicka på **resursen** och välj sedan **Mina entiteten**.
3. Klicka på **Ja** att ställa in instrumentpanelen som en offentlig instrumentpanel.

## <a name="duplicate-a-custom-dashboard"></a>Duplicera en anpassade instrumentpanel

När du skapar en ny instrumentpanel kan du vill använda samma egenskaper från en befintlig instrumentpanel. Du kan duplicera instrumentpanelen för att skapa en ny.

Du kan bara duplicera anpassade instrumentpaneler. Du kan inte duplicera standard instrumentpaneler.

Duplicera (klona) en anpassad instrumentpanel:

1. Klicka på den instrumentpanel du vill duplicera **Spara som**. En ny instrumentpanel öppnas med samma namn och en siffra.
2. Byt namn på duplicerade instrumentpanelen och ändra den som du vill.

Eller

1. I inställningar för instrumentpanelen, klickar du på **Spara som** på raden i den instrumentpanel du vill duplicera.
2. Duplicerade instrumentpanelen öppnas.
3. Byt namn på instrumentpanelen och ändra den som du vill.

## <a name="set-a-default-dashboard"></a>Ange en standardinstrumentpanel

Du kan ange en instrumentpanel som standard. Ange värdet till din gör det visas som vänstra fliken i listan instrumentpanelen. Standardinstrumentpanelen visar när öppna Cloudyn-portalen.

- Klicka på fliken instrumentpanel du vill ange som standard och klicka sedan på **standard** till höger.

Eller

1. Klicka på **inställningar för instrumentpanelen** att se en lista över tillgängliga instrumentpaneler och markera den instrumentpanel du vill ange som standard.  
    ![instrumentpanelen alternativ](./media/dashboards/dashboard-options.png)
2. Klicka på **standard** på raden i instrumentpanelen. Dialogrutan Bekräfta standard instrumentpanelen visas.
3. Klicka på **Ja**. Instrumentpanelen är inställd på standardvärdet.

## <a name="management-dashboard"></a>Hanteringspanelen
Hanteringen (eller MSP-instrumentpanelen för MSP-användare) instrumentpanelen innehåller visar vilka huvudrapporten.  
![Hanteringspanelen](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Kostnad entitet Sammanfattning (endast Enterprise)
Denna widget sammanfattas kostnaden för hanterade entiteter, inklusive antal entiteter och antalet konton.
- Klicka på widgeten för att öppna rapporten företagets information.

### <a name="cost-over-time"></a>Kostnad över tid
Denna widget kan du lättare upptäcka kostnaden trender. Den visar kostnaden för den senaste dagen, baserat på trend för de senaste 30 dagarna.
- Klicka på widgeten att öppna den faktiska kostnaden över tid att visa och filtrera ytterligare information.

### <a name="asset-controller"></a>Styrenhet för tillgångsinformation
Widgenten visar antalet instanser som körs från föregående dag, ovan trend för användning under de senaste 30 dagarna.
- Klicka på widgeten för att öppna instrumentpanelen tillgången domänkontrollant.

### <a name="unused-ri-detector"></a>Oanvända RI detektor
Widgenten visar antalet Amazon EC2 oanvända reservationer.
- Klicka på widgeten att öppna den för närvarande inte används reservationer där du kan visa den oanvända reservationer som du kan ändra.

### <a name="cost-by-service"></a>Kostnaden med tjänsten
Widgenten visar amorterade kostnader av tjänsten för de senaste 30 dagarna. Hovra över cirkeldiagrammet för att visa kostnaderna per tjänst.
- Klicka på widgeten öppna verklig kostnad analysrapporten.

### <a name="potential-savings"></a>Potentiella besparingar
Widgenten visar instanstyp priser rekommendationer för Amazon EC2 och Amazon Fjärrskrivbordstjänster.
- Klicka på widget öppna rapporten besparingar. Det visar dina kostnader per instans typer med potentiella besparingar.

### <a name="compute-instances---daily-trend"></a>Compute-instanser - dagliga Trend
Widgenten visar aktiva instanser efter typ, under de senaste 30 dagarna.
- Klicka på widgeten för att öppna rapporten instanser över tid, där du kan visa en sammanställning av alla instanser som körs under de senaste 30 dagarna.

### <a name="storage-by-department"></a>Lagring av avdelning
Widgenten visar storage-tjänster som används av avdelningar. Hovra över cirkeldiagrammet om du vill se användningen av lagringsutrymme per avdelning.
- Klicka på widgeten öppna S3 spåraren instrumentpanelen.

## <a name="cost-controller-dashboard"></a>Kostnad Controller instrumentpanelen
Kostnaden Controller instrumentpanelen visar förinställda kostnaden allokering visar.  
![Kostnad Controller instrumentpanelen](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Kostnad över tid
Denna widget hjälper dig att upptäcka kostnaden trender. Den visar kostnaden för den senaste dagen, baserat på trend för de senaste 30 dagarna.
- Klicka på widgeten att öppna den faktiska kostnaden över tid att visa och filtrera ytterligare information.

### <a name="monthly-cost-trends"></a>Månatliga kostnad trender
Widgenten visar planerade amorterade utgifter och den faktiska tillbringar sedan början på månaden.
- Klicka på widgeten att öppna den aktuella månaden beräknad kostnad, vilket ger en översikt över månad hittills kostnad.

Den här rapporten visar kostnaden från början på månaden, kostnaden för föregående månad och den aktuella månad planerat kostnaden. Den aktuella månaden planerad kostnad beräknas genom att lägga till den uppdaterade månadskostnaden och projektion. Projektionen som baseras på kostnaden övervakas under de senaste 30 dagarna.

### <a name="12-month-planner"></a>12 månad Planner
Widgenten visar beräknade kostnader över nästa 12 månader och potentiella besparingar.
- Klicka på widgeten för att öppna rapporten årliga beräknad kostnad.

### <a name="cost-by-service"></a>Kostnaden med tjänsten
Widgenten visar amorterade kostnader av tjänsten för de senaste 30 dagarna.
- Hovra över cirkeldiagrammet för att visa kostnaderna per tjänst.
- Klicka på widgeten öppna verklig kostnad analysrapporten.

### <a name="cost-by-account"></a>Kostnaden per konto
Widgenten visar amorterade kostnader per konto under de senaste 30 dagarna.
- Hovra över cirkeldiagrammet för att visa kostnaderna per konto.
- Klicka på widgeten öppna verklig kostnad analysrapporten.

### <a name="cost-trend-by-day"></a>Kostnad Trend per dag
Widgenten visar tillbringar under de senaste 30 dagarna.
- Hovra över stapeldiagrammet till se kostnader per dag.
- Klicka på widgeten för att öppna rapporten faktiska kostnaden över tid.

### <a name="cost-trend-by-month---last-6-months"></a>Kostnad Trend per månad – senaste 6 månaderna

Widgenten visar utgifter över de senaste sex månaderna.
- Hovra över stapeldiagrammet till se kostnader per månad.
- Klicka på widgeten för att öppna rapporten faktiska kostnaden över tid.

## <a name="asset-controller-dashboard"></a>Tillgångsinformation Controller instrumentpanelen

Den här instrumentpanelen visar antalet instanser, tillgänglig och används diskar, distribution av instans typer och storage-informationen som körs.  
![Tillgångsinformation Controller instrumentpanelen](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Compute-instanser
Widgenten visar antalet instanser, baserat på trend för användning under de senaste 30 dagarna som körs.
- Klicka på widgeten för att öppna rapporten instanser över tid.

### <a name="disks"></a>Diskar
Widgenten visar det totala antalet och volymen av diskar som används och är tillgängliga.
- Klicka på widgeten för att öppna rapporten Active diskar.

### <a name="instance-type-distribution"></a>Instansen typen Distribution
Widgenten visar instans-typer i ett cirkeldiagram.
- Klicka på widgeten att öppna rapporten instans Distribution som innehåller en uppdelning av din aktiva instanser av den valda aggregeringen.

### <a name="compute-instances---daily-trend"></a>Compute-instanser - dagliga Trend
Widgenten visar compute-instanser (plats, reserverade och på begäran) per dag under de senaste 30 dagarna.
- Hovra över att diagrammet visar antalet compute-instanser per typ per dag.
- Klicka på widgeten för att öppna rapporten instanser över tid.

### <a name="all-buckets-s3"></a>Alla Buckets (S3)
Widgenten visar den totala S3 lagring och antalet objekt som lagras.
- Klicka på widgeten öppna S3 spåraren instrumentpanelen. Med instrumentpanelen kan du hitta, analysera och visa dina aktuella lagringsanvändningen och trender.

### <a name="sql-db-instances-rds"></a>SQL DB-instanser (RDS)
Widgenten visar antalet Amazon RDS instanser, baserat på de senaste 30 dagarna trend som körs.
- Klicka på widgeten för att öppna rapporten RDS instans över tid.

## <a name="optimizer-dashboard"></a>Instrumentpanel för optimering
Den här instrumentpanelen visar downsizing rekommendationer, oanvända resurser och potentiella besparingar.  
![Instrumentpanel för optimering](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI Kalkylatorn
Widgenten visar antalet RI köpa rekommendationer och visar de potentiella årliga besparingarna.
- Klicka på widgeten öppna reserverade instans Kalkylatorn där du kan bestämma när du ska använda på begäran kontra reserverade prissättning.

### <a name="sizing"></a>Storleksändring
Denna widget illustrerar sizing rekommenderas och potentiella besparingar om implementerad.
- Klicka på widgeten för att öppna rapporten EC2 kostnadseffektiv Sizing rekommendationer.

### <a name="unused-ri-detector"></a>Oanvända RI detektor
Widgenten visar antalet Amazon EC2 oanvända reservationer.
- Klicka på widgeten att öppna den för närvarande inte används reservationer där du kan visa oanvända reservationer som du kan ändra.

###  <a name="available-disks"></a>Tillgängliga diskar
Widgenten visar antalet ej anslutna diskar i distributionen.
- Klicka på widgeten för att öppna rapporten ej anslutna diskar.

### <a name="rds-ri-calculator"></a>RDS RI Kalkylatorn
Widgenten visar antalet reservation rekommendationer för Amazon RDS-instanser och potentiella besparingar.
- Klicka på widgeten för att öppna rapporten RDS RI köpa rekommendationer där du kan se Cloudyn rekommendationer för att använda reserverade instanser i stället för på-begäran-instanser.

### <a name="rds-sizing"></a>RDS storlek
Widgenten visar antalet sizing rekommendationer och potentiella besparingar.
- Klicka på widgeten för att öppna rapporten RDS Sizing rekommendationer som visar detaljerad Amazon RDS storleksanpassa rekommendationer.

Optimering rekommendationer baserat på informationen användnings- och prestandadata som övervakas i den senaste månaden.

## <a name="s3-tracker-dashboard"></a>S3-spåraren instrumentpanelen
S3-spåraren instrumentpanelen hjälper dig att hitta, analysera och visa dina aktuella lagringsanvändningen och trender.  
![S3-spåraren instrumentpanelen](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Alla Buckets
Widgenten visar den totala storleken på alla dina buckets, i GB, och det totala antalet objekt i din buckets.
- Klicka på widgeten för att öppna rapporten Distribution S3 storlek. Rapporten hjälper dig att analysera storleken på din S3 av bucket, mapp, lagringsklass och versionstillstånd.

### <a name="bucket-properties"></a>Bucket-egenskaper
Widgenten visar det totala antalet buckets för lagring.
- Klicka på widgeten om du vill visa egenskaper för S3-Bucket-rapporten.

### <a name="scan-status"></a>Skanna Status
Widgenten visar när den senaste S3 genomsökningen gjordes och när nästa startas.
- Klicka på widgeten för att öppna rapporten S3 skanna Status.

### <a name="storage-by-bucket"></a>Lagring av Bucket
Widgenten visar procentandelen som använder varje klass för lagring av bucket.
- Klicka på widgeten för att öppna rapporten Distribution S3 storlek. Rapporten hjälper dig att analysera storleken på din S3 av bucket, mapp, lagringsklass och versionstillstånd.

### <a name="number-of-objects-by-bucket"></a>Antal objekt med Bucket
Widgenten visar antalet objekt per bucket i faktiska antal och procent. Hovra över bucket att se det totala antalet objekten.
- Klicka på widgeten för att öppna rapporten Distribution S3 storlek (sökning baserat).

## <a name="cloud-comparison-dashboard"></a>Cloud jämförelse instrumentpanelen
Molnet jämförelse instrumentpanelen hjälper dig att jämföra kostnader från olika molntjänstleverantörer baserat på prissättning, processortyp och RAM-storlek.  
![Cloud jämförelse instrumentpanelen](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Kostnaden i Azure genom instanstyp
Widgenten visar de senaste 30 dagarna för användning i priser på begäran. Den comares kostnaden med den aktuella Amazon EC2 kostnaden jämfört med potentiella kostnaden i Azure.
- Hovra över staplar jämföra kostnader per instanstyp.
- Klicka på widgeten för att öppna den portera distributionen – kostnaden analysrapporten.

### <a name="ec2-cost-in-azure"></a>EC2 Kostnaden i Azure
Widgenten visar aktuella Amazon EC2 kostnaderna och jämför dem till Azure. Jämförelsen baseras på de senaste 30 dagarna för användning i priser på begäran.
- Klicka på widgeten för att öppna den portera distributionen - kostnad analysrapporten.

### <a name="ec2azure-instance-type-mapping"></a>Mappning EC2-/ Azure-instans
Widgenten visar bästa mappningen av elastisk beräknings-enheter mellan Amazon EC2 och Azure.
- Klicka på widgeten för att öppna rapporten instanser typen mappning.

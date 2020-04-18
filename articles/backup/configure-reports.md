---
title: Konfigurera Azure Backup-rapporter
description: Konfigurera och visa rapporter för Azure Backup med hjälp av Log Analytics- och Azure-arbetsböcker
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcc87deb19190149329369ca58f54b45b62b41fe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617822"
---
# <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter

Ett vanligt krav för administratörer för säkerhetskopiering är att få insikter om säkerhetskopior baserat på data som sträcker sig över en lång tidsperiod. Användningsfall för en sådan lösning inkluderar:

 - Allokering och prognostisering av molnlagring som förbrukas.
 - Granskning av säkerhetskopior och återställningar.
 - Identifiera viktiga trender på olika nivåer av granularitet.

Idag tillhandahåller Azure Backup en rapporteringslösning som använder [Azure Monitor-loggar](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) och [Azure-arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks). Dessa resurser hjälper dig att få rika insikter om dina säkerhetskopior över hela din backup egendom. I den här artikeln beskrivs hur du konfigurerar och visar Azure Backup-rapporter.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Säkerhetskopieringsrapporter stöds för virtuella Azure-datorer, SQL i virtuella Azure-datorer, SAP HANA/ASE i Virtuella Azure-datorer, MICROSOFT Azure Recovery Services (MARS),Microsoft Azure Backup Server (MABS) och DPM (System Center Data Protection Manager).
* För DPM-arbetsbelastningar stöds säkerhetskopieringsrapporter för DPM Version 5.1.363.0 och högre och Agent Version 2.0.9127.0 och senare.
* För MABS-arbetsbelastningar stöds säkerhetskopieringsrapporter för MABS Version 13.0.415.0 och högre och Agent Version 2.0.9170.0 och senare.
* Säkerhetskopieringsrapporter kan visas i alla säkerhetskopieringsobjekt, valv, prenumerationer och regioner så länge deras data skickas till en Log Analytics-arbetsyta som användaren har åtkomst till. Om du vill visa rapporter för en uppsättning valv behöver du bara ha läsaråtkomst till arbetsytan Log Analytics som valven skickar sina data till. Du behöver inte ha tillgång till de enskilda valven.
* Om du är en [Azure Lighthouse-användare](https://docs.microsoft.com/azure/lighthouse/) med delegerad åtkomst till dina kunders prenumerationer kan du använda dessa rapporter med Azure Lighthouse för att visa rapporter över alla dina klienter.
* Data för jobb för säkerhetskopiering av loggar visas för närvarande inte i rapporterna.

## <a name="get-started"></a>Kom igång

Följ dessa steg för att börja använda rapporterna.

#### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Skapa en log Analytics-arbetsyta eller använd en befintlig

Konfigurera en eller flera Log Analytics-arbetsytor för att lagra rapporteringsdata för säkerhetskopiering. Den plats och prenumeration där den här Log Analytics-arbetsytan kan skapas är oberoende av den plats och prenumeration där dina valv finns. 

Information om hur du konfigurerar en Log Analytics-arbetsyta finns [i Skapa en log analytics-arbetsyta i Azure-portalen](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Som standard sparas data på en Log Analytics-arbetsyta i 30 dagar. Om du vill visa data för en längre tidshorisont ändrar du lagringsperioden för log analytics-arbetsytan. Information om hur du ändrar kvarhållningsperioden finns i [Hantera användning och kostnader med Azure Monitor-loggar](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

#### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Konfigurera diagnostikinställningar för dina valv

Azure Resource Manager-resurser, till exempel Recovery Services-valv, registrera information om schemalagda åtgärder och användarutlösta åtgärder som diagnostikdata. 

I övervakningsavsnittet i återställningstjänstvalvet väljer du **Diagnostikinställningar** och anger målet för Recovery Services-valvets diagnostikdata. Mer information om hur du använder diagnostikhändelser finns i [Använda diagnostikinställningar för Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Fönstret Diagnostikinställningar](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup innehåller också en inbyggd Azure-princip som automatiserar konfigurationen av diagnostikinställningar för alla valv i ett visst scope. Mer information om hur du använder den här principen finns i [Konfigurera inställningar för arkivdiagnostik i stor skala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics).

> [!NOTE]
> När du har konfigurerat diagnostik kan det ta upp till 24 timmar innan den första datare pushen har slutförts. När data börjar flöda in på log analytics-arbetsytan kanske du inte ser data i rapporterna omedelbart eftersom data för den aktuella deldagen inte visas i rapporterna. Mer information finns [i Konventioner som används i säkerhetskopieringsrapporter](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). Vi rekommenderar att du börjar visa rapporterna två dagar efter att du har konfigurerat valven för att skicka data till Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Visa rapporter i Azure-portalen

När du har konfigurerat valven för att skicka data till Log Analytics kan du visa rapporterna för säkerhetskopiering genom att gå till valfritt valvs fönster och välja **Säkerhetsrapporter**.

![Instrumentpanel för arkiv](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Välj den här länken om du vill öppna rapportarbetsboken För säkerhetskopiering.

> [!NOTE]
> * För närvarande kan den första belastningen av rapporten ta upp till 1 minut.
> * Valvet för återställningstjänster är bara en startpunkt för säkerhetskopieringsrapporter. När arbetsboken För säkerhetskopieringsrapporten har öppnats från ett valvs fönster väljer du lämplig uppsättning Log Analytics-arbetsytor för att se data som aggregerats över alla dina valv.

Rapporten innehåller olika flikar:

* **Sammanfattning:** Använd den här fliken för att få en översikt på hög nivå över din säkerhetskopia. Du kan få en snabb överblick över det totala antalet säkerhetskopieringsobjekt, totalt molnlagring som förbrukats, antalet skyddade instanser och jobbets framgångsfrekvens per arbetsbelastningstyp. Mer detaljerad information om en viss artefakttyp för säkerhetskopiering finns på respektive flikar.

   ![Fliken Sammanfattning](./media/backup-azure-configure-backup-reports/summary.png)

* **Säkerhetskopior:** Använd den här fliken om du vill visa information och trender för molnlagring som förbrukas på en artikelnivå för säkerhetskopiering. Om du till exempel använder SQL i en Azure VM-säkerhetskopia kan du se molnlagringen som förbrukas för varje SQL-databas som säkerhetskopieras. Du kan också välja att visa data för säkerhetskopieringsobjekt med en viss skyddsstatus. Om du till exempel väljer panelen **Skydd stoppad** högst upp på fliken filtreras alla widgetar under för att visa data endast för säkerhetskopieringsobjekt i tillståndet Skydd stoppad.

   ![Fliken Säkerhetskopieringsobjekt](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Användning**: Använd den här fliken om du vill visa viktiga faktureringsparametrar för dina säkerhetskopior. Informationen som visas på den här fliken är på faktureringsentitetsnivå (skyddad behållare). När det till exempel gäller en DPM-server som säkerhetskopieras till Azure kan du visa trenden för skyddade instanser och molnlagring som förbrukas för DPM-servern. Om du använder SQL i Azure Backup eller SAP HANA i Azure Backup, ger den här fliken användningsrelaterad information på nivån för den virtuella datorn där dessa databaser finns.

   ![Fliken Användning](./media/backup-azure-configure-backup-reports/usage.png)

* **Jobb**: Använd den här fliken om du vill visa tidskrävande trender för jobb, till exempel antalet misslyckade jobb per dag och de vanligaste orsakerna till jobbfel. Du kan visa den här informationen både på aggregerad nivå och på en nivå med säkerhetskopieringsobjekt. Välj ett visst säkerhetskopieringsobjekt i ett rutnät om du vill visa detaljerad information om varje jobb som utlöstes på det säkerhetskopieringsobjektet i det valda tidsintervallet.

   ![Fliken Jobb](./media/backup-azure-configure-backup-reports/jobs.png)

* **Principer**: Använd den här fliken om du vill visa information om alla dina aktiva principer, till exempel antalet associerade objekt och den totala molnlagring som förbrukas av artiklar som säkerhetskopierats under en viss princip. Välj en viss princip om du vill visa information om var och en av dess associerade säkerhetskopieringsobjekt.

   ![Fliken Principer](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exportera till Excel

Markera nedpilknappen längst upp till höger i en widget, till exempel en tabell eller ett diagram, om du vill exportera innehållet i den widgeten som ett Excel-blad som det är med befintliga filter. Om du vill exportera fler rader i en tabell till Excel kan du öka antalet rader som visas på sidan med hjälp av listpilen **Rader per sida** högst upp i varje rutnät.

## <a name="pin-to-dashboard"></a>Fäst vid instrumentpanelen

Välj pin-knappen högst upp i varje widget för att fästa widgeten på instrumentpanelen i Azure-portalen. Den här funktionen hjälper dig att skapa anpassade instrumentpaneler som är skräddarsydda för att visa den viktigaste informationen du behöver.

## <a name="cross-tenant-reports"></a>Rapporter över flera innehavare

Om du använder [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) med delegerad åtkomst till prenumerationer i flera klientmiljöer kan du använda standardprenumerationsfiltret. Välj filterknappen i det övre högra hörnet av Azure-portalen för att välja alla prenumerationer som du vill visa data för. På så sätt kan du välja Log Analytics-arbetsytor över dina klienter för att visa rapporter med flera egenskaper.

## <a name="conventions-used-in-backup-reports"></a>Konventioner som används i säkerhetskopieringsrapporter

* Filter fungerar från vänster till höger och uppifrån och ned på varje flik. Det vill än, alla filter gäller endast för alla de widgets som är placerade antingen till höger om filtret eller under filtret. 
* Om du väljer en färgad panel filtreras widgetarna under panelen efter poster som hör till värdet på den panelen. Om du till exempel väljer panelen **Skydd stoppad** på fliken **Säkerhetskopieringsobjekt** filtreras rutnäten och diagrammen nedan för att visa data för säkerhetskopierade objekt i tillståndet Skydd stoppad.
* Paneler som inte är färgade kan inte klickas.
* Data för den aktuella deldagen visas inte i rapporterna. Så när det valda värdet för **Tidsintervall** är **Senaste 7 dagarna**visar rapporten poster för de senaste sju slutförda dagarna. Den aktuella dagen ingår inte.
* Rapporten visar information om jobb (förutom loggjobb) som *utlöstes* i det valda tidsintervallet. 
* De värden som visas för **molnlagring** och **skyddade instanser** är i *slutet* av det valda tidsintervallet.
* De säkerhetskopieringsobjekt som visas i rapporterna är de objekt som finns i *slutet* av det valda tidsintervallet. Säkerhetskopieringsobjekt som togs bort mitt i det valda tidsintervallet visas inte. Samma konvention gäller även för principer för säkerhetskopiering.

## <a name="query-load-times"></a>Inläsningstider för frågor

Widgetarna i rapporten Säkerhetskopiering drivs av Kusto-frågor som körs på användarens Log Analytics-arbetsytor. Dessa frågor innebär vanligtvis bearbetning av stora mängder data, med flera kopplingar för att aktivera rikare insikter. Därför kan det hända att widgetarna inte läses in omedelbart när användaren visar rapporter över en stor säkerhetskopia. Den här tabellen ger en grov uppskattning av den tid som olika widgets kan ta att läsa in, baserat på antalet säkerhetskopierade objekt och det tidsintervall som rapporten visas för.

| **# Datakällor**                         | **Tidshorisont** | **Ungefärliga laddningstider**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 månad          | Plattor: 5-10 sek <br> Galler: 5-10 sek <br> Diagram: 5-10 sek <br> Filter på rapportnivå: 5-10 sekunder|
| ~5 K                       | 3 månader          | Plattor: 5-10 sek <br> Galler: 5-10 sek <br> Diagram: 5-10 sek <br> Filter på rapportnivå: 5-10 sekunder|
| ~10 K                       | 3 månader          | Plattor: 15-20 sek <br> Galler: 15-20 sek <br> Diagram: 1-2 minuter <br> Filter på rapportnivå: 25-30 sekunder|
| ~15 K                       | 1 månad          | Plattor: 15-20 sek <br> Galler: 15-20 sek <br> Diagram: 50-60 sek <br> Filter på rapportnivå: 20-25 sekunder|
| ~15 K                       | 3 månader          | Plattor: 20-30 sek <br> Galler: 20-30 sek <br> Diagram: 2-3 minuter <br> Filter på rapportnivå: 50-60 sekunder |

## <a name="what-happened-to-the-power-bi-reports"></a> Vad har hänt med Power BI-rapporterna? 
* Den tidigare Power BI-mallappen för rapportering, som köpte data från ett Azure-lagringskonto, finns på en infasningssökväg. Vi rekommenderar att du börjar skicka diagnostikdata för arkiv till Log Analytics för att visa rapporter.

* Dessutom finns V1-schemat för att skicka diagnostikdata till ett lagringskonto eller en Log Analytics-arbetsyta också på en infasningssökväg. Om du har skrivit anpassade frågor eller automatiseringar baserat på V1-schemat rekommenderas du att uppdatera dessa frågor för att använda det V2-schema som för närvarande stöds.

## <a name="next-steps"></a>Nästa steg
[Läs mer om övervakning och rapportering med Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)

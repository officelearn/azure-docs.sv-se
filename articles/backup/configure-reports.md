---
title: Konfigurera Azure Backup-rapporter
description: Konfigurera och Visa rapporter för Azure Backup med Log Analytics och Azure-arbetsböcker
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 5d1c7d628a61e550aa9dc4a5265ae16c5ed5336a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513633"
---
# <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter

Ett vanligt krav för säkerhets kopierings administratörer är att få insikter om säkerhets kopieringar baserat på data som omfattar en lång tids period. Användnings fall för sådan lösning är:

- Allokering och Prognosticering av moln lagring förbrukat.
- Granskning av säkerhets kopiering och återställning.
- Identifiera viktiga trender på olika nivåer av granularitet.

Idag tillhandahåller Azure Backup en rapporterings lösning som använder [Azure Monitor loggar](../azure-monitor/log-query/get-started-portal.md) och [Azure-arbetsböcker](../azure-monitor/platform/workbooks-overview.md). De här resurserna hjälper dig att få omfattande insikter om dina säkerhets kopieringar i hela reserv fastigheten. Den här artikeln förklarar hur du konfigurerar och visar Azure Backup rapporter.

## <a name="supported-scenarios"></a>Scenarier som stöds

- Säkerhets kopierings rapporter stöds för virtuella Azure-datorer, SQL på virtuella Azure-datorer, SAP HANA i virtuella Azure-datorer, Microsoft Azure Recovery Services MARS-agent (MARS), Microsoft Azure Backup Server (MABS) och System Center-Data Protection Manager (DPM). För säkerhets kopiering av Azure-filresurs visas data för alla poster som skapats den 1 juni 2020.
- För DPM-arbetsbelastningar stöds säkerhets kopierings rapporter för DPM version 5.1.363.0 och senare och agent version 2.0.9127.0 och senare.
- För MABS-arbetsbelastningar stöds backup-rapporter för MABS version 13.0.415.0 och senare samt agent version 2.0.9170.0 och senare.
- Säkerhets kopierings rapporter kan visas i alla säkerhets kopierings objekt, valv, prenumerationer och regioner så länge som deras data skickas till en Log Analytics-arbetsyta som användaren har åtkomst till. Om du vill visa rapporter för en uppsättning valv behöver du bara ha Läs behörighet till den Log Analytics arbets ytan som valven skickar data till. Du behöver inte ha åtkomst till enskilda valv.
- Om du är en [Azure Lighthouse](../lighthouse/index.yml) -användare med delegerad åtkomst till dina kunders prenumerationer kan du använda dessa rapporter med Azure Lighthouse för att visa rapporter över alla dina klienter.
- För närvarande kan data visas i säkerhets kopierings rapporter över högst 100 Log Analytics arbets ytor (mellan klienter).
- Data för säkerhets kopierings jobb för loggar visas inte i rapporterna.

## <a name="get-started"></a>Kom igång

Följ de här stegen för att börja använda rapporterna.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. skapa en Log Analytics arbets yta eller Använd en befintlig

Konfigurera en eller flera Log Analytics arbets ytor för att lagra dina säkerhets kopierings rapporterings data. Platsen och prenumerationen där Log Analytics arbets ytan kan skapas är oberoende av platsen och prenumerationen där valven finns.

Om du vill konfigurera en Log Analytics arbets yta, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Som standard behålls data i en Log Analytics-arbetsyta i 30 dagar. Om du vill visa data under en längre tids Horisont ändrar du kvarhållningsperioden för Log Analytics arbets ytan. Information om hur du ändrar kvarhållningsperioden finns i [Hantera användning och kostnader med Azure Monitor loggar](../azure-monitor/platform/manage-cost-storage.md).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Konfigurera diagnostikinställningar för dina valv

Azure Resource Manager resurser, till exempel Recovery Services-valv, registrera information om schemalagda åtgärder och användar utlösta åtgärder som diagnostikdata.

I avsnittet övervakning i Recovery Services-valvet väljer du **diagnostikinställningar** och anger målet för Recovery Services valvets diagnostikdata. Mer information om hur du använder diagnostiska händelser finns i [använda diagnostikinställningar för Recovery Services valv](./backup-azure-diagnostic-events.md).

![Fönstret Diagnostikinställningar](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup innehåller också en inbyggd Azure Policy definition som automatiserar konfigurationen av diagnostikinställningar för alla valv i ett angivet omfång. Information om hur du använder den här principen finns i [Konfigurera inställningar för valv diagnostik i skala](./azure-policy-configure-diagnostics.md).

> [!NOTE]
> När du har konfigurerat diagnostik kan det ta upp till 24 timmar innan den första data-push-installationen har slutförts. När data börjar flöda in i Log Analytics-arbetsytan kan du inte se data i rapporterna direkt eftersom data för den aktuella del dagen inte visas i rapporterna. Mer information finns i [konventioner som används i säkerhets kopierings rapporter](#conventions-used-in-backup-reports). Vi rekommenderar att du börjar visa rapporterna två dagar efter att du har konfigurerat dina valv för att skicka data till Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Visa rapporter i Azure Portal

När du har konfigurerat dina valv för att skicka data till Log Analytics visar du dina säkerhets kopierings rapporter genom att gå till ett valv fönster och välja **säkerhets kopierings rapporter**.

![Valv instrument panel](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Välj den här länken om du vill öppna arbets boken för säkerhets kopierings rapporten.

> [!NOTE]
>
> - Den första inläsningen av rapporten kan för närvarande ta upp till 1 minut.
> - Recovery Services valvet är bara en start punkt för säkerhets kopierings rapporter. När arbets boken för säkerhets kopierings rapporten öppnas från ett valvs fönster väljer du lämplig uppsättning Log Analytics arbets ytor för att se data som sammanställs för alla dina valv.

Rapporten innehåller olika flikar:

- **Sammanfattning**: Använd den här fliken för att få en översikt över reserv egendomen. Du kan få en snabb överblick över det totala antalet säkerhets kopierings objekt, total moln lagring som förbrukas, antalet skyddade instanser och jobbets slut för ande frekvens per arbets belastnings typ. Mer detaljerad information om en speciell typ av säkerhets kopierings artefakt finns på respektive flik.

   ![Fliken Sammanfattning](./media/backup-azure-configure-backup-reports/summary.png)

- **Säkerhets kopierings objekt**: Använd den här fliken om du vill se information och trender i moln lagring som förbrukas på en säkerhets kopierings nivå. Om du till exempel använder SQL i en Azure VM-säkerhetskopiering kan du se den förbrukade moln lagringen för varje SQL-databas som säkerhets kopie ras. Du kan också välja att se data för säkerhets kopierings objekt av en viss skydds status. Om du till exempel väljer rutan **skydds stoppad** överst på fliken filtrerar alla widgetar under för att endast visa data för säkerhets kopierings objekt i läget skydd har stoppats.

   ![Fliken säkerhets kopierings objekt](./media/backup-azure-configure-backup-reports/backup-items.png)

- **Användning**: Använd den här fliken för att Visa nyckel fakturerings parametrar för dina säkerhets kopior. Informationen som visas på den här fliken finns på en fakturerings enhets nivå (skyddad container). Om en DPM-server säkerhets kopie ras till Azure kan du till exempel se trenden för skyddade instanser och moln lagring som förbrukas för DPM-servern. Om du använder SQL i Azure Backup eller SAP HANA i Azure Backup, visar den här fliken användnings relaterad information på nivån för den virtuella dator där dessa databaser finns.

   ![Fliken användning](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> För DPM-arbetsbelastningar kan användarna se en liten skillnad (i ordningen 20 MB per DPM-server) mellan användnings värden som visas i rapporterna jämfört med värdet för den sammanlagda användningen som visas på fliken Översikt för Recovery Services-valvet. Denna skillnad redovisas av faktum att varje DPM-server som registreras för säkerhets kopiering har en associerad data källa för metadata som inte är kopplad till en artefakt för rapportering.

- **Jobb**: Använd den här fliken för att Visa tids krävande trender för jobb, till exempel antalet misslyckade jobb per dag och de vanligaste orsakerna till jobbfel. Du kan visa den här informationen på både en aggregerad nivå och på en säkerhets kopierings objekt nivå. Välj ett visst säkerhets kopierings objekt i ett rutnät om du vill visa detaljerad information om varje jobb som har utlösts på det säkerhetskopierade objektet i det valda tidsintervallet.

   ![Fliken jobb](./media/backup-azure-configure-backup-reports/jobs.png)

- **Principer**: Använd den här fliken om du vill visa information om alla dina aktiva principer, till exempel antalet associerade objekt och den totala moln lagring som förbrukas av objekt som har säkerhetskopierats under en specifik princip. Välj en viss princip om du vill visa information om var och en av de associerade säkerhets kopierings objekten.

   ![Fliken principer](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exportera till Excel

Välj nedåtpilen längst upp till höger om en widget, t. ex. en tabell eller ett diagram, för att exportera innehållet i widgeten som ett Excel-blad som är med befintliga filter applicerade. Om du vill exportera fler rader i en tabell till Excel kan du öka antalet rader som visas på sidan med hjälp av den nedrullningsbara pilen **rader per sida** överst i varje rutnät.

## <a name="pin-to-dashboard"></a>Fäst vid instrumentpanelen

Välj knappen Fäst längst upp i varje widget för att fästa widgeten på Azure Portal instrument panelen. Med den här funktionen kan du skapa anpassade instrument paneler som är skräddarsydda för att visa den viktigaste information som du behöver.

## <a name="cross-tenant-reports"></a>Rapporter över flera klienter

Om du använder [Azure-Lighthouse](../lighthouse/index.yml) med delegerad åtkomst till prenumerationer i flera klient miljöer kan du använda standard prenumerations filtret. Välj filter knappen i det övre högra hörnet i Azure Portal för att välja alla prenumerationer som du vill se data för. På så sätt kan du välja Log Analytics arbets ytor över dina klienter för att visa rapporter från flera klienter.

## <a name="conventions-used-in-backup-reports"></a>Konventioner som används i säkerhets kopierings rapporter

- Filtren arbetar från vänster till höger och uppifrån och ned på varje flik. Det innebär att alla filter endast gäller för alla widgetar som är placerade antingen till höger om filtret eller under filtret.
- Om du väljer en färgad panel filtreras widgetarna under panelen för poster som hör till värdet i den panelen. Om du till exempel väljer ikonen **stoppad skydd** på fliken **säkerhets kopierings objekt** filtreras rutnätet och diagrammen nedan för att visa data för säkerhets kopierings objekt i tillstånds stopp.
- Paneler som inte är färgade är inte klickade.
- Data för den aktuella del dagen visas inte i rapporterna. När det valda värdet för **tidsintervallet** är de **senaste 7 dagarna**, visar rapporten poster för de senaste sju slutförda dagarna. Aktuell dag ingår inte.
- Rapporten visar information om jobb (förutom logg jobb) som har *utlösts* i det valda tidsintervallet.
- Värdena som visas för **moln lagring** och **skyddade instanser** är i *slutet* av det valda tidsintervallet.
- De säkerhets kopierings objekt som visas i rapporterna är de objekt som finns i *slutet* av det valda tidsintervallet. Säkerhets kopierings objekt som har tagits bort i mitten av det valda tidsintervallet visas inte. Samma konvention gäller även för säkerhets kopierings principer.

## <a name="query-load-times"></a>Fråga om inläsnings tider

Widgetarna i säkerhets kopierings rapporten drivs av Kusto-frågor som körs på användarens Log Analytics-arbetsytor. Dessa frågor omfattar vanligt vis bearbetning av stora mängder data med flera kopplingar för att ge bättre insikter. Därför kanske widgeten inte kommer att läsas in omedelbart när användaren tittar på rapporter över en stor reserv fastighet. Den här tabellen innehåller en grov uppskattning av tiden som olika widgetar kan ta för att läsa in, baserat på antalet säkerhets kopierings objekt och tidsintervallet som rapporten visas för.

| **Antal data källor**                         | **Tids Horisont** | **Ungefärlig inläsnings tider**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 månad          | Paneler: 5-10 sekunder <br> Rutnät: 5-10 sekunder <br> Diagram: 5-10 sekunder <br> Filter på rapport nivå: 5-10 sekunder|
| ~ 5 K                       | 3 månader          | Paneler: 5-10 sekunder <br> Rutnät: 5-10 sekunder <br> Diagram: 5-10 sekunder <br> Filter på rapport nivå: 5-10 sekunder|
| ~ 10 K                       | 3 månader          | Paneler: 15-20 sekunder <br> Rutnät: 15-20 sekunder <br> Diagram: 1-2 minuter <br> Filter på rapport nivå: 25-30 sekunder|
| ~ 15 K                       | 1 månad          | Paneler: 15-20 sekunder <br> Rutnät: 15-20 sekunder <br> Diagram: 50-60 sekunder <br> Filter på rapport nivå: 20-25 sekunder|
| ~ 15 K                       | 3 månader          | Paneler: 20-30 sekunder <br> Rutnät: 20-30 sekunder <br> Diagram: 2-3 minuter <br> Filter på rapport nivå: 50-60 sekunder |

## <a name="what-happened-to-the-power-bi-reports"></a> Vad har hänt med Power BI-rapporterna? 

- Den tidigare Power BI Template-appen för rapportering, som har käll data från ett Azure Storage-konto, finns på en föråldrad sökväg. Vi rekommenderar att du börjar skicka valv diagnostikdata till Log Analytics för att visa rapporter.

- Dessutom finns v1- [schemat](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) för att skicka diagnostikdata till ett lagrings konto eller en La-arbetsytan på en föråldrad sökväg. Det innebär att om du har skrivit anpassade frågor eller automationer baserat på schemat v1, uppmanas du att uppdatera dessa frågor för att använda det v2-schema som stöds för närvarande.

## <a name="next-steps"></a>Nästa steg

[Läs mer om övervakning och rapportering med Azure Backup](./backup-azure-monitor-alert-faq.md)

---
title: Konfigurera Azure Backup-rapporter
description: Konfigurera och Visa rapporter för Azure Backup med Log Analytics och Azure-arbetsböcker
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 5c43efc8d61d7aa6c8fc94c6c39294d744cd6b87
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501126"
---
# <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter

Ett vanligt krav för säkerhets kopierings administratörer är att få insikter om säkerhets kopieringar, baserat på data som sträcker sig över en lång tids period. Det kan finnas flera användnings fall för en sådan lösning – tilldelning och prognoser av moln lagring som förbrukas, granskning av säkerhets kopiering och återställning samt för att identifiera viktiga trender på olika nivåer av granularitet.

Idag tillhandahåller Azure Backup en rapporterings lösning som använder [Azure Monitor loggar](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) och [Azure-arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks), vilket hjälper dig att få omfattande insikter om dina säkerhets kopieringar i hela reserv fastigheten. Den här artikeln förklarar hur du konfigurerar och visar säkerhets kopierings rapporter.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Säkerhets kopierings rapporter stöds för virtuella Azure-datorer, SQL på virtuella Azure-datorer, SAP HANA/ASE i virtuella Azure-datorer, Azure Backup Agent (MARS), Azure Backup Server (MABS) och System Center DPM.
* För DPM-arbetsbelastningar stöds säkerhets kopierings rapporter för DPM version 5.1.363.0 och senare, och agent version 2.0.9127.0 och senare.
* För MABS-arbetsbelastningar stöds backup-rapporter för MABS version 13.0.415.0 och senare, samt agent version 2.0.9170.0 och senare.
* Säkerhets kopierings rapporter kan visas i alla säkerhets kopierings objekt, valv, prenumerationer och regioner så länge som deras data skickas till en Log Analytics (LA) som användaren har åtkomst till. 
* Om du är en [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) -användare med delegerad åtkomst till dina kunders prenumerationer kan du använda dessa rapporter med Azure Lighthouse för att visa rapporter över alla dina klienter.
* Data för logg säkerhets kopierings jobb visas för närvarande inte i rapporterna.

## <a name="getting-started"></a>Komma igång

Om du vill komma igång med att använda rapporterna följer du de tre stegen som beskrivs nedan:

1. **Skapa en arbets yta för Log Analytics (LA) (eller Använd en befintlig):**

Du måste konfigurera en eller flera LA arbets ytor för att lagra dina säkerhets kopierings rapporterings data. Platsen och prenumerationen där denna LA-arbets yta kan skapas är oberoende av platsen och prenumerationen där valven finns. 

Läs följande artikel: [skapa en Log Analytics arbets yta i Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) för att konfigurera en La-arbetsyta.

Som standard behålls data i en LA-arbetsyta i 30 dagar. Om du vill se data under en längre tids Horisont ändrar du kvarhållningsperioden för LA-arbetsytan. Information om hur du ändrar kvarhållningsperioden finns i följande artikel: [Hantera användning och kostnader med Azure Monitor loggar](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Konfigurera diagnostikinställningar för dina valv:**

Azure Resource Manager resurser, till exempel Recovery Services-valv, registrera information om schemalagda åtgärder och användar utlösta åtgärder som diagnostikdata. 

I avsnittet övervakning i Recovery Services-valvet väljer du **diagnostikinställningar** och anger målet för Recovery Services valvets diagnostikdata. [Lär dig mer om att använda diagnostiska händelser](https://aka.ms/AzureBackupDiagnosticDocs).

![Bladet inställningar för diagnostik](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup innehåller också en inbyggd Azure Policy som automatiserar konfigurationen av diagnostikinställningar för alla valv i ett angivet omfång. Läs följande artikel för att lära dig hur du använder den här principen: [Konfigurera valvets diagnostikinställningar i skala](https://aka.ms/AzureBackupDiagnosticsPolicyDocs)

3. **Visa rapporter på Azure Portal:**

När du har konfigurerat dina valv för att skicka data till LA visar du dina säkerhets kopierings rapporter genom att gå till ett valvs blad och klicka på meny alternativet **säkerhetskopiera rapporter** . 

![Valv instrument panel](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

När du klickar på den här länken öppnas arbets boken säkerhets kopierings rapport. 

> [!NOTE]
> Den första inläsningen av rapporten kan för närvarande ta upp till 1 minut.

Nedan visas en beskrivning av de olika flikar som rapporten innehåller:

* **Sammanfattning** – fliken Sammanfattning ger en översikt över reserv egendomen. På fliken Sammanfattning kan du få en snabb överblick över det totala antalet säkerhets kopierings objekt, total moln lagring som förbrukas, antalet skyddade instanser och jobbets slut för ande frekvens per arbets belastnings typ. Om du vill ha mer detaljerad information kring en speciell typ av säkerhets kopierings artefakt navigerar du till respektive flik.

![Fliken Sammanfattning](./media/backup-azure-configure-backup-reports/summary.png)

* **Säkerhets kopierings objekt** – på fliken säkerhets kopierings objekt kan du se information och trender i moln lagring som förbrukas på en säkerhets kopierings objekt nivå. Om du till exempel använder SQL i Azure VM Backup kan du se den förbrukade moln lagringen för varje SQL-databas som säkerhets kopie ras. Du kan också välja att se data för säkerhets kopierings objekt av en viss skydds status. Om du till exempel klickar på rutan **skydds stoppad** överst på fliken filtreras alla nedanstående widgetar för att endast visa data för säkerhets kopierings objekt som har stoppats.

![Fliken säkerhets kopierings objekt](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Användning** – fliken användning hjälper dig att Visa viktiga fakturerings parametrar för dina säkerhets kopior. Informationen som visas på den här fliken finns på en fakturerings enhets nivå (skyddad container). Om en DPM-server säkerhets kopie ras till Azure kan du till exempel se trenden för skyddade instanser och moln lagring som förbrukas för DPM-servern. Om du använder SQL i Azure Backup eller SAP HANA i Azure Backup, ger den här fliken användnings relaterad information på nivån för den virtuella dator som dessa databaser finns i.

![Fliken användning](./media/backup-azure-configure-backup-reports/usage.png)

* **Jobb** – med fliken jobb kan du se tids krävande trender för jobb, till exempel antalet misslyckade jobb per dag och de vanligaste orsakerna till jobbfel. Du kan visa den här informationen på både en aggregerad nivå och på en säkerhets kopierings objekt nivå. Om du klickar på ett visst säkerhets kopierings objekt i ett rutnät kan du Visa detaljerad information om varje jobb som har utlösts på det säkerhetskopierade objektet i det valda tidsintervallet.

![Fliken jobb](./media/backup-azure-configure-backup-reports/jobs.png)

* **Principer** – med fliken principer kan du Visa information om alla dina aktiva principer, till exempel antalet associerade objekt och den totala moln lagring som används av objekt som har säkerhetskopierats under en specifik princip. Genom att klicka på en viss princip kan du Visa information om var och en av de associerade säkerhets kopierings objekten.

![Fliken principer](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exportera till Excel

Om du klickar på nedåtpilen längst upp till höger i en widget (tabell/diagram) exporteras innehållet i widgeten som ett Excel-blad, precis som när befintliga filter tillämpas. Om du vill exportera fler rader i en tabell till Excel kan du öka antalet rader som visas på sidan genom att använda List rutan **rader per sida** överst i varje rutnät.

## <a name="pinning-to-dashboard"></a>Fäst på instrument panelen

Klicka på PIN-ikonen längst upp i varje widget för att fästa widgeten på Azure Portal instrument panelen. På så sätt kan du skapa anpassade instrument paneler som är skräddarsydda för att visa den viktigaste information som du behöver.

## <a name="cross-tenant-reports"></a>Rapporter över flera klienter

Om du är en [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) -användare med delegerad åtkomst till prenumerationer i flera klient miljöer kan du använda standard prenumerations filtret (genom att klicka på filter ikonen längst upp till höger i Azure Portal) för att välja alla prenumerationer som du vill se data för. På så sätt kan du välja LA arbets ytor över dina klienter för att visa rapporter med flera klienter.

## <a name="conventions-used-in-backup-reports"></a>Konventioner som används i säkerhets kopierings rapporter

* Filter fungerar från vänster till höger och uppifrån och ned på varje flik, det vill säga alla filter gäller endast för alla widgetar som är placerade till höger om filtret eller under filtret. 
* Om du klickar på en färgad panel filtreras widgetarna under panelen för poster som hör till värdet för den panelen. Om du till exempel klickar på ikonen *stoppad skydd* på fliken säkerhets kopierings objekt filtreras rutnätet och diagrammen nedan för att visa data för säkerhets kopierings objekt i status "skyddet har stoppats".
* Paneler som inte är färgade är inte klickade.
* Data för en del av den aktuella dagen visas inte i rapporterna. Om det valda värdet för **tidsintervallet** är, de ***senaste 7 dagarna***, visar rapporten poster för de senaste 7 slutförda dagarna (som inte omfattar den aktuella dagen).
* Rapporten visar information om jobb (förutom logg jobb) som har **utlösts** i det valda tidsintervallet. 
* Värdena som visas för moln lagring och skyddade instanser är i **slutet** av det valda tidsintervallet.
* De säkerhets kopierings objekt som visas i rapporterna är de objekt som finns i **slutet** av det valda tidsintervallet. Säkerhets kopierings objekt som har tagits bort i mitten av det valda tidsintervallet visas inte. Samma konvention gäller även för säkerhets kopierings principer.

## <a name="query-load-times"></a>Fråga om inläsnings tider

Widgetarna i säkerhets kopierings rapporten drivs av Kusto-frågor som körs på användarens LA arbets ytor. Eftersom dessa frågor vanligt vis innebär bearbetning av stora mängder data, med flera kopplingar för att ge bättre insikter, kanske widgeten inte inaktive ras omedelbart när användaren visar rapporter över en stor reserv fastighets reserv. Tabellen nedan innehåller en grov uppskattning av tiden som olika widgetar kan ta för att läsa in, baserat på antalet säkerhets kopierings objekt och tidsintervallet som rapporten visas för:

| **Antal data källor**                         | **Tids Horisont** | **Inläsnings tider (ca.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 månad          | Paneler: 5-10 sekunder <br> Rutnät: 5-10 sekunder <br> Diagram: 5-10 sekunder <br> Filter på rapport nivå: 5-10 sekunder|
| ~ 5 K                       | 3 månader          | Paneler: 5-10 sekunder <br> Rutnät: 5-10 sekunder <br> Diagram: 5-10 sekunder <br> Filter på rapport nivå: 5-10 sekunder|
| ~ 10 K                       | 3 månader          | Paneler: 15-20 sekunder <br> Rutnät: 15-20 sekunder <br> Diagram: 1-2 minuter <br> Filter på rapport nivå: 25-30 sekunder|
| ~ 15 K                       | 1 månad          | Paneler: 15-20 sekunder <br> Rutnät: 15-20 sekunder <br> Diagram: 50-60 sekunder <br> Filter på rapport nivå: 20-25 sekunder|
| ~ 15 K                       | 3 månader          | Paneler: 20-30 sekunder <br> Rutnät: 20-30 sekunder <br> Diagram: 2-3 minuter <br> Filter på rapport nivå: 50-60 sekunder |

## <a name="what-happened-to-the-power-bi-reports"></a>Vad hände med Power BI rapporter?
* Vår tidigare Power BI Template-app för rapportering (som käll data från ett Azure Storage-konto) är på en utfasnings väg. Vi rekommenderar att du börjar skicka valv diagnostikdata till Log Analytics enligt beskrivningen ovan för att visa rapporter.

* Dessutom finns v1-schemat för att skicka diagnostikdata till ett lagrings konto eller en LA-arbetsytan på en föråldrad sökväg. Det innebär att om du har skrivit anpassade frågor eller automationer baserat på schemat v1, uppmanas du att uppdatera dessa frågor för att använda det v2-schema som stöds för närvarande.

## <a name="next-steps"></a>Nästa steg
[Läs mer om övervakning och rapportering med Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
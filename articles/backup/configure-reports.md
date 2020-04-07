---
title: Konfigurera Azure Backup-rapporter
description: Konfigurera och visa rapporter för Azure Backup med Log Analytics och Azure-arbetsböcker
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 497d70c1bcc577faa467720b959eb828e785a26a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672668"
---
# <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter

Ett vanligt krav för administratörer för säkerhetskopiering är att få insikter om säkerhetskopior, baserat på data som sträcker sig över en lång tidsperiod. Det kan finnas flera användningsfall för en sådan lösning - allokering och prognostisering av molnlagring som förbrukas, granskning av säkerhetskopior och återställningar och identifiering av viktiga trender på olika detaljnivåer.

Idag tillhandahåller Azure Backup en rapporteringslösning som utnyttjar [Azure Monitor Logs](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) och [Azure Workbooks,](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)vilket hjälper dig att få omfattande insikter om dina säkerhetskopior i hela din säkerhetskopieringsegendom. I den här artikeln beskrivs hur du konfigurerar och visar rapporter för säkerhetskopiering.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Säkerhetskopieringsrapporter stöds för virtuella Azure-datorer, SQL i virtuella Azure-datorer, SAP HANA/ASE i virtuella Azure-datorer, Azure Backup Agent (MARS), Azure Backup Server (MABS) och System Center DPM.
* För DPM-arbetsbelastningar stöds säkerhetskopieringsrapporter för DPM Version 5.1.363.0 och högre och Agent Version 2.0.9127.0 och senare.
* För MABS-arbetsbelastningar stöds säkerhetskopieringsrapporter för MABS Version 13.0.415.0 och senare och Agent Version 2.0.9170.0 och senare.
* Säkerhetskopieringsrapporter kan visas i alla säkerhetskopieringsobjekt, valv, prenumerationer och regioner så länge deras data skickas till en Log Analytics (LA) arbetsyta som användaren har åtkomst till. Observera att om du vill visa rapporter för en uppsättning valv behöver du bara ha **läsaråtkomst till DEN LA-arbetsyta** som valven skickar sina data till. Du **behöver inte** ha tillgång till de enskilda valven.
* Om du är en [Azure Lighthouse-användare](https://docs.microsoft.com/azure/lighthouse/) med delegerad åtkomst till dina kunders prenumerationer kan du använda dessa rapporter med Azure Lighthouse för att visa rapporter över alla dina klienter.
* Data för jobb för säkerhetskopiering av loggar visas för närvarande inte i rapporterna.

## <a name="getting-started"></a>Komma igång

Följ nedan följande tre steg för att komma igång med rapporterna:

1. **Skapa en LCK-arbetsyta (ELLER använd en befintlig):**

Du måste konfigurera en eller flera LA-arbetsytor för att lagra dina rapportdata för säkerhetskopiering. Platsen och prenumerationen där den här LA-arbetsytan kan skapas är oberoende av den plats och prenumeration där dina valv finns. 

Se följande artikel: [Skapa en log analytics-arbetsyta i Azure-portalen](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) för att konfigurera en LA-arbetsyta.

Som standard sparas data i en LA-arbetsyta i 30 dagar. Om du vill visa data för en längre tidshorisont ändrar du kvarhållningsperioden för LA Workspace. Information om hur du ändrar kvarhållningsperioden finns i följande artikel: [Hantera användning och kostnader med Azure Monitor Logs](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Konfigurera diagnostikinställningar för dina valv:**

Azure Resource Manager-resurser, till exempel Recovery Services-valv, registrera information om schemalagda åtgärder och användarutlösta åtgärder som diagnostikdata. 

I övervakningsavsnittet i valvet för Återställningstjänster väljer du **Diagnostikinställningar** och anger målet för Recovery Services-valvets diagnostikdata. [Läs mer om hur du använder diagnostiska händelser](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Diagnostikinställningar Blad](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup innehåller också en inbyggd Azure-princip som automatiserar konfigurationen av diagnostikinställningar för alla valv i ett visst scope. Se följande artikel om du vill veta hur du använder den här principen: [Konfigurera inställningar för arkivdiagnostik i stor skala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> När du har konfigurerat diagnostik kan det ta upp till 24 timmar innan den första datare pushen har slutförts. När data börjar flöda in i LA Workspace kanske du inte kan se data i rapporterna omedelbart, eftersom data för den aktuella deldagen inte visas i rapporterna (mer information [här).](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports) Därför rekommenderas att du börjar visa rapporterna 2 dagar efter att du har konfigurerat valven för att skicka data till Log Analytics.

3. **Visa rapporter på Azure-portalen:**

När du har konfigurerat valven för att skicka data till LA kan du visa dina säkerhetskopieringsrapporter genom att navigera till valfritt valvs blad och klicka på **menyalternativet För säkerhetskopieringsrapporter.** 

![Instrumentpanel för arkiv](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Om du klickar på den här länken öppnas arbetsboken för säkerhetskopieringsrapport.

> [!NOTE]
> * För närvarande kan den första belastningen av rapporten ta upp till 1 minut.
> * Valvet för återställningstjänster är bara en startpunkt för säkerhetskopieringsrapporter. När arbetsboken för säkerhetskopieringsrapporter öppnas från ett valvs blad kan du se data som aggregeras över alla dina valv (genom att välja lämplig uppsättning LA-arbetsytor).

Nedan följer en beskrivning av de olika flikar som rapporten innehåller:

* **Sammanfattning** - Fliken Sammanfattning ger en översikt på hög nivå över din säkerhetskopieringsegendom. Under fliken Sammanfattning kan du få en snabb överblick över det totala antalet säkerhetskopieringsobjekt, totalt molnlagring som förbrukats, antalet skyddade instanser och jobbets framgångsfrekvens per arbetsbelastningstyp. Mer detaljerad information om en viss artefakttyp för säkerhetskopiering finns i respektive flikar.

![Fliken Sammanfattning](./media/backup-azure-configure-backup-reports/summary.png)

* **Backup Objekt** - Fliken Säkerhetskopiering objekt kan du se information och trender om molnlagring förbrukas på en säkerhetskopiering objektnivå. Om du till exempel använder SQL i Azure VM-säkerhetskopiering kan du se molnlagringen som förbrukas för varje SQL-databas som säkerhetskopieras. Du kan också välja att visa data för säkerhetskopieringsobjekt med en viss skyddsstatus. Om du till exempel klickar på panelen **Skyddsstopp längst** upp på fliken filtrerar alla nedanstående widgetar för att visa data endast för säkerhetskopieringsobjekt i tillståndet Skydd stoppad.

![Fliken Säkerhetskopieringsobjekt](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Användning** - Fliken Användning hjälper dig att visa viktiga faktureringsparametrar för dina säkerhetskopior. Informationen som visas på den här fliken är på en faktureringsenhet (skyddad behållare). När det till exempel gäller en DPM-server som säkerhetskopieras till Azure kan du visa trenden för skyddade instanser och molnlagring som förbrukas för DPM-servern. Om du använder SQL i Azure Backup eller SAP HANA i Azure Backup, ger den här fliken användningsrelaterad information på nivån för den virtuella datorn som dessa databaser finns i.

![Fliken Användning](./media/backup-azure-configure-backup-reports/usage.png)

* **Jobb** - På fliken Jobb kan du visa tidskrävande trender för jobb, till exempel antalet misslyckade jobb per dag och de vanligaste orsakerna till jobbfel. Du kan visa den här informationen både på aggregerad nivå och på en artikelnivå för säkerhetskopiering. Genom att klicka på ett visst säkerhetskopieringsobjekt i ett rutnät kan du visa detaljerad information om varje jobb som utlöstes på det säkerhetskopieringsobjektet i det valda tidsintervallet.

![Fliken Jobb](./media/backup-azure-configure-backup-reports/jobs.png)

* **Principer** – På fliken Principer kan du visa information om alla dina aktiva principer, till exempel antalet associerade objekt, och den totala molnlagring som förbrukas av artiklar som säkerhetskopierats under en viss princip. Om du klickar på en viss princip kan du visa information om var och en av dess associerade säkerhetskopior.

![Fliken Principer](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exportera till Excel

Om du klickar på nedpilsknappen längst upp till höger i en widget (tabell/diagram) exporteras innehållet i den widgeten som ett Excel-blad, i befintligt syfte med befintliga filter. Om du vill exportera fler rader i en tabell till Excel kan du öka antalet rader som visas på sidan med hjälp av **listrutan Rader per sida** högst upp i varje rutnät.

## <a name="pinning-to-dashboard"></a>Fästa på instrumentpanelen

Klicka på pinikonen högst upp i varje widget om du vill fästa widgeten på instrumentpanelen för Azure-portalen. På så sätt kan du skapa anpassade instrumentpaneler som är skräddarsydda för att visa den viktigaste informationen du behöver.

## <a name="cross-tenant-reports"></a>Rapporter över flera innehavare

Om du är en [Azure Lighthouse-användare](https://docs.microsoft.com/azure/lighthouse/) med delegerad åtkomst till prenumerationer i flera klientmiljöer kan du använda standardprenumerationsfiltret (genom att klicka på filterikonen längst upp till höger i Azure-portalen) för att välja alla prenumerationer som du vill se data för. Om du gör det kan du välja LA-arbetsytor över dina klienter för att visa rapporter med flera innehavare.

## <a name="conventions-used-in-backup-reports"></a>Konventioner som används i säkerhetskopieringsrapporter

* Filter fungerar från vänster till höger och uppifrån och ned på varje flik, det vill än, vilket är fallet gäller alla filter som bara gäller för alla de widgets som är placerade antingen till höger om filtret eller under filtret. 
* Om du klickar på en färgad panel filtreras widgetarna under panelen för poster som hör till värdet på den panelen. Om du till exempel klickar på panelen *Skyddsstopp i* fliken Säkerhetskopieringsobjekt filtreras rutnäten och diagrammen nedan för att visa data för säkerhetskopierade objekt i tillståndet "Stoppat skydd".
* Paneler som inte är färgade kan inte klickas.
* Data för en del av den aktuella dagen visas inte i rapporterna. När det valda värdet för **Tidsintervall** är, ***Senaste 7 dagarna,*** visar rapporten poster för de senaste 7 slutförda dagarna (som inte inkluderar den aktuella dagen).
* Rapporten visar information om jobb (förutom loggjobb) som **utlöstes** i det valda tidsintervallet. 
* De värden som visas för molnlagring och skyddade instanser finns i **slutet** av det valda tidsintervallet.
* De säkerhetskopieringsobjekt som visas i rapporterna är de objekt som finns i **slutet** av det valda tidsintervallet. Säkerhetskopieringsobjekt som togs bort mitt i det valda tidsintervallet visas inte. Samma konvention gäller även för principer för säkerhetskopiering.

## <a name="query-load-times"></a>Inläsningstider för frågor

Widgetarna i säkerhetskopieringsrapporten drivs av Kusto-frågor som körs på användarens LA-arbetsytor. Eftersom dessa frågor vanligtvis innebär bearbetning av stora mängder data, med flera kopplingar för att aktivera rikare insikter, kan widgetarna inte läsas in omedelbart när användaren visar rapporter över en stor säkerhetskopia. Tabellen nedan ger en grov uppskattning av den tid som olika widgets kan ta att läsa in, baserat på antalet säkerhetskopieringsobjekt och det tidsintervall för vilket rapporten visas:

| **# Datasources # Datasources # Datasources # Data**                         | **Tidshorisont** | **Laddningstider (ca)Load Times (ca.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 månad          | Plattor: 5-10 sek <br> Galler: 5-10 sek <br> Diagram: 5-10 sek <br> Filter på rapportnivå: 5-10 sekunder|
| ~5 K                       | 3 månader          | Plattor: 5-10 sek <br> Galler: 5-10 sek <br> Diagram: 5-10 sek <br> Filter på rapportnivå: 5-10 sekunder|
| ~10 K                       | 3 månader          | Plattor: 15-20 sek <br> Galler: 15-20 sek <br> Diagram: 1-2 minuter <br> Filter på rapportnivå: 25-30 sekunder|
| ~15 K                       | 1 månad          | Plattor: 15-20 sek <br> Galler: 15-20 sek <br> Diagram: 50-60 sek <br> Filter på rapportnivå: 20-25 sekunder|
| ~15 K                       | 3 månader          | Plattor: 20-30 sek <br> Galler: 20-30 sek <br> Diagram: 2-3 minuter <br> Filter på rapportnivå: 50-60 sekunder |

## <a name="what-happened-to-the-power-bi-reports"></a>Vad har hänt med Power BI-rapporterna?
* Vår tidigare Power BI-mallapp för rapportering (som har köpt data från ett Azure Storage-konto) finns på en infasningssökväg. Vi rekommenderar att du börjar skicka diagnostikdata för arkiv till Log Analytics enligt beskrivningen ovan för att visa rapporter.

* Dessutom är V1-schemat för att skicka diagnostikdata till ett lagringskonto eller en LA Workspace också på en infasningssökväg. Det innebär att om du har skrivit några anpassade frågor eller automatiseringar baserat på V1-schemat, rekommenderas du att uppdatera dessa frågor för att använda det V2-schema som för närvarande stöds.

## <a name="next-steps"></a>Nästa steg
[Läs mer om övervakning och rapportering med Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
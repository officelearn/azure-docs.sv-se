---
title: Spåra ändringar med Azure Automation
description: Lösningen ändringsspårning kan du identifiera programvara och Windows Service-ändringar som sker i din miljö.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 08/27/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8066612db20d1569920835a67d84b27d1b852e6e
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128134"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Spåra ändringar i miljön med lösningen ändringsspårning

Den här artikeln hjälper dig att använda ändringsspårning-lösning lätt kan identifiera ändringar i miljön. Lösningen spårar ändringar i Windows och Linux, Windows och Linux-filer, registernycklar för Windows, Windows-tjänster och Linux-daemon. Identifiera konfigurationsändringar kan hjälpa dig att precisera Driftproblem.

Ändringar av installerad programvara, tjänster för Windows, Windows-registret och filer och Linux-Daemon på de övervakade servrarna skickas till Log Analytics-tjänsten i molnet för bearbetning. Logiken tillämpas på den mottagna data och Molntjänsten innehåller data. Du kan enkelt se ändringar som gjorts i din serverinfrastruktur med hjälp av informationen på instrumentpanelen för ändringsspårning.

## <a name="supported-windows-operating-systems"></a>Windows-operativsystem som stöds

Följande versioner av Windows-operativsystemet stöds officiellt för Windows-agenten:

* Windows Server 2008 Service Pack 1 (SP1) eller senare
* Windows 7 SP1 och senare.

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Följande Linux-distributioner stöds officiellt. Linux-agenten kan också köra på andra distributioner som inte visas. Om inget annat anges stöds alla mindre versioner för varje större version i listan.  

* Amazon Linux 2012.09 till 2015.09 (x86/x64)
* CentOS Linux 5, 6 och 7 (x86/x64)  
* Oracle Linux 5, 6 och 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 och 7 (x86/x64)
* Debian GNU/Linux 6, 7 och 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 och 12 (x86/x64)

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Om du vill börja spåra ändringar, måste du Aktivera ändringsspårning och inventering lösningen för ditt Automation-konto.

1. I Azure-portalen går du till ditt Automation-konto
1. Välj **ändringsspårning** under **CONFIGURATION**.
1. Välj en befintlig Log analytics-arbetsyta eller **Skapa ny arbetsyta** och klicka på **aktivera**.

Detta gör att lösningen för ditt automation-konto. Lösningen kan ta upp till 15 minuter att aktivera. Blå banderoll meddelar dig när lösningen har aktiverats. Gå tillbaka till den **ändringsspårning** att hantera lösningen.

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurera ändringsspårning och inventering

Läs hur att integrera datorer till lösningen finns: [Onboarding automatiseringslösningar](automation-onboard-solutions-from-automation-account.md). Du kan konfigurera de objekt som ska spåra när du har en dator onboarding med lösningen ändringsspårning och inventering. När du aktiverar en ny fil eller registernyckel att spåra kan aktiveras för ändringsspårning och inventering.

MD5-hash-värden för filerna som används för att spåra ändringar i filer på både Windows och Linux. Dessa hashvärden används sedan för att identifiera om en ändring har gjorts sedan den senaste inventeringen.

### <a name="configure-linux-files-to-track"></a>Konfigurera Linux-filer att spåra

Använd följande steg för att konfigurera filspårning på Linux-datorer:

1. I ditt Automation-konto väljer **ändringsspårning** under **KONFIGURATIONSHANTERING**. Klicka på **redigera inställningar för** (kugghjulet).
2. På den **ändringsspårning** väljer **Linux-filer**, klicka sedan på **+ Lägg till** att lägga till en ny fil och spåra.
3. På den **Lägg till Linux-fil för ändringsspårning**, anger du informationen för filen eller katalogen som ska spåras och klickar på **spara**.

|Egenskap   |Beskrivning  |
|---------|---------|
|Enabled     | Avgör om inställningen tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt.        |
|Ange sökväg     | Sökvägen till att söka efter filen. Till exempel: ”/etc/*.conf”       |
|Sökvägstyp     | Typ av objekt som ska spåras, möjliga värden är fil och katalog.        |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Använda Sudo     | Den här inställningen styr om sudo ska användas vid sökningen efter objektet.         |
|Länkar     | Den här inställningen styr hur symboliska länkar ska hanteras när de passerar kataloger.<br> **Ignorera** – ignorerar symboliska länkar och inkluderar inte refererade filer/kataloger.<br>**Följ** – följer de symboliska länkarna under rekursion och dessutom ingår refererade filer/kataloger.<br>**Hantera** – följer de symboliska länkarna och tillåter ändring av returnerat innehåll.     |
|Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: **True** eller **False**.|

> [!NOTE]
> Länkalternativet ”Hantera” rekommenderas inte. Hämtning av filinnehåll stöds inte.

### <a name="configure-windows-files-to-track"></a>Konfigurera Windows-filer för att spåra

Använd följande steg för att konfigurera filer spårning på Windows-datorer:

1. I ditt Automation-konto väljer **ändringsspårning** under **KONFIGURATIONSHANTERING**. Klicka på **redigera inställningar för** (kugghjulet).
2. På den **ändringsspårning** väljer **Windows filer**, klicka sedan på **+ Lägg till** att lägga till en ny fil och spåra.
3. På den **Lägg till Windows-fil för ändringsspårning**, anger du informationen för den fil som ska spåras och klickar på **spara**.

|Egenskap   |Beskrivning  |
|---------|---------|
|Enabled     | Avgör om inställningen tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt.        |
|Ange sökväg     | Sökvägen till att söka efter filen till exempel ”: c:\temp\\\*.txt”<br>Du kan också använda miljövariabler som ”%winDir%\System32\\\*. *”       |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: **True** eller **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Inställningar för jokertecken, rekursion och miljö

Rekursion kan du ange jokertecken för att förenkla spårning i kataloger och miljövariabler att spåra filer i miljöer med flera eller dynamisk enhet namn. Här följer en lista över vanliga information som du bör känna till när du konfigurerar rekursion:

* Jokertecken krävs för att spåra flera filer
* Om du använder jokertecken, kan de bara användas i det sista segmentet i en sökväg. (till exempel C:\folder\\**filen** eller /etc/*.conf)
* Om en miljövariabel har en ogiltig sökväg, verifiering lyckas, men den sökvägen misslyckas när maskinvaruinventering körs.
* Undvika Allmänt sökvägar som `c:\*.*` när du ställer in sökvägen eftersom detta skulle resultera i för många mappar är slut.

## <a name="configure-file-content-tracking"></a>Konfigurera filinnehåll spårning

Du kan visa innehållet före och efter en ändring av en fil med filen innehåll ändringsspårning. Detta är tillgänglig för Windows och Linux-filer för varje ändring i filen, innehållet i filen lagras i ett lagringskonto och visar filen före och efter ändringen, infogade eller sida vid sida. Mer information finns i [visa innehållet i en spårade fil](change-tracking-file-contents.md).

![Visa ändringar i en fil](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurera Windows registernycklar för att spåra

Använd följande steg för att konfigurera viktiga registerspårning på Windows-datorer:

1. I ditt Automation-konto väljer **ändringsspårning** under **KONFIGURATIONSHANTERING**. Klicka på **redigera inställningar för** (kugghjulet).
2. På den **ändringsspårning** väljer **Windows-registret**, klicka sedan på **+ Lägg till** att lägga till en ny registernyckel för att spåra.
3. På den **lägga till Windows-registret för ändringsspårning**, anger du information för nyckeln som ska spåras och klickar på **spara**.

|Egenskap   |Beskrivning  |
|---------|---------|
|Enabled     | Avgör om inställningen tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt.        |
|Windows-registernyckel   | Sökvägen till att söka efter filen. Till exempel: ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

## <a name="limitations"></a>Begränsningar

Lösningen ändringsspårning stöder för närvarande inte följande objekt:

* Rekursion för Windows-registret spårning
* Network file system

Andra begränsningar:

* Den **största filstorlek** kolumnen och värdena är oanvända i den aktuella implementeringen.
* Om du samlar in mer än 2 500 filer i 30-minuters insamlingscykel kan lösningens prestanda försämras.
* När nätverkstrafik är hög kan ändringsposter ta upp till sex timmar att visa.
* Om du ändrar konfigurationen när en dator är avstängd, kanske datorn publicerar ändringar som tillhör den tidigare konfigurationen.

## <a name="known-issues"></a>Kända problem

Lösningen ändringsspårning har för närvarande följande problem:

* Hotfix-uppdateringar har inte samlats in för Windows 10 Creators Update och Windows Server 2016 Core RS3 datorer.

## <a name="change-tracking-data-collection-details"></a>Ändra spårningsinformation data samling

I följande tabell visas insamlingsfrekvens data för typerna av ändringar. För varje uppdateras ögonblicksbild av det aktuella tillståndet var 24 timmar:

| **Ändra typ** | **Frekvens** |
| --- | --- |
| Windows-registret | 50 minuter |
| Windows-fil | 30 minuter |
| Linux-fil | 15 minuter |
| Windows-tjänster | 10 sekunder till 30 minuter</br> Standard: 30 minuter |
| Linux-Daemon | 5 minuter |
| Windows-programvara | 30 minuter |
| Linux-programvara | 5 minuter |

### <a name="windows-service-tracking"></a>Windows service-spårning

Insamlingsfrekvens standard för Windows-tjänster är 30 minuter. Konfigurera hur ofta genom att gå till **ändringsspårning**. Under **redigera inställningar för** på den **Windows Services** fliken finns det ett skjutreglage som gör det möjligt att ändra insamlingsfrekvens för Windows-tjänster från så snabbt som 10 sekunder till upp till 30 minuter. Flytta skjutreglaget till hur ofta du vill och det sparas automatiskt.

![Skjutreglaget för Windows-tjänster](./media/automation-change-tracking/windowservices.png)

Agenten endast spårar ändringar, detta optimerar prestanda för agenten. Genom att ange för hög tröskel kan ändringar missats om tjänsten har återställts till deras ursprungliga tillstånd. Ange frekvensen till ett mindre värde kan du fånga ändringar som annars kan missas.

> [!NOTE]
> Agenten kan spåra ändringar till ett 10 andra intervall, data fortfarande tar några minuter som ska visas i portalen. Ändringar under den tid som ska visas i portalen är fortfarande spåras och loggas.
  
### <a name="registry-key-change-tracking"></a>Viktiga registerändringsspårning

Syftet med att övervaka ändringar i registernycklarna är att identifiera punkter där kod från tredje part och skadlig kod kan aktivera. I följande lista visas listan över förkonfigurerade registernycklar. De här nycklarna konfigureras, men inte aktiverad. Du måste aktivera var och en för att spåra dessa registernycklar.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_lokala\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare vanliga autostart transaktioner som anslutas direkt till Windows Explorer och vanligtvis kör i processen med Explorer.exe.    |
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare-skript som körs vid start.     |
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare-skript som körs vid avstängningen.     |
> |**HKEY\_lokala\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar nycklar som har lästs in innan användaren loggar in på sina Windows-konto. Nyckeln används för 32-bitars program som körs på 64-bitarsdatorer.    |
> |**HKEY\_lokala\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed komponenter**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar ändringar på programinställningar.     |
> |**HKEY\_lokala\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare vanliga autostart transaktioner som anslutas direkt till Windows Explorer och vanligtvis kör i processen med Explorer.exe.|
> |**HKEY\_lokala\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare vanliga autostart transaktioner som anslutas direkt till Windows Explorer och vanligtvis kör i processen med Explorer.exe.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för ikonen täcker registrering.|
|**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för ikonen täcker registrering för 32-bitars program som körs på 64-bitarsdatorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper-objekt**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya webbläsare helper objektet plugin-program för Internet Explorer. För att få åtkomst till den modellen DOM (Document Object) för den aktuella sidan och styra navigering.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper-objekt**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya webbläsare helper objektet plugin-program för Internet Explorer. För att få åtkomst till den modellen DOM (Document Object) för den aktuella sidan och styra navigeringen för 32-bitars program som körs på 64-bitarsdatorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya tillägg i Internet Explorer, till exempel anpassat verktyg menyer och egna verktygsfältsknappar.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya tillägg i Internet Explorer, till exempel anpassat verktyg menyer och egna verktygsfältsknappar för 32-bitars program som körs på 64-bitarsdatorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar de 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc. Liknar [drivrutinsavsnittet] i systemet. INI-filen.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc för 32-bitars program som körs på 64-bitarsdatorer. Liknar [drivrutinsavsnittet] i systemet. INI-filen.|
> |**HKEY\_lokala\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar listan över kända eller vanliga system DLL: er; Det här systemet förhindrar att användare utnyttjar svaga directory programbehörigheter genom att släppa i trojansk häst versioner av system-DLL: er.|
> |**HKEY\_lokala\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar listan över paket som kan ta emot meddelanden om händelser från Winlogon supportmodell interaktiv inloggning för Windows-operativsystem.|

## <a name="use-change-tracking"></a>Använda ändringsspårning

När lösningen har aktiverats kan du visa en sammanfattning av ändringar för övervakade datorer genom att välja **ändringsspårning** under **KONFIGURATIONSHANTERING** i ditt Automation-konto.

Du kan visa ändringar i dina datorer och sedan gå till information för varje händelse. Listrutor finns överst i diagrammet för att begränsa diagrammet och detaljerad information baserat på Ändra typ och tidsintervall. Du kan också klicka och dra i diagrammet och välj ett anpassat tidsintervall.

![Bild av instrumentpanelen för ändringsspårning](./media/automation-change-tracking/change-tracking-dash01.png)

När du klickar på en ändring eller händelse öppnas den detaljerade informationen om den ändringen. Som du ser i exemplet har starttypen för tjänsten ändrats från manuell till automatisk.

![Bild av information för att spåra ändringar](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Sök i loggar

Förutom den information som tillhandahålls i portalen, kan du göra sökningar mot loggarna. Med den **ändringsspårning** öppen, klickar du på **Log Analytics**, öppnas den **Loggsökning** sidan.

### <a name="sample-queries"></a>Exempelfrågor

Följande tabell innehåller exempel på loggsökningar för ändra poster som samlas in av den här lösningen:

|Fråga  |Beskrivning  |
|---------|---------|
|ConfigurationData<br>&#124;där ConfigDataType == ”WindowsServices” och SvcStartupType == ”automatisk”<br>&#124;där SvcState == ”stoppad”<br>&#124;Sammanfatta arg_max(TimeGenerated, *) av SoftwareName dator         | Visar senaste lagerposter för Windows-tjänster som har ställts in på automatisk men har rapporterats som stoppas<br>Frågeresultaten har begränsats till den senaste posten för den SoftwareName och Computer      |
|ConfigurationChange<br>&#124;där ConfigChangeType == ”programvara” och ChangeCategory == ”borttagen”<br>&#124;order by-TimeGenerated fall|Visar ändringsposter för borttagna program|

## <a name="next-steps"></a>Nästa steg

Gå kursen på ändringsspårning vill veta mer om hur du använder lösningen:

> [!div class="nextstepaction"]
> [Felsöka ändringar i din miljö](automation-tutorial-troubleshoot-changes.md)

* Använd [Loggsökningar i Log Analytics](../log-analytics/log-analytics-log-searches.md) att visa detaljerad spårning av data.

---
title: Spåra ändringar med Azure Automation
description: Ändringsspårning-lösningen hjälper dig att identifiera program- och Windows-tjänst-ändringar som sker i din miljö.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b110f83274b2b42896bd18fb364c355ecc97a028
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Spåra ändringar i din miljö med lösningen för ändringsspårning

Den här artikeln hjälper dig att använda ändringsspårning-lösning för att enkelt identifiera ändringar i din miljö. Lösningen spårar ändringar i Windows och Linux programvara, Windows och Linux-filer, registernycklar i Windows, Windows-tjänster och Linux-daemon. Identifierar konfigurationsändringar kan hjälpa dig hitta operativa problem.

Ändringar av installerad programvara, Windows-tjänster, Windows-registret och filer och Linux-Daemon på övervakade servrar skickas till Log Analytics-tjänst i molnet för bearbetning. Molntjänsten innehåller data att logik tillämpas för mottagna data. Med hjälp av informationen på instrumentpanelen för spårning av ändringar, kan du enkelt se ändringar som gjorts i serverinfrastrukturen.

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Om du vill starta spårning av ändringar, måste du Aktivera ändringsspårning och lager lösningen för ditt Automation-konto.

1. Navigera till ditt Automation-konto i Azure-portalen
1. Välj **ändringsspårning** under **CONFIGURATION**.
1. Välj en befintlig Log analytics-arbetsyta eller **Skapa ny arbetsyta** och på **aktivera**.

Detta gör att lösningen för ditt automation-konto. Lösningen kan ta upp till 15 minuter att aktivera. Den blå banderollen meddelar dig när lösningen har aktiverats. Gå tillbaka till den **ändringsspårning** att hantera lösningen.

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurera spårning av ändringar och inventering

Mer information hur finns inbyggda datorer till lösningen: [Onboarding Automation lösningar](automation-onboard-solutions-from-automation-account.md). Du kan konfigurera de objekt du vill spåra när du har en dator onboarding med lösningen för att spåra ändringar och lager. När du aktiverar en ny fil eller registernyckel för att spåra kan aktiveras för ändringsspårning och lager.

MD5-hash-värden för filerna som används för att spåra ändringar i filer på Windows- och Linux. De här hashvärden används sedan för att identifiera om en ändring har gjorts sedan den senaste inventeringen.

### <a name="configure-linux-files-to-track"></a>Konfigurera Linux-filer för att spåra

Använd följande steg för att konfigurera spårning av filen på Linux-datorer:

1. Välj i ditt Automation-konto **ändringsspårning** under **KONFIGURATIONSHANTERING**. Klicka på **redigera inställningar för** (kugghjulet symbol).
2. På den **ändringsspårning** väljer **Linux filer**, klicka på **+ Lägg till** att lägga till en ny fil att spåra.
3. På den **Lägg till Linux-fil för ändringsspårning**, ange information för filen eller katalogen för att spåra och klicka på **spara**.

|Egenskap   |Beskrivning  |
|---------|---------|
|Enabled     | Bestämmer om inställningen som tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för logiskt gruppera filer.        |
|Ange sökväg     | Sökvägen till att söka efter filen. Till exempel: ”/etc/*.conf”       |
|Sökvägstyp     | Typ av objekt som ska vara spårade möjliga värden är filer och kataloger.        |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Använda Sudo     | Den här inställningen styr om sudo ska användas vid sökningen efter objektet.         |
|Länkar     | Den här inställningen styr hur symboliska länkar ska hanteras när de passerar kataloger.<br> **Ignorera** – ignorerar symboliska länkar och inte innehåller filer och kataloger som refererar till.<br>**Följ** – följer symboliska länkar under rekursion och även filer och kataloger som refererar till.<br>**Hantera** – följer symboliska länkar och tillåter ändring av returnerade innehåll.     |

> [!NOTE]
> Länkalternativet ”Hantera” rekommenderas inte. Hämtning av filinnehåll stöds inte.

### <a name="configure-windows-files-to-track"></a>Konfigurera Windows-filer för att spåra

Använd följande steg för att konfigurera filer spårning på Windows-datorer:

1. Välj i ditt Automation-konto **ändringsspårning** under **KONFIGURATIONSHANTERING**. Klicka på **redigera inställningar för** (kugghjulet symbol).
2. På den **ändringsspårning** väljer **Windows-filer**, klicka på **+ Lägg till** att lägga till en ny fil att spåra.
3. På den **lägga till Windows-filen för ändringsspårning**, ange information att spåra och klicka på filen **spara**.

|Egenskap   |Beskrivning  |
|---------|---------|
|Enabled     | Bestämmer om inställningen som tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för logiskt gruppera filer.        |
|Ange sökväg     | Sökvägen för att söka efter filen Till exempel: ”c:\temp\myfile.txt”       |

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurera Windows registernycklar för att spåra

Använd följande steg för att konfigurera registret viktiga spårning på Windows-datorer:

1. Välj i ditt Automation-konto **ändringsspårning** under **KONFIGURATIONSHANTERING**. Klicka på **redigera inställningar för** (kugghjulet symbol).
2. På den **ändringsspårning** väljer **Windows-registret**, klicka på **+ Lägg till** att lägga till en ny registernyckel för att spåra.
3. På den **lägga till Windows-registret för ändringsspårning**, ange information om nyckeln att spåra och klicka på **spara**.

|Egenskap   |Beskrivning  |
|---------|---------|
|Enabled     | Bestämmer om inställningen som tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för logiskt gruppera filer.        |
|Windows-registernyckel   | Sökvägen till att söka efter filen. Till exempel: ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Start”      |

## <a name="limitations"></a>Begränsningar

Ändringsspårning-lösningen stöder för närvarande inte följande:

* Mappar (kataloger) för Windows-spårning
* Rekursion för Windows-spårning
* Jokertecken för Windows-spårning
* Rekursion för spårning av Windows-registret
* Sökvägsvariabler
* Network file system
* Filinnehåll

Andra begränsningar:

* Den **maximal filstorlek** kolumnen och värdena är inte används i den aktuella implementeringen.
* Om du samlar in mer än 2 500 filer i 30 minuter insamlingscykel kan lösningens prestanda försämras.
* När nätverkstrafiken är hög, kan ändra poster ta upp till sex timmar att visa.
* Om du ändrar konfigurationen när en dator är avstängd, kan datorn efter ändringar som tillhör den tidigare konfigurationen.

## <a name="known-issues"></a>Kända problem

Ändringsspårning lösningen har för närvarande följande problem:

* Hotfix-uppdateringar har inte samlats in för Windows 10 skapare Update och Windows Server 2016 Core RS3 datorer.

## <a name="change-tracking-data-collection-details"></a>Ändra data collection detaljer

I följande tabell visas data collection frekvensen för vilka typer av ändringar. Ögonblicksbild av det aktuella tillståndet uppdateras också minst en gång per dygn för varje typ:

| **Ändra typen** | **Frekvens** |
| --- | --- |
| Windows-registret | 50 minuter |
| Windows-filen | 30 minuter |
| Linux-fil | 15 minuter |
| Windows-tjänster | 30 minuter |
| Linux-Daemon | 5 minuter |
| Windows-program | 30 minuter |
| Linux-programmet | 5 minuter |

### <a name="registry-key-change-tracking"></a>Ändringen av registernyckeln spårning

Syftet med att övervaka ändringar i registernycklar är att hitta punkter där kod från tredje part och skadlig kod kan aktivera. I följande lista innehåller en lista med förkonfigurerade registernycklar. De här nycklarna konfigureras, men inte aktiverad. Om du vill följa dessa registernycklar, måste du aktivera varje kriterium.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_lokala\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare vanliga autostart transaktioner koppla direkt i Utforskaren och kör vanligtvis i processen med Explorer.exe.    |
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare skript som körs vid start.     |
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare skript som körs vid avstängningen.     |
> |**HKEY\_lokala\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar nycklar som har lästs in innan användaren loggar in till sina Windows-konto. Nyckeln används för 32-bitars program som körs på 64-bitarsdatorer.    |
> |**HKEY\_lokala\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed komponenter**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar ändringar på programinställningar.     |
> |**HKEY\_lokala\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare vanliga autostart transaktioner koppla direkt i Utforskaren och kör vanligtvis i processen med Explorer.exe.|
> |**HKEY\_lokala\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare vanliga autostart transaktioner koppla direkt i Utforskaren och kör vanligtvis i processen med Explorer.exe.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för ikon täcker registrering.|
|**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för ikon täcker registrering för 32-bitars program som körs på 64-bitarsdatorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper objekt**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya webbläsare helper objektet plugin-program för Internet Explorer. För att komma åt den modellen DOM (Document Object) för den aktuella sidan och att styra navigeringen.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper objekt**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya webbläsare helper objektet plugin-program för Internet Explorer. För att komma åt den modellen DOM (Document Object) för den aktuella sidan och styra navigeringen för 32-bitars program som körs på 64-bitarsdatorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya tillägg för Internet Explorer, till exempel anpassade verktyget menyer och anpassade knappar.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya tillägg för Internet Explorer, till exempel anpassade verktyget menyer och anpassade knappar för 32-bitars program som körs på 64-bitarsdatorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc. Liknar [drivers]-avsnitt i systemet. INI-filen.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc för 32-bitars program som körs på 64-bitarsdatorer. Liknar [drivers]-avsnitt i systemet. INI-filen.|
> |**HKEY\_lokala\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar listan över kända eller vanliga system DLL: er; Det här systemet förhindrar att personer utnyttjar katalogbehörigheter svaga program genom att släppa trojansk häst versioner av system-DLL-filer.|
> |**HKEY\_lokala\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar listan över paket som kan ta emot meddelanden om händelser från Winlogon modellen interaktiv inloggning stöd för Windows-operativsystemet.|

## <a name="use-change-tracking"></a>Ändrar spårning

När lösningen har aktiverats kan du visa en sammanfattning av ändringar för övervakade datorer genom att välja **ändringsspårning** under **KONFIGURATIONSHANTERING** i Automation-kontot.

Du kan visa ändringar i dina datorer och detaljerat information för varje händelse. Listrutor finns högst upp i diagrammet för att begränsa diagram och detaljerad information baserat på ändra datatyp och tid intervall. Du kan också klicka och dra på diagrammet och välj ett anpassat tidsintervall.

![Bild av instrumentpanel för ändringsspårning](./media/automation-change-tracking/change-tracking-dash01.png)

Klicka på en ändring eller händelse visar detaljerad information om ändringen. Som du ser i exemplet har starttypen för tjänsten ändrats från manuell till automatisk.

![Bild av information för ändringsspårning](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Sökloggar

Förutom de information som finns i portalen kan sökningar göras mot loggarna. Med den **ändringsspårning** öppen, klicka på **logganalys**, öppnas den **loggen Sök** sidan.

### <a name="sample-queries"></a>Exempelfrågor

Följande tabell innehåller exempel loggen söker efter ändra poster som samlas in av den här lösningen:

|Fråga  |Beskrivning  |
|---------|---------|
|ConfigurationData<br>&#124;där ConfigDataType == ”WindowsServices” och SvcStartupType == ”automatisk”<br>&#124;där SvcState == ”stoppades”<br>&#124;Sammanfatta arg_max(TimeGenerated, *) av SoftwareName dator         | Visar senaste lagerposter för Windows-tjänster som har ställts in på automatisk men rapporterades som stoppats<br>Resultatet är begränsade till den senaste posten för den SoftwareName och dator      |
|ConfigurationChange<br>&#124;där ConfigChangeType == ”programvara” och ChangeCategory == ”borttagen”<br>&#124;order by-TimeGenerated desc|Visar ändra poster för borttagna programvara|

## <a name="next-steps"></a>Nästa steg

Besök kursen om spårning av ändringar för att lära dig mer om hur du använder lösningen:

> [!div class="nextstepaction"]
> [Felsökning av ändringar i din miljö](automation-tutorial-troubleshoot-changes.md)

* Använd [logga sökningar i logganalys](../log-analytics/log-analytics-log-searches.md) att visa detaljerade data för ändringsspårning.

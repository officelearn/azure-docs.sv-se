---
title: Spåra ändringar med Azure Automation
description: Lösningen ändringsspårning kan du identifiera programvara och Windows Service-ändringar som sker i din miljö.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e997d9e9f38fee52cd9fc007fe12cac68e3aa5a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008689"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Spåra ändringar i miljön med lösningen ändringsspårning

Den här artikeln hjälper dig att använda ändringsspårning-lösning lätt kan identifiera ändringar i miljön. Lösningen spårar ändringar i Windows och Linux, Windows och Linux-filer, registernycklar för Windows, Windows-tjänster och Linux-daemon. Identifiera konfigurationsändringar kan hjälpa dig att precisera Driftproblem.

Ändringar av installerad programvara, tjänster för Windows, Windows-registret och filer och Linux-Daemon på de övervakade servrarna skickas till Azure Monitor-tjänsten i molnet för bearbetning. Logiken tillämpas på den mottagna data och Molntjänsten innehåller data. Du kan enkelt se ändringar som gjorts i din serverinfrastruktur med hjälp av informationen på instrumentpanelen för ändringsspårning.

> [!NOTE]
> Azure Automation Change Tracking spårar ändringar i virtuella datorer. Om du vill spåra egenskapsändringar för Azure Resource Manager finns i Azure Resource Graph [ändringshistorik](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="supported-windows-operating-systems"></a>Windows-operativsystem som stöds

Följande versioner av Windows-operativsystemet stöds officiellt för Windows-agenten:

* Windows Server 2008 R2 eller senare

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Följande Linux-distributioner stöds officiellt. Linux-agenten kan också köra på andra distributioner som inte visas. Om inget annat anges stöds alla mindre versioner för varje större version i listan.  

### <a name="64-bit"></a>64-bitars

* CentOS 6 och 7
* Amazon Linux 2017.09
* Oracle Linux 6 och 7
* Red Hat Enterprise Linux Server 6 och 7
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS och 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32-bitars

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14.04 LTS och 16.04 LTS

## <a name="onboard"></a>Aktivera ändringsspårning och inventering

Om du vill börja spåra ändringar, måste du aktivera lösningen ändringsspårning och inventering. Det finns många sätt att registrera datorer till ändringsspårning och inventering. Följande är den rekommenderade och stöd för hur du publicerar lösningen.

* [Från en virtuell dator](automation-onboard-solutions-from-vm.md)
* [Från flera datorer-surfning](automation-onboard-solutions-from-browse.md)
* [Från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md)
* [Med en Azure Automation-runbook](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurera ändringsspårning och inventering

Läs hur att integrera datorer till lösningen finns: [Onboarding-automatiseringslösningar](automation-onboard-solutions-from-automation-account.md). När du har en dator onboarding med lösningen ändringsspårning och inventering kan konfigurera du de objekt som ska spåra. När du aktiverar en ny fil eller registernyckel att spåra kan aktiveras för ändringsspårning och inventering.

MD5-hash-värden för filerna som används för att spåra ändringar i filer på både Windows och Linux. Dessa hashvärden används sedan för att identifiera om en ändring har gjorts sedan den senaste inventeringen.

### <a name="configure-linux-files-to-track"></a>Konfigurera Linux-filer att spåra

Använd följande steg för att konfigurera filspårning på Linux-datorer:

1. I ditt Automation-konto väljer **ändringsspårning** under **KONFIGURATIONSHANTERING**. Klicka på **redigera inställningar för** (kugghjulet).
2. På den **ändringsspårning** väljer **Linux-filer**, klicka sedan på **+ Lägg till** att lägga till en ny fil och spåra.
3. På den **Lägg till Linux-fil för ändringsspårning**, anger du informationen för filen eller katalogen som ska spåras och klickar på **spara**.

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Avgör om inställningen tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt.        |
|Ange sökväg     | Sökvägen till att söka efter filen. Till exempel: ”/etc/*.conf”       |
|Sökvägstyp     | Typ av objekt som ska spåras, möjliga värden är fil och katalog.        |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Använda Sudo     | Den här inställningen styr om sudo ska användas vid sökningen efter objektet.         |
|Länkar     | Den här inställningen styr hur symboliska länkar ska hanteras när de passerar kataloger.<br> **Ignorera** – ignorerar symboliska länkar och inkluderar inte refererade filer/kataloger.<br>**Följ** – följer de symboliska länkarna under rekursion och dessutom ingår refererade filer/kataloger.<br>**Hantera** – följer de symboliska länkarna och tillåter ändring av returnerat innehåll.     |
|Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: **Sant** eller **Falskt**.|

> [!NOTE]
> Länkalternativet ”Hantera” rekommenderas inte. Hämtning av filinnehåll stöds inte.

### <a name="configure-windows-files-to-track"></a>Konfigurera Windows-filer för att spåra

Använd följande steg för att konfigurera filer spårning på Windows-datorer:

1. I ditt Automation-konto väljer **ändringsspårning** under **KONFIGURATIONSHANTERING**. Klicka på **redigera inställningar för** (kugghjulet).
2. På den **ändringsspårning** väljer **Windows filer**, klicka sedan på **+ Lägg till** att lägga till en ny fil och spåra.
3. På den **Lägg till Windows-fil för ändringsspårning**, anger du informationen för den fil som ska spåras och klickar på **spara**.

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Avgör om inställningen tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt.        |
|Ange sökväg     | Sökvägen för att söka efter filen Till exempel: "c:\temp\\\*.txt"<br>Du kan också använda miljövariabler som "%winDir%\System32\\\*.*"       |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: **Sant** eller **Falskt**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Inställningar för jokertecken, rekursion och miljö

Rekursion kan du ange jokertecken för att förenkla spårning i kataloger och miljövariabler att spåra filer i miljöer med flera eller dynamisk enhet namn. I följande lista visas vanliga information som du bör känna till när du konfigurerar rekursion:

* Jokertecken krävs för att spåra flera filer
* Om du använder jokertecken, kan de bara användas i det sista segmentet i en sökväg. (till exempel `c:\folder\*file*` eller `/etc/*.conf`)
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

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Avgör om inställningen tillämpas.        |
|Objektnamn     | Eget namn för registernyckeln som ska spåras.        |
|Grupp     | Ett gruppnamn för att gruppera logiskt registernycklar.        |
|Windows-registernyckel   | Sökvägen för att söka för registernyckel. Exempel: ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

## <a name="limitations"></a>Begränsningar

Lösningen ändringsspårning stöder inte för närvarande följande objekt:

* Rekursion för Windows-registret spårning
* Network file system

Andra begränsningar:

* Den **största filstorlek** kolumnen och värdena är oanvända i den aktuella implementeringen.
* Om du samlar in mer än 2 500 filer i 30-minuters insamlingscykel kan lösningens prestanda försämras.
* När nätverkstrafik är hög kan ändringsposter ta upp till sex timmar att visa.
* Om du ändrar konfigurationen när en dator är avstängd, kanske datorn publicerar ändringar som tillhör den tidigare konfigurationen.

## <a name="known-issues"></a>Kända problem

Lösningen ändringsspårning har för närvarande följande problem:

* Hotfix-uppdateringar har inte samlats in på Windows Server 2016 Core RS3 datorer.
* Linux-Daemon kan indikera ett ändrade tillstånd även om det fanns ingen ändring. Detta beror på hur `SvcRunLevels` fältet avbildas.

## <a name="change-tracking-data-collection-details"></a>Ändra spårningsinformation data samling

I följande tabell visas insamlingsfrekvens data för typerna av ändringar. För varje uppdateras ögonblicksbild av det aktuella tillståndet var 24 timmar:

| **Ändra typ** | **Frekvens** |
| --- | --- |
| Windows-registret | 50 minuter |
| Windows-fil | 30 minuter |
| Linux-fil | 15 minuter |
| Windows-tjänster | 10 sekunder till 30 minuter</br> Standard: 30 minuter |
| Linux-Daemon | 5 minuter |
| Windows-programvara | 30 minuter |
| Linux-programvara | 5 minuter |

I följande tabell visar de spårade objekt gränserna per dator för ändringsspårning.

| **Resurs** | **Gräns**| **Anteckningar** |
|---|---|---|
|Fil|500||
|Register|250||
|Windows-programvara|250|Omfattar inte programuppdateringar|
|Linux-paket|1250||
|Tjänster|250||
|Daemon|250||

Genomsnittlig användning av Log Analytics-data för en virtuell dator med ändringsspårning och inventering är cirka 40MB per månad. Det här värdet är bara en uppskattning och kan komma att ändras baserat på din miljö. Vi rekommenderar att du övervakar din miljö för att se exakt användningen som du har.

### <a name="windows-service-tracking"></a>Windows service-spårning

Insamlingsfrekvens standard för Windows-tjänster är 30 minuter. Om du vill ange hur ofta, gå till **ändringsspårning**. Under **redigera inställningar för** på den **Windows Services** fliken finns det ett skjutreglage som gör det möjligt att ändra insamlingsfrekvens för Windows-tjänster från så snabbt som 10 sekunder till upp till 30 minuter. Flytta skjutreglaget till hur ofta du vill och det sparas automatiskt.

![Skjutreglaget för Windows-tjänster](./media/automation-change-tracking/windowservices.png)

Agenten endast spårar ändringar, detta optimerar prestanda för agenten. Ange en hög tröskel kan missa ändringar om tjänsten har återställts till deras ursprungliga tillstånd. Ange frekvensen till ett mindre värde kan du fånga ändringar som annars kan missas.

> [!NOTE]
> Agenten kan spåra ändringar till ett 10 andra intervall, data fortfarande tar några minuter som ska visas i portalen. Ändringar under den tid som ska visas i portalen är fortfarande spåras och loggas.
  
### <a name="registry-key-change-tracking"></a>Viktiga registerändringsspårning

Syftet med att övervaka ändringar i registernycklarna är att identifiera punkter där kod från tredje part och skadlig kod kan aktivera. I följande lista visas listan över förkonfigurerade registernycklar. De här nycklarna konfigureras, men inte aktiverad. Du måste aktivera var och en för att spåra dessa registernycklar.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare vanliga autostart transaktioner som anslutas direkt till Windows Explorer och vanligtvis kör i processen med Explorer.exe.    |
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare-skript som körs vid start.     |
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare-skript som körs vid avstängningen.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar nycklar som har lästs in innan användaren loggar in på sina Windows-konto. Nyckeln används för 32-bitars program som körs på 64-bitarsdatorer.    |
> |**HKEY\_lokala\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed komponenter**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar ändringar på programinställningar.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare vanliga autostart transaktioner som anslutas direkt till Windows Explorer och vanligtvis kör i processen med Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare vanliga autostart transaktioner som anslutas direkt till Windows Explorer och vanligtvis kör i processen med Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för ikonen täcker registrering.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för ikonen täcker registrering för 32-bitars program som körs på 64-bitarsdatorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper-objekt**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya webbläsare helper objektet plugin-program för Internet Explorer. För att få åtkomst till den modellen DOM (Document Object) för den aktuella sidan och styra navigering.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper-objekt**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya webbläsare helper objektet plugin-program för Internet Explorer. För att få åtkomst till den modellen DOM (Document Object) för den aktuella sidan och styra navigeringen för 32-bitars program som körs på 64-bitarsdatorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya tillägg i Internet Explorer, till exempel anpassat verktyg menyer och egna verktygsfältsknappar.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för nya tillägg i Internet Explorer, till exempel anpassat verktyg menyer och egna verktygsfältsknappar för 32-bitars program som körs på 64-bitarsdatorer.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar de 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc. Liknar [drivrutinsavsnittet] i systemet. INI-filen.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc för 32-bitars program som körs på 64-bitarsdatorer. Liknar [drivrutinsavsnittet] i systemet. INI-filen.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar listan över kända eller vanliga system DLL: er; Det här systemet förhindrar att användare utnyttjar svaga directory programbehörigheter genom att släppa i trojansk häst versioner av system-DLL: er.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar listan över paket som kan ta emot meddelanden om händelser från Winlogon supportmodell interaktiv inloggning för Windows-operativsystem.|

## <a name="network-requirements"></a>Nätverkskrav

Följande adresser krävs för ändringsspårning. Kommunikation till dessa adresser görs via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Använda ändringsspårning

När lösningen har aktiverats kan du visa en sammanfattning av ändringar för övervakade datorer genom att välja **ändringsspårning** under **KONFIGURATIONSHANTERING** i ditt Automation-konto.

Du kan visa ändringar i dina datorer och sedan gå till information för varje händelse. Listrutor finns överst i diagrammet för att begränsa diagrammet och detaljerad information baserat på Ändra typ och tidsintervall. Du kan också klicka och dra i diagrammet och välj ett anpassat tidsintervall.

![Bild av instrumentpanelen för ändringsspårning](./media/automation-change-tracking/change-tracking-dash01.png)

När du klickar på en ändring eller händelse öppnas den detaljerade informationen om den ändringen. Som du ser i exemplet har starttypen för tjänsten ändrats från manuell till automatisk.

![Bild av information för att spåra ändringar](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Sök i loggar

Förutom den information som tillhandahålls i portalen, kan du göra sökningar mot loggarna. Med den **ändringsspårning** öppen, klickar du på **Log Analytics**, öppnas den **loggar** sidan.

### <a name="sample-queries"></a>Exempelfrågor

Följande tabell innehåller exempel på loggsökningar för ändra poster som samlas in av den här lösningen:

|Fråga  |Beskrivning  |
|---------|---------|
|ConfigurationData<br>&#124;där ConfigDataType == ”WindowsServices” och SvcStartupType == ”automatisk”<br>&#124;där SvcState == ”stoppad”<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Visar senaste lagerposter för Windows-tjänster som har ställts in på automatisk men har rapporterats som stoppas<br>Frågeresultaten har begränsats till den senaste posten för den SoftwareName och Computer      |
|ConfigurationChange<br>&#124;där ConfigChangeType == ”programvara” och ChangeCategory == ”borttagen”<br>&#124;order by-TimeGenerated fall|Visar ändringsposter för borttagna program|

## <a name="alert-on-changes"></a>Avisera om ändringar

En viktig funktion för ändringsspårning och inventering är möjligheten att Avisera om konfigurationstillståndet och eventuella ändringar gällande konfigurationstillståndet för din hybridmiljön.  

I följande exempel skärmbilden visar att filen `C:\windows\system32\drivers\etc\hosts` har ändrats på en dator. Den här filen är viktigt eftersom Hosts-filen används av Windows för att matcha värdnamn till IP-adresser och har företräde framför även DNS, vilket kan leda till problem med nätverksanslutningen eller omdirigering av trafik till skadliga eller annars farliga webbplatser.

![Ett diagram som visar värdarna filändring](./media/automation-change-tracking/changes.png)

Om du vill analysera den här ändringen ytterligare, gå till loggsökning från att klicka på **Log Analytics**. En gång i loggsökning, Sök efter innehållet ändras Hosts-filen med frågan `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Den här frågan söker efter ändringar som innehöll en ändring av filinnehåll för filer vars fullständigt kvalificerade sökvägen innehåller ordet ”värdar”. Du kan också fråga efter en viss fil genom att ändra sökvägsdelen till dess fullständigt kvalificerade (till exempel `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

När frågan returnerar det önskade resultatet, klickar du på den **ny aviseringsregel** knappen i loggen sökupplevelsen att öppna sidan skapande av varning. Du kan också navigera till den här upplevelsen via **Azure Monitor** i Azure-portalen. Kontrollera frågan igen och ändra avisering logiken i skapande av varning-upplevelse. I detta fall använder vill du att aviseringen ska utlösas om det finns även en ändring som har identifierats för alla datorer i miljön.

![En bild som visar ändra frågan för att spåra ändringar i värdfilen](./media/automation-change-tracking/change-query.png)

Tilldela åtgärdsgrupper för att utföra åtgärder som svar på aviseringen utlöses när du har angett villkorsstyrd logik. I det här fallet har jag ställa in e-postmeddelanden som ska skickas och ITSM-biljett som ska skapas.  Många andra användbara åtgärder kan också hämtas som att utlösa en Azure-funktion, Automation runbook, webhook eller Logikapp.

![En avbildning som konfigurerar en åtgärdsgrupp att aviseringen på ändringar](./media/automation-change-tracking/action-groups.png)

Efter alla parametrar och logik ställs in, kan vi använda aviseringen för miljön.

### <a name="alert-suggestions"></a>Aviseringen förslag

Varna vid ändringar i värdfilen är en bra tillämpning av aviseringar för ändringsspårning och inventering data, finns men det många fler scenarier för avisering, inklusive fall som definierats tillsammans med deras exempelfrågor i avsnittet nedan.

|Fråga  |Beskrivning  |
|---------|---------|
|ConfigurationChange <br>&#124;där ConfigChangeType == ”filer” och FileSystemPath innehåller ”c:\\windows\\system32\\drivrutiner\\”|Användbart för att spåra ändringar av kritiska systemfiler|
|ConfigurationChange <br>&#124;där FieldsChanged innehåller ”FileContentChecksum” och FileSystemPath == ”c:\\windows\\system32\\drivrutiner\\osv\\värdar”|Användbart för att spåra ändringar av viktiga konfigurationsfiler|
|ConfigurationChange <br>&#124;där ConfigChangeType == ”WindowsServices” och SvcName innehåller ”w3svc” och SvcState == ”stoppad”|Användbart för att spåra ändringar av kritiska systemtjänster|
|ConfigurationChange <br>&#124;där ConfigChangeType == ”Daemons” och SvcName innehåller ”ssh” och SvcState! = ”körs”|Användbart för att spåra ändringar av kritiska systemtjänster|
|ConfigurationChange <br>&#124;där ConfigChangeType == ”programvara” och ChangeCategory == ”har lagts till”|Användbart för miljöer som behöver låsta konfigurationer|
|ConfigurationData <br>&#124;där SoftwareName innehåller ”Monitoring Agent” och CurrentVersion! = ”8.0.11081.0”|Användbar för att se vilka datorer har en inaktuell eller icke-kompatibla program-version som är installerad. Rapporterar den senaste rapporterade Konfigurationsstatus, inte ändringar.|
|ConfigurationChange <br>&#124; where RegistryKey == "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Användbart för att spåra ändringar av avgörande antivirus-nycklar|
|ConfigurationChange <br>&#124;där RegistryKey innehåller ”HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\parametrar\\FirewallPolicy”| Användbart för att spåra ändringar av brandväggsinställningar|

## <a name="next-steps"></a>Nästa steg

Gå kursen på ändringsspårning vill veta mer om hur du använder lösningen:

> [!div class="nextstepaction"]
> [Felsöka ändringar i din miljö](automation-tutorial-troubleshoot-changes.md)

* Använd [Loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md) att visa detaljerad spårning av data.


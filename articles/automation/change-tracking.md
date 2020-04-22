---
title: Spåra ändringar med Azure Automation
description: Lösningen för ändringsspårning hjälper dig att identifiera ändringar av programvara och Windows-tjänster som sker i din miljö.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: d84566c7680081561f60d4825f25a9ce19e02b24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682981"
---
# <a name="track-environment-changes-with-change-tracking"></a>Spåra miljöändringar med ändringsspårning

Den här artikeln hjälper dig att använda lösningen för ändringsspårning för att enkelt identifiera ändringar i din miljö. Lösningen spårar följande konfigurationsändringar som hjälper dig att identifiera driftproblem:

- Windows-programvara
- Linux-programvara (paket)
    >[!NOTE]
    >Ändra spårning spårar bara programvara som hanteras med distributionspakethanteraren.

- Windows- och Linux-filer
- Windows-registernycklar
- Windows-tjänster
- Linux-demoner

När lösningen har aktiverats kan du visa sammanfattningen av ändringar för dina övervakade datorer genom att välja **Ändra spårning** under **Konfigurationshantering** i ditt Automation-konto.

> [!NOTE]
> Azure Automation Change Tracking spårar ändringar i virtuella datorer. Information om hur du spårar ändringar i Azure Resource Manager finns i [Ändringshistoriken för](../governance/resource-graph/how-to/get-resource-changes.md)Azure Resource Graph .

Du kan visa ändringar på dina datorer och sedan gå in på information om varje händelse. Listningsrullgarde finns högst upp i diagrammet för att begränsa diagrammet och detaljerad information baserat på ändringstyp och tidsintervall. Du kan också klicka och dra i diagrammet för att välja ett anpassat tidsintervall. **Ändringstyp** är ett av följande värden **Händelser**, **Demoner**, **Filer**, **Register**, **Programvara**, **Windows Services**. Kategorin visar typen av ändring och kan **läggas till,** **Ändrad**eller **Borttagen**.

![bild på instrumentpanelen Ändra spårning](./media/change-tracking/change-tracking-dash01.png)

Om du klickar på en ändring eller händelse visas detaljerad information om ändringen. Som du kan se i exemplet ändrades tjänstens starttyp från Manuell till Auto.

![bild av information om ändringsspårning](./media/change-tracking/change-tracking-details.png)

Ändringar av installerad programvara, Windows-tjänster, Windows-register och filer och Linux-demoner på de övervakade servrarna skickas till Azure Monitor-tjänsten i molnet för bearbetning. Logik tillämpas på mottagna data och molntjänsten registrerar data. Genom att använda informationen på instrumentpanelen för ändringsspårning kan du enkelt se de ändringar som har gjorts i serverinfrastrukturen.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

### <a name="windows-operating-systems"></a>Windows-operativsystem

Följande versioner av Operativsystemet Windows stöds officiellt för Windows-agenten:

* Windows Server 2008 R2 eller senare

### <a name="linux-operating-systems"></a>Linux-operativsystem

Följande Linux-distributioner stöds officiellt. Linux-agenten kan dock också köras på andra distributioner som inte finns med i listan. Om inget annat anges stöds alla mindre versioner för varje huvudversion som anges.

#### <a name="64-bit"></a>64-bitars

* CentOS 6 och 7
* Amazon Linux 2017.09
* Oracle Linux 6 och 7
* Red Hat Enterprise Linux Server 6 och 7
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS och 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit"></a>32-bitars

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14.04 LTS och 16.04 LTS

## <a name="limitations"></a>Begränsningar

Lösningen för ändringsspårning stöder för närvarande inte följande objekt:

* Rekursion för Windows-registerspårning
* Filsystem för nätverk
* Olika installationsmetoder
* ***.exe-filer** för Windows

Andra begränsningar:

* Kolumnen **Max filstorlek** och värden är oanvända i den aktuella implementeringen.
* Om du samlar in fler än 2500 filer i en 30-minuters insamlingscykel kan lösningsprestanda försämras.
* När nätverkstrafiken är hög kan det ta upp till sex timmar att visa ändringsposter.
* Om du ändrar konfigurationen medan en dator stängs av kan datorn publicera ändringar som hör till den tidigare konfigurationen.

## <a name="known-issues"></a>Kända problem

Lösningen för ändringsspårning har för närvarande följande problem:

* Snabbkorrigeringsuppdateringar samlas inte in på Windows Server 2016 Core RS3-datorer.
* Linux Daemons kan visa ett förändrat tillstånd även om det inte fanns någon förändring. Detta beror på `SvcRunLevels` hur fältet fångas in.

## <a name="network-requirements"></a>Nätverkskrav

Ändringsspårning kräver specifikt följande adresser. Kommunikation till dessa adresser använder port 443.

|Azure, offentlig  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="wildcard-recursion-and-environment-settings"></a>Jokertecken, rekursion och miljöinställningar

Med rekursion kan du ange jokertecken för att förenkla spårningen mellan kataloger och miljövariabler så att du kan spåra filer i miljöer med flera eller dynamiska enhetsnamn. Följande lista visar allmän information som du bör känna till när du konfigurerar rekursion:

* Jokertecken krävs för att spåra flera filer.
* Jokertecken och endast användas i det sista segmentet i en\\sökväg, till exempel c:\folder file* eller /etc/*.conf.
* Om en miljövariabel har en ogiltig sökväg lyckas valideringen, men sökvägen misslyckas när lager körs.
* Undvik allmänna sökvägar när du anger sökvägen, eftersom den här typen av inställning kan orsaka att för många mappar går igenom.

## <a name="change-tracking-data-collection-details"></a>Information om insamling av ändringsspårning

I följande tabell visas datainsamlingsfrekvensen för typerna av ändringar. För varje typ uppdateras också dataögonblicksbilden av det aktuella tillståndet minst var 24:e timme:

| **Ändra typ** | **Frekvens** |
| --- | --- |
| Windows-registret | 50 minuter |
| Windows-fil | 30 minuter |
| Linux-fil | 15 minuter |
| Windows-tjänster | 10 sekunder till 30 minuter</br> Standard: 30 minuter |
| Linux-demoner | 5 minuter |
| Windows-programvara | 30 minuter |
| Linux-programvara | 5 minuter |

I följande tabell visas de spårade artikelgränserna per dator för ändringsspårning.

| **Resurs** | **Gräns**| **Obs!** |
|---|---|---|
|Fil|500||
|Register|250||
|Windows-programvara|250|Innehåller inte snabbkorrigeringar för programvara|
|Linux-paket|1250||
|Tjänster|250||
|Daemon|250||

Den genomsnittliga logganalysdataanvändningen för en dator som använder ändringsspårning är cirka 40 MB per månad. Det här värdet är bara en approximation och kan komma att ändras baserat på din miljö. Vi rekommenderar att du övervakar din miljö för att se exakt vilken användning du har.

### <a name="windows-service-tracking"></a>Spårning av Windows-tjänster

Standardinsamlingsfrekvensen för Windows-tjänster är 30 minuter. Om du vill konfigurera frekvensen går du till **Ändra spårning**. Under **Redigera inställningar** på fliken **Windows-tjänster** finns ett skjutreglage som gör att du kan ändra insamlingsfrekvensen för Windows-tjänster från så snabbt som 10 sekunder till så länge som 30 minuter. Flytta skjutreglaget till den frekvens du vill ha och spara det automatiskt.

![Skjutreglaget Windows-tjänster](./media/change-tracking/windowservices.png)

Agenten spårar bara ändringar, detta optimerar agentens prestanda. Om du anger ett högt tröskelvärde kan det gå fel vid ändringar om tjänsten återgår till sitt ursprungliga tillstånd. Om du ställer in frekvensen på ett mindre värde kan du fånga upp ändringar som kan missas annars.

> [!NOTE]
> Agenten kan spåra ändringar till ett 10 sekunders intervall, men det tar fortfarande några minuter att visa data i portalen. Ändringar under den tid som ska visas i portalen spåras fortfarande och loggas.

### <a name="registry-key-change-tracking"></a>Spårning av ändring av registernyckel

Syftet med att övervaka ändringar av registernycklar är att precisera utökningspunkter där kod och skadlig kod från tredje part kan aktiveras. I följande lista visas en lista över förkonfigurerade registernycklar. Dessa nycklar är konfigurerade men inte aktiverade. Om du vill spåra dessa registernycklar måste du aktivera var och en.

> [!div class="mx-tdBreakAll"]
> |Registernyckel | Syfte |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Övervakar vanliga autostartposter som ansluts direkt till Utforskaren och som vanligtvis körs i processen med Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Övervakar skript som körs vid start.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Övervakar skript som körs vid avstängning.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Övervakar nycklar som läses in innan användaren loggar in på sitt Windows-konto. Nyckeln används för 32-bitarsprogram som körs på 64-bitarsdatorer.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Övervakar ändringar av programinställningarna.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Övervakar vanliga autostartposter som ansluts direkt till Utforskaren och som vanligtvis körs i processen med Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Övervakar vanliga autostartposter som ansluts direkt till Utforskaren och som vanligtvis körs i processen med Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Övervakare för registrering av ikonöverläggshanterare.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Övervakare för registrering av ikonöverlagring för 32-bitarsprogram som körs på 64-bitarsdatorer.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Övervakare för nya webbläsarhjälpobjektinsticksprogram för Internet Explorer. Används för att komma åt dokumentobjektmodellen (DOM) på den aktuella sidan och för att styra navigeringen.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Övervakare för nya webbläsarhjälpobjektinsticksprogram för Internet Explorer. Används för att komma åt dokumentobjektmodellen (DOM) på den aktuella sidan och för att styra navigeringen för 32-bitarsprogram som körs på 64-bitarsdatorer.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Övervakare för nya Internet Explorer-tillägg, till exempel anpassade verktygsmenyer och anpassade verktygsfältsknappar.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Bildskärmar för nya Internet Explorer-tillägg, till exempel anpassade verktygsmenyer och anpassade verktygsfältsknappar för 32-bitarsprogram som körs på 64-bitarsdatorer.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Övervakar de 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc. Liknar avsnittet [drivrutiner] i systemet. INI-fil.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Övervakar de 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc för 32-bitarsprogram som körs på 64-bitars datorer. Liknar avsnittet [drivrutiner] i systemet. INI-fil.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Övervakar listan över kända eller vanliga system-DLL:er. detta system hindrar människor från att utnyttja svaga programkatalog behörigheter genom att släppa i trojansk häst versioner av systemet DLL.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Övervakar listan över paket som kan ta emot händelsemeddelanden från Winlogon, den interaktiva inloggningssupportmodellen för Operativsystemet Windows.

## <a name="enable-change-tracking"></a><a name="onboard"></a>Aktivera ändringsspårning

För att börja spåra ändringar måste du aktivera lösningen Ändra spårning. Det finns många sätt att omborda datorer för att ändra spårning. Följande är de rekommenderade och stöds sätt att ombord på lösningen.

* [Från en virtuell dator](automation-onboard-solutions-from-vm.md)
* [Från att bläddra i flera maskiner](automation-onboard-solutions-from-browse.md)
* [Från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md)
* [Med en Azure Automation-runbook](automation-onboard-solutions.md)

## <a name="configure-change-tracking"></a>Konfigurera ändringsspårning

Mer information om hur du kontrollerar datorer som finns i [Onboarding Automation-lösningar](automation-onboard-solutions-from-automation-account.md). När du har en datorinrovering med lösningen Ändra spårning kan du konfigurera artiklarna så att de spåras. När du aktiverar en ny fil eller registernyckel för att spåra, är den aktiverad för både ändringsspårning.

För att spåra ändringar i filer på både Windows och Linux används MD5-hashar av filerna. Avhandlingar hashar används sedan för att identifiera om en ändring har gjorts sedan den senaste inventeringen.

## <a name="enable-file-integrity-monitoring-in-azure-security-center"></a>Aktivera filintegritetsövervakning i Azure Security Center

Azure Security Center har lagt till FIM (File Integrity Monitoring), som bygger på Azure Change Tracking. Medan FIM endast övervakar filer och register innehåller den fullständiga ändringsspårningslösningen även:

- Ändringar av programvara
- Windows-tjänster
- Linux demoner

Om du redan har aktiverat FIM och vill prova den fullständiga lösningen för ändringsspårning måste du utföra följande steg. Du inställningar tas inte bort av den här processen.

> [!NOTE]
> Om du aktiverar den fullständiga lösningen för ändringsspårning kan det orsaka ytterligare avgifter, mer information, se [Automatiseringspriser](https://azure.microsoft.com/pricing/details/automation/).

1. Ta bort övervakningslösningen genom att navigera till arbetsytan och hitta den i [listan över installerade övervakningslösningar](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Klicka på namnet på lösningen för att öppna sammanfattningssidan och klicka sedan på Ta bort, som beskrivs i [Ta bort en övervakningslösning](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Aktivera lösningen igen genom att navigera till Automation-kontot och välja **Ändra spårning** under **Konfigurationshantering**.
4. Bekräfta information om arbetsyteinställningen och klicka på **Aktivera**.

## <a name="configure-file-content-change-tracking"></a>Konfigurera spårning av ändring av filinnehåll

Du kan visa innehållet före och efter filändring med spårning av filinnehållsändringar. Den här funktionen är tillgänglig för Windows- och Linux-filer. För varje ändring av en fil lagras innehållet i filen i ett lagringskonto. Filen visas före och efter ändringen, infogad eller sida vid sida. Mer information finns [i Visa innehållet i en spårad fil](change-tracking-file-contents.md).

![visa ändringar i en fil](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="configure-windows-registry-keys-to-track"></a>Konfigurera Windows-registernycklar för att spåra

Så här konfigurerar du spårning av registernyckel på Windows-datorer:

1. I ditt Automation-konto väljer du **Ändra spårning** under **Konfigurationshantering**. Klicka på **Redigera inställningar** (kugghjulssymbolen).
2. På sidan Ändra spårning väljer du **Windows-register**och klickar sedan på **+ Lägg** till för att lägga till en ny registernyckel för att spåra.
3. I **Lägg till Windows-registret för ändringsspårning**anger du informationen för att spåra nyckeln och klickar på **Spara**.

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Avgör om inställningen används.        |
|Objektnamn     | Eget namn på registernyckeln som ska spåras.        |
|Grupp     | Ett gruppnamn för logiskt gruppering av registernycklar.        |
|Windows-registernyckel   | Sökvägen för att söka efter registernyckeln. Till exempel: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="configure-file-tracking-on-windows"></a>Konfigurera filspårning i Windows

Så här konfigurerar du filspårning på Windows-datorer:

1. I ditt Automation-konto väljer du **Ändra spårning** under **Konfigurationshantering**. Klicka på **Redigera inställningar** (kugghjulssymbolen).
2. På sidan Ändra spårning väljer du **Windows-filer**och klickar sedan på **+ Lägg** till för att lägga till en ny fil som ska spåras.
3. I **Lägg till Windows-fil för ändringsspårning**anger du informationen för filen att spåra och klickar på **Spara**.

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Sant om inställningen tillämpas och Falskt annars.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för logiskt gruppering av filer.        |
|Ange sökväg     | Sökvägen för att söka efter filen, till exempel **c:\temp\\\*.txt**<br>Du kan också använda miljövariabler, till exempel `%winDir%\System32\\\*.*`.       |
|Rekursion     | Sant om rekursion används när du letar efter objektet som ska spåras, och Falskt annars.        |
|Ladda upp filinnehåll för alla inställningar| Sant att ladda upp filinnehåll vid spårade ändringar och Falskt annars.|

## <a name="configure-file-tracking-on-linux"></a>Konfigurera filspårning på Linux

Så här konfigurerar du filspårning på Linux-datorer:

1. I ditt Automation-konto väljer du **Ändra spårning** under **Konfigurationshantering**. Klicka på **Redigera inställningar** (kugghjulssymbolen).
2. På sidan Ändra spårning väljer du **Linux-filer**och klickar sedan på **+ Lägg** till för att lägga till en ny fil för att spåra.
3. På **Lägg till Linux-fil för ändringsspårning**anger du informationen för filen eller katalogen för att spåra och klickar på **Spara**.

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Avgör om inställningen används.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för logiskt gruppering av filer.        |
|Ange sökväg     | Sökvägen för att söka efter filen. Till exempel: "/etc/*.conf"       |
|Sökvägstyp     | Typ av objekt som ska spåras, möjliga värden är Arkiv och Katalog.        |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Använda Sudo     | Den här inställningen styr om sudo ska användas vid sökningen efter objektet.         |
|Länkar     | Den här inställningen styr hur symboliska länkar ska hanteras när de passerar kataloger.<br> **Ignorera** - Ignorerar symboliska länkar och inkluderar inte de filer/kataloger som refereras.<br>**Följ** - Följer de symboliska länkarna under rekursion och innehåller även de filer/kataloger som refereras.<br>**Hantera** – Följer de symboliska länkarna och tillåter ändring av returnerat innehåll.     |
|Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: **True** eller **False**.|

> [!NOTE]
> Länkalternativet ”Hantera” rekommenderas inte. Hämtning av filinnehåll stöds inte.

## <a name="search-logs"></a>Sök loggar

Du kan göra olika sökningar mot loggarna för ändringsposter. När sidan Ändra spårning är öppen klickar du på **Logganalys**, så öppnas sidan Loggar. I följande tabell visas exempelloggsökningar efter ändringsposter som samlats in av den här lösningen:

|Söka i data  |Beskrivning  |
|---------|---------|
|ConfigurationData (KonfigurationData)<br>&#124; där ConfigDataType == "WindowsServices" och SvcStartupType == "Auto"<br>&#124; där SvcState == "Stoppad"<br>&#124; sammanfatta arg_max(TimeGenerated, *) efter SoftwareName, Computer         | Visar de senaste lagerposterna för Windows Services som har angetts till Auto men som rapporterats som stoppad<br>Resultaten är begränsade till den senaste posten för det SoftwareName och Computer      |
|KonfigurationÄndning<br>&#124; där ConfigChangeType == "Programvara" och ChangeCategory == "Removed"<br>&#124; order efter TimeGenerated desc|Visar ändringsposterna för borttaget program|

## <a name="alert-on-changes"></a>Avisering om ändringar

En viktig funktion för ändringsspårning varnar om konfigurationstillståndet och eventuella ändringar i konfigurationstillståndet för hybridmiljön. I följande exempel visas att filen **C:\windows\system32\drivers\etc\hosts** har ändrats på en dator. Den här filen är viktig eftersom den används för att matcha värdnamn till IP-adresser. Den här åtgärden har företräde framför DNS och kan resultera i anslutningsproblem eller omdirigering av trafik till skadliga eller på annat sätt farliga webbplatser.

![Ett diagram som visar namnändringen av värdfilerna](./media/change-tracking/changes.png)

Om du vill analysera den här ändringen ytterligare går du till Loggsökning från att klicka på **Logganalys**. En gång i Loggsökning, sök efter innehållsändringar i Hosts-filen med frågan `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Den här frågan söker efter ändringar som innehöll en ändring av filinnehåll för filer vars fullt kvalificerade sökväg innehåller ordet "värdar". Du kan också be om en viss fil genom att ändra `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`sökvägsdelen till dess fullt kvalificerade formulär (till exempel ).

När frågan har returnerat önskat resultat klickar du på **Ny aviseringsregel** i loggsökningen för att öppna sidan för att skapa aviseringar. Du kan också navigera till den här upplevelsen via **Azure Monitor** i Azure-portalen. 

Kontrollera frågan igen och ändra varningslogiken. I det här fallet vill du att aviseringen ska utlösas om det till och med har upptäckts en ändring på alla datorer i miljön.

![En bild som visar ändringsfrågan för att spåra ändringar i hosts-filen](./media/change-tracking/change-query.png)

När villkorslogiken har angetts tilldelar du åtgärdsgrupper för att utföra åtgärder som svar på den avisering som utlöses. I det här fallet har jag satt upp e-postmeddelanden som ska skickas och en ITSM-biljett som ska skapas.  Många andra användbara åtgärder kan också vidtas, till exempel utlösa en Azure-funktion, Automation runbook, webhook eller Logic App.

![En bild som konfigurerar en åtgärdsgrupp för att avisera ändringen](./media/change-tracking/action-groups.png)

När alla parametrar och logik har ställts in kan vi tillämpa aviseringen på miljön.

### <a name="alert-suggestions"></a>Varningsförslag

När du varnar för ändringar i hosts-filen är ett bra program av aviseringar för ändringsspårning eller lagerdata, finns det många fler scenarier för aviseringar, inklusive de fall som definierats tillsammans med deras exempelfrågor i avsnittet nedan.

|Söka i data  |Beskrivning  |
|---------|---------|
|KonfigurationÄndning <br>&#124; där ConfigChangeType == "Files" och FileSystemPath innehåller\\\\" c: windows system32\\drivers\\"|Användbart för att spåra ändringar i systemkritiska filer|
|KonfigurationÄndning <br>&#124; där FieldsChanged innehåller "FileContentChecksum" och FileSystemPath ==\\\\"c:\\\\windows\\system32 drivers etc hosts"|Användbart för att spåra ändringar av viktiga konfigurationsfiler|
|KonfigurationÄndning <br>&#124; där ConfigChangeType == "WindowsServices" och SvcName innehåller "w3svc" och SvcState == "Stopped"|Användbart för att spåra ändringar av systemkritiska tjänster|
|KonfigurationÄndning <br>&#124; där ConfigChangeType == "Demoner" och SvcName innehåller "ssh" och SvcState != "Running"|Användbart för att spåra ändringar av systemkritiska tjänster|
|KonfigurationÄndning <br>&#124; där ConfigChangeType == "Programvara" och ChangeCategory == "Added"|Användbart för miljöer som behöver låsta programvarukonfigurationer|
|ConfigurationData (KonfigurationData) <br>&#124; där SoftwareName innehåller "Monitoring Agent" och CurrentVersion != "8.0.11081.0"|Användbart för att se vilka datorer som har en inaktuell eller icke-kompatibel programvaruversion installerad. Den rapporterar det senast rapporterade konfigurationstillståndet, inte ändringar.|
|KonfigurationÄndning <br>&#124; där RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\\\Microsoft\\Windows\\CurrentVersion QualityCompat"| Användbart för att spåra ändringar av viktiga antivirusnycklar|
|KonfigurationÄndning <br>&#124; där RegistryKey innehåller\\@"HKEY_LOCAL_MACHINE SYSTEM\\CurrentControlSet\\\\Services\\SharedAccess Parameters\\FirewallPolicy"| Användbart för att spåra ändringar av brandväggsinställningar|

## <a name="next-steps"></a>Nästa steg

Besök självstudien om ändringsspårning om du vill veta mer om hur du använder lösningen:

> [!div class="nextstepaction"]
> [Felsöka ändringar i miljön](automation-tutorial-troubleshoot-changes.md)

* Använd [loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md) för att visa detaljerade ändringsspårningsdata.

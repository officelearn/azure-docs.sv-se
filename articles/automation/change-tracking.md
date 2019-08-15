---
title: Spåra ändringar med Azure Automation
description: Ändringsspårning-lösningen hjälper dig att identifiera ändringar i program vara och Windows-tjänster som inträffar i din miljö.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a681daa60503ff08320b25155e201ca0e7a4a001
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68953002"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Spåra ändringar i din miljö med Ändringsspårning-lösningen

Den här artikeln hjälper dig att använda Ändringsspårning lösning för att enkelt identifiera ändringar i din miljö. Lösningen spårar ändringar i Windows-och Linux-programvara, Windows-och Linux-filer, Windows-registernycklar, Windows-tjänster och Linux-daemon. Att identifiera konfigurations ändringar kan hjälpa dig att hitta drifts problem.

Ändringar av installerad program vara, Windows-tjänster, Windows-register och filer och Linux-daemon på de övervakade servrarna skickas till Azure Monitor tjänsten i molnet för bearbetning. Logiken tillämpas på den mottagna data och Molntjänsten innehåller data. Genom att använda informationen på Ändringsspårning instrument panelen kan du enkelt se de ändringar som har gjorts i Server infrastrukturen.

> [!NOTE]
> Azure Automation Ändringsspårning spårar ändringar i virtuella datorer. Information om hur du spårar Azure Resource Manager egenskaps ändringar finns i Azure Resource graphs [ändrings historik](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="supported-windows-operating-systems"></a>Windows-operativsystem som stöds

Följande versioner av Windows-operativsystemet stöds officiellt för Windows-agenten:

* Windows Server 2008 R2 eller senare

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Följande Linux-distributioner stöds officiellt. Men Linux-agenten kan också köras på andra distributioner som inte visas i listan. Om inget annat anges stöds alla mindre versioner för varje huvud version som anges.

### <a name="64-bit"></a>64-bitars

* CentOS 6 och 7
* Amazon Linux 2017,09
* Oracle Linux 6 och 7
* Red Hat Enterprise Linux Server 6 och 7
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS och 18,04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32-bitars

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14,04 LTS och 16,04 LTS

## <a name="onboard"></a>Aktivera Ändringsspårning och inventering

Om du vill börja spåra ändringar måste du aktivera Ändringsspårning-och inventerings lösningen. Det finns många sätt att publicera datorer till Ändringsspårning och inventering. Följande är de rekommenderade och de sätt som stöds för att publicera lösningen.

* [Från en virtuell dator](automation-onboard-solutions-from-vm.md)
* [Från att bläddra flera datorer](automation-onboard-solutions-from-browse.md)
* [Från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md)
* [Med en Azure Automation Runbook](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurera Ändringsspårning och inventering

Information om hur du kan publicera datorer till lösningen finns i: [Onboarding av Automation-lösningar](automation-onboard-solutions-from-automation-account.md). När du har en dator som du registrerar med Ändringsspårning-och inventerings lösningen kan du konfigurera de objekt som ska spåras. När du aktiverar en ny fil eller register nyckel som ska spåras, aktive ras den för både Ändringsspårning och inventering.

När du spårar ändringar i filer på både Windows och Linux används MD5-hashar av filerna. De här-hashar används sedan för att identifiera om en ändring har gjorts sedan den senaste inventeringen.

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Övervakning av fil integritet i Azure Security Center

Azure Security Center har lagt till FIM (File Integrity Monitoring) som bygger på Azure Ändringsspårning. Även om FIM övervakar filer och register, innehåller den fullständiga Ändringsspårning lösningen även:

- Program varu ändringar
- Windows-tjänster
- Linux-bakgrundsprogram

Om du redan har aktiverat FIM och vill testa den fullständiga Ändringsspårning lösningen måste du utföra följande steg. Inställningarna tas inte bort av den här processen.

> [!NOTE]
> Att aktivera den fullständiga Ändringsspårning-lösningen kan orsaka ytterligare avgifter, mer information finns i avsnittet om [automatiserings priser](https://azure.microsoft.com/en-us/pricing/details/automation/).

1. Ta bort övervaknings lösningen genom att gå till arbets ytan och hitta den i [listan över installerade övervaknings lösningar](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Klicka på namnet på lösningen för att öppna sammanfattnings sidan och klicka sedan på ta bort, enligt beskrivningen i [ta bort en övervaknings lösning](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Återaktivera lösningen genom att gå till Automation-kontot och välja **ändringsspårning** på resurs-menyn under **konfigurations hantering**.
4. Bekräfta inställningarna för arbets ytans inställning och klicka på **Aktivera**.

### <a name="configure-linux-files-to-track"></a>Konfigurera Linux-filer att spåra

Använd följande steg för att konfigurera fil spårning på Linux-datorer:

1. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
2. På sidan **ändringsspårning** väljer du **Linux-filer**och klickar sedan på **+ Lägg** till för att lägga till en ny fil som ska spåras.
3. I **Lägg till Linux-fil för ändringsspårning**anger du informationen för filen eller katalogen som ska spåras och klickar på **Spara**.

|Egenskap  |Beskrivning  |
|---------|---------|
|Aktiverad     | Anger om inställningen tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett grupp namn för logisk gruppering av filer.        |
|Ange sökväg     | Sökvägen för att söka efter filen. Exempel: "/etc/*. conf"       |
|Typ av sökväg     | Typ av objekt som ska spåras, möjliga värden är fil och katalog.        |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Använda Sudo     | Den här inställningen styr om sudo ska användas vid sökningen efter objektet.         |
|Länkar     | Den här inställningen styr hur symboliska länkar ska hanteras när de passerar kataloger.<br> **Ignore** -ignorerar symboliska länkar och inkluderar inte de filer/kataloger som refereras.<br>**Följ** – följer de symboliska länkarna under rekursion och inkluderar även de filer/kataloger som refereras till.<br>**Hantera** – följer de symboliska länkarna och tillåter ändring av returnerat innehåll.     |
|Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: **Sant** eller **Falskt**.|

> [!NOTE]
> Länkalternativet ”Hantera” rekommenderas inte. Hämtning av filinnehåll stöds inte.

### <a name="configure-windows-files-to-track"></a>Konfigurera Windows-filer att spåra

Använd följande steg för att konfigurera fil spårning på Windows-datorer:

1. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
2. På sidan **ändringsspårning** väljer du **Windows-filer**och klickar sedan på **+ Lägg** till för att lägga till en ny fil som ska spåras.
3. I **Lägg till Windows-fil för ändringsspårning**anger du informationen för filen som ska spåras och klickar på **Spara**.

|Egenskap  |Beskrivning  |
|---------|---------|
|Aktiverad     | Anger om inställningen tillämpas.        |
|Objektnamn     | Eget namn på filen som ska spåras.        |
|Grupp     | Ett grupp namn för logisk gruppering av filer.        |
|Ange sökväg     | Sökvägen för att söka efter filen Till exempel: "c:\temp\\\*.txt"<br>Du kan också använda miljövariabler som "%winDir%\System32\\\*.*"       |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: **Sant** eller **Falskt**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Inställningar för jokertecken, rekursion och miljö

Med rekursion kan du ange jokertecken för att förenkla spårning över kataloger och miljövariabler så att du kan spåra filer i miljöer med flera eller dynamiska enhets namn. I följande lista visas allmän information som du bör känna till när du konfigurerar rekursion:

* Jokertecken krävs för att spåra flera filer
* Om jokertecken används kan de bara användas i det sista segmentet i en sökväg. (till exempel `c:\folder\*file*` eller `/etc/*.conf`)
* Om en miljö variabel har en ogiltig sökväg kommer verifieringen att lyckas, men den sökvägen kommer att Miss lyckas När inventeringen körs.
* Undvik allmänna sökvägar som `c:\*.*` när du anger sökvägen, eftersom detta skulle leda till att för många mappar passerade.

## <a name="configure-file-content-tracking"></a>Konfigurera spårning av fil innehåll

Du kan visa innehållet innan och efter en ändring av en fil med fil innehåll Ändringsspårning. Detta är tillgängligt för Windows-och Linux-filer, för varje ändring av filen, lagras filens innehåll i ett lagrings konto och filen visas före och efter en ändring, infogad eller sida vid sida. Mer information finns i [Visa innehållet i en spårad fil](change-tracking-file-contents.md).

![Visa ändringar i en fil](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurera Windows-registernycklar att spåra

Använd följande steg för att konfigurera register nyckel spårning på Windows-datorer:

1. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
2. På sidan **ändringsspårning** väljer du **Windows-register**och klickar sedan på **+ Lägg** till för att lägga till en ny register nyckel som ska spåras.
3. I **Lägg till Windows-registret för ändringsspårning**anger du informationen för nyckeln som ska spåras och klickar på **Spara**.

|Egenskap  |Beskrivning  |
|---------|---------|
|Aktiverad     | Anger om inställningen tillämpas.        |
|Objektnamn     | Eget namn på register nyckeln som ska spåras.        |
|Grupp     | Ett grupp namn för register nycklar för logisk gruppering.        |
|Windows-registernyckel   | Sökvägen för att kontrol lera register nyckeln. Exempel: ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

## <a name="limitations"></a>Begränsningar

Ändringsspårning-lösningen stöder för närvarande inte följande objekt:

* Rekursion för spårning av Windows-register
* Nätverks fil system

Andra begränsningar:

* Kolumnen **maximal fil storlek** och värden används inte i den aktuella implementeringen.
* Om du samlar in fler än 2500 filer i insamlings cykeln på 30 minuter kan lösningens prestanda försämras.
* När nätverks trafiken är hög kan det ta upp till sex timmar att ändra poster.
* Om du ändrar konfigurationen när en dator stängs av kan datorn Publicera ändringar som tillhörde den tidigare konfigurationen.

## <a name="known-issues"></a>Kända problem

Följande fel har uppstått i Ändringsspårning-lösningen:

* Uppdateringar av snabb korrigeringar samlas inte in på Windows Server 2016 Core RS3-datorer.
* Linux-Daemonar kan visa ett ändrat tillstånd även om det inte har ändrats. Detta beror på hur `SvcRunLevels` fältet fångas.

## <a name="change-tracking-data-collection-details"></a>Information om Ändringsspårning data insamling

I följande tabell visas data insamlings frekvensen för olika typer av ändringar. För varje typ uppdateras även data ögonblicks bilden av det aktuella läget minst var 24: e timme:

| **Ändra typ** | **Frekvens** |
| --- | --- |
| Windows-register | 50 minuter |
| Windows-fil | 30 minuter |
| Linux-fil | 15 minuter |
| Windows-tjänster | 10 sekunder till 30 minuter</br> Standard: 30 minuter |
| Linux-daemon | 5 minuter |
| Windows-programvara | 30 minuter |
| Linux-programvara | 5 minuter |

I följande tabell visas gränserna för spårade objekt per dator för Ändringsspårning.

| **Resurs** | **Gränsen**| **Anteckningar** |
|---|---|---|
|Fil|500||
|Register|250||
|Windows-programvara|250|Innehåller inte program uppdateringar|
|Linux-paket|1250||
|Tjänster|250||
|Daemon|250||

Genomsnittlig Log Analytics data användning för en dator som använder Ändringsspårning och inventering är cirka 40MB per månad. Det här värdet är bara en uppskattning och kan komma att ändras baserat på din miljö. Vi rekommenderar att du övervakar din miljö för att se den exakta användningen som du har.

### <a name="windows-service-tracking"></a>Spårning av Windows-tjänst

Standard frekvensen för insamling av Windows-tjänster är 30 minuter. Om du vill konfigurera frekvensen går du till **ändringsspårning**. Under **Redigera inställningar** på fliken **Windows-tjänster** finns det ett skjutreglage som gör att du kan ändra samlings frekvensen för Windows-tjänster från så snabbt som 10 sekunder till så länge som 30 minuter. Flytta skjutreglaget till den frekvens som du vill ha och spara den automatiskt.

![Skjutreglage för Windows Services](./media/change-tracking/windowservices.png)

Agenten spårar bara ändringar, vilket optimerar agentens prestanda. Att ange ett högt tröskelvärde kan missa ändringar om tjänsten återställs till sitt ursprungliga tillstånd. Genom att ange frekvensen till ett mindre värde kan du fånga in ändringar som annars kan missas.

> [!NOTE]
> Även om agenten kan spåra ändringar nedåt till ett 10 sekunders intervall, tar det fortfarande några minuter innan data visas i portalen. Ändringar under tiden som visas i portalen spåras och loggas fortfarande.

### <a name="registry-key-change-tracking"></a>Ändrings spårning av register nyckel

Syftet med att övervaka ändringar i register nycklar är att hitta utöknings punkter där kod från tredje part och skadlig kod kan aktive ras. I följande lista visas en lista över förkonfigurerade register nycklar. Dessa nycklar har kon figurer ATS men inte Aktiver ATS. Om du vill spåra register nycklarna måste du aktivera var och en.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**\_Lokala\_HKEY-MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar vanliga Autostart-poster som kopplas direkt till Utforskaren i Windows och körs vanligt vis med Explorer. exe.    |
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group-Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar skript som körs vid start.     |
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group-Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar skript som körs vid avstängning.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar nycklar som läses in innan användaren loggar in på sitt Windows-konto. Nyckeln används för 32-bitars program som körs på 64-bitars datorer.    |
> |**HKEY\_lokala\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed-komponenter**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar ändringar i program inställningarna.     |
> |**\_Lokala\_HKEY-MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar vanliga Autostart-poster som kopplas direkt till Utforskaren i Windows och körs vanligt vis med Explorer. exe.|
> |**\_Lokala\_HKEY-MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar vanliga Autostart-poster som kopplas direkt till Utforskaren i Windows och körs vanligt vis med Explorer. exe.|
> |**\_Lokala\_HKEY-MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för registrering av ikon överläggs hanterare.|
|**\_Lokala\_HKEY-MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakare för ikon överläggs hanterare registrering för 32-bitars program som körs på 64-bitars datorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper-objekt**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar nya plugin-program för webb läsar tillägg för Internet Explorer. Används för att få åtkomst till Document Object Model (DOM) för den aktuella sidan och för att kontrol lera navigeringen.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper-objekt**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar nya plugin-program för webb läsar tillägg för Internet Explorer. Används för att få åtkomst till Document Object Model (DOM) för den aktuella sidan och för att kontrol lera navigering för 32-bitars program som körs på 64-bitars datorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Internet-Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar nya Internet Explorer-tillägg, till exempel anpassade verktygs menyer och anpassade verktygsfälts knappar.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Internet-Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar nya Internet Explorer-tillägg, till exempel anpassade verktygs menyer och anpassade verktygsfälts knappar för 32-bitars program som körs på 64-bitars datorer.|
> |**HKEY\_lokala\_MACHINE\Software\Microsoft\Windows-NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar de 32-bitars driv rutiner som är kopplade till wavemapper, wave1 och Wave2, MSACM. imaadpcm,. msadpcm,. msgsm610 och vidc. Liknar avsnittet [driv rutiner] i systemet. INI-fil.|
> |**HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows-NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar de 32-bitars driv rutiner som är associerade med wavemapper, wave1 och Wave2, MSACM. imaadpcm,. msadpcm,. msgsm610 och vidc för 32-bitars program som körs på 64-bitars datorer. Liknar avsnittet [driv rutiner] i systemet. INI-fil.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar listan över kända eller ofta använda system-DLL-filer. Det här systemet förhindrar att personer utnyttjar svaga program katalog behörigheter genom att släppa i trojanska hästar versioner av system-DLL: er.|
> |**HKEY\_lokala\_MACHINE\SOFTWARE\Microsoft\Windows-NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Övervakar listan över paket som kan ta emot händelse meddelanden från Winlogon, den interaktiva inloggnings support modellen för Windows-operativsystemet.|

## <a name="network-requirements"></a>Nätverkskrav

Följande adresser krävs specifikt för Ändringsspårning. Kommunikationen med de här adresserna görs via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Använd Ändringsspårning

När lösningen har Aktiver ATS kan du visa sammanfattningen av ändringarna för dina övervakade datorer genom att välja **ändringsspårning** under **konfigurations hantering** i ditt Automation-konto.

Du kan visa ändringar på datorerna och sedan granska informationen för varje händelse. Nedrullningsbara List rutor är tillgängliga överst i diagrammet för att begränsa diagrammet och detaljerad information baserat på ändrings typ och tidsintervall. Du kan också klicka och dra i diagrammet för att välja ett anpassat tidsintervall. **Ändra typ** är ett av följande värden: **händelser**, **daemonar**, **filer**, **register**, **program vara**, **Windows-tjänster**. Kategori visar vilken typ av ändring och som kan **läggas till**, **ändras**eller **tas bort**.

![bild av Ändringsspårning instrument panel](./media/change-tracking/change-tracking-dash01.png)

Om du klickar på en ändring eller händelse får du detaljerad information om ändringen. Som du kan se från exemplet ändrades start typen för tjänsten från manuell till automatisk.

![bild av information om ändrings spårning](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Sök i loggar

Förutom den information som finns i portalen kan du göra sökningar mot loggarna. När sidan **ändringsspårning** är öppen klickar du på **Log Analytics**. Då öppnas sidan **loggar** .

### <a name="sample-queries"></a>Exempelfrågor

Följande tabell innehåller exempel på loggs ökningar för ändrings poster som samlas in av den här lösningen:

|Söka i data  |Beskrivning  |
|---------|---------|
|ConfigurationData<br>&#124;där ConfigDataType = = "WindowsServices" och SvcStartupType = = "Auto"<br>&#124;där SvcState = = "stoppad"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Visar de senaste inventerings posterna för Windows-tjänster som har ställts in på auto men som har rapporter ATS som stoppade<br>Resultaten är begränsade till den senaste posten för den SoftwareName och datorn      |
|ConfigurationChange<br>&#124;där ConfigChangeType = = "Software" och ChangeCategory = = "borttaget"<br>&#124;Sortera efter TimeGenerated DESC|Visar ändrings poster för borttagen program vara|

## <a name="alert-on-changes"></a>Varning vid ändringar

En viktig funktion i Ändringsspårning och inventering är möjligheten att meddela konfigurations tillstånd och eventuella ändringar i konfigurations läget för din hybrid miljö.

I följande exempel visar skärm bilden att filen `C:\windows\system32\drivers\etc\hosts` har ändrats på en dator. Den här filen är viktig eftersom hosts-filen används av Windows för att matcha värdnamn till IP-adresser och har företräde framför DNS, vilket kan leda till anslutnings problem eller omdirigering av trafik till skadliga eller på annat sätt farliga webbplatser.

![Ett diagram som visar fil ändringen för hosts](./media/change-tracking/changes.png)

Om du vill analysera ändringen ytterligare går du till loggs ökningen genom att klicka på **Log Analytics**. När du har loggat in söker du efter innehålls ändringar till värd filen med frågan `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Den här frågan söker efter ändringar som inkluderar en ändring av fil innehåll för filer vars fullständiga sökväg innehåller ordet "hosts". Du kan också fråga efter en viss fil genom att ändra Sök vägs delen till dess fullständigt kvalificerade form (till `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`exempel).

När frågan returnerar önskade resultat klickar du på knappen **ny varnings regel** i loggs ökningen för att öppna sidan Skapa avisering. Du kan också navigera till den här upplevelsen genom **Azure Monitor** i Azure Portal. I upplevelsen för att skapa aviseringar, kontrollerar du frågan igen och ändrar aviserings logiken. I det här fallet vill du att aviseringen ska utlösas om det inte finns någon ändring som upptäcks på alla datorer i miljön.

![En bild som visar ändrings frågan för spårning av ändringar i värd filen](./media/change-tracking/change-query.png)

När villkors logiken har angetts tilldelar du åtgärds grupper för att utföra åtgärder som svar på aviseringen utlöses. I det här fallet har jag konfigurerat e-postmeddelanden som ska skickas och en ITSM-biljett skapas.  Många andra användbara åtgärder kan också vidtas, till exempel för att utlösa en Azure Function-, Automation-Runbook, webhook eller Logic app.

![En bild som konfigurerar en åtgärds grupp för varning vid ändringen](./media/change-tracking/action-groups.png)

När du har angett alla parametrar och logik kan vi tillämpa aviseringen på miljön.

### <a name="alert-suggestions"></a>Aviserings förslag

Aviseringar om ändringar i värd filen är en lämplig tillämpning av aviseringar för Ändringsspårning eller inventerings data, det finns många fler scenarier för aviseringar, inklusive de fall som definierats tillsammans med deras exempel frågor i avsnittet nedan.

|Söka i data  |Beskrivning  |
|---------|---------|
|ConfigurationChange <br>&#124;där ConfigChangeType = = "Files" och FileSystemPath innehåller "c:\\Windows\\system32\\-\\drivrutiner"|Användbart för att spåra ändringar i system kritiska filer|
|ConfigurationChange <br>&#124;där FieldsChanged innehåller "FileContentChecksum" och FileSystemPath = = "c:\\Windows\\system32\\-\\drivrutiner\\osv"|Användbart för att spåra ändringar i konfigurationsfiler för nycklar|
|ConfigurationChange <br>&#124;där ConfigChangeType = = "WindowsServices" och SvcName innehåller "W3SVC" och SvcState = = "stoppad"|Användbart för att spåra ändringar i system kritiska tjänster|
|ConfigurationChange <br>&#124;där ConfigChangeType = = "daemons" och SvcName innehåller "SSH" och SvcState! = "kör"|Användbart för att spåra ändringar i system kritiska tjänster|
|ConfigurationChange <br>&#124;där ConfigChangeType = = "Software" och ChangeCategory = = "Added"|Användbart för miljöer som behöver låsta program varu konfigurationer|
|ConfigurationData <br>&#124;där SoftwareName innehåller "övervaknings agent" och CurrentVersion! = "8.0.11081.0"|Användbart för att se vilka datorer som har en inaktuell eller icke-kompatibel program varu version installerad. Den rapporterar det senaste rapporterade konfigurations läget, inte ändringar.|
|ConfigurationChange <br>&#124;där RegistryKey = = "HKEY_LOCAL_MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Användbart för att spåra ändringar i viktiga Antivirus nycklar|
|ConfigurationChange <br>&#124;där RegistryKey innehåller "HKEY_LOCAL_MACHINE\\system\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Användbart för att spåra ändringar i brand Väggs inställningar|

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder lösningen finns i kursen om Ändringsspårning:

> [!div class="nextstepaction"]
> [Felsöka ändringar i din miljö](automation-tutorial-troubleshoot-changes.md)

* Använd [loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md) om du vill visa detaljerade ändrings spårnings data.

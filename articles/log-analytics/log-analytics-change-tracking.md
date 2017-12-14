---
title: "Spåra ändringar med Azure Log Analytics | Microsoft Docs"
description: "Ändringsspårning lösningen i logganalys hjälper dig att identifiera program- och Windows-tjänst-ändringar som sker i din miljö."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81cc7f78ef777e02b195422a81d9a9f15cb63564
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>Spåra ändringar av programvaran i din miljö med lösningen för ändringsspårning

![Ändra spårning symbol](./media/log-analytics-change-tracking/change-tracking-symbol.png)

Den här artikeln kan du använda lösningen ändringsspårning i logganalys lätt kan identifiera ändringar i din miljö. Lösningen spårar ändringar i Windows-och Linux, Windows-filer och registernycklar, Windows-tjänster och Linux-daemon. Identifierar konfigurationsändringar kan hjälpa dig hitta operativa problem.

Du installerar lösningen för att uppdatera typ av agenten som är installerad. Ändringar av installerad programvara, Windows-tjänster och Linux-Daemon på de övervakade servrarna är skrivskyddade. Informationen skickas sedan till Log Analytics-tjänst i molnet för bearbetning. Molntjänsten innehåller data att logik tillämpas för mottagna data. Med hjälp av informationen på instrumentpanelen för spårning av ändringar, kan du enkelt se ändringar som gjorts i serverinfrastrukturen.

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

* Du måste ha en [Windows](log-analytics-windows-agent.md), [Operations Manager](log-analytics-om-agents.md), eller [Linux](log-analytics-linux-agents.md) agenten på varje dator där du vill övervaka ändringar.
* Lägg till ändringsspårning lösningen till OMS-arbetsyta från den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview). Du kan också lägga till lösningen med hjälp av informationen i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). Det krävs ingen ytterligare konfiguration.

### <a name="configure-linux-files-to-track"></a>Konfigurera Linux-filer för att spåra
Använd följande steg för att konfigurera filer att spåra på Linux-datorer.

1. I OMS-portalen klickar du på **inställningar** (kugghjulet symbol).
2. På den **inställningar** klickar du på **Data**, och klicka sedan på **Linux File Tracking**.
3. Under Linux filen ändringsspårning, skriver du hela sökvägen och filnamnet för filen som du vill spåra och klicka sedan på den **Lägg till** symbolen. Till exempel: ”/etc/*.conf”
4. Klicka på **Spara**.  

> [!NOTE]
> Linux-filen spårning har ytterligare funktioner, inklusive directory spårning, rekursion via kataloger och jokertecken spårning.

### <a name="configure-windows-files-to-track"></a>Konfigurera Windows-filer för att spåra
Använd följande steg för att konfigurera filer att spåra på Windows-datorer.

1. I OMS-portalen klickar du på **inställningar** (kugghjulet symbol).
2. På den **inställningar** klickar du på **Data**, och klicka sedan på **Windows File Tracking**.
3. Under Windows-filen ändringsspårning, skriver du hela sökvägen och filnamnet för filen som du vill spåra och klicka sedan på den **Lägg till** symbolen. Till exempel: C:\Program Files (x86) \Internet Explorer\iexplore.exe eller C:\Windows\System32\drivers\etc\hosts.
4. Klicka på **Spara**.  
   ![Windows-filen för ändringsspårning](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurera Windows registernycklar för att spåra
Använd följande steg för att konfigurera registernycklar för att spåra på Windows-datorer.

1. I OMS-portalen klickar du på **inställningar** (kugghjulet symbol).
2. På den **inställningar** klickar du på **Data**, och klicka sedan på **spårning av Windows-registret**.
3. Under Windows-registret ändringsspårning, skriver du hela nyckeln som du vill spåra och klicka sedan på den **Lägg till** symbolen.
4. Klicka på **Spara**.  
   ![Windows-registret med ändringsspårning](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Förklaring av Linux samling filegenskaper
1. **Typ**
   * **Filen** (rapportera filens metadata - storlek, ändringsdatum, hash, etc.)
   * **Directory** (rapporten directory metadata - storlek, ändringsdatum osv.)
2. **Länkar** (hantera Linux symlink referenser till andra filer eller kataloger)
   * **Ignorera** (ignorera symlinks under rekursion att inte inkludera filer och kataloger som refererar till)
   * **Följ** (följer symlinks under rekursion ska också innehålla filer och kataloger som refererar till)
   * **Hantera** (följer symlinks och ändra behandlingen av returnerade innehåll)

   > [!NOTE]   
   > Alternativet ”hantera” länkar rekommenderas inte. Filen innehållshämtning stöds inte.

3. **Recurse** (Recurse via mappen nivåer och spåra alla filer som uppfyller sökvägen)
4. **Sudo** (aktivera åtkomst till filer eller kataloger som behöver sudo-behörighet)

### <a name="limitations"></a>Begränsningar
Ändringsspårning-lösningen stöder för närvarande inte följande:

* Mappar (kataloger) för spårning av Windows-filen
* Rekursion för Windows-spårning
* Jokertecken för spårning av Windows-filen
* Sökvägsvariabler
* Network file system
* Filinnehåll

Andra begränsningar:

* Den **maximal filstorlek** kolumnen och värdena är inte används i den aktuella implementeringen.
* Om du samlar in mer än 2 500 filer i 30 minuter insamlingscykel kan lösningens prestanda försämras.
* När nätverkstrafiken är hög, kan ändra poster ta upp till maximalt sex timmar att visa.
* Om du ändrar konfigurationen när en dator är avstängd, kan datorn efter filändringar som tillhör den tidigare konfigurationen.

### <a name="known-issues"></a>Kända problem
Ändringsspårning lösningen har för närvarande följande problem:
* Hotfix-uppdateringar har inte samlats in för Windows 10 skapare Update och Windows Server 2016 Core RS3 datorer.

## <a name="change-tracking-data-collection-details"></a>Ändra data collection detaljer
Ändringsspårning samlar in programvaruinventering och Windows-tjänst metadata med hjälp av de agenter som du har aktiverat.

I följande tabell visar metoder för insamling av data och annan information om hur data samlas in för spårning av ändringar.

| Plattform | Styr Agent | Operations Manager-agent | Linux-agent | Azure Storage | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows- och Linux | &#8226; | &#8226; | &#8226; |  |  | &#8226; | 5 minuter till 50 minuter beroende på vilken ändringstyp. Visa tabellen nedan för mer information. |


I följande tabell visas data collection frekvensen för vilka typer av ändringar.

| **ändra typen** | **frekvens** | **Har****agent****skicka skillnaderna när?**  |
| --- | --- | --- |
| Windows-registret | 50 minuter | Nej |
| Windows-filen | 30 minuter | Ja. Om ingen ändring har 24 timmar, skickas en ögonblicksbild. |
| Linux-fil | 15 minuter | Ja. Om ingen ändring har 24 timmar, skickas en ögonblicksbild. |
| Windows-tjänster | 30 minuter | Ja, var 30: e minut när ändringar finns. Var 24: e timme en ögonblicksbild som skickas, oavsett ändringen. Därför ögonblicksbilden skickas även om det finns inga ändringar. |
| Linux-Daemon | 5 minuter | Ja. Om ingen ändring har 24 timmar, skickas en ögonblicksbild. |
| Windows-program | 30 minuter | Ja, var 30: e minut när ändringar finns. Var 24: e timme en ögonblicksbild som skickas, oavsett ändringen. Därför ögonblicksbilden skickas även om det finns inga ändringar. |
| Linux-programmet | 5 minuter | Ja. Om ingen ändring har 24 timmar, skickas en ögonblicksbild. |

### <a name="registry-key-change-tracking"></a>Ändringen av registernyckeln spårning

Logganalys utför Windows-registret övervakning och spårning med lösningen för spårning av ändringar. Syftet med att övervaka ändringar i registernycklar är att hitta punkter där kod från tredje part och skadlig kod kan aktivera. I följande lista visas standardinställningarna registernycklar som spåras av lösningen och varför varje spåras.

- HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - Övervakare skript som körs vid start.
- HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - Övervakare skript som körs vid avstängningen.
- HKEY\_lokala\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - Övervakar nycklar som har lästs in innan användaren loggar in till sina Windows-konto. Nyckeln används för 32-bitars program som körs på 64-bitarsdatorer.
- HKEY\_lokala\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed komponenter
    - Övervakar ändringar på programinställningar.
- HKEY\_lokala\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - Övervakare vanliga autostart transaktioner koppla direkt i Utforskaren och kör vanligtvis i processen med Explorer.exe.
- HKEY\_lokala\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - Övervakare vanliga autostart transaktioner koppla direkt i Utforskaren och kör vanligtvis i processen med Explorer.exe.
- HKEY\_lokala\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - Övervakare vanliga autostart transaktioner koppla direkt i Utforskaren och kör vanligtvis i processen med Explorer.exe.
- HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Övervakare för ikon täcker registrering.
- HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Övervakare för ikon täcker registrering för 32-bitars program som körs på 64-bitarsdatorer.
- HKEY\_lokala\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper objekt
    - Övervakare för nya webbläsare helper objektet plugin-program för Internet Explorer. För att komma åt den modellen DOM (Document Object) för den aktuella sidan och att styra navigeringen.
- HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper objekt
    - Övervakare för nya webbläsare helper objektet plugin-program för Internet Explorer. För att komma åt den modellen DOM (Document Object) för den aktuella sidan och styra navigeringen för 32-bitars program som körs på 64-bitarsdatorer.
- HKEY\_lokala\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - Övervakare för nya tillägg för Internet Explorer, till exempel anpassade verktyget menyer och anpassade knappar.
- HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - Övervakare för nya tillägg för Internet Explorer, till exempel anpassade verktyget menyer och anpassade knappar för 32-bitars program som körs på 64-bitarsdatorer.
- HKEY\_lokala\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Övervakar 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc. Liknar [drivers]-avsnitt i systemet. INI-filen.
- HKEY\_lokala\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Övervakare 32-bitars drivrutiner som är associerade med wavemapper, wave1 och wave2, msacm.imaadpcm, .msadpcm, .msgsm610 och vidc för 32-bitars program som körs på 64-bitarsdatorer. Liknar [drivers]-avsnitt i systemet. INI-filen.
- HKEY\_lokala\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - Övervakar listan över kända eller vanliga system DLL: er; Det här systemet förhindrar att personer utnyttjar katalogbehörigheter svaga program genom att släppa trojansk häst versioner av system-DLL-filer.
- HKEY\_lokala\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - Övervakar listan över paket som kan ta emot meddelanden om händelser från Winlogon modellen interaktiv inloggning stöd för Windows-operativsystemet.


## <a name="use-change-tracking"></a>Ändrar spårning
När lösningen har installerats kan du visa sammanfattning av ändringar för övervakade servrar med hjälp av **ändringsspårning** panelen på den **översikt** sida i OMS.

![Bild av ändringsspårning sida vid sida](./media/log-analytics-change-tracking/change-tracking-tile.png)

Du kan visa ändringar i din infrastruktur och gå till information i följande kategorier:

* Ändringar efter konfigurationstyp för programvara och tjänster för Windows
* Programvaruändringar till program och uppdateringar för enskilda servrar
* Totalt antal ändringar av programvaran för varje program
* Linux-paket
* Windows-tjänsten ändras för enskilda servrar
* Ändringar i Linux-daemon

![Bild av instrumentpanel för ändringsspårning](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![Bild av instrumentpanel för ändringsspårning](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>Om du vill visa ändringar för att ändra typen
1. På den **översikt** klickar du på den **ändringsspårning** panelen.
2. På den **ändra spårning** instrumentpanel, Granska sammanfattningen i ett ändra typen blad och klicka på en om du vill visa detaljerad information om den i den **loggen Sök** sidan.
3. Du kan visa resultaten av tid, detaljerade resultat och Logghistoriken på någon av sidorna loggen sökning. Du kan också filtrera efter aspekter att begränsa resultaten.

## <a name="next-steps"></a>Nästa steg
* Använd [logga sökningar i logganalys](log-analytics-log-searches.md) att visa detaljerade data för ändringsspårning.

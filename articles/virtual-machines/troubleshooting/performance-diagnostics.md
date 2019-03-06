---
title: Prestandadiagnostik för Azure virtual machines | Microsoft Docs
description: Introducerar Azure Prestandadiagnostik för Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: c2089f9f6267f318dafe641a6a5b22e7e87427ca
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441074"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Prestandadiagnostik för virtuella Azure-datorer

Diagnostikverktyget prestanda hjälper dig att felsöka prestandaproblem som kan påverka en Windows virtuell dator (VM). Felsökning scenarier som stöds med snabb kontroll av kända problem och bästa praxis och komplexa problem som rör långsam virtuell dator eller hög användning av CPU, ledigt diskutrymme eller minne. 

Du kan köra Prestandadiagnostik direkt från Azure-portalen där du kan också granska insikter och en rapport på olika loggar, omfattande konfiguration och diagnostikdata. Vi rekommenderar att du kör Prestandadiagnostik och granska de insikter och diagnostik data innan du kontaktar Microsoft Support.

> [!NOTE]
> Prestandadiagnostik stöds för närvarande på Windows virtuella datorer som har .NET SDK version 4.5 eller senare installerad. Anvisningar om hur köra Prestandadiagnostik på klassiska virtuella datorer finns i [Azure prestanda diagnostik VM-tillägg](performance-diagnostics-vm-extension.md).

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Installera och köra Prestandadiagnostik på den virtuella datorn
Prestandadiagnostik installerar ett VM-tillägg som kör en diagnostikverktyget som heter [PerfInsights](https://aka.ms/perfinsights). Följ dessa steg för att installera och köra Prestandadiagnostik:
1.  I den vänstra kolumnen kommandon väljer **virtuella datorer**.
1.  Välj den virtuella dator som du vill köra diagnostik i listan med namn på virtuella datorer.
1.  I den högra kolumnen kommandon väljer **Prestandadiagnostik**.

    ![Skärmbild av Azure portal utan att installera prestanda diagnostik knappen markerad](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > I den här skärmbilden döljs bladet i namn på virtuella datorer.
1. Välj ett lagringskonto (valfritt)

    Om du vill använda ett enda lagringskonto för att lagra Diagnostikresultat prestanda för flera virtuella datorer kan du välja ett lagringskonto genom att klicka på den **inställningar** i verktygsfältet. Klicka på den **OK** knappen när du har valt lagringskontot.

    Om du inte anger ett storage-konto skapas ett nytt lagringskonto som standard.

    ![Skärmbild av prestanda diagnostik bladet med inställningar verktygsfältsknappen markerat](media/performance-diagnostics/settings-button.png)

    ![Skärmbild av val av mållagring kontot från bladet med inställningar för diagnostik prestanda](media/performance-diagnostics/select-storage-account.png)

1. Välj den **installera Prestandadiagnostik** knappen.
1. Välj den **kör diagnostik** markerar du kryssrutan om du vill köra en diagnostik när installationen har slutförts. Om du gör det här alternativet, kommer du att kunna välja dataanalysscenario för prestanda och relaterade alternativ.

    ![Skärmbild av Prestandadiagnostik installera knappen](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Välj en dataanalysscenario ska köras

De följande scenarierna för prestandaanalys är tillgängliga från Azure-portalen. Välj en analys, beroende på prestandaproblem som du har problem. Välj alternativen varaktighet och spårning vid behov för analys.

* **Snabb prestandaanalys**  
    Söker efter kända problem, analyserar metodtips och samlar in diagnostikdata. Den här analysen tar flera minuter att köra. [Läs mer](https://aka.ms/perfinsights/quick)

* **Prestandaanalys**  
    Innehåller alla kontroller i snabb prestandaanalys och övervakar hög resursförbrukning. Använd den här versionen för att felsöka allmänna prestandaproblem som hög CPU, minne och diskanvändning. Den här analysen tar 30 sekunder till 15 minuter, beroende på den valda perioden. [Läs mer](https://aka.ms/perfinsights/vmslow) 
    
* **Avancerade prestandaanalys**  
    Innehåller alla kontroller i prestandaanalys och samlar in en eller flera av spårning, som anges i följande avsnitt. Använd det här scenariot för att felsöka komplexa problem som kräver ytterligare spårningar. Kör det här scenariot under längre perioder ökar den sammanlagda storleken för diagnostik-utdata, beroende på storleken på den virtuella datorn och spårningsalternativ som har valts. Den här analysen tar 30 sekunder till 15 minuter att köra, beroende på den valda perioden. [Läs mer](https://aka.ms/perfinsights/advanced) 
    
* **Azure filer analys**  
    Innehåller alla kontroller i prestandaanalys och samlar in en nätverksspårning och SMB-räknare. Använd det här scenariot för att felsöka prestanda för Azure files. Den här analysen tar 30 sekunder till 15 minuter att köra, beroende på den valda perioden. [Läs mer](https://aka.ms/perfinsights/azurefiles)


![Skärmbild av kör diagnostik-fönstret i prestandabladet för diagnostik](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Ange symptom (valfritt)
Välj symtom förvalda i listan eller Lägg till nya problem. Detta hjälper oss att förbättra analysen i framtiden. 

### <a name="provide-support-request-number-if-available-optional"></a>Ange numret för stöd för begäran om de är tillgängliga (valfritt)
Om du arbetar med en Microsoft-supporttekniker på en befintlig supportärende, anger du numret för supportbegäran. 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Granska den sekretesspolicy och juridiska villkoren och markera kryssrutan för att bekräfta (krävs)
För att köra diagnostiken, måste du godkänner de juridiska villkoren och acceptera sekretesspolicy.

### <a name="select-ok-to-run-the-diagnostics"></a>Välj OK om du vill köra diagnostiken 
Ett meddelande visas som Prestandadiagnostik börjar installera. När installationen är klar visas ett meddelande som anger att installationen har slutförts. Den markerade analysen körs sedan för den angivna tidsperioden. Detta är ett bra tillfälle att återskapa prestandaproblemet så att diagnostics-data samlas in vid rätt tid. 

När analysen är klar, överförs följande objekt till Azure-tabeller och en behållare för stora binära objekt (BLOB) i det angivna lagringskontot:

*   Alla insikter och relaterad information om körningen
*   En utdata komprimerade ZIP-fil (med namnet **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**) som innehåller loggfiler
*   En HTML-rapport

Efter överföringen visas en ny rapport för diagnostik i Azure-portalen.

![Skärmbild av en lista över diagnostik rapporten i prestandabladet för diagnostik](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Så här ändrar du inställningarna för startdiagnostik för prestanda
Använd den **inställningar** knapp i verktygsfältet för att ändra storage-konto där diagnostik insikter och utdata kan lagras. Du kan använda samma lagringskonto för flera virtuella datorer som använder Prestandadiagnostik. När du ändrar lagringskontot, raderas inte den gamla rapporter och insikter. Men visas de inte längre i listan över rapporter för diagnostik. 

## <a name="review-insights-and-performance-diagnostics-report"></a>Granska insikter och diagnostik prestandarapport
Varje kör diagnostik innehåller en lista över insikter och rekommendationer, berörda resurser, loggfiler och andra omfattande diagnostikinformation som samlas in, plus en rapport för visning offline. En fullständig lista över alla diagnostikdata som samlas in finns [vilken typ av information som samlas in av PerfInsights?](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights) 

### <a name="select-a-performance-diagnostics-report"></a>Välj en diagnostik prestandarapport
Du kan använda rapportlistan diagnostik för att hitta alla diagnostik-rapporter som kördes. Listan innehåller information om analysen som användes, insikter som hittades och deras effektnivåer. Välj en rad för att visa mer information.

![Skärmbild över att välja en diagnostik-rapport från prestandabladet för diagnostik](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Granska en diagnostik prestandarapport
Varje prestandarapport för diagnostik kan innehålla flera insikter och ange en inverkan hög, medel eller låg. Varje insight innehåller även rekommendationer för att minska problemet. Insights grupperas för enkel filtrering. 

Impact Level representerar möjliga prestandaproblem, baserat på faktorer som felkonfiguration, kända problem, eller problem som rapporteras av andra användare. Du kan inte ännu råka en eller flera av de listade problem. Du kanske till exempel SQL-loggfilerna och databasfiler på samma datadisk. Det här villkoret har hög risk för flaskhalsar och andra prestandaproblem om databasanvändningen är hög, medan du inte märker ett problem om användningen är låg.

![Skärmbild av Prestandadiagnostik rapportera översiktsbladet](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Granska prestandainsikter för diagnostik och rekommendationer
Du kan välja en insikt om du vill visa mer information om de berörda resurser och föreslagna åtgärder referenslänkar. 

![Skärmbild av en prestandainformation diagnostik insight](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Ladda ned och granska den fullständiga prestandarapporten för diagnostik
Du kan använda den **ladda ned rapporten** för att ladda ned en HTML-rapport som innehåller ytterligare omfattande diagnostikinformation, t.ex lagring och nätverkskonfiguration, prestandaräknare, spårningar, lista över processer, och loggar. Innehållet är beroende av den markerade analysen. Rapporten kan innehålla ytterligare information och interaktiva diagram som är relaterade till hög CPU-användning, hög diskanvändning och processer som förbrukar mycket minne för avancerad felsökning. Läs mer om diagnostik prestandarapporten [granskningsrapport diagnostik](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Hantera prestanda diagnostik rapporter
Du kan ta bort en eller flera prestanda diagnostik rapporter med hjälp av den **ta bort rapporten** knappen.

## <a name="how-to-uninstall-performance-diagnostics"></a>Så här avinstallerar du Prestandadiagnostik
Du kan avinstallera Prestandadiagnostik från en virtuell dator. Den här åtgärden tar bort VM-tillägget, men påverkar inte alla diagnostikdata som finns i lagringskontot. 

![Skärmbild av verktygsfältet prestanda diagnostik bladet med knappen Avinstallera markerat](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Var finns diagnostics-data från den virtuella datorn lagras? 
Alla prestandainsikter för diagnostik och rapporter lagras i ditt eget lagringskonto. Insikter är lagrade i Azure-tabeller. Den komprimerade filen i rapporter lagras i ett binärt stort objekt (BLOB)-behållare med namnet azdiagextnresults.

Du kan visa informationen om lagringsutrymme med hjälp av knappen Inställningar i verktygsfältet. 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Hur gör jag för att dela dessa data med Microsofts kundsupport? 
Det finns flera sätt att dela diagnostik-rapport med Microsoft.

**Alternativ 1:** Automatiskt dela den senaste rapporten  
När du öppnar ett supportärende med Microsoft, är det viktigt att dela diagnostik prestandarapporten. Om du har valt för att dela den här informationen med Microsoft medan du kör diagnostiken (genom att välja den ”**jag samtycker till att dela diagnostikinformation med Microsoft**” kryssrutan), Microsoft kommer att kunna komma åt rapporten från din lagring konto med hjälp av en SAS-länk till zip-filen för utdata i upp till 30 dagar från datumet som kör. Endast den senaste rapporten är tillgänglig för supportteknikern. 

**Alternativ 2:** Generera en signatur för delad åtkomst för den komprimerade filen för diagnostik-rapport  
Du kan dela en länk till den komprimerade filen i rapporter med hjälp av signaturer för delad åtkomst. Det gör du genom att följa dessa steg: 
1.  Bläddra till det lagringskonto där diagnostics-data lagras i Azure-portalen.
1.  Välj **Blobar** under den **Blobtjänst** avsnittet. 
1.  Välj den **azdiagextnresults** behållare.
1.  Välj prestanda diagnostik komprimerade utdatafilen som du vill dela.
1.  På den **generera SAS** , Välj kriterier för att dela. 
1.  Klicka på **generera blob SAS-token och URL: en**.
1.  Kopiera den **Blob SAS-Webbadressen**, och dela dem med supportteknikern. 

**Alternativ 3:** Ladda ned rapporten från storage-kontot

Du kan även hitta den komprimerade filen för prestanda diagnostik rapporten med hjälp av steg 1 – 4 i alternativ 2. Välj det här alternativet om du vill ladda ned filen, och sedan dela den via e-post eller be supportteknikern anvisningar att ladda upp filen.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Hur jag för att samla in diagnostikdata vid rätt tid?
Varje Prestandadiagnostik kör har två steg: 
1.  Installera eller uppdatera Prestandadiagnostik VM-tillägget.
1.  Kör diagnostik för den angivna tidsperioden.

Det finns för närvarande inget enkelt sätt att veta exakt när installationen av VM-tillägget är klar. Det tar vanligtvis ungefär 45 sekunder till 1 minut att installera VM-tillägg. När VM-tillägget har installerats kan köra du dina reproduktionssteg för Prestandadiagnostik avbilda rätt uppsättning data för felsökning. 

## <a name="next-steps"></a>Nästa steg
När du granskar prestandainsikter för diagnostik och rapporten om du fortfarande inte kan ta reda på orsaken till problemet och behöver mer hjälp kan öppna du ett supportärende med Microsofts kundsupport. 

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/), och välj **få support**. Information om hur du använder Azure-support finns på [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

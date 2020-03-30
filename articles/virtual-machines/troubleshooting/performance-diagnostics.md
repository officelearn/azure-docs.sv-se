---
title: Prestandadiagnostik för virtuella Azure-datorer| Microsoft-dokument
description: Introducerar Azure Performance Diagnostics för Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 16be3d1695608165405a3490b686a01ba6a2a62c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70080603"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Prestandadiagnostik för virtuella Azure-datorer

Verktyget för prestandadiagnostik hjälper dig att felsöka prestandaproblem som kan påverka en virtuell Dator (Windows eller Linux virtual machine). Felsökningsscenarier som stöds omfattar snabba kontroller av kända problem och metodtips och komplexa problem som innebär långsam vm-prestanda eller hög användning av CPU, diskutrymme eller minne.

Du kan köra prestandadiagnostik direkt från Azure-portalen, där du också kan granska insikter och en rapport om olika loggar, omfattande konfiguration och diagnostikdata. Vi rekommenderar att du kör prestandadiagnostik och granskar insikter och diagnostikdata innan du kontaktar Microsoft Support.

> [!NOTE]
> För Windows stöds prestandadiagnostik för närvarande på virtuella datorer som har .NET SDK version 4.5 eller en senare version installerad. Steg för att köra prestandadiagnostik på klassiska virtuella datorer finns i [Azure Performance Diagnostics VM-tillägg](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Operativsystem som stöds

### <a name="windows"></a>Windows

Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

### <a name="linux"></a>Linux

Oracle Linux Server 6,10 [`*`], 7,3, 7,6, 7.5 (Oracle-Database-Ee 13.8`*`marketplace image), CentOS 6.5 [ ], 7.6, RHEL`*`7.2, 7.5, 8.0 [ ], Ubuntu 14.04, 16.04, 18.04, Debian 8, 9, 10 [`*`], SLES 12 SP4 [`*`]

>[!Note]
>[`*`] Se [kända frågor](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Installera och köra prestandadiagnostik på den virtuella datorn

Prestandadiagnostik installerar ett VM-tillägg som kör ett diagnostikverktyg som heter PerfInsights. PerfInsights är tillgängligt för både [Windows](https://aka.ms/perfinsights) och [Linux](https://aka.ms/perfinsightslinux). Så här installerar och kör du prestandadiagnostik:

1. I den vänstra kolumnen med kommandon väljer du **Virtuella datorer**.
1. Välj den virtuella dator som du vill köra diagnostik på i listan över VM-namn.
1. I den högra kolumnen med kommandon väljer du **Prestandadiagnostik**.

    ![Skärmbild av Azure-portalen, med knappen Installera prestandadiagnostik markerad](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > I den här skärmbilden döljs bladet med VM-namn.
1. Välj ett lagringskonto (valfritt)

    Om du vill använda ett enda lagringskonto för att lagra prestandadiagnostikresultaten för flera virtuella datorer kan du välja ett lagringskonto genom att klicka på knappen **Inställningar** i verktygsfältet. Klicka **på** OK när du har valt lagringskontot.

    Om du inte anger ett lagringskonto skapas ett nytt lagringskonto som standard.

    ![Skärmbild av bladet Prestandadiagnostik, med knappen Verktygsfältsknapp Inställningar markerad](media/performance-diagnostics/settings-button.png)

    ![Skärmbild av val av lagringskonto från prestandadiagnostik inställning blad](media/performance-diagnostics/select-storage-account.png)

1. Välj knappen **Installera prestandadiagnostik.**
1. Markera kryssrutan **Kör diagnostik** om du vill köra en diagnostik när installationen är klar. Om du gör det här valet kan du välja scenario för prestandaanalys och relaterade alternativ.

    ![Skärmbild av installationsknappen För prestandadiagnostik](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Välj ett analysscenario som ska köras

Följande analysscenarier är tillgängliga från Azure-portalen. Välj en analys, beroende på prestandaproblemet som du har. Välj varaktighets- och spårningsalternativen efter behov för analysen.

* **Snabb prestandaanalys**  
    Söker efter kända problem, analyserar metodtips och samlar in diagnostikdata. Den här analysen tar flera minuter att köra. Läs mer om [Windows](https://aka.ms/perfinsights/quick) eller [Linux](https://aka.ms/perfinsightslinux/quick)

* **Resultatanalys**  
    Inkluderar alla kontroller i snabbprestandaanalysen och övervakar hög resursförbrukning. Använd den här versionen för att felsöka allmänna prestandaproblem, till exempel hög processor, minne och diskanvändning. Denna analys tar 30 sekunder till 15 minuter, beroende på den valda varaktigheten. Läs mer om [Windows](https://aka.ms/perfinsights/vmslow) eller [Linux](https://aka.ms/perfinsightslinux/vmslow)

* **Avancerad prestandaanalys**`*`  
    Inkluderar alla kontroller i prestandaanalysen och samlar in en eller flera av spårningarna, enligt följande avsnitt. Använd det här scenariot om du vill felsöka komplexa problem som kräver ytterligare spårningar. Om du kör det här scenariot under längre perioder ökar den totala storleken på diagnostikutdata, beroende på storleken på den virtuella datorn och de spårningsalternativ som har valts. Den här analysen tar 30 sekunder till 15 minuter att köra, beroende på den valda varaktigheten. [Läs mer](https://aka.ms/perfinsights/advanced)

* **Analys av Azure-filer**`*`  
    Inkluderar alla kontroller i prestandaanalysen och samlar in en nätverksspårning och SMB-räknare. Använd det här scenariot för att felsöka prestanda för Azure-filer. Den här analysen tar 30 sekunder till 15 minuter att köra, beroende på den valda varaktigheten. [Läs mer](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[`*`] Dessa analysscenarier stöds bara i Windows.

![Skärmbild av fönstret Kör diagnostik i prestandadiagnostikbladet](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Ge symptom (valfritt)

Välj eventuella förvalda symptom i listan eller lägg till nya symptom. Detta hjälper oss att förbättra analysen i framtiden.

### <a name="provide-support-request-number-if-available-optional"></a>Ange nummer för supportbegäran, om tillgängligt (valfritt)

Om du arbetar med en Microsoft-supporttekniker på en befintlig supportbiljett anger du supportbiljettnumret.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Granska sekretesspolicyn och juridiska termer, och markera kryssrutan för att bekräfta (krävs)

För att köra diagnostiken måste du godkänna de juridiska villkoren och acceptera sekretesspolicyn.

### <a name="select-ok-to-run-the-diagnostics"></a>Välj OK för att köra diagnostiken

Ett meddelande visas när prestandadiagnostik börjar installeras. När installationen är klar visas ett meddelande som anger att installationen lyckas. Den valda analysen körs sedan för den angivna varaktigheten. Detta skulle vara ett bra tillfälle att återskapa prestandaproblemet så att diagnostikdata kan fångas in vid rätt tidpunkt.

När analysen är klar överförs följande objekt till Azure-tabeller och en BLOB-behållare (Binary Large Object) i det angivna lagringskontot:

* Alla insikter och relaterad information om körningen
* En utdata komprimerad (.zip) fil (med namnet **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip** ) på Windows och en tar ball-fil (med namnet **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.tar.gz** ) på Linux som innehåller loggfiler
* En HTML-rapport

Efter överföringen visas en ny diagnostikrapport i Azure-portalen.

![Skärmbild av en lista över diagnostikrapport i prestandadiagnostikbladet](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Så här ändrar du inställningar för prestandadiagnostik

Använd knappen **Verktygsfältet Inställningar** för att ändra lagringskontot där diagnostikstatistiken och utdata kan lagras. Du kan använda samma lagringskonto för flera virtuella datorer som använder prestandadiagnostik. När du ändrar lagringskontot tas inte de gamla rapporterna och insikterna bort. De visas dock inte längre i listan över diagnostikrapporter.

## <a name="review-insights-and-performance-diagnostics-report"></a>Granska rapporten insikter och prestandadiagnostik

Varje diagnostikkörning innehåller en lista med insikter och rekommendationer, berörda resurser, loggfiler och annan omfattande diagnostikinformation som samlas in, plus en rapport för offlinevisning. En fullständig lista över alla insamlade diagnostikdata finns i Vilken typ av information som [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) samlas [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights) **in av PerfInsights?**

### <a name="select-a-performance-diagnostics-report"></a>Välj en prestandadiagnostikrapport

Du kan använda diagnostikrapportlistan för att hitta alla diagnostikrapporter som kördes. Listan innehåller information om analysen som användes, insikter som hittades och deras effektnivåer. Välj en rad om du vill visa mer information.

![Skärmbild av att välja en diagnostikrapport från prestandadiagnostikblad](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Granska en prestandadiagnostikrapport

Varje rapport om prestandadiagnostik kan innehålla flera insikter och ange en effektnivå för hög, medel eller låg. Varje insikt innehåller också rekommendationer för att minska oron. Insikter grupperas för enkel filtrering.

Påverkansnivåer representerar risken för prestandaproblem, baserat på faktorer som felkonfiguration, kända problem eller problem som rapporteras av andra användare. Du kanske ännu inte har haft ett eller flera av de angivna problemen. Du kan till exempel ha SQL-loggfiler och databasfiler på samma datadisk. Det här villkoret har stor potential för flaskhalsar och andra prestandaproblem om databasanvändningen är hög, medan du kanske inte märker ett problem om användningen är låg.

![Skärmbild av rapportöversiktsbladet för prestandadiagnostik](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Granska insikter och rekommendationer för prestandadiagnostik

Du kan välja en insikt om du vill visa mer information om de berörda resurserna, föreslagna begränsningsåtgärder och referenslänkar.

![Skärmbild av en insiktsinformation om prestandadiagnostik](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Ladda ned och granska rapporten fullständig prestandadiagnostik

Du kan använda knappen **Hämta rapport** för att hämta en HTML-rapport som innehåller ytterligare omfattande diagnostikinformation, till exempel lagrings- och nätverkskonfiguration, prestandaräknare, spårningar, en lista över processer och loggar. Innehållet beror på den valda analysen. För avancerad felsökning kan rapporten innehålla ytterligare information och interaktiva diagram som är relaterade till hög CPU-användning, hög diskanvändning och processer som förbrukar onödigt minne. Mer information om prestandadiagnostikrapporten finns i [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) eller [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Hantera prestandadiagnostikrapporter

Du kan ta bort en eller flera prestandadiagnostikrapporter med hjälp av knappen **Ta bort rapport.**

## <a name="how-to-uninstall-performance-diagnostics"></a>Avinstallera prestandadiagnostik

Du kan avinstallera prestandadiagnostik från en virtuell dator. Den här åtgärden tar bort vm-tillägget men påverkar inte diagnostikdata som finns i lagringskontot.

![Skärmbild av verktygsfältet Prestandadiagnostik med knappen Avinstallera markerad](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Var lagras diagnostikdata från min virtuella dator

Alla insikter och rapporter för prestandadiagnostik lagras i ditt eget lagringskonto. Insikter lagras i Azure-tabeller. Den komprimerade filen lagras i en BLOB-behållare (Binary Large Object) som heter azdiagextnresults.

Du kan visa lagringskontoinformationen med knappen Inställningar i verktygsfältet.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Hur delar jag dessa data med Microsofts kundtjänst

Det finns flera sätt att dela diagnostikrapporten med Microsoft.

**Alternativ 1:** Dela automatiskt den senaste rapporten  
När du öppnar en supportbiljett med Microsoft är det viktigt att du delar rapporten prestandadiagnostik. Om du har valt att dela den här informationen med Microsoft medan du kör diagnostiken (genom att markera kryssrutan "**Jag godkänner att dela diagnostikinformation med Microsoft**" kan Microsoft komma åt rapporten från ditt lagringskonto med hjälp av en SAS-länk till zip-filen för utdata i upp till 30 dagar från körningsdatumet. Endast den senaste rapporten är tillgänglig för supportteknikern.

**Alternativ 2:** Generera en signatur för delad åtkomst för den komprimerade diagnostikrapporten  
Du kan dela en länk till den komprimerade filen med delad åtkomstsignaturer. Det gör du genom att följa dessa steg:

1. I Azure-portalen bläddrar du till lagringskontot där diagnostikdata lagras.
1. Välj **Blobbar** under avsnittet **Blob-tjänst.**
1. Välj **behållaren azdiagextnresults.**
1. Välj den komprimerade fil för prestandadiagnostik som du vill dela.
1. Välj villkoren för delning på fliken **Generera SAS.**
1. Klicka på **Generera SAS-token och URL för blob.**
1. Kopiera **Blob SAS-URL:en**och dela den med supportteknikern.

**Alternativ 3:** Hämta rapporten från lagringskontot

Du kan också hitta den komprimerade rapporten för prestandadiagnostik med hjälp av steg 1–4 i alternativ 2. Välj om du vill hämta filen och dela den sedan via e-post eller be supportteknikern om instruktioner om hur du laddar upp filen.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Hur samlar jag in diagnostikdata vid rätt tidpunkt

Varje prestandadiagnostikkörning har två steg:

1. Installera eller uppdatera vm-tillägget för prestandadiagnostik.
1. Kör diagnostiken för den angivna varaktigheten.

För närvarande finns det inget enkelt sätt att veta exakt när vm-tilläggsinstallationen är klar. Generellt tar det ungefär 45 sekunder till 1 minut att installera VM-tillägget. När vm-tillägget har installerats kan du köra dina repro-steg för att få prestandadiagnostiken att samla in rätt uppsättning data för felsökning.

## <a name="next-steps"></a>Nästa steg

När du har granskat insikterna och rapporten för prestandadiagnostik kan du öppna en supportbiljett med Microsoft Kundsupport om du fortfarande inte kan fastställa orsaken till problemet och behöver mer hjälp.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/)och välj **Hämta support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

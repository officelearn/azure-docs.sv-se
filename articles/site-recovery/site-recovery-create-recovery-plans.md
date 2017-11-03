---
title: "Skapa återställningsplaner för redundans och återställning i Azure Site Recovery | Microsoft Docs"
description: "Beskriver hur du skapar och anpassa återställningsplaner i Azure Site Recovery växla över och återställa virtuella datorer och fysiska servrar"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: raynew
ms.openlocfilehash: 202e0ac8be36e9156ec16fadc1b722f4eb3d1432
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
---
# <a name="create-recovery-plans"></a>Skapa återställningsplaner


Den här artikeln innehåller information om att skapa och anpassa återställningsplaner i [Azure Site Recovery](site-recovery-overview.md).

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Skapa återställningsplaner för att göra följande:

* Definiera grupper av datorer som redundansväxlar tillsammans och sedan starta tillsammans.
* Modellen beroenden mellan datorer genom att gruppera dem tillsammans i en återställning planera grupp. Till exempel för att växla över och få fram ett visst program, gruppera du alla virtuella datorer för programmet i samma grupp för återställning-plan.
* Kör en redundansväxling. Du kan köra en test, planerad eller oplanerad växling vid fel på en återställningsplan.


## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan

1. Klicka på **Återställningsplaner** > **skapa återställningsplan**.
   Ange ett namn för återställningsplanen, och en källa och mål. Källplatsen måste ha virtuella datorer som är aktiverade för redundans och återställning.

    - VMM till VMM-replikering, Välj **källtypen** > **VMM**, och de käll- och VMM-servrarna. Klicka på **Hyper-V** att se moln som skyddas.
    - VMM till Azure, Välj **källtypen** > **VMM**.  Välj VMM-källservern och **Azure** som mål.
    - Hyper-V-replikering till Azure (utan VMM), Välj **källtypen** > **Hyper-V-platsen**. Välj platsen som källa och **Azure** som mål.
    - Välj en server configuration som källa för en VMware-VM eller en fysisk lokal server till Azure och **Azure** som mål.
    - Välj en Azure-region som käll- och en sekundär Azure-region som mål för en återställningsplan med Azure till Azure. Sekundär Azure-regioner är endast de som virtuella datorer är skyddade.
2. I **Välj virtuella datorer**, Välj virtuella datorer (eller replikeringsgrupp) som du vill lägga till i standardgruppen (grupp 1) i återställningsplanen.

## <a name="customize-and-extend-recovery-plans"></a>Anpassa och utöka återställningsplaner

Du kan anpassa och utöka återställningsplaner:

- **Lägga till nya grupper**– lägga till ytterligare återställningspunkter planeringsgrupper (upp till sju) i standardgruppen och sedan lägga till flera datorer eller replikgrupper grupperna recovery plan. Grupper är numrerade i den ordning i vilken du lägger till dem. En virtuell dator eller en replikeringsgrupp kan bara ingå i en återställning plan gruppen.
- **Lägg till en manuell åtgärd**– du kan lägga till manuella åtgärder som körs före eller efter en återställning plan grupp. När återställningsplanen körs, stoppas på den plats där du har infogat åtgärden manuellt. En dialogruta där uppmanas du att ange att den manuella åtgärden har slutförts.
- **Lägga till ett skript**– du kan lägga till skript som körs före eller efter en återställning plan grupp. När du lägger till ett skript lägger den till en ny uppsättning åtgärder för gruppen. Till exempel en uppsättning före steg för grupp 1 kommer att skapas med namnet: grupp 1: innan steg. Alla före steg visas i den här uppsättningen. Du kan bara lägga till ett skript på den primära platsen om du har en VMM-server distribueras.
- **Lägg till Azure-runbooks**– du kan utöka återställningsplaner med Azure-runbooks. Till exempel att automatisera uppgifter eller skapa enda steg återställning. [Läs mer](site-recovery-runbook-automation.md).

## <a name="add-scripts"></a>Lägga till skript

Du kan använda PowerShell-skript i din återställningsplaner.

 - Kontrollera att skript använder trycatch-block så att undantagen hanteras korrekt.
    - Om det finns ett undantag i skriptet, avbryts körningen och uppgiften visar som misslyckades.
    - Alla återstående delen av skriptet körs inte om ett fel inträffar.
    - Om det uppstår ett fel när du kör en oplanerad redundans, fortsätter återställningsplanen.
    - Om ett fel uppstår när du kör en planerad redundans, stoppar återställningsplanen. Du behöver åtgärda skript, kontrollera att den körs som förväntat och kör återställningen planera igen.
- Kommandot Write-Host fungerar inte i en återställningsplanskriptet och skriptet misslyckas. Skapa en proxyskript som körs i sin tur huvudsakliga skriptet för att skapa utdata. Kontrollera att alla utdata skickas ut med den >> kommando.
  * Skriptet timeout om det inte returnera inom 600 sekunder.
  * Om något skrivs till STDERR, klassificeras skriptet som misslyckad. Den här informationen visas i skript för körning av information.

Om du använder VMM i distributionen:

* Skript i en återställningsplan körs i kontexten för VMM-tjänstkontot. Kontrollera att det här kontot har läsbehörighet för fjärresursen där skriptet finns. Testa skriptet ska köras på VMM-kontot behörighet tjänstnivå.
* VMM-cmdlets levereras i ett Windows PowerShell-modulen. Modulen är installerad när du installerar VMM-konsolen. Det går att läsa in i ditt skript med hjälp av följande kommando i skriptet:
   - Import-Module-Name virtualmachinemanager. [Läs mer](https://technet.microsoft.com/library/hh875013.aspx).
* Se till att du har minst en biblioteksserver i VMM-distributionen. Sökvägen till Biblioteksresursen för en VMM-server finns lokalt på VMM-servern med namnet på mappen MSCVMMLibrary som standard.
    * Om din biblioteksresurssökväg är fjärransluten (eller lokala men inte delas med MSCVMMLibrary), konfigurera resursen på följande sätt (med hjälp av \\libserver2.contoso.com\share\ som exempel):
      * Öppna Registereditorn och navigera till **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure plats Recovery\Registration**.
      * Redigera värdet **ScriptLibraryPath** och placera den som \\libserver2.contoso.com\share\. Ange fullständig FQDN. Ge behörigheter till resursens plats. Observera att detta är rotnoden för resursen. **Du kan kontrollera detta bläddrar du biblioteket på root-noden i VMM. Den sökväg som öppnar blir roten till sökvägen, av du behöver använda i variabeln**.
      * Se till att du testar skriptet med ett konto som har samma behörigheter som VMM-tjänstkontot. Kontrollerar detta som fristående testas skript körs på samma sätt som i återställningsplaner. Ange körningsprincip för att kringgå på följande sätt på VMM-servern:
        * Öppna den **64-bitars Windows PowerShell** konsol med utökade privilegier.
        * Typ: **Set-executionpolicy bypass**. [Läs mer](https://technet.microsoft.com/library/ee176961.aspx).

> [!IMPORTANT]
> Du bör ange körningsprincipen att Bypass på i 64-bitars powershell. Om du har angett det för 32-bitars powershell, kommer skript inte exeute.

## <a name="add-a-script-or-manual-action-to-a-plan"></a>Lägg till ett skript eller en manuell åtgärd till en plan

Du kan lägga till ett skript plan standardgruppen återställning när du har lagt till virtuella datorer eller replikeringsgrupper till den och skapa planen.

1. Öppna återställningsplanen.
2. Klicka på ett objekt i den **steg** listan och klicka sedan på **skriptet** eller **manuell åtgärd**.
3. Ange om du vill lägga till det skript eller åtgärder före eller efter det valda objektet. Använd den **Flytta upp** och **Flytta ned** knappar för att flytta positionen för skriptet uppåt eller nedåt.
4. Om du lägger till ett skript i VMM, väljer **redundans till VMM-skript**. I **skriptsökvägen**, skriver du den relativa sökvägen till resursen. I VMM exemplet nedan, anger du sökvägen: **\RPScripts\RPScript.PS1**.
5. Om du lägger till en Azure automation kör book ange Azure Automation-konto där runbook finns och välj lämplig Azure runbook-skriptet.
6. Gör en redundansväxling i återställningsplanen och kontrollera att skriptet fungerar som förväntat.


### <a name="add-a-vmm-script"></a>Lägg till en VMM-skript

Om du har en VMM-källplats kan du skapa ett skript på VMM-servern och inkludera den i din återställningsplan.

1. Skapa en ny mapp i Biblioteksresursen. Till exempel \<VMMServerName > \MSSCVMMLibrary\RPScripts. Placera den på käll- och mål-VMM-servrar.
2. Skapa skript (till exempel RPScript) och kontrollera att den fungerar som förväntat.
3. Placera skriptet på plats \<VMMServerName > \MSSCVMMLibrary på käll- och VMM-servrarna.


## <a name="next-steps"></a>Nästa steg

[Lär dig mer](site-recovery-failover.md) om att köra redundansväxlingar.

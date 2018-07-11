---
title: Lägga till ett skript i en återställningsplan i Azure Site Recovery | Microsoft Docs
description: Läs mer om kraven för att lägga till ett nytt System Center Virtual Machine Manager (VMM)-skript i en återställningsplan i Azure.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: rochakm
editor: ''
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 71991347ffaf036065aae9e1a93b7eb83a14b15c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917356"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Lägg till ett VMM-skript i en återställningsplan

Den här artikeln beskrivs hur du skapar ett skript i System Center Virtual Machine Manager (VMM) och lägga till den i en återställningsplan i [Azure Site Recovery](site-recovery-overview.md).

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Förutsättningar

Du kan använda PowerShell-skript i dina återställningsplaner. För att vara tillgängliga från återställningsplanen, måste du redigera skriptet och placera skriptet i VMM-biblioteket. Ha följande i åtanke när du skriver skriptet:

* Kontrollera att skript använder trycatch-block, så att undantag hanteras på ett smidigt sätt.
    - Om ett undantag inträffar i skriptet skriptet har körts och uppgiften visar som misslyckades.
    - Resten av skriptet körs inte om ett fel inträffar.
    - Om ett felmeddelande när du kör en oplanerad redundans, fortsätter återställningsplanen.
    - Om ett fel inträffar när du kör en planerad redundans, stoppar återställningsplanen. Åtgärda de skript, kontrollera att den körs som förväntat och kör sedan återställningsplanen igen.
        - Den `Write-Host` kommandot inte fungerar i ett recovery plan-skript. Om du använder den `Write-Host` kommandot i ett skript skriptet misslyckas. Skapa en proxyskript som körs i sin tur huvudsakliga skriptet för att skapa utdata. För att säkerställa att alla utdata skickas ut, använda den **\> \>** kommando.
        - Skriptet sin tidsgräns om den inte returnera inom 600 sekunder.
        - Om något skrivs till STDERR klassificeras skriptet som misslyckad. Den här informationen visas i de information om skriptkörning.

* Skript i en återställningsplan körs i kontexten för VMM-tjänstkontot. Se till att det här kontot har läsbehörighet för fjärresursen där skriptet finns. Testa skriptet ska köras med samma nivå av användarrättigheter som VMM-tjänstkontot.
* VMM-cmdlets levereras i en Windows PowerShell-modulen. Modulen är installerad när du installerar VMM-konsolen. Om du vill läsa in modulen i skriptet, använder du följande kommando i skriptet: 

    `Import-Module -Name virtualmachinemanager`

    Mer information finns i [Kom igång med Windows PowerShell och VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Se till att du har minst en biblioteksserver i VMM-distribution. Som standard är sökvägen till Biblioteksresursen för en VMM-server finns lokalt på VMM-servern. Mappnamnet är MSCVMMLibrary.

  Om din biblioteksresurssökväg är remote (eller om den är lokal utan inte delas med MSCVMMLibrary), konfigurera filresursen på följande sätt med hjälp av \\libserver2.contoso.com\share\ som exempel:
  
  1. Öppna Registereditorn och gå sedan till **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure plats Recovery\Registration**.

  2. Ändra värdet för **ScriptLibraryPath** till  **\\\libserver2.contoso.com\share\\**. Ange fullständig FQDN. Ge behörighet till plats där. Det här är rotnoden i resursen. Om du vill söka efter rotnoden i VMM, går du till rotnoden i biblioteket. Sökvägen som öppnas är roten till sökvägen. Det här är den sökväg som du måste använda i variabeln.

  3. Testa skriptet genom att använda ett konto som har samma nivå av användarrättigheter som VMM-tjänstkontot. Med hjälp av dessa användarrättigheter verifierar som fristående testade skript som körs på samma sätt som de körs i återställningsplaner. På VMM-servern ställer du in körningsprincipen att kringgå på följande sätt:

     a. Öppna den **64-bitars Windows PowerShell** konsolen som administratör.
     
     b. Ange **Set-executionpolicy kringgå**. Mer information finns i [med hjälp av cmdleten Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Ange **Set-executionpolicy kringgå** endast i 64-bitars PowerShell-konsolen. Om du ställer in det för 32-bitars PowerShell-konsolen kör inte skripten.

## <a name="add-the-script-to-the-vmm-library"></a>Lägg till skript till VMM-biblioteket

Om du har en VMM-källplats kan skapa du ett skript på VMM-servern. Ta sedan skriptet i din plan.

1. Skapa en ny mapp i Biblioteksresursen. Till exempel \<VMM-servernamn > \MSSCVMMLibrary\RPScripts. Placera mappen på käll- och målservrar för VMM.
2. Skapa skriptet. Till exempel kalla skriptet RPScript. Kontrollera att skriptet fungerar som förväntat.
3. Placera skriptet i den \<VMM-servernamn > \MSSCVMMLibrary mapp på de käll- och VMM-servrarna.

## <a name="add-the-script-to-a-recovery-plan"></a>Lägg till skriptet i en återställningsplan

När du har lagt till virtuella datorer eller replikeringsgrupper i en återställningsplan och skapat planen kan du lägga till skriptet i gruppen.

1. Öppna återställningsplanen.
2. I den **steg** väljer du ett objekt. Välj antingen **skriptet** eller **manuell åtgärd**.
3. Ange om du vill lägga till skript eller åtgärder före eller efter det valda objektet. Om du vill flytta positionen för skriptet uppåt eller nedåt, Välj den **Flytta upp** och **Flytta ned** knappar.
4. Om du lägger till en VMM-skript, väljer **redundans till VMM skript**. I **skriptets sökväg**, ange den relativa sökvägen till resursen. Ange till exempel **\RPScripts\RPScript.PS1**.
5. Om du lägger till en Azure Automation-runbook kan du ange Automation-konto där runbook finns. Välj den Azure-runbookskript som du vill använda.
6. För att säkerställa att skriptet fungerar som förväntat, gör du ett redundanstest av återställningsplanen.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [köra redundansväxlingar](site-recovery-failover.md).


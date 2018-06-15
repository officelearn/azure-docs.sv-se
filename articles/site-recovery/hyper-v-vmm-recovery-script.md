---
title: Lägga till ett skript i en återställningsplan i Azure Site Recovery | Microsoft Docs
description: Lär dig mer om kraven för att lägga till ett nytt skript i System Center Virtual Machine Manager (VMM) till en återställningsplan i Azure.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: shons
editor: ''
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 2e00f812fb35ac9a0cb390fc6a3ba40a8678f8dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29402435"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Lägga till en VMM-skript till en återställningsplan

Den här artikeln beskriver hur du skapar ett skript för System Center Virtual Machine Manager (VMM) och lägga till den i en återställningsplan i [Azure Site Recovery](site-recovery-overview.md).

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Förutsättningar

Du kan använda PowerShell-skript i din återställningsplaner. För att vara tillgänglig från återställningsplanen, måste du redigera skriptet och placerar skriptet i VMM-biblioteket. Tänk på följande när du skriver skriptet:

* Kontrollera att skript använder trycatch-block, så att undantag hanteras korrekt.
    - Om ett undantag inträffar i skriptet skriptet har körts och uppgiften visar som misslyckades.
    - Resten av skriptet körs inte om ett fel inträffar.
    - Om det uppstår ett fel när du kör en oplanerad redundans, fortsätter återställningsplanen.
    - Om ett fel uppstår när du kör en planerad redundans, stoppar återställningsplanen. Åtgärda skriptet, kontrollera att den körs som förväntat och kör återställningen planera igen.
        - Den `Write-Host` kommandot fungerar inte i en återställningsplanskriptet. Om du använder den `Write-Host` kommandot i ett skript, skriptet misslyckas. Skapa en proxyskript som körs i sin tur huvudsakliga skriptet för att skapa utdata. Använd för att säkerställa att alla utdata skickas ut den  **\> \>**  kommando.
        - Skriptet timeout om det inte returnera inom 600 sekunder.
        - Om något skrivs till STDERR klassificeras skriptet som misslyckad. Den här informationen visas i skript för körning av information.

* Skript i en återställningsplan körs i kontexten för VMM-tjänstkontot. Se till att det här kontot har behörighet för fjärresursen där skriptet finns. Testa skriptet ska köras med samma nivå av användarrättigheter som VMM-tjänstkontot.
* VMM-cmdlets levereras i ett Windows PowerShell-modulen. Modulen är installerad när du installerar VMM-konsolen. Om du vill läsa in modulen i skriptet använder du följande kommando i skriptet: 

    `Import-Module -Name virtualmachinemanager`

    Mer information finns i [Kom igång med Windows PowerShell och VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Se till att du har minst en biblioteksserver i VMM-distributionen. Som standard är sökvägen till Biblioteksresursen för en VMM-server finns lokalt på VMM-servern. Mappnamnet är MSCVMMLibrary.

  Om din biblioteksresurssökväg är fjärransluten (eller om den är lokal utan inte delas med MSCVMMLibrary), konfigurera resursen på följande sätt med hjälp av \\libserver2.contoso.com\share\ som exempel:
  
  1. Öppna Registereditorn och gå sedan till **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure plats Recovery\Registration**.

  2. Ändra värdet för **ScriptLibraryPath** till  **\\\libserver2.contoso.com\share\\**. Ange fullständig FQDN. Ge behörigheter till resursens plats. Detta är rotnoden för resursen. Gå till rotnoden i biblioteket för att kontrollera root-noden i VMM. Sökvägen som öppnas är roten till sökvägen. Det här är den sökväg som du måste använda i variabeln.

  3. Testa skriptet genom att använda ett konto som har samma användarrättigheter som VMM-tjänstkontot. Med hjälp av de här rättigheterna verifierar som fristående testade skript som körs på samma sätt som de körs i återställningsplaner. Ange körningsprincip för att kringgå på följande sätt på VMM-servern:

     a. Öppna den **64-bitars Windows PowerShell** konsolen som administratör.
     
     b. Ange **Set-executionpolicy bypass**. Mer information finns i [med cmdlet Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Ange **Set-executionpolicy bypass** endast i 64-bitars PowerShell-konsolen. Om du ställer in den för 32-bitars PowerShell-konsolen kör inte skript.

## <a name="add-the-script-to-the-vmm-library"></a>Lägg till skriptet i VMM-biblioteket

Om du har en VMM-källplats kan skapa du ett skript på VMM-servern. Inkludera skriptet i din återställningsplan.

1. Skapa en ny mapp i en biblioteksresurs. Till exempel \<VMM-servernamn > \MSSCVMMLibrary\RPScripts. Placera mappen på käll- och målservrarna i VMM.
2. Skapa skriptet. Till exempel kalla skriptet RPScript. Kontrollera att skriptet fungerar som förväntat.
3. Placera skriptet i den \<VMM-servernamn > \MSSCVMMLibrary mapp på de käll- och VMM-servrarna.

## <a name="add-the-script-to-a-recovery-plan"></a>Lägg till skriptet i en återställningsplan

När du har lagt till virtuella datorer eller replikeringsgrupper till en återställningsplan och skapat planen kan du lägga till skriptet i gruppen.

1. Öppna återställningsplanen.
2. I den **steg** väljer du ett objekt. Markera antingen **skriptet** eller **manuell åtgärd**.
3. Ange om du vill lägga till det skript eller åtgärder före eller efter det valda objektet. För att flytta positionen för skriptet uppåt eller nedåt, Välj den **Flytta upp** och **Flytta ned** knappar.
4. Om du lägger till ett skript i VMM, väljer **redundans till VMM-skript**. I **skriptsökvägen**, ange den relativa sökvägen till resursen. Ange till exempel **\RPScripts\RPScript.PS1**.
5. Om du lägger till en Azure Automation-runbook ange Automation-konto där runbook finns. Välj det Azure runbook-skript som du vill använda.
6. För att säkerställa att skriptet fungerar som förväntat, gör ett redundanstest för återställningsplanen.


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [köra redundansväxlingar](site-recovery-failover.md).


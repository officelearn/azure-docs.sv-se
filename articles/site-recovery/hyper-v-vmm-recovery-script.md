---
title: Lägga till ett skript i en återställningsplan i Azure Site Recovery
description: Lär dig hur du lägger till ett VMM-skript i en återställningsplan för haveriberedskap av virtuella hyper-virtuella datorer i VMM-moln.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6902876e066649ae4dff4134fb8cc462f30dd0b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084879"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Lägga till ett VMM-skript i en återställningsplan

I den hÃ¤r artikeln beskrivs hur du skapar ett VMM-skript (System Center Virtual Machine Manager) och lägger till det i en återställningsplan i [Azure Site Recovery](site-recovery-overview.md).

Publicera kommentarer eller frågor längst ned i den här artikeln eller på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Krav

Du kan använda PowerShell-skript i dina återställningsplaner. För att vara tillgänglig från återställningsplanen måste du skapa skriptet och placera skriptet i VMM-biblioteket. Tänk på följande när du skriver skriptet:

* Se till att skript använder try-catch-block, så att undantag hanteras smidigt.
    - Om ett undantag inträffar i skriptet slutar skriptet att köras och aktiviteten visas som misslyckad.
    - Om ett fel uppstår körs inte resten av skriptet.
    - Om ett fel uppstår när du kör en oplanerad redundans fortsätter återställningsplanen.
    - Om ett fel uppstår när du kör en planerad redundans stoppas återställningsplanen. Åtgärda skriptet, kontrollera att det körs som förväntat och kör sedan återställningsplanen igen.
        - Kommandot `Write-Host` fungerar inte i ett skript för återställningsplan. Om du `Write-Host` använder kommandot i ett skript misslyckas skriptet. Om du vill skapa utdata skapar du ett proxyskript som i sin tur kör huvudskriptet. Använd ** \> ** kommandot för att säkerställa att alla utdata är utstjedda.
        - Skriptet time out om det inte returneras inom 600 sekunder.
        - Om något skrivs till STDERR klassificeras skriptet som misslyckat. Den här informationen visas i information om skriptkörning.

* Skript i en återställningsplan körs i samband med VMM-tjänstkontot. Kontrollera att det här kontot har läsbehörighet för fjärrresursen som skriptet finns på. Testa skriptet för att köras med samma användarrättigheter som VMM-tjänstkontot.
* VMM-cmdlets levereras i en Windows PowerShell-modul. Modulen installeras när du installerar VMM-konsolen. Om du vill läsa in modulen i skriptet använder du följande kommando i skriptet: 

    `Import-Module -Name virtualmachinemanager`

    Mer information finns i [Komma igång med Windows PowerShell och VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Kontrollera att du har minst en biblioteksserver i VMM-distributionen. Som standard finns sökvägen till biblioteksresurs för en VMM-server lokalt på VMM-servern. Mappnamnet är MSCVMMLibrary.

  Om sökvägen till biblioteksresursen är fjärråtkomst (eller om den är lokal men inte delad \\med MSCVMMLibrary) konfigurerar du resursen på följande sätt med libserver2.contoso.com\share\ som exempel:
  
  1. Öppna Registereditorn och gå sedan till **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Ändra värdet för **ScriptLibraryPath** till ** \\\libserver2.contoso.com\share\\**. Ange hela FQDN. Ge behörighet till resursplatsen. Det här är resursens rotnod. Om du vill söka efter rotnoden går du till rotnoden i biblioteket i VMM. Banan som öppnas är roten till banan. Det här är sökvägen som du måste använda i variabeln.

  1. Testa skriptet med hjälp av ett användarkonto som har samma användarrättighet som VMM-tjänstkontot. Med hjälp av dessa användarrättigheter verifierar du att fristående, testade skript körs på samma sätt som de körs i återställningsplaner. På VMM-servern anger du att körningsprincipen ska kringgås enligt följande:

     a. Öppna **64-bitars Windows PowerShell-konsolen** som administratör.
     
     b. Ange **förbikoppling av Set-executionpolicy**. Mer information finns i [Använda cmdleten Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Ange **förbikoppling av Set-executionpolicy** endast i 64-bitars PowerShell-konsolen. Om du ställer in den för 32-bitars PowerShell-konsolen körs inte skripten.

## <a name="add-the-script-to-the-vmm-library"></a>Lägga till skriptet i VMM-biblioteket

Om du har en VMM-källplats kan du skapa ett skript på VMM-servern. Inkludera sedan skriptet i återställningsplanen.

1. Skapa en ny mapp i biblioteksresursen. \<VMM-servernamn>\MSSCVMMLibrary\RPScripts. Placera mappen på käll- och mål-VMM-servrarna.
1. Skapa skriptet. Namnge till exempel skriptet RPScript. Kontrollera att skriptet fungerar som förväntat.
1. Placera skriptet \<i VMM-servernamnet>\MSSCVMMLibrary-mappen på käll- och mål-VMM-servrarna.

## <a name="add-the-script-to-a-recovery-plan"></a>Lägga till skriptet i en återställningsplan

När du har lagt till virtuella datorer eller replikeringsgrupper i en återställningsplan och skapat planen kan du lägga till skriptet i gruppen.

1. Öppna återställningsplanen.
1. Markera ett objekt i listan **Steg.** Välj sedan **skript** eller **manuell åtgärd**.
1. Ange om skriptet eller åtgärden ska läggas till före eller efter det markerade objektet. Om du vill flytta skriptets position uppåt eller nedåt väljer du knapparna **Flytta upp** och **Flytta ned.**
1. Om du lägger till ett VMM-skript väljer du **Redundans till VMM-skript**. Ange den relativa sökvägen till resursen i **Skriptsökväg.** Ange till exempel **\RPScripts\RPScript.PS1**.
1. Om du lägger till en Azure Automation-runbook anger du det Automation-konto där runbooken finns. Välj sedan det Azure runbook-skript som du vill använda.
1. Om du vill vara medveten om att skriptet fungerar som förväntat gör du en testväxling av återställningsplanen.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [att köra redundans .](site-recovery-failover.md)


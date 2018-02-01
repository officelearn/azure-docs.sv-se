---
title: "Lägga till skript i återställningsplanen i Azure Site Recovery | Microsoft Docs"
description: "Beskriver förutsättningarna i att lägga till ett nytt skript till en återställningsplan i VMM till Azure"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: shons
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 60c6eebd9323028c63f42bd8a0996e3c0a2a1cf1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="add-vmm-scripts-to-a-recovery-plan"></a>Lägga till VMM-skript till en återställningsplan


Den här artikeln innehåller information om hur du skapar och lägger till VMM-skript för återställningsplaner i [Azure Site Recovery](site-recovery-overview.md).

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites-of-adding-a-script-to-recovery-plan"></a>Förutsättningar för att lägga till ett skript till återställningsplanen

Du kan använda PowerShell-skript i din återställningsplaner. Du måste redigera dem och placera dem i VMM-biblioteket är tillgänglig från återställningsplanen. Nedan ser du några överväganden när du skriver skriptet.

* Kontrollera att skript använder trycatch-block så att undantagen hanteras korrekt.
    - Om det finns ett undantag i skriptet, avbryts körningen och uppgiften visar som misslyckades.
    - Alla återstående delen av skriptet körs inte om ett fel inträffar.
    - Om det uppstår ett fel när du kör en oplanerad redundans, fortsätter återställningsplanen.
    - Om ett fel uppstår när du kör en planerad redundans, stoppar återställningsplanen. Du behöver åtgärda skript, kontrollera att den körs som förväntat och kör återställningen planera igen.
        - Kommandot Write-Host fungerar inte i en återställningsplanskriptet och skriptet misslyckas. Skapa en proxyskript som körs i sin tur huvudsakliga skriptet för att skapa utdata. Kontrollera att alla utdata skickas ut med den >> kommando.
        - Skriptet timeout om det inte returnera inom 600 sekunder.
        - Om något skrivs till STDERR, klassificeras skriptet som misslyckad. Den här informationen visas i skript för körning av information.

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
> Du bör ange körningsprincipen att Bypass på i 64-bitars PowerShell. Om du har angett det för 32-bitars PowerShell, kommer skript inte exeute.

## <a name="add-the-script-to-the-vmm-library"></a>Lägg till skriptet i VMM-biblioteket

Om du har en VMM-källplats kan du skapa ett skript på VMM-servern och inkludera den i din återställningsplan.

1. Skapa en ny mapp i Biblioteksresursen. Till exempel \<VMMServerName > \MSSCVMMLibrary\RPScripts. Placera den på käll- och mål-VMM-servrar.
2. Skapa skript (till exempel RPScript) och kontrollera att den fungerar som förväntat.
3. Placera skriptet på plats \<VMMServerName > \MSSCVMMLibrary på käll- och VMM-servrarna.

## <a name="add-the-script-to-a-recovery-plan"></a>Lägg till skriptet i en återställningsplan

Du kan lägga till skriptet i gruppen när du har lagt till virtuella datorer eller replikeringsgrupper till den och skapa planen.

1. Öppna återställningsplanen.
2. Klicka på ett objekt i den **steg** listan och klicka sedan på **skriptet** eller **manuell åtgärd**.
3. Ange om du vill lägga till det skript eller åtgärder före eller efter det valda objektet. Använd den **Flytta upp** och **Flytta ned** knappar för att flytta positionen för skriptet uppåt eller nedåt.
4. Om du lägger till ett skript i VMM, väljer **redundans till VMM-skript**. I **skriptsökvägen**, skriver du den relativa sökvägen till resursen. I VMM exemplet nedan, anger du sökvägen: **\RPScripts\RPScript.PS1**.
5. Om du lägger till en Azure automation kör book ange Azure Automation-konto där runbook finns och välj lämplig Azure runbook-skriptet.
6. Göra ett redundanstest i återställningsplanen och kontrollera att skriptet fungerar som förväntat.


## <a name="next-steps"></a>Nästa steg

[Lär dig mer](site-recovery-failover.md) om att köra redundansväxlingar.

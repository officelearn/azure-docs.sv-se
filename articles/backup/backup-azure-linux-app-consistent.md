---
title: 'Azure Backup: programkonsekventa säkerhetskopior av virtuella Linux-datorer'
description: Skapa programkonsekventa säkerhetskopior av dina virtuella Linux-datorer till Azure. Den här artikeln förklarar Konfigurera skript-ramverk för att säkerhetskopiera Azure-distribuerade virtuella Linux-datorer. Den här artikeln innehåller också information om felsökning.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: programkonsekvent säkerhetskopia. programkonsekvent säkerhetskopiering av Azure virtuella datorer; Linux VM-säkerhetskopia. Azure Backup
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: a81c0b9c87db85771fcecab87c6b9ac88dcbd472
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581866"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Programkonsekvent säkerhetskopiering av virtuella Linux-datorer

När du tar ögonblicksbilder av säkerhetskopior av dina virtuella datorer innebär programkonsekvens dina program startar när de virtuella datorerna startar när du håller på att återställas. Eftersom du kan föreställa dig är programkonsekvens mycket viktigt. För att säkerställa att är din virtuella Linux-datorer program som är konsekvent, du kan använda Linux-ramverk för förskript och efterskript för att göra programkonsekventa säkerhetskopior. Ramverk för förskript och efterskript har stöd för Azure Resource Manager-distribuerade Linux-datorer. Skript för programkonsekvens har inte stöd för Service Manager-distribuerade virtuella datorer eller Windows-datorer.

## <a name="how-the-framework-works"></a>Så här fungerar ramen

Ramverket innehåller ett alternativ för att köra anpassade skript som före och efter skript när du tar med ögonblicksbilder av Virtuella. Före skript köras precis innan du tar den ögonblicksbild för virtuell dator och efter skript som körs omedelbart när du har tagit ögonblicksbilden av virtuella datorn. Ger flexibilitet för att styra din applikation och miljö, medan du tar med ögonblicksbilder av Virtuella förskript och efterskript.

Förskript och anropa internt program API: er, vilka inaktivera IOs och tömma InMemory-innehåll till disken. De här åtgärderna Kontrollera ögonblicksbilden är program som är konsekvent. Efterskript använda programspecifik API: er låsas upp IOs, vilket gör att programmet kan återställa normal drift när ögonblicksbilden av virtuella datorn.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Steg för att konfigurera förskript och efterskript

1. Logga in som rotanvändare för Linux VM som du vill säkerhetskopiera.

2. Från [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), ladda ned **VMSnapshotScriptPluginConfig.json** och kopiera det till den **/etc/azure** mappen för alla virtuella datorer som du vill säkerhetskopiera. Om den **/etc/azure** mappen inte finns, skapar den.

3. Kopiera förskript och efterskript för ditt program på alla virtuella datorer som du planerar att säkerhetskopiera. Du kan kopiera skripten till valfri plats på den virtuella datorn. Se till att uppdatera den fullständiga sökvägen till skriptfiler i den **VMSnapshotScriptPluginConfig.json** fil.

4. Kontrollera att följande behörigheter för dessa filer:

   - **VMSnapshotScriptPluginConfig.json**: Behörigheten ”600”. Till exempel ”rot”-användare bör ha behörigheterna ”Läsa” och ”skriva” till den här filen och ingen användare bör ha ”kör”-behörighet.

   - **Förskript filen**: Behörigheten ”700”.  Till exempel ”rot”-användare bör ha ”läs”, ”skriva” och ”kör”-behörighet till den här filen.

   - **Efterskript** behörighet ”700”. Till exempel ”rot”-användare bör ha ”läs”, ”skriva” och ”kör”-behörighet till den här filen.

   > [!Important]
   > Ramverket ger användare en stor förmåga. Skydda ramverket och kontrollera ”rot”-användare har åtkomst till viktiga JSON och skriptfiler.
   > Om kraven inte uppfylls körs skriptet inte, vilket resulterar i en systemkrasch på filen och inkonsekvent säkerhetskopiering.
   >

5. Konfigurera **VMSnapshotScriptPluginConfig.json** enligt nedan:
    - **pluginName**: Lämna det här fältet eftersom eller skripten kanske inte fungerar som förväntat.

    - **preScriptLocation**: Ange den fullständiga sökvägen före skriptet på den virtuella datorn som ska säkerhetskopieras.

    - **postScriptLocation**: Ange den fullständiga sökvägen för skriptet efter på den virtuella datorn som ska säkerhetskopieras.

    - **preScriptParams**: Ange valfria parametrar som ska skickas till förskript. Alla parametrar ska vara inom citattecken. Om du använder flera parametrar, avgränsa parametrarna med kommatecken.

    - **postScriptParams**: Ange valfria parametrar som ska skickas till efterskript. Alla parametrar ska vara inom citattecken. Om du använder flera parametrar, avgränsa parametrarna med kommatecken.

    - **preScriptNoOfRetries**: Ange hur många gånger förskript ska göras om det uppstår något fel innan försöket avbryts. Noll innebär att endast en försök och det görs inget nytt om det uppstår ett fel.

    - **postScriptNoOfRetries**:  Ange hur många gånger efter skriptet ska göras om det uppstår något fel innan försöket avbryts. Noll innebär att endast en försök och det görs inget nytt om det uppstår ett fel.

    - **Timeout_sekunder**: Ange enskilda tidsgränser för förskript och efterskript som (högsta värdet kan vara 1800).

    - **continueBackupOnFailure**: Det här värdet till **SANT** om du vill att Azure Backup för att återgå till en konsekvent/krascher filsystemkonsekvent säkerhetskopia om förskript eller misslyckas i efterskript. Du anger detta till **FALSKT** misslyckas säkerhetskopieringen reservlösning skript (utom när du har enskild disk VM som faller tillbaka till kraschkonsekvent säkerhetskopiering oavsett denna inställning).

    - **fsFreezeEnabled**: Ange om Linux fsfreeze ska anropas när du tar med VM-ögonblicksbilden så filsystemkonsekvens. Vi rekommenderar att den här inställningen angiven som **SANT** om inte ditt program är beroende av inaktiverar fsfreeze.

6. Skript-ramverket har nu konfigurerats. Om säkerhetskopiering av virtuella datorer är redan konfigurerad, anropar skripten nästa säkerhetskopiering och utlöser programkonsekvent säkerhetskopiering. Om säkerhetskopiering av virtuella datorer inte är konfigurerad, konfigurera den med hjälp av [säkerhetskopiera virtuella Azure-datorer till Recovery Services-valv.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Felsökning

Kontrollera att du lägger till lämplig loggning vid skrivning till din förskript och efterskript och granska loggarna skript för att åtgärda problemen skript. Om du fortfarande har problem med att köra skript, se tabellen nedan för mer information.

| Fel | Felmeddelande | Rekommenderad åtgärd |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Förskript returnerade ett fel så säkerhetskopiering inte kanske är konsekventa.   | Titta på felloggarna för skriptet för att åtgärda problemet.|  
|   Post-ScriptExecutionFailed |    Efterskript returnerade ett fel som kan påverka programmets status. |    Titta på felloggarna för skriptet för att åtgärda problemet och kontrollera programstatusen. |
| Pre-ScriptNotFound |  Förskript hittades inte på den plats som anges i den **VMSnapshotScriptPluginConfig.json** konfigurationsfilen. |   Se till att det förskript finns vid den sökväg som anges i konfigurationsfilen så att programkonsekvent säkerhetskopiering.|
| Post-ScriptNotFound | Det gick inte att hitta efterskript på plats som anges i den **VMSnapshotScriptPluginConfig.json** konfigurationsfilen. |   Se till att det efterskript finns vid den sökväg som anges i konfigurationsfilen så att programkonsekvent säkerhetskopiering.|
| IncorrectPluginhostFile | Den **Pluginhost** filen som medföljer tillägget VmSnapshotLinux är skadad, så att förskript och efterskript kan inte köras och säkerhetskopian inte konsekventa. | Avinstallera den **VmSnapshotLinux** tillägg och ominstalleras automatiskt nästa säkerhetskopiering för att lösa problemet. |
| IncorrectJSONConfigFile | Den **VMSnapshotScriptPluginConfig.json** filen är felaktig, så förskript och efterskript kan inte köras och säkerhetskopian inte programkonsekvent. | Ladda ned kopia från [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) och konfigurera den igen. |
| InsufficientPermissionforPre-Script | För att köra skript, ”rot”-användare ska vara filens ägare och filen ska ha behörigheter som ”700” (det vill säga bara ”ägare” ska ha ”läs”, ”skriva” och ”kör”-behörighet). | Kontrollera att ”rot” användaren är ”ägare” till skriptfilen och att endast ”ägare” har ”läsbehörighet”, ”skriva” och ”kör”. |
| InsufficientPermissionforPost-Script | För att köra skript, rotanvändaren ska vara filens ägare och filen ska ha behörigheter som ”700” (det vill säga bara ”ägare” ska ha ”läs”, ”skriva” och ”kör”-behörighet). | Kontrollera att ”rot” användaren är ”ägare” till skriptfilen och att endast ”ägare” har ”läsbehörighet”, ”skriva” och ”kör”. |
| Pre-ScriptTimeout | Körningen av programkonsekventa före skriptet säkerhetskopieringen tog för lång tid. | Kontrollera skriptet och öka tidsgränsen i den **VMSnapshotScriptPluginConfig.json** fil som finns på **/etc/azure**. |
| Post-ScriptTimeout | Tidsgränsen nåddes för körning av efterskript för programkonsekvent säkerhetskopiering. | Kontrollera skriptet och öka tidsgränsen i den **VMSnapshotScriptPluginConfig.json** fil som finns på **/etc/azure**. |

## <a name="next-steps"></a>Nästa steg
[Konfigurera säkerhetskopiering av virtuella datorer till ett Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

---
title: 'Azure-säkerhetskopiering: programkonsekvent säkerhetskopiering av virtuella Linux-datorer'
description: Skapa programkonsekventa säkerhetskopior av din virtuella Linux-datorer till Azure. Den här artikeln förklarar hur du konfigurerar skript framework att säkerhetskopiera Azure distribuerade virtuella Linux-datorer. Den här artikeln innehåller information om felsökning.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: programkonsekventa säkerhetskopia. programkonsekventa Virtuella Azure-säkerhetskopia. Linux VM-säkerhetskopia. Azure-säkerhetskopiering
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: 027fc4098e7760de276a8548453bb83599ed0521
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605220"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Programkonsekvent säkerhetskopiering av virtuella Azure Linux-datorer

När du tar ögonblicksbilder av säkerhetskopior av dina virtuella datorer innebär programkonsekvens dina program startar när de virtuella datorerna startar efter återställs. Du kan föreställa dig är programkonsekvens mycket viktigt. Din virtuella Linux-datorer är för att säkerställa konsekvent, du kan använda ramverket för Linux-skript före och efter skript för att göra säkerhetskopior av programkonsekventa program. Skript före och efter skript framework stöder Azure Resource Manager distribuerade Linux virtuella datorer. Skript för programkonsekvens har inte stöd för Service Manager-distribuerade virtuella datorer eller Windows-datorer.

## <a name="how-the-framework-works"></a>Så här fungerar ramen

Ramen innehåller ett alternativ för att köra skript före och efter skript när du tar med VM-ögonblicksbilder. Före skript körs precis innan du utför VM-ögonblicksbild och efter skript körs omedelbart efter att du skapar VM-ögonblicksbild. Skript före och efter skript ger flexibilitet för att styra dina program och din miljö, när du tar med VM-ögonblicksbilder.

Före skript och anropa programspecifika API: er, vilka inaktivera IOs och rensa innehållet i minnet till disken. De här åtgärderna Kontrollera ögonblicksbilden är program som är konsekventa. Efter skript använder API: er för det ursprungliga programmet låsas upp IOs, vilket gör att programmet kan återuppta normal drift efter VM-ögonblicksbild.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Steg för att konfigurera skript före och efter skript

1. Logga in som rotanvändare för Linux-VM som du vill säkerhetskopiera.

2. Från [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), hämta **VMSnapshotScriptPluginConfig.json** och kopiera den till den **/etc/azure** mapp för alla virtuella datorer som du vill säkerhetskopiera. Om den **/etc/azure** mappen inte finns, skapar du den.

3. Kopiera skript före och efter skriptet för programmet på alla virtuella datorer som du planerar att säkerhetskopiera. Du kan kopiera skripten till valfri plats på den virtuella datorn. Se till att uppdatera den fullständiga sökvägen till skriptfilerna i den **VMSnapshotScriptPluginConfig.json** fil.

4. Se till att följande behörigheter för dessa filer:

   - **VMSnapshotScriptPluginConfig.json**: behörigheten ”600”. Till exempel bara ”rot” användaren ska ha behörigheten ”läsa” och ”write” till den här filen och ingen användare ska ha ”behörighet att köra”.

   - **Före skriptfilen**: behörigheten ”700”.  Till exempel bara ”rot” användaren ska ha ”läsa”, ”skriva” och ”kör”-behörighet till den här filen.
  
   - **Efter skriptet** behörigheten ”700”. Till exempel bara ”rot” användaren ska ha ”läsa”, ”skriva” och ”kör”-behörighet till den här filen.

   > [!Important]
   > Ramen ger mycket ström. Skydda ramen och se till att endast ”rot” användare har åtkomst till viktiga JSON och skriptfiler.
   > Om kraven inte uppfylls körs skriptet inte, vilket resulterar i en fil systemkrasch och inkonsekvent säkerhetskopiering.
   >

5. Konfigurera **VMSnapshotScriptPluginConfig.json** enligt nedan:
    - **pluginName**: lämna det här fältet är eller skripten kanske inte fungerar som förväntat.

    - **preScriptLocation**: Ange den fullständiga sökvägen för skriptet före på den virtuella datorn som ska säkerhetskopieras.

    - **postScriptLocation**: Ange den fullständiga sökvägen för skriptet efter på den virtuella datorn som ska säkerhetskopieras.

    - **preScriptParams**: Ange valfria parametrar som ska skickas till skriptet före. Alla parametrar ska vara inom citattecken. Om du använder flera parametrar, avgränsa parametrarna med kommatecken.

    - **postScriptParams**: Ange valfria parametrar som ska skickas till skriptet efter avslutad. Alla parametrar ska vara inom citattecken. Om du använder flera parametrar, avgränsa parametrarna med kommatecken.

    - **preScriptNoOfRetries**: Ange antalet gånger som skriptet före ska göras om det uppstår något fel innan försöket avbryts. Noll innebär bara en försök och inget nytt försök om det uppstår ett fel.

    - **postScriptNoOfRetries**: Ange hur många gånger efter skriptet ska göras om det uppstår något fel innan försöket avbryts. Noll innebär bara en försök och inget nytt försök om det uppstår ett fel.
    
    - **Timeout_sekunder**: Ange enskilda tidsgränser för skriptet före och efter skriptet.

    - **continueBackupOnFailure**: det här värdet till **SANT** om du vill att Azure Backup för att återgå till en konsekvent/krascher konsekvent filsystemsäkerhetskopia om skript före eller efter skriptet misslyckas. Att **FALSKT** misslyckas säkerhetskopieringen om fel uppstår skript (utom när du har en disk virtuell dator som faller tillbaka till kraschkonsekvent säkerhetskopiering oavsett denna inställning).

    - **fsFreezeEnabled**: Ange om Linux fsfreeze ska anropas när du tar med VM-ögonblicksbild så filsystemkonsekvens. Vi rekommenderar att du behåller den här inställningen **SANT** om programmet är beroende av inaktiverar fsfreeze.

6. Skript-ramverket har nu konfigurerats. Om säkerhetskopian VM redan har konfigurerats under nästa säkerhetskopiering anropar skripten och utlöser programkonsekvent säkerhetskopiering. Om VM-säkerhetskopiering inte har konfigurerats, konfigurerar du den med hjälp av [säkerhetskopiera virtuella Azure-datorer till Recovery Services-valv.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Felsökning

Kontrollera att du lägger till lämplig loggning vid skrivning till skript före och efter skript och granska loggarna skript för att åtgärda eventuella problem med skript. Om du fortfarande har problem med att köra skript finns i tabellen nedan för mer information.

| Fel | Felmeddelande | Rekommenderad åtgärd |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Skriptet före returnerade ett fel, så inte kanske är programkonsekvent säkerhetskopiering.   | Titta på fel loggarna för ditt skript för att åtgärda problemet.|  
|   Bokför ScriptExecutionFailed |    Efter skriptet returnerade ett fel som kan påverka programmets tillstånd. |    Titta på fel loggarna för ditt skript för att åtgärda problemet och kontrollera programmets tillstånd. |
| Pre-ScriptNotFound |  Skriptet före hittades inte på den plats som anges i den **VMSnapshotScriptPluginConfig.json** config-fil. |   Se till att det före skriptet finns på sökvägen som anges i konfigurationsfilen så programkonsekvent säkerhetskopiering.|
| Bokför ScriptNotFound | Det gick inte att hitta efter skriptet på den plats som anges i den **VMSnapshotScriptPluginConfig.json** config-fil. |   Se till att det efter skriptet finns på sökvägen som anges i konfigurationsfilen så programkonsekvent säkerhetskopiering.|
| IncorrectPluginhostFile | Den **Pluginhost** filen som levereras med filnamnstillägget VmSnapshotLinux är skadad, så att skript före och efter skriptet kan inte köras och säkerhetskopieringen inte kan programkonsekventa. | Avinstallera den **VmSnapshotLinux** -tillägget och installeras automatiskt med nästa säkerhetskopiering för att lösa problemet. |
| IncorrectJSONConfigFile | Den **VMSnapshotScriptPluginConfig.json** filen är felaktig, så före skript och efter skriptet kan inte köras och säkerhetskopieringen kan inte programkonsekventa. | Hämta kopia från [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) och konfigurera den igen. |
| InsufficientPermissionforPre-skript | För att köra skript, ”rot” användaren ska vara ägare av filen och filen måste ha behörighet för ”700” (det vill säga endast ”ägare” bör ha ”läsa”, ”skriva” och ”behörighet att köra”). | Kontrollera att ”rot” användare är ”ägare” för skriptfilen och att endast ”ägare” har ”läsa”, ”skriva” och ”kör”. |
| InsufficientPermissionforPost-skript | För att köra skript rotanvändaren bör vara ägare av filen och filen måste ha behörighet för ”700” (det vill säga endast ”ägare” bör ha ”läsa”, ”skriva” och ”behörighet att köra”). | Kontrollera att ”rot” användare är ”ägare” för skriptfilen och att endast ”ägare” har ”läsa”, ”skriva” och ”kör”. |
| Pre-ScriptTimeout | Körningen av programkonsekvent säkerhetskopiering före skriptet timeout. | Kontrollera om det och öka tidsgränsen, i den **VMSnapshotScriptPluginConfig.json** fil som finns på **/etc/azure**. |
| Post ScriptTimeout | Körningen av programkonsekventa efter skriptet säkerhetskopieringen tidsgränsen. | Kontrollera om det och öka tidsgränsen, i den **VMSnapshotScriptPluginConfig.json** fil som finns på **/etc/azure**. |

## <a name="next-steps"></a>Nästa steg
[Konfigurera säkerhetskopiering till Recovery Services-valvet](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

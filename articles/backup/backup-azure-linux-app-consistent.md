---
title: "Azure-säkerhetskopiering: programkonsekvent säkerhetskopiering av virtuella Linux-datorer | Microsoft Docs"
description: "Använda skript för att garantera en programkonsekvent säkerhetskopiering till Azure för Linux virtuella datorer. Skripten gäller endast för Linux virtuella datorer i en Resource Manager distribution; skripten gäller inte för virtuella Windows-datorer eller service manager-distributioner. Den här artikeln tar dig igenom stegen för att konfigurera skript, inklusive felsökning."
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: "programkonsekventa säkerhetskopia. programkonsekventa Virtuella Azure-säkerhetskopia. Linux VM-säkerhetskopia. Azure-säkerhetskopiering"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
ms.openlocfilehash: 378c65bec8fd1f880ed459e76f5e4b5d85e49d2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Programkonsekvent säkerhetskopiering av virtuella Azure Linux-datorer (förhandsgranskning)

Den här artikeln pratar om Linux skript före och efter skript framework och hur det kan användas för att göra programkonsekventa säkerhetskopior av virtuella Azure Linux-datorer.

> [!Note]
> Skript före och efter skript framework stöds bara för Azure Resource Manager distribuerade Linux virtuella datorer. Skript för programkonsekvens stöds inte för Service Manager-distribuerade virtuella datorer eller Windows-datorer.
>

## <a name="how-the-framework-works"></a>Så här fungerar ramen

Ramen innehåller ett alternativ för att köra skript före och efter skript när du tar med VM-ögonblicksbilder. Före skript körs innan du vidtar VM-ögonblicksbild och efter skript körs omedelbart efter att du skapar VM-ögonblicksbild. Detta ger dig flexibiliteten att styra dina program och miljö när du tar med VM-ögonblicksbilder.

I det här fallet är det viktigt att kontrollera programkonsekvent säkerhetskopiering. Skriptet före kan anropa programmet enhetligt API: er att inaktivera IOs och rensa innehållet i minnet till disken. Detta säkerställer att ögonblicksbilden programkonsekventa (det vill säga som programmet kommer upp när den virtuella datorn startas efter återställning). Efter skript kan användas för att låsas upp IOs. Detta sker med hjälp av programmet enhetligt API: er så att programmet kan återupptas normal drift post VM-ögonblicksbild.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Steg för att konfigurera skript före och efter skript

1. Logga in som rotanvändare för Linux-VM som du vill säkerhetskopiera.

2. Hämta **VMSnapshotScriptPluginConfig.json** från [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), och sedan kopiera den till den **/etc/azure** mapp på alla virtuella datorer som du vill säkerhetskopiera. Skapa den **/etc/azure** directory om den inte redan finns.

3. Kopiera skript före och efter skriptet för ditt program på alla virtuella datorer som du planerar att säkerhetskopiera. Du kan kopiera skripten till valfri plats på den virtuella datorn. Se till att uppdatera den fullständiga sökvägen till skriptfilerna i den **VMSnapshotScriptPluginConfig.json** fil.

4. Se till att följande behörigheter för dessa filer:

   - **VMSnapshotScriptPluginConfig.json**: behörigheten ”600”. Till exempel bara ”rot” användaren ska ha behörigheten ”läsa” och ”write” till den här filen och ingen användare ska ha ”behörighet att köra”.

   - **Före skriptfilen**: behörigheten ”700”.  Till exempel bara ”rot” användaren ska ha ”läsa”, ”skriva” och ”kör”-behörighet till den här filen.
  
   - **Efter skriptet** behörigheten ”700”. Till exempel bara ”rot” användaren ska ha ”läsa”, ”skriva” och ”kör”-behörighet till den här filen.

   > [!Important]
   > Ramen ger mycket ström. Det är viktigt att den är skyddad och ”rot” användare har åtkomst till viktiga skript och JSON-filer.
   > Om tidigare krav inte uppfylls köra inte skriptet. Detta resulterar i systemkrasch/konsekvent säkerhetskopiering.
   >

5. Konfigurera **VMSnapshotScriptPluginConfig.json** enligt nedan:
    - **pluginName**: lämna det här fältet är eller skripten kanske inte fungerar som förväntat.

    - **preScriptLocation**: Ange den fullständiga sökvägen för skriptet före på den virtuella datorn som ska säkerhetskopieras.

    - **postScriptLocation**: Ange den fullständiga sökvägen för skriptet efter på den virtuella datorn som ska säkerhetskopieras.

    - **preScriptParams**: Ange valfria parametrar som ska skickas till skriptet före. Alla parametrar ska vara inom citattecken och måste vara avgränsade med kommatecken om det finns flera parametrar.

    - **postScriptParams**: Ange valfria parametrar som ska skickas till skriptet efter avslutad. Alla parametrar ska vara inom citattecken och måste vara avgränsade med kommatecken om det finns flera parametrar.

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

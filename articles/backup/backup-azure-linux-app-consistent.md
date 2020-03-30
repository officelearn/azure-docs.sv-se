---
title: Programkonsekventa säkerhetskopior av virtuella Linux-datorer
description: Skapa programkonsekventa säkerhetskopior av dina virtuella Linux-datorer till Azure. I den här artikeln beskrivs hur du konfigurerar skriptramverket för att säkerhetskopiera virtuella Azure-distribuerade Linux-datorer. Den här artikeln innehåller även felsökningsinformation.
ms.reviewer: anuragm
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 36eeb9f63c67a01bf37412101e23be035596de94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173008"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Programkonsekvent säkerhetskopiering av virtuella Azure Linux-datorer

När du tar ögonblicksbilder av dina virtuella datorer för säkerhetskopiering innebär programkonsekvens att dina program startar när de virtuella datorerna startas efter att ha återställts. Som ni kan föreställa er är ansökan konsekvens oerhört viktigt. För att säkerställa att dina virtuella Linux-datorer är konsekventa i programmet kan du använda Linux-ramverket för förskript och efterskript för att göra programkonsekventa säkerhetskopior. Ramverket för förskript och efterskript stöder virtuella Azure Resource Manager-distribuerade Virtuella Linux-datorer. Skript för programkonsekvens stöder inte Service Manager-distribuerade virtuella datorer eller virtuella Windows-datorer.

## <a name="how-the-framework-works"></a>Hur ramen fungerar

Ramverket är ett alternativ för att köra anpassade förskript och postskript medan du tar ögonblicksbilder av virtuella datorer. Förskript körs precis innan du tar ögonblicksbilden av den virtuella datorn och efterskripten körs direkt efter att du har tagit ögonblicksbilden av den virtuella datorn. Förskript och postskript ger flexibiliteten att styra ditt program och din miljö, medan du tar ögonblicksbilder av virtuella datorer.

Förskript anropar inbyggda program-API:er, som anropar IOs och rensar innehåll i minnet till disken. Dessa åtgärder säkerställer att ögonblicksbilden är programkonsekvent. Postskript använder inbyggda program-API:er för att tina upp IOs, vilket gör att programmet kan återuppta normala åtgärder efter ögonblicksbilden av den virtuella datorn.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Steg för att konfigurera förskript och efterskript

1. Logga in som rotanvändare till den virtuella Linux-datorn som du vill säkerhetskopiera.

2. Från [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), ladda ner **VMSnapshotScriptPluginConfig.json** och kopiera den till **mappen /etc/azure** för alla virtuella datorer som du vill säkerhetskopiera. Om mappen **/etc/azure** inte finns skapar du den.

3. Kopiera förskriptet och postskriptet för ditt program på alla virtuella datorer som du planerar att säkerhetskopiera. Du kan kopiera skripten till valfri plats på den virtuella datorn. Var noga med att uppdatera den fullständiga sökvägen till skriptfilerna i filen **VMSnapshotScriptPluginConfig.json.**

4. Kontrollera följande behörigheter för dessa filer:

   - **VMSnapshotScriptPluginConfig.json**: Behörighet "600". Till exempel bör endast "root"-användare ha behörigheter för "läsa" och "skriva" till den här filen, och ingen användare ska ha behörigheter för att "köra".

   - **Förskriptfil**: Behörighet "700".  Till exempel bör endast "root"-användare ha behörigheter för "läsa", "skriva" och "kör" till den här filen.

   - **Efter manus** Tillstånd "700". Till exempel bör endast "root"-användare ha behörigheter för "läsa", "skriva" och "kör" till den här filen.

   > [!IMPORTANT]
   > Ramverket ger användarna mycket kraft. Säkra ramverket och se till att endast "root"-användare har åtkomst till kritiska JSON- och skriptfiler.
   > Om kraven inte uppfylls körs inte skriptet, vilket resulterar i en krasch i filsystemet och inkonsekvent säkerhetskopiering.
   >

5. Konfigurera **VMSnapshotScriptPluginConfig.json** enligt beskrivningen här:
    - **pluginName**: Lämna det här fältet som det är, annars kanske skripten inte fungerar som förväntat.

    - **preScriptLocation**: Ange den fullständiga sökvägen för förskriptet på den virtuella datorn som ska säkerhetskopieras.

    - **postScriptLocation**: Ange den fullständiga sökvägen för postskriptet på den virtuella datorn som ska säkerhetskopieras.

    - **preScriptParams**: Ange valfria parametrar som måste skickas till förskriptet. Alla parametrar bör vara inom citationstecken. Om du använder flera parametrar avgränsar du parametrarna med ett kommatecken.

    - **postScriptParams**: Ange de valfria parametrar som måste skickas till postskriptet. Alla parametrar bör vara inom citationstecken. Om du använder flera parametrar avgränsar du parametrarna med ett kommatecken.

    - **preScriptNoOfRetries**: Ange hur många gånger förskriptet ska göras om det finns något fel innan du avslutar. Noll betyder bara ett försök och inget nytt försök om det finns ett fel.

    - **postScriptNoOfRetries**: Ange hur många gånger postskriptet ska göras om det finns något fel innan du avslutar. Noll betyder bara ett försök och inget nytt försök om det finns ett fel.

    - **timeoutInSeconds**: Ange enskilda timeouts för förskriptet och postskriptet (maximalt värde kan vara 1800).

    - **continueBackupOnFailure**: Ange det här värdet till **true** om du vill att Azure Backup ska återgå till ett filsystem konsekvent / krasch konsekvent säkerhetskopiering om pre-script eller post-script misslyckas. Om du anger detta för **false** misslyckas säkerhetskopieringen i händelse av skriptfel (förutom när du har en enda disk-vm som faller tillbaka till kraschkonsekvent säkerhetskopiering oavsett den här inställningen).

    - **fsFreezeEnabled:** Ange om Linux fsfreeze ska anropas medan du tar ögonblicksbilden av den virtuella datorn för att säkerställa filsystemets konsekvens. Vi rekommenderar att du håller den här inställningen **true** om inte ditt program är beroende av att inaktivera fsfreeze.

    - **ScriptsExecutionPollTimeSeconds**: Ange den tid tillägget måste försvaga mellan varje omröstning till skriptkörningen. Om värdet till exempel är 2 kontrollerar tillägget om körningen före/efter-skript har slutförts varannan sekund. Det lägsta och högsta värde det kan ta är 1 respektive 5. Värdet bör vara strikt ett heltal.

6. Skriptramverket är nu konfigurerat. Om säkerhetskopian av den virtuella datorn redan är konfigurerad anropar nästa säkerhetskopia skripten och utlöser programkonsekvent säkerhetskopiering. Om säkerhetskopieringen av den virtuella datorn inte är konfigurerad konfigurerar du den med hjälp av [Säkerhetskopiera virtuella Azure-datorer till Recovery Services-valv.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Felsökning

Se till att du lägger till lämplig loggning när du skriver ditt förskript och efterskript och granska skriptloggarna för att åtgärda eventuella skriptproblem. Om du fortfarande har problem med att köra skript läser du följande tabell för mer information.

| Fel | Felmeddelande | Rekommenderad åtgärd |
| ------------------------ | -------------- | ------------------ |
| Före skriptFörbästFat |Förskriptet returnerade ett fel, så säkerhetskopiering kanske inte är programkonsekvent.| Titta på felloggarna för skriptet för att åtgärda problemet.|  
|Efter skriptUtstyrtFat |Efterskriptet returnerade ett fel som kan påverka programmets tillstånd. |Titta på felloggarna för skriptet för att åtgärda problemet och kontrollera programtillståndet. |
| Förskripta inteFound |Det gick inte att hitta förskriptet på den plats som anges i filen **VMSnapshotScriptPluginConfig.json** config. |Kontrollera att förskriptet finns på sökvägen som anges i konfigurationsfilen för att säkerställa att säkerhetskopieringen av programmet är konsekvent.|
| Efter skriptEt Är integrundat |Postskriptet hittades inte på den plats som anges i **VMSnapshotScriptPluginConfig.json** config-filen. |Kontrollera att postskriptet finns på sökvägen som anges i konfigurationsfilen för att säkerställa att säkerhetskopieringen av programmet är konsekvent.|
| FelaktigtPluginhostFile |**Den Pluginhost** filen, som levereras med VmSnapshotLinux förlängning, är skadad, så pre-script och post-script kan inte köras och säkerhetskopian kommer inte att vara programkonsekvent.| Avinstallera **VmSnapshotLinux-tillägget,** och det kommer automatiskt att installeras om med nästa säkerhetskopia för att åtgärda problemet. |
| FelaktigT JSONConfigFile | **Filen VMSnapshotScriptPluginConfig.json** är felaktig, så förskript och postskript kan inte köras och säkerhetskopian blir inte programkonsekvent. | Hämta kopian från [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) och konfigurera den igen. |
| Otillräckligpre-script | För att köra skript bör "root"-användare vara ägare till filen och filen ska ha behörigheter för "700" (det vill säga endast "ägare" ska ha "läst", "skriv" och "kör" behörigheter). | Kontrollera att "root"-användaren är "ägare" av skriptfilen och att endast "ägare" har "läst", "skriv" och "kör" behörigheter. |
| OtillräckligtPressförbehörpostskript | För att köra skript bör rotanvändare vara ägare till filen och filen ska ha "700" behörigheter (det vill säga endast "ägare" ska ha "läsa", "skriva" och "köra" behörigheter). | Kontrollera att "root"-användaren är "ägare" av skriptfilen och att endast "ägare" har "läst", "skriv" och "kör" behörigheter. |
| Tid för förskript | Körningen av programkonsekvent säkerhetskopiering före skriptet tidsinställd. | Kontrollera skriptet och öka timeouten i filen **VMSnapshotScriptPluginConfig.json** som finns på **/etc/azure**. |
| Tidsgränsen för efter skript | Körningen av programmet konsekvent säkerhetskopiering efter skriptet tidsinställd. | Kontrollera skriptet och öka timeouten i filen **VMSnapshotScriptPluginConfig.json** som finns på **/etc/azure**. |

## <a name="next-steps"></a>Nästa steg

[Konfigurera vm-säkerhetskopiering till ett Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

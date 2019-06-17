---
title: Felsöka säkerhetskopiering av systemtillstånd med Azure Backup
description: Felsöka problem i säkerhetskopiering av systemtillstånd.
services: backup
author: srinathvasireddy
manager: sivan
keywords: Så här säkerhetskopierar du; säkerhetskopiera systemtillstånd
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathvasireddy
ms.openlocfilehash: 8a94994d697784fb9dab8027e5a43f24c135b32c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059744"
---
# <a name="troubleshoot-system-state-backup"></a>Felsöka säkerhetskopiering av systemtillstånd

Den här artikeln beskriver lösningar på problem som kan uppstå när du använder säkerhetskopiering av systemtillstånd.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning
Vi rekommenderar att du utför den under verifieringen, innan du börjar felsöka säkerhetskopiering av systemtillstånd:

- [Se till att Microsoft Azure Recovery Services MARS-agenten är uppdaterade](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Kontrollera att det finns nätverksanslutning mellan MARS-agenten och Azure](https://aka.ms/AB-A4dp50)
- Kontrollera att Microsoft Azure Recovery Services körs (i tjänstkonsolen). Starta om åtgärden och försök igen vid behov
- [Kontrollera att det finns 5–10 % ledigt utrymme i den tillfälliga mappen](https://aka.ms/AB-AA4dwtt)
- [Kontrollera att inte andra processer eller antivirusprogram stör Azure Backup](https://aka.ms/AB-AA4dwtk)
- [Schemalagd säkerhetskopiering misslyckas, men manuell säkerhetskopiering fungerar](https://aka.ms/ScheduledBackupFailManualWorks)
- Kontrollera att ditt operativsystem har de senaste uppdateringarna
- [Se till att enheter som inte stöds och filer med attribut som inte stöds är undantagna från säkerhetskopia](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Kontrollera att **systemklockan** i det skyddade systemet är konfigurerad till rätt tidszon <br>
- [Kontrollera att servern har minst .NET Framework version 4.5.2 eller senare](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Om du försöker **omregistrera din server** till ett valv: <br>
  - Kontrollera att agenten är avinstallerad på servern och raderad från portalen <br>
  - Använd samma lösenfras som användes vid den första registreringen av servern <br>
- Se till att Azure PowerShell-version 3.7.0 är installerad på både käll- och kopiera datorn innan du påbörjar säkerhetskopieringen offline vid säkerhetskopiering offline
- [Faktor vid Backup-agenten körs på virtuella Azure-datorer](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Begränsning
- Återställning till annan maskinvara genom återställning av systemtillståndet rekommenderas inte av Microsoft
- Säkerhetskopiering av systemtillstånd stöder för närvarande ”lokalt” Windows-servrar, den här funktionen är inte tillgänglig för virtuella Azure-datorer.

## <a name="pre-requisite"></a>Förhandskrav

Innan vi felsöka säkerhetskopiering av systemtillstånd med Azure Backup kan du kontrollera att du utför den nedan förutsättningar kontrollera.  

### <a name="verify-windows-server-backup-is-installed"></a>Kontrollera Windows Server Backup är installerad

Se till att Windows Server Backup har installerats och aktiverats på servern. Kontrollera installationsstatus, köra de PowerShell-kommandot nedan:

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
Om utdata visar den **installationstillståndet** som **tillgängliga**, innebär det till att Windows Server backup funktionen är tillgänglig för installation, men inte installerade på servern. Men om Windows Server Backup inte är installerad kan sedan använda en av de metoderna nedan för att installera den.

**Metod 1: Installera Windows Server Backup med hjälp av PowerShell**

Installera Windows Server Backup med hjälp av PowerShell genom att köra i kommandot nedan:

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**Metod 2: Installera Windows Server Backup med hjälp av Serverhanteraren**

Så här installerar du Windows Server Backup med hjälp av Serverhanteraren den nedan:

1. I den **Server Manager** och klicka på **Lägg till roller och funktioner**. Den **guiden för Lägg till roller och funktioner** visas.

    ![Instrumentpanel](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Välj **installationstyp** och klicka på **nästa**.

    ![Installationstyp](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Välj en server från serverpoolen och klicka på **nästa**. Lämna kvar standardvalet i Server-rollen och klickar på **nästa**.
4. Välj **Windows Server Backup** i **funktioner** fliken och klicka på **nästa**.

    ![funktioner](./media/backup-azure-system-state-troubleshoot/features.png)

5. I den **bekräftelse** fliken **installera** att starta installationen.
6. I den **resultat** fliken visas i Windows Server Backup funktionen har installerats på Windows Server.

    ![Resultatet](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>System Volume information behörighet

Kontrollera att det lokala systemet har fullständig behörighet på **System Volume Information** mappen finns på volymen där windows är installerat. Detta är vanligtvis **C:\System Volume Information**. Windows Server backup kan misslyckas om dessa behörigheter inte är korrekt inställda

### <a name="dependent-services"></a>Beroende tjänster

Se till att den är i körningstillstånd nedan tjänster:

**Tjänstnamn** | **Starttyp**
--- | ---
RPC-Call(RPC) | Automatisk
COM + Event System(EventSystem) | Automatisk
System Event Notification Service(SENS) | Automatisk
Volume Shadow startmetoden | Manuellt
Microsoft-programvara Shadow Copy Provider(SWPRV) | Manuellt

### <a name="validate-windows-server-backup-status"></a>Validera status för Windows Server Backup

Du kan kontrollera status för Windows Server Backup genom att utföra den nedan:

  * Kontrollera att WSB PowerShell körs

    -   Kör `Get-WBJob` från en upphöjd PowerShell och kontrollera att den inte returnerar följande fel:

    > [!WARNING]
    > Get-WBJob: Termen ”Get-WBJob” identifieras inte som namnet på en cmdlet, funktion, skriptfil eller ett körbart program. Kontrollera stavningen av namnet, eller om en sökväg har inkluderats, kontrollera att sökvägen är korrekt och försök igen.

    -   Om det misslyckas med felet sedan installera om Windows Server Backup funktionen på server-datorn som vi nämnde i steg 1-krav.

  * Kontrollera att WSB-säkerhetskopiering fungerar, genom att köra den nedanstående kommando från en upphöjd kommandotolk:

      ` wbadmin start systemstatebackup -backuptarget:X: -quiet `

      > [!NOTE]
      >Ersätt X med enhetsbeteckningen för volymen där du vill lagra systemtillståndet säkerhetskopiera bild.

    - Regelbundet kontrollera status för jobbet genom att köra `Get-WBJob` från utökad PowerShell-session        
    - När säkerhetskopieringsjobbet är klart kontrollerar du den slutgiltiga statusen för jobbet genom att köra `Get-WBJob -Previous 1` kommando

Om jobbet misslyckats, anger ett WSB-problem som skulle resultera i MARS-agenten fel vid säkerhetskopiering av systemtillstånd.

## <a name="common-errors"></a>Vanliga fel

### <a name="vss-writer-timeout-error"></a>VSS-skrivaren tidsgränsfel

| Symtom | Orsak | Lösning
| -- | -- | --
| -MARS agenten misslyckas med felmeddelandet: ”Windows Server Backup-jobbet misslyckades med VSS-fel. Kontrollera händelseloggarna för VSS att lösa felet ”<br/><br/> – Loggen är finns i Loggboken VSS-program följande fel: ”En VSS-skrivare har avvisat en händelse med fel 0x800423f2, av skrivaren tidsgränsen gick ut mellan händelserna Freeze och Thaw”.| VSS-skrivaren kan inte slutföra tidpunkt på grund av bristande resurser för CPU och minne på datorn <br/><br/> Ett annat säkerhetskopieringsprogram använder redan VSS-skrivaren, därmed ögonblicksbildsåtgärden kunde inte slutföra för den här säkerhetskopian | Vänta tills processor/minne att frigöras på system eller avbryta processer som äger för mycket minne/CPU och försök sedan igen <br/><br/>  Vänta tills pågående säkerhetskopieringen har slutförts och försök igen senare när inga säkerhetskopieringar som körs på datorn


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Inte tillräckligt mycket ledigt utrymme att växa skuggkopior

| Symtom | Lösning
| -- | --
| -MARS agenten misslyckas med felmeddelandet: Säkerhetskopieringen misslyckades eftersom det inte gick att utöka skuggkopian på grund av otillräckligt diskutrymme på volymer som innehåller systemfiler <br/><br/> -Följande fel/varninglogg finns i volsnap systemhändelseloggarna: ”Det fanns inte tillräckligt med diskutrymme på volymen C: att öka lagringsutrymmet för skuggkopian för skuggkopior av C: på grund av felet alla skuggkopior för volymen C: finns risk för att tas bort” | -Frigöra utrymme i den markerade volymen i händelseloggen så att det finns tillräckligt med utrymme för skuggkopior att växa när säkerhetskopiering pågår <br/><br/> – När du konfigurerar shadow copy utrymme som vi kan begränsa mängden utrymme som används för skuggkopian, mer information finns i den här [artikel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>EFI-partition som låst

| Symtom | Lösning
| -- | --
| MARS-agenten misslyckas med felmeddelandet: ”Systemtillstånd tillbaka upp misslyckades eftersom EFI-systempartitionen är låst. Det kan bero på partitionen användare av en säkerhetsprogram från tredje part eller säkerhetskopiera programvara ” | -Om problemet beror på ett säkerhetsprogram från tredje part-programvara, måste du kontakta leverantören för skydd mot Virus så att de kan tillåta MARS-agenten <br/><br/> – Om en tredje parts programvara för säkerhetskopiering körs, vänta tills den har slutförts och försök sedan tillbaka upp


## <a name="next-steps"></a>Nästa steg

- Mer information om Windows system-tillstånd i Resource Manager-distribution finns i [Säkerhetskopiera Windows Server System-tillstånd](backup-azure-system-state.md)

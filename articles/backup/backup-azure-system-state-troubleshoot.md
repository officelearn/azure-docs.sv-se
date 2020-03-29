---
title: Felsöka säkerhetskopiering av systemtillstånd
description: I den här artikeln kan du läsa om hur du felsöker problem i Säkerhetskopiering av systemtillstånd för lokala Windows-servrar.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969581"
---
# <a name="troubleshoot-system-state-backup"></a>Felsöka säkerhetskopiering av systemtillstånd

I den här artikeln beskrivs lösningar på problem som kan uppstå när du använder Säkerhetskopiering av systemtillstånd.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du utför valideringen nedan innan du börjar felsöka säkerhetskopiering av systemtillstånd:

- [Se till att MARS-agenten (Microsoft Azure Recovery Services) är uppdaterad](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Kontrollera att det finns nätverksanslutning mellan MARS-agenten och Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Kontrollera att Microsoft Azure Recovery Services körs (i tjänstkonsolen). Om det behövs startar du om och försöker igen
- [Kontrollera att det finns 5–10 % ledigt utrymme i den tillfälliga mappen](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Kontrollera att inte andra processer eller antivirusprogram stör Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Schemalagd säkerhetskopiering misslyckas, men manuell säkerhetskopiering fungerar](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Kontrollera att ditt operativsystem har de senaste uppdateringarna
- [Kontrollera att enheter och filer som inte stöds med attribut som inte stöds utesluts från säkerhetskopiering](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Se till att **systemklockan** på det skyddade systemet är konfigurerad för att korrigera tidszonen <br>
- [Kontrollera att servern har minst .NET Framework version 4.5.2 eller senare](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Om du försöker **registrera om servern** till ett valv gör du så här: <br>
  - Se till att agenten avinstalleras på servern och att den tas bort från portalen <br>
  - Använd samma lösenfras som användes vid den första registreringen av servern <br>
- Om detta är en offline-säkerhetskopia kontrollerar du att Azure PowerShell version 3.7.0 är installerad på både käll- och kopieringsdatorn innan du påbörjar säkerhetskopiering offline
- [Övervägande när säkerhetskopieringsagent körs på en virtuell Azure-dator](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Begränsning

- Återställning till annan maskinvara genom återställning av systemtillståndet rekommenderas inte av Microsoft
- Säkerhetskopiering av systemtillstånd stöder för närvarande "lokala" Windows-servrar. Den här funktionen är inte tillgänglig för virtuella Azure-datorer.

## <a name="prerequisites"></a>Krav

Innan vi felsöker Säkerhetskopiering av systemtillstånd med Azure Backup, utför nedanstående kravkontroll.  

### <a name="verify-windows-server-backup-is-installed"></a>Kontrollera att Windows Server Backup är installerat

Kontrollera att Windows Server Backup är installerat och aktiverat på servern. Om du vill kontrollera installationsstatusen kör du det här PowerShell-kommandot:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Om utdata visar **installationstillståndet** som **tillgängligt**betyder det att säkerhetskopieringsfunktionen för Windows Server är tillgänglig för installationen men inte installerad på servern. Men om Windows Server Backup inte är installerat använder du någon av metoderna nedan för att installera den.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metod 1: Installera Windows Server Backup med PowerShell

Om du vill installera Windows Server Backup med PowerShell kör du kommandot nedan:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metod 2: Installera Windows Server Backup med Serverhanteraren

Så här installerar du Windows Server Backup med Serverhanteraren:

1. Klicka på Lägg **till roller och funktioner**i **Serverhanteraren**. **Guiden Lägg till roller och funktioner** visas.

    ![Instrumentpanel](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Välj **Installationstyp** och klicka på **Nästa**.

    ![Installationstyp](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Välj en server i serverpoolen och klicka på **Nästa**. Lämna standardvalet i serverrollen och klicka på **Nästa**.
4. Välj **Windows Server Backup** på fliken **Funktioner** och klicka på **Nästa**.

    ![funktioner](./media/backup-azure-system-state-troubleshoot/features.png)

5. Klicka på **Installera** på fliken **Bekräftelse** för att starta installationsprocessen.
6. På fliken **Resultat** visas windows serversäkerhetsfunktionen som har installerats på Windows Server.

    ![resultat](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Behörighet för information om systemvolym

Kontrollera att det lokala systemet har full kontroll på mappen **Systemvolyminformation** i den volym där Windows är installerat. Vanligtvis är detta **C:\System Volume Information**. Windows Server-säkerhetskopieringen kan misslyckas om ovanstående behörigheter inte har angetts korrekt

### <a name="dependent-services"></a>Beroende tjänster

Se till att nedanstående tjänster är i körläge:

**Tjänstnamn** | **Startmetod**
--- | ---
Anrop för fjärrprocedur(RPC) | Automatisk
COM+ händelsesystem(EventSystem) | Automatisk
Meddelandetjänst för systemhändelse(SENS) | Automatisk
Volym skuggkopia (VSS) | Manuell
Microsoft Software Shadow Copy Provider (SWPRV) | Manuell

### <a name="validate-windows-server-backup-status"></a>Verifiera windows server-säkerhetskopieringsstatus

Så här validerar du windows serversäkerhetsstatus:

- Se till att WSB PowerShell körs

  - Kör `Get-WBJob` från ett upphöjt PowerShell och se till att det inte returnerar följande fel:

    > [!WARNING]
    > Get-WBJob: Termen "Get-WBJob" känns inte igen som namnet på en cmdlet, funktion, skriptfil eller funktionsdugliga program. Kontrollera stavningen av namnet, eller om en sökväg har inkluderats, kontrollera att sökvägen är korrekt och försök igen.

    - Om det misslyckas med det här felet installerar du om windows serversäkerhetsfunktionen på servermaskinen som nämns i steg 1 av förutsättningarna.

  - Kontrollera att WSB-säkerhetskopian fungerar korrekt genom att köra kommandot nedan från den upphöjda kommandotolken:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Ersätt X med enhetsbeteckningen för den volym där du vill lagra systemtillståndsuppbackningsbilden.

    - Kontrollera regelbundet jobbets status `Get-WBJob` genom att köra kommandot från förhöjda PowerShell
    - När säkerhetskopieringsjobbet har slutförts kontrollera `Get-WBJob -Previous 1` jobbets slutliga status genom att köra kommandot

Om jobbet misslyckas anger det ett WSB-problem som skulle resultera i ett fel på SÄKERHETSKOPIERing av MARS-agentsystemtillstånd.

## <a name="common-errors"></a>Vanliga fel

### <a name="vss-writer-timeout-error"></a>TIMEOUT-fel för VSS Writer

| Symptom | Orsak | Lösning
| -- | -- | --
| - MARS-agenten misslyckas med felmeddelandet: "WSB-jobbet misslyckades med VSS-fel. Kontrollera VSS-händelseloggar för att lösa felet"<br/><br/> - Följande fellogg finns i VSS Application händelseloggar: "En VSS-skrivare har avvisat en händelse med fel 0x800423f2, författarens timeout löpte ut mellan Freeze och Tina händelser."| VSS-skrivaren kan inte slutföras i tid på grund av brist på CPU- och minnesresurser på datorn <br/><br/> En annan säkerhetskopieringsprogram använder redan VSS-skrivaren, eftersom en ögonblicksbildåtgärd för resultat inte kunde slutföras för den här säkerhetskopian | Vänta tills CPU/minne frigörs på systemet eller avbryter processerna som tar för mycket minne/CPU och försöker igen. <br/><br/>  Vänta tills den pågående säkerhetskopieringen har slutförts och prova åtgärden vid ett senare tillfälle när inga säkerhetskopior körs på datorn.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Otillräckligt diskutrymme för att få skuggkopior

| Symptom | Lösning
| -- | --
| - MARS-agenten misslyckas med felmeddelandet: Säkerhetskopieringen misslyckades eftersom skuggkopieringsvolymen inte kunde växa på grund av otillräckligt diskutrymme på volymer som innehåller systemfiler <br/><br/> - Följande fel / varningslogg finns i volsnap system händelseloggar: "Det fanns otillräckligt diskutrymme på volym C: att växa skuggkopiering lagring för skuggkopior av C: på grund av detta fel alla skuggkopior av volym C: riskerar att tas bort" | - Frigör utrymme i den markerade volymen i händelseloggen så att det finns tillräckligt med utrymme för skuggkopior att växa medan säkerhetskopiering pågår <br/><br/> - Samtidigt konfigurera skugga kopiering utrymme vi kan begränsa mängden utrymme som används för skugga kopia. Mer information finns i den här [artikeln](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>EFI-partitionen låst

| Symptom | Lösning
| -- | --
| MARS-agenten misslyckas med felmeddelandet: "Säkerhetskopiering av systemtillstånd misslyckades eftersom EFI-systempartitionen är låst. Detta kan bero på systempartitionsåtkomst av en säkerhet från tredje part eller säkerhetskopieringsprogramvara" | - Om problemet beror på en tredje parts säkerhetsprogram, måste du kontakta Anti Virus säljaren så att de kan tillåta MARS agent <br/><br/> - Om en programvara för säkerhetskopiering från tredje part körs väntar du sedan på att den ska slutföras och försöker sedan igen

## <a name="next-steps"></a>Nästa steg

- Mer information om Windows-systemtillstånd i Resurshanterarens distribution finns i [Säkerhetskopiera systemtillstånd för Windows Server](backup-azure-system-state.md)

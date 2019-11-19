---
title: Felsöka säkerhets kopiering av system tillstånd
description: I den här artikeln får du lära dig hur du felsöker problem med säkerhets kopiering av system tillstånd för lokala Windows-servrar.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 116f8f40193ea276c6150452b0aa6f2d2ce5bc6c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172608"
---
# <a name="troubleshoot-system-state-backup"></a>Felsöka säkerhets kopiering av system tillstånd

I den här artikeln beskrivs lösningar för problem som kan uppstå när du använder säkerhets kopiering av system tillstånd.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du utför nedanstående verifiering innan du börjar felsöka säkerhets kopiering av system tillstånd:

- [Se till att Microsoft Azure Recovery Services (MARS) Agent är uppdaterad](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Kontrollera att det finns nätverksanslutning mellan MARS-agenten och Azure](https://aka.ms/AB-A4dp50)
- Kontrollera att Microsoft Azure Recovery Services körs (i tjänstkonsolen). Om det behövs startar du om och försöker igen
- [Kontrollera att det finns 5–10 % ledigt utrymme i den tillfälliga mappen](https://aka.ms/AB-AA4dwtt)
- [Kontrollera att inte andra processer eller antivirusprogram stör Azure Backup](https://aka.ms/AB-AA4dwtk)
- [Schemalagd säkerhetskopiering misslyckas, men manuell säkerhetskopiering fungerar](https://aka.ms/ScheduledBackupFailManualWorks)
- Kontrollera att ditt operativsystem har de senaste uppdateringarna
- [Se till att enheter och filer som inte stöds med attribut som inte stöds undantas från säkerhets kopian](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Kontrollera att **systemklockan** i det skyddade systemet är konfigurerad till rätt tidszon <br>
- [Kontrollera att servern har minst .NET Framework version 4.5.2 eller senare](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Om du försöker **omregistrera din server** till ett valv: <br>
  - Kontrollera att agenten är avinstallerad på servern och raderad från portalen <br>
  - Använd samma lösenfras som användes vid den första registreringen av servern <br>
- I händelse av säkerhets kopiering offline kontrollerar du att Azure PowerShell version 3.7.0 är installerad på både käll-och kopierings datorn innan du påbörjar säkerhets kopiering offline
- [Att tänka på när säkerhets kopierings agenten körs på en virtuell Azure-dator](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Begränsning

- Återställning till annan maskinvara genom återställning av systemtillståndet rekommenderas inte av Microsoft
- Säkerhets kopiering av system tillstånd stöder för närvarande lokala Windows-servrar, men den här funktionen är inte tillgänglig för virtuella Azure-datorer.

## <a name="prerequisites"></a>Krav

Innan vi felsöker säkerhets kopiering av system tillstånd med Azure Backup utför du nedanstående krav kontroll.  

### <a name="verify-windows-server-backup-is-installed"></a>Verifiera Windows Server Backup har installerats

Se till att Windows Server Backup är installerat och aktiverat på servern. Om du vill kontrol lera installations statusen kör du följande PowerShell-kommando:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Om utdata visar **installations status** som **tillgänglig**innebär det att Windows Server Backup-funktionen är tillgänglig för installationen men inte är installerad på servern. Men om Windows Server Backup inte är installerat kan du använda någon av metoderna nedan för att installera den.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metod 1: installera Windows Server Backup med PowerShell

Kör följande kommando för att installera Windows Server Backup med PowerShell:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metod 2: installera Windows Server Backup med Serverhanteraren

Följ stegen nedan om du vill installera Windows Server Backup med hjälp av Serverhanteraren:

1. I **Server hanteraren**klickar du på **Lägg till roller och funktioner**. **Guiden Lägg till roller och funktioner** visas.

    ![Instrumentpanel](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Välj **Installations typ** och klicka på **Nästa**.

    ![Installations typ](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Välj en server från serverpoolen och klicka på **Nästa**. I Server rollen lämnar du standard valet och klickar på **Nästa**.
4. Välj **Windows Server Backup** på fliken **funktioner** och klicka på **Nästa**.

    ![funktioner](./media/backup-azure-system-state-troubleshoot/features.png)

5. På fliken **bekräftelse** klickar du på **Installera** för att starta installations processen.
6. På fliken **resultat** visas Windows Server Backup funktionen har installerats på Windows-servern.

    ![Resultatet](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Behörighet för system volym information

Se till att det lokala systemet har fullständig kontroll över mappen **System Volume Information** som finns i den volym där Windows är installerat. Detta är vanligt vis **C:\System volym information**. Windows Server Backup kan inte köras om ovanstående behörigheter inte har angetts korrekt

### <a name="dependent-services"></a>Beroende tjänster

Se till att nedanstående tjänster är i körnings tillstånd:

**Tjänst namn** | **Starttyp**
--- | ---
RPC (Remote Procedure Call) | Automatisk
COM+-händelse system (EventSystem) | Automatisk
SENS (system Event Notification Service) | Automatisk
Volume Shadow Copy (VSS) | Manuell
Microsoft Software Shadow Copy-Provider (SWPRV) | Manuell

### <a name="validate-windows-server-backup-status"></a>Verifiera Windows Server Backup status

Utför stegen nedan för att verifiera Windows Server Backup status:

- Se till att WSB PowerShell körs

  - Kör `Get-WBJob` från en upphöjd PowerShell och se till att det inte returnerar följande fel:

    > [!WARNING]
    > Get-WBJob: termen "Get-WBJob" känns inte igen som namnet på en cmdlet, Function, skript fil eller fungerande program. Kontrol lera stavningen av namnet eller om en sökväg har inkluderats, kontrol lera att sökvägen är korrekt och försök igen.

    - Om det Miss lyckas med det här felet kan du installera om Windows Server Backup-funktionen på serverdatorn enligt anvisningarna i steg 1.

  - Se till att säkerhets kopieringen WSB fungerar korrekt genom att köra kommandot nedan från upphöjd kommando tolk:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Ersätt X med enhets bokstaven för den volym där du vill lagra säkerhets kopian av system tillstånd.

    - Kontrol lera regelbundet status för jobbet genom att köra `Get-WBJob` kommando från utökad PowerShell
    - När säkerhets kopierings jobbet har slutförts kontrollerar du jobbets slut status genom att köra `Get-WBJob -Previous 1` kommandot

Om jobbet Miss lyckas indikerar det ett WSB-problem som skulle resultera i att säkerhets kopieringen av MARS-agenten Miss lyckas.

## <a name="common-errors"></a>Vanliga fel

### <a name="vss-writer-timeout-error"></a>Tids gräns fel för VSS-skrivare

| Symptom | Orsak | Lösning
| -- | -- | --
| -MARS-agenten Miss lyckas med fel meddelandet "WSB-jobbet misslyckades med VSS-fel. Kontrol lera händelse loggarna för VSS för att lösa problemet "<br/><br/> – Följande fel logg finns i händelse loggar för VSS-program: "en VSS-skrivare har avvisat en händelse med fel 0x800423f2, skrivarens tids gräns överskreds mellan låsnings-och Tina-händelserna."| VSS Writer kan inte slutföras i tid på grund av brist på processor-och minnes resurser på datorn <br/><br/> En annan säkerhets kopierings program vara använder redan VSS-skrivaren eftersom det inte gick att slutföra ögonblicks bilds åtgärden för den här säkerhets kopian | Vänta tills CPU/minne har frigjorts på systemet eller Avbryt processer som tar för mycket minne/CPU och försök sedan igen <br/><br/>  Vänta tills den pågående säkerhets kopieringen har slutförts och försök sedan igen vid ett senare tillfälle när inga säkerhets kopior körs på datorn

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Det finns inte tillräckligt med disk utrymme för att öka skugg kopior

| Symptom | Lösning
| -- | --
| -MARS-agenten Miss lyckas med fel meddelandet: det gick inte att säkerhetskopiera eftersom skugg kopie volymen inte kunde växa på grund av otillräckligt disk utrymme på volymer som innehåller systemfiler <br/><br/> -Följande fel/varnings logg finns i volsnap-systemets händelse loggar: "det fanns inte tillräckligt med disk utrymme på volym C: för att öka skugg kopians lagrings utrymme för skugg kopior av C: på grund av detta fel alla skugg kopior av volym C: riskerar att tas bort" | – Frigör utrymme på den markerade volymen i händelse loggen så att det finns tillräckligt med utrymme för skugg kopior som ska växa medan säkerhets kopiering pågår <br/><br/> – När du konfigurerar skugg kopierings utrymme kan vi begränsa mängden utrymme som används för skugg kopior. Mer information finns i den här [artikeln](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)

### <a name="efi-partition-locked"></a>EFI-partitionen är låst

| Symptom | Lösning
| -- | --
| MARS-agenten Miss lyckas med fel meddelandet: "Det gick inte att säkerhetskopiera system tillstånd eftersom EFI-systempartitionen är låst. Detta kan bero på systempartitions åtkomst av säkerhets-eller säkerhets kopierings program från tredje part | – Om problemet beror på en säkerhets program vara från tredje part måste du kontakta leverantören av antivirus programmet så att de kan tillåta MARS-agenten <br/><br/> – Om en program vara från tredje part körs, väntar du tills den är klar och försöker sedan säkerhetskopiera igen

## <a name="next-steps"></a>Nästa steg

- Mer information om Windows system State i Resource Manager-distribution finns i [Säkerhetskopiera Windows Server System State](backup-azure-system-state.md)

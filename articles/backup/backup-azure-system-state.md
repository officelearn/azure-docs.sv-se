---
title: "Säkerhetskopiera Windows systemtillståndet till Azure | Microsoft Docs"
description: "Lär dig hur du säkerhetskopierar systemtillståndet för Windows Server och/eller Windows datorer till Azure."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: carmonm
editor: 
keywords: "hur du säkerhetskopierar; säkerhetskopiera; säkerhetskopiera filer och mappar"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: saurse;markgal
ms.openlocfilehash: 6fbd96935f444d8b0c6d068ebd0d28e612f19816
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Säkerhetskopiera systemtillståndet för Windows i Resource Manager-distribution
Den här artikeln förklarar hur du säkerhetskopierar systemtillståndet Windows Server till Azure. I den här självstudiekursen går vi igenom grunderna.

Om du vill veta mer om Azure Backup läser du den här [översikten](backup-introduction-to-azure-backup.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) som ger dig åtkomst till Azure-tjänsten.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
Innan du kan säkerhetskopiera filer och mappar måste du skapa ett Recovery Services-valv i den region som du vill lagra informationen i. Du måste också bestämma hur du vill att lagringen ska replikeras.

### <a name="to-create-a-recovery-services-vault"></a>Så här skapar du ett Recovery Services-valv
1. Om du inte redan gjort det loggar du in på [Azure-portalen](https://portal.azure.com/) med din Azure-prenumeration.
2. På navigeringsmenyn klickar du på **Fler tjänster** och skriver **Recovery Services** i listan över resurser och klickar sedan på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Om det finns Recovery Services-valv i prenumerationen visas valven.
3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.

    ![Skapa Recovery Services-valv (steg 3)](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.

5. I avsnittet **Prenumeration** använder du listrutan för att välja Azure-prenumerationen. Om du bara använder en prenumeration visas den och du kan gå vidare till nästa steg. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (eller den föreslagna). Du kan bara välja mellan flera alternativ om ditt organisationskonto är associerat med flera Azure-prenumerationer.

6. Gör följande i avsnittet **Resursgrupp**:

    * Välj **Skapa ny** om du vill skapa en resursgrupp.
    Eller
    * Välj **Använd befintlig** och klicka på listrutan om du vill se listan över tillgängliga resursgrupper.

  Fullständig information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Klicka på **Plats** för att välja en geografisk region för valvet. Det här alternativet anger den geografiska region som dina säkerhetskopierade data skickas till.

8. Längst ned på bladet för Recovery Services-valvet klickar du på **Skapa**.

    Det kan ta flera minuter innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv efter ett par minuter klickar du på **Uppdatera**.

    ![Klicka på Uppdatera](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    När du ser valvet i listan över Recovery Services-valv kan du ange lagringsredundansen.

### <a name="set-storage-redundancy-for-the-vault"></a>Ange lagringsredundans för valvet
När du skapar ett Recovery Services-valv ska du alltid kontrollera att lagringsredundansen är konfigurerad på det sätt som du vill.

1. På bladet **Recovery Services-valv** klickar du på det nya valvet.

    ![Välj det nya valvet i listan över Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Om du väljer valvet minimeras bladet **Recovery Services-valv** och bladet Inställningar (*som har namnet på valvet överst*) och bladet med valvinformation öppnas.

    ![Visa lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. På det nya valvets inställningsblad använder du det lodräta reglaget och bläddrar ned till avsnittet Hantera. Där klickar du på **Infrastruktur för säkerhetskopiering**.
    Bladet Infrastruktur för säkerhetskopiering öppnas.
3. På bladet Infrastruktur för säkerhetskopiering klickar du på **Konfiguration av säkerhetskopiering** för att öppna bladet **Konfiguration av säkerhetskopiering**.

    ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Välj lämpligt alternativ för lagringsreplikering för valvet.

    ![alternativ för lagringskonfiguration](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Valvet använder geo-redundant lagring som standard. Om du använder Azure som primär slutpunkt för lagring av säkerhetskopior fortsätter du att använda **geo-redundant** lagring. Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy.md#geo-redundant-storage) och [lokalt redundant](../storage/common/storage-redundancy.md#locally-redundant-storage) i denna [översikt av lagringsredundans](../storage/common/storage-redundancy.md).

Nu när du har skapat ett valv, kan du konfigurera den för att säkerhetskopiera systemtillståndet i Windows.

## <a name="configure-the-vault"></a>Konfigurera valvet
1. På bladet Recovery Services-valv (för valvet som du precis har skapat) klickar du på **Säkerhetskopiering** i avsnittet Komma igång och väljer sedan **Säkerhetskopieringsmål** på bladet **Kom igång med säkerhetskopiering**.

    ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Bladet **Säkerhetskopieringsmål** öppnas.

    ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Från listrutan **Var körs din arbetsbelastning?** väljer du **Lokalt**.

    Du väljer **Lokalt** eftersom din Windows Server- eller Windows-dator är en fysisk dator som inte finns i Azure.

3. Från den **vad vill du säkerhetskopiera?** väljer du **systemtillstånd**, och klicka på **OK**.

    ![Konfigurera filer och mappar](./media/backup-azure-system-state/backup-goal-system-state.png)

    När du klickar på OK visas en markering bredvid **Säkerhetskopieringsmål** och bladet **Förbered infrastruktur** öppnas.

    ![När säkerhetskopieringsmålet har konfigurerats går du vidare och förbereder infrastrukturen](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. På bladet **Förbered infrastruktur** klickar du på **Ladda ned agent för Windows Server eller Windows Client**.

    ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Om du använder Windows Server Essential väljer du att hämta agenten för Windows Server Essential. En popup-meny visas och du uppmanas att köra eller spara MARSAgentInstaller.exe.

    ![Dialogrutan MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. På snabbmenyn för hämtningen klickar du på **Spara**.

    Som standard sparas filen **MARSagentinstaller.exe** i mappen för nedladdningar. När installationsprogrammet har slutförts visas ett popup-fönster och du tillfrågas om du vill köra installationsprogrammet eller öppna mappen.

    ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Du behöver inte installera agenten än. Du kan installera agenten när du har hämtat valvautentiseringsuppgifterna.

6. Klicka på **Ladda ned** på bladet **Förbered infrastruktur**.

    ![hämta autentiseringsuppgifter för valvet](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Autentiseringsuppgifterna för valvet hämtas till mappen Hämtningsbara filer. När autentiseringsuppgifterna för valvet har hämtats visas ett popup-fönster och du tillfrågas om du vill öppna eller spara autentiseringsuppgifterna. Klicka på **Save** (Spara). Om du råkar klicka på **Öppna** av misstag väntar du tills dialogrutan som försöker öppna autentiseringsuppgifterna för valvet misslyckas. Du kan inte öppna valvautentiseringsuppgifterna. Gå vidare till nästa steg. Valvautentiseringsuppgifterna finns i mappen Hämtade filer.   

    ![valvautentiseringsuppgifterna har hämtats](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

> [!NOTE]
> Aktivering av säkerhetskopiering via Azure Portal är inte tillgängligt ännu. Använda Microsoft Azure Recovery Services-agenten för säkerhetskopiering av systemtillstånd för Windows Server.
>

1. Leta upp och dubbelklicka på **MARSagentinstaller.exe** från mappen för nedladdade filer (eller en annan lagringsplats).

    Installationsprogrammet visar ett antal meddelanden när Recovery Services-agenten extraheras, installeras och registreras.

    ![Autentiseringsuppgifter för att köra Recovery Services-agentinstallationsprogrammet](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Slutför installationsguiden för Microsoft Azure Recovery Services Agent. För att slutföra guiden måste du:

   * Välja en plats för installationen och cachelagringsmappen.
   * Ange information om proxyservern om du använder en proxyserver för att ansluta till Internet.
   * Ange ditt användarnamn och lösenord om du använder en autentiserad proxyserver.
   * Ange autentiseringsuppgifterna som du hämtat för valvet.
   * Spara krypteringslösenfrasen på en säker plats.

     > [!NOTE]
     > Om du tappar bort eller glömmer lösenfrasen kan Microsoft inte hjälpa dig att återställa dina säkerhetskopierade data. Spara filen på en säker plats. Det krävs för att återställa en säkerhetskopia.
     >
     >

Nu installeras agenten och datorn registreras i valvet. Nu kan du konfigurera och schemalägga säkerhetskopieringen.

## <a name="back-up-windows-server-system-state-preview"></a>Säkerhetskopiera systemtillståndet för Windows Server (förhandsgranskning)
Den första säkerhetskopian innehåller tre uppgifter:

* Aktivera säkerhetskopiering av systemtillstånd med Azure Backup-agenten
* Schemalägg säkerhetskopieringen
* Säkerhetskopiera filer och mappar för första gången

För att slutföra den första säkerhetskopieringen använder du Microsoft Azure Recovery Services-agenten.

### <a name="to-enable-system-state-backup-using-the-azure-backup-agent"></a>Så här aktiverar du säkerhetskopian av systemtillstånd med Azure Backup-agenten

1. Kör följande kommando för att stoppa Azure Backup-motorn i en PowerShell-session.

  ```
  PS C:\> Net stop obengine
  ```

2. Öppna Windows-registret.

  ```
  PS C:\> regedit.exe
  ```

3. Lägg till följande registernyckel med det angivna DWord-värdet.

  | Sökväg i registret | Registernyckel | DWord-värde |
  |---------------|--------------|-------------|
  | HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | TurnOffSSBFeature | 2 |

4. Starta om motorn för säkerhetskopiering genom att köra följande kommando i en kommandotolk med förhöjd behörighet.

  ```
  PS C:\> Net start obengine
  ```

### <a name="to-schedule-the-backup-job"></a>Så här schemalägger du säkerhetskopieringsjobbet

1. Öppna Microsoft Azure Recovery Services-agenten. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

    ![Starta Azure Recovery Services-agenten](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Klicka på Recovery Services-agenten och sedan på **Schemalägg säkerhetskopiering**.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Klicka på **Nästa** på sidan Komma igång i guiden Schemalägg säkerhetskopiering.

4. På sidan Välj objekt som ska säkerhetskopieras klickar du på **Lägg till objekt**.

5. Välj **systemtillstånd** och klicka sedan på **OK**.

6. Klicka på **Nästa**.

7. Schemat för säkerhetskopiering av systemtillstånd och kvarhållning anges automatiskt att säkerhetskopiera varje söndag vid 21:00:00 lokal tid och kvarhållningsperioden anges till 60 dagar.

   > [!NOTE]
   > Princip för säkerhetskopiering och kvarhållning av systemtillståndet konfigureras automatiskt. Ange endast säkerhetskopiering och kvarhållning principen för säkerhetskopior av filer från guiden om du säkerhetskopierar filer och mappar samt status för Windows Server-System. 
   >

8. Läs informationen på sidan Bekräftelse och klicka sedan på **Slutför**.

9. När guiden har skapat säkerhetskopieringsschemat klickar du på **Stäng**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Säkerhetskopiera Windows Server System tillstånd för första gången

1. Kontrollera att det finns inga väntande uppdateringar för Windows Server som kräver en omstart.

2. I Recovery Services-agenten klickar du på **Säkerhetskopiera nu** för att slutföra en inledande seeding över nätverket.

    ![Säkerhetskopiera Windows Server nu](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Gå igenom inställningarna på sidan Bekräftelse som guiden Säkerhetskopiera nu ska använda för att säkerhetskopiera datorn. Klicka på **Säkerhetskopiera**.

4. Stäng guiden genom att klicka på **Stäng**. Om du stänger guiden innan säkerhetskopieringen är klar fortsätter guiden att köras i bakgrunden.

5. Om du säkerhetskopierar filer och mappar på din server, förutom systemtillstånd för Windows Server, kommer guiden Säkerhetskopiering nu bara säkerhetskopiera filer. Använd följande PowerShell-kommando för att utföra en ad hoc-systemtillstånd säkerhetskopiera:

    ```
    PS C:\> Start-OBSystemStateBackup
    ```

  När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i säkerhetskopieringskonsolen.

  ![IR slutfört](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Följande frågor och svar kan du ange ytterligare information.

### <a name="what-is-the-staging-volume"></a>Vad är mellanlagring volymen?

Mellanlagring volymen representerar den mellanliggande platsen där den inbyggt, Windows Server Backup skapar säkerhetskopian av systemtillståndet. Azure Backup-agenten och sedan komprimeras och krypterar mellanliggande säkerhetskopian och skickar den via säker HTTPS-protokoll till konfigurerade Recovery Services-valvet. **Vi rekommenderar starkt att du etablerar mellanlagring volymen i en Windows OS-volym. Om du upptäcker problem med säkerhetskopiering av systemtillståndet, Kontrollera platsen för mellanlagring volymen är det första steget för felsökning.** 

### <a name="how-can-i-change-the-staging-volume-path-specified-in-the-azure-backup-agent"></a>Hur kan jag ändra mellanlagring volym sökvägen som anges i Azure Backup-agenten?

Mellanlagring volymen finns som standard i cachemappen. 

1. Om du vill ändra den här platsen, använder du följande kommando (i en upphöjd kommandotolk):
  ```
  PS C:\> Net stop obengine
  ```

2. Uppdatera följande registerposter med sökvägen till mappen mellanlagring volym.

  |Sökväg i registret|Registernyckel|Värde|
  |-------------|------------|-----|
  |HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | SSBStagingPath | nya mellanlagringsplatsen volym |

Sökvägen till mellanlagring är skiftlägeskänsligt och måste vara exakt samma skiftläge som det som finns på servern. 

3. Starta om motorn för säkerhetskopiering när du har ändrat volymsökväg mellanlagring:
  ```
  PS C:\> Net start obengine
  ```
4. Öppna Microsoft Azure Recovery Services-agenten för att hämta upp den ändrade sökvägen och Utlös en ad hoc-säkerhetskopiering av systemtillstånd.

### <a name="why-is-the-system-state-default-retention-set-to-60-days"></a>Varför anges systemtillstånd Standardkvarhållning till 60 dagar?

En säkerhetskopia av systemtillståndet livslängd är samma som ”tombstone-livslängden” för rollen Windows Server Active Directory. Standardvärdet för posten tombstone-livslängden är 60 dagar. Det här värdet kan ställas in på konfigurationsobjekt för Directory Service (NTDS).

### <a name="how-do-i-change-the-default-backup-and-retention-policy-for-system-state"></a>Hur kan jag ändra standard säkerhetskopiering och bevarandeprincip för systemtillstånd?

Ändra standard säkerhetskopiering och bevarandeprincip för systemtillstånd:
1. Stäng av motorn för säkerhetskopiering. Kör följande kommando från en upphöjd kommandotolk.

  ```
  PS C:\> Net stop obengine
  ```

2. Lägg till eller uppdatera följande viktiga registerposter i HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.

  |Namn i registret|Beskrivning|Värde|
  |-------------|-----------|-----|
  |SSBScheduleTime|Används för att konfigurera tidpunkten för säkerhetskopiering. Standardvärdet är 21: 00 lokaltid.|DWord: Formatet HHMM (decimalt) till exempel 2130 för 9:30:00 lokal tid|
  |SSBScheduleDays|Används för att konfigurera de dagar då systemtillstånd måste utföras på den angivna tiden. Enskilda siffror Ange dagar i veckan. 0 representerar söndag, 1 är måndag och så vidare. Standard dag för säkerhetskopiering är söndag.|DWord: dagar i veckan för att köra säkerhetskopiering (decimalt), till exempel 1230 schemalägger säkerhetskopieringar på måndag, tisdag, onsdag och söndag.|
  |SSBRetentionDays|Används för att konfigurera dagar för att bevara säkerhetskopior. Standardvärdet är 60. Högsta tillåtna värdet är 180.|DWord: Dagar att behålla säkerhetskopiering (decimalt).|

3. Använd följande kommando för att starta om säkerhetskopieringen motorn.
    ```
    PS C:\> Net start obengine
    ```

4. Öppna Microsoft Recovery Services-agenten.

5. Klicka på **schemalägga säkerhetskopiering** och klicka sedan på **nästa** tills du ser ändringarna återges.

6. Klicka på **Slutför** att tillämpa ändringarna.


## <a name="questions"></a>Frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
* Få mer information om hur du [säkerhetskopierar Windows-datorer](backup-configure-vault.md).
* Nu när du har säkerhetskopierat dina filer och mappar kan du [hantera dina valv och servrar](backup-azure-manage-windows-server.md).
* Om du behöver återställa en säkerhetskopia använder du den här artikeln för att [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).

---
title: Säkerhetskopiera Windows-datorer med Azure Backup MARS-agenten
description: Säkerhetskopiera Windows-datorer med hjälp av Azure Backup Microsoft Recovery Services MARS-agenten.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: raynew
ms.openlocfilehash: 5558fbc3ecaad2ae3ca7fce7da57b1f0fed9081b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451815"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Säkerhetskopiera Windows-datorer med Azure Backup MARS-agenten

Den här artikeln förklarar hur du säkerhetskopierar Windows-datorer med hjälp av den [Azure Backup](backup-overview.md) -tjänsten och Microsoft Azure Recovery Services MARS-agenten, även kallat Azure Backup-agenten.

I den här artikeln lär du dig hur du: 


> [!div class="checklist"]
> * Verifiera kraven och skapa ett Recovery Services-valv.
> * Ladda ned och installera MARS-agenten
> * Skapa en princip för säkerhetskopiering och schema. 
> * Utföra en ad hoc säkerhetskopiera.

## <a name="about-the-mars-agent"></a>Om MARS-agenten

MARS-agenten används av Azure Backup för att säkerhetskopiera filer, mappar och systemtillstånd från lokala datorer och virtuella Azure-datorer till säkerhetskopieringsvalvet i Azure Recovery Services. Du kan köra agenten på följande sätt:

- Kör agenten direkt på den lokala Windows-datorer så att de kan säkerhetskopiera direkt till ett säkerhetskopieringsvalv i Azure Recovery Services.
- Kör agenten virtuella Azure-datorer som kör Windows (sida-vid-sida med tillägget Azure VM backup) för säkerhetskopiering av specifika filer och mappar på den virtuella datorn.
- Kör agenten på en Microsoft Azure Backup Server (MABS) eller en System Center Data Protection - server Manager (DPM). I det här scenariot säkerhetskopiera datorer och arbetsbelastningar till MABS/DPM och MABS/DPM säkerhetskopierar upp till ett valv i Azure med hjälp av MARS-agenten.
Vad du kan säkerhetskopiera beror på där agenten är installerad.

> [!NOTE]
> Det är den primära metoden för att säkerhetskopiera virtuella Azure-datorer med hjälp av ett Azure Backup-tillägg på den virtuella datorn. Säkerhetskopierar hela VM. Du kanske vill installera och använda MARS-agenten tillsammans med tillägget om du vill säkerhetskopiera vissa filer och mappar på den virtuella datorn. [Läs mer](backup-architecture.md#architecture-direct-backup-of-azure-vms).



![Säkerhetskopieringsprocessen steg](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Innan du börjar

- [Lär dig hur](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) Azure Backup säkerhetskopierar Windows-datorer med MARS-agenten.
- [Lär dig mer om](backup-architecture.md#architecture-back-up-to-dpmmabs) arkitektur för säkerhetskopiering körs MARS-agenten på en sekundär MABS eller DPM-server.
- [Granska](backup-support-matrix-mars-agent.md) vad stöds och vad kan säkerhetskopieras med MARS-agenten.
- Kontrollera åtkomst för datorer som du vill säkerhetskopiera.
- Om du vill säkerhetskopiera en server eller klient till Azure, behöver du ett Azure-konto. Om du inte har någon kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter.


### <a name="verify-internet-access"></a>Kontrollera åtkomst till internet

Om din dator har begränsad tillgång till internet, kontrollerar du att brandväggsinställningarna på den datorn eller proxyservern tillåter dessa webbadresser: 

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Recovery Services-valvet lagrar alla säkerhetskopior och återställningspunkter som du skapar med tiden och innehåller säkerhetskopieringspolicyn som tillämpas på säkerhetskopierade datorerna. Skapa ett valv enligt följande:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med din Azure-prenumeration.
2. Skriv i Sök **återställningstjänster** och klicka på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png)

3. På den **Recovery Services-valv** -menyn klickar du på **+ Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.

5. Välj Azure-prenumeration, resursgrupp och geografiska region där valvet ska skapas. Säkerhetskopierade data skickas till valvet. Klicka sedan på **Skapa**.

    ![Skapa Recovery Services-valv (steg 3)](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

  Det kan ta flera minuter att skapa valvet. Övervaka Statusmeddelandena i portalen. När valvet har skapats visas den i listan över Recovery Services-valv. Om du inte ser valvet efter ett par minuter, klickar du på **uppdatera**.

      ![Klicka på Uppdatera](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

  

### <a name="set-storage-redundancy"></a>Ange lagringsredundans

Azure Backup hanterar automatiskt lagring för valvet. Du måste ange hur den lagringen ska replikeras. 

1. På bladet **Recovery Services-valv** klickar du på det nya valvet. Under den **inställningar** klickar du på **egenskaper**.
2. I **egenskaper**under **Säkerhetskopieringskonfigurationen**, klickar du på **uppdatering**.

  
4. Välj lagringstyp för replikering och klicka på **spara**.

  ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

  - Vi rekommenderar som om du använder Azure som en slutpunkt för primär lagring av säkerhetskopior fortsätter att använda standard **Geo-redundant** inställningen.
  - Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
  - Läs mer om [geo](../storage/common/storage-redundancy-grs.md) och [lokala](../storage/common/storage-redundancy-lrs.md) redundans.

## <a name="download-the-mars-agent"></a>Hämta MARS-agenten

Hämta MARS-agenten installeras på datorer som du vill säkerhetskopiera.

- Om du har redan installerat agenten på alla datorer kan du kontrollera att du kör den senaste versionen. 
- Den senaste versionen är tillgänglig i portalen eller med en [direkt download](https://aka.ms/azurebackup_agent)

1. I valvet under **komma igång**, klickar du på **Backup**.

  ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)


2. I **var körs din arbetsbelastning?** väljer **lokala**. Du bör välja det här alternativet, även om du vill installera MARS-agenten på en Azure VM.
3. I **vad vill du säkerhetskopiera?** väljer **filer och mappar** och/eller **systemtillstånd**. Det finns ett antal andra alternativ, men dessa stöds endast om du kör en sekundär server för säkerhetskopiering. Klicka på **förbereda infrastrukturen**.

  ![Konfigurera filer och mappar](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)


4. På den **Förbered infrastruktur**under **installera Recovery Services-agenten**, hämta MARS-agenten.

  ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. På snabbmenyn för hämtningen klickar du på **Spara**. Som standard sparas filen **MARSagentinstaller.exe** i mappen för nedladdningar. 
  
6. Kontrollera nu **redan ladda ned eller använda den senaste Recovery Services-agenten**, och sedan hämta autentiseringsuppgifterna för valvet. 
  ![ladda ned valvautentiseringsuppgifter](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Klicka på **Spara**. Filen laddas ned till mappen. Du kan inte öppna filen med valvautentiseringsuppgifter. 
    
  

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

1. Kör den **MARSagentinstaller.exe** fil på datorer som du vill säkerhetskopiera.
2. I MARS Agent Setup Wizard > **installationsinställningar**, ange var du vill installera agenten och en plats som ska användas för cachen. Klicka sedan på **Nästa**.
    - Azure Backup använder cachen för att lagra ögonblicksbilder av data innan de skickas till Azure.
    - Cacheplatsen bör ha ledigt utrymme som motsvarar minst 5% av storleken på de data du säkerhetskopierar.

    ![MARS guiden installationsinställningar](./media/backup-configure-vault/mars1.png)

2. I **proxykonfiguration**, anger hur agenten som körs på Windows-datorn ska ansluta till internet. Klicka sedan på **Nästa**.

    - Om du använder med en anpassad proxy ange proxyinställningar och autentiseringsuppgifter om det behövs.
    - Kom ihåg att agenten behöver åtkomst till [dessa URL: er](#verify-internet-access).

    ![Internetåtkomst för MARS-guiden](./media/backup-configure-vault/mars2.png)

3. I **Installation** granska kravkontrollen och klicka på **installera**.
4. När agenten är installerad klickar du på **fortsätta till registreringen**.
5. I den **guiden för serverregistrering** > **Vault identifiering**bläddrar du och väljer du laddade ned filen med autentiseringsuppgifter. Klicka sedan på **Nästa**.

    ![Register - autentiseringsuppgifterna för valvet](./media/backup-configure-vault/register1.png)

6. I **krypteringsinställning**, ange en lösenfras som används för att kryptera och dekryptera säkerhetskopior för datorn. 
    
    - Spara krypteringslösenfrasen på en säker plats.
    - Om du förlorar eller glömmer lösenfrasen kan Microsoft inte hjälpa dig att återställa dina säkerhetskopierade data. Spara filen på en säker plats. Du behöver den för att återställa en säkerhetskopia.

7. Klicka på **Slutför**. Nu installeras agenten och datorn registreras i valvet. Nu kan du konfigurera och schemalägga säkerhetskopieringen.


## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy
Säkerhetskopieringspolicyn anger när du ska ta ögonblicksbilder av data för att skapa återställningspunkter, och hur lång tid att behålla återställningspunkter. 

- Du kan konfigurera en princip för säkerhetskopiering med MARS-agenten.
- Azure Backup beakta inte automatiskt sommartid (DST). Det kan leda till vissa avvikelse mellan faktiska tid och schema för säkerhetskopiering. 

Skapa en princip på följande sätt:

1. Öppna MARS-agenten på varje dator. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.
2. I **åtgärder**, klickar du på **Schemalägg säkerhetskopiering**.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)

3. I guiden Schemalägg säkerhetskopiering > **komma igång**, klickar du på **nästa**.
4. I **Välj objekt som ska säkerhetskopieras**, klickar du på **Lägg till objekt**.
5. I **Välj objekt**, väljer du vill säkerhetskopiera. Klicka sedan på **OK**.
6. I **Välj objekt som ska säkerhetskopieras** klickar du på **nästa**.
7. I **Ange schema för säkerhetskopiering** anger när du vill göra dagliga och veckovisa säkerhetskopior. Klicka sedan på **Nästa**. 

    - En återställningspunkt skapas när en säkerhetskopia görs.
    - Antalet återställningspunkter som skapats i din miljö är beroende av schemat för säkerhetskopiering.

1. Du kan schemalägga dagliga säkerhetskopior, upp till tre gånger per dag. Skärmbilden visar till exempel två dagliga säkerhetskopior, en vid midnatt och en vid 18: 00.

    ![Dagsschema](./media/backup-configure-vault/day-schedule.png)

9. Du kan köra veckovisa säkerhetskopior för. Skärmbilden visar exempelvis säkerhetskopieringar som gjorts varje alternativ söndag & onsdag kl. 9:30 och 01:00:00.

    ![Schema för veckovis](./media/backup-configure-vault/week-schedule.png)  

8. På den **välja bevarandeprincip** anger du hur du lagrar historisk kopior av dina data. Klicka sedan på **Nästa**.


    - Kvarhållningsinställningar ange vilka återställningspunkter som ska lagras och hur länge de ska lagras för.
    - Till exempel när du anger en daglig kvarhållningsinställning kan ange du att den senaste återställningspunkten på den tid som anges under den dagliga kvarhållningsperioden ska behållas för det angivna antalet dagar. Eller ett annat exempel kan du ange en månatlig bevarandeprincip som indikerar att den återställningspunkt som skapats på 30 i varje månad ska lagras i 12 månader.
    - Dagliga och veckovisa återställningspunkt för kvarhållning är vanligtvis i linje med schemat för säkerhetskopiering. Vilket innebär att när säkerhetskopieringen utlöses enligt schema, lagras den återställningspunkt som skapats av säkerhetskopian för den tid som anges under varje dag eller varje vecka bevarandeprincip.
    - Till exempel i följande skärmbild:
        - Dagliga säkerhetskopior vid midnatt och 18: 00 hålls i sju dagar.
        - Säkerhetskopieringar på en lördag vid midnatt och 18: 00 hålls i 4 veckor.
        - Säkerhetskopieringar på lördag på den sista veckan i månaden vid midnatt och 18: 00 hålls i 12 månader. -Säkerhetskopior som har gjorts på en lördag i den sista veckan i mars hålls för 10 år. 

        ![Kvarhållning exempel](./media/backup-configure-vault/retention-example.png)  

11. I **Välj inledande säkerhetskopieringstyp** anger du hur du utför första backup, via nätverket eller offline. Klicka sedan på **Nästa**.


10. I **bekräftelse**, granskar du informationen och klicka sedan på **Slutför**.
11. När guiden har skapat säkerhetskopieringsschemat klickar du på **Stäng**.

### <a name="perform-the-initial-backup-offline"></a>Utför inledande säkerhetskopiering offline

Du kan köra en inledande säkerhetskopiera automatiskt över nätverket eller offline. Det är användbart om du har stora mängder data som kräver massor av nätverksbandbredden och överföra offlineseeding för någon säkerhetskopia. Du gör en offline överföring på följande sätt:

1. Du kan skriva dina säkerhetskopierade data till en mellanlagringsplats.
2. Du kan använda verktyget AzureOfflineBackupDiskPrep för att kopiera data från mellanlagringsplatsen till en eller flera SATA-diskar.
3. Verktyget skapar en Azure-importjobbet. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) om Azure import och export.
4. Du skickar SATA-diskar till ett Azure-datacenter.
5. I datacentret kopieras diskens data till ett Azure storage-konto.
6. Azure Backup kopierar data från storage-kontot till valvet och inkrementella säkerhetskopieringar är schemalagda.

[Läs mer](backup-azure-backup-import-export.md) om offlineseeding.

### <a name="enable-network-throttling"></a>Aktivera nätverksbegränsning

Du kan styra hur nätverksbandbredden används av MARS-agenten genom att aktivera nätverksbegränsning. Begränsning är användbart om du vill säkerhetskopiera data under arbetstid, men vill styra hur mycket bandbredd som används för säkerhetskopiering och Återställ aktivitet. 

- Azure Backup nätverksbegränsning använder [tjänstkvalitet (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) på det lokala operativsystemet.
- Nätverksbegränsning för säkerhetskopiering är tillgänglig på Windows Server 2008 R2 och senare och Windows 7 och senare. Operativsystem som ska köras med senaste servicepack.

Aktivera begränsning på följande sätt:

1. I MARS-agenten klickar du på **ändra egenskaper för**.
2. På den **begränsning** fliken Kontrollera **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder**.

    ![Nätverksbegränsning](./media/backup-configure-vault/throttling-dialog.png)
3. Ange tillåtna bandbredd vid arbete och utanför arbetstid. Bandbreddsvärden börjar på 512 kbit/s och gå upp till 1,023 Mbit/s. Klicka sedan på **OK**.

## <a name="run-an-ad-hoc-backup"></a>Köra en ad hoc-säkerhetskopiering 

1. I MARS-agenten klickar du på **Säkerhetskopiera nu**. Detta startar den inledande replikeringen över nätverket.

    ![Säkerhetskopiera Windows Server nu](./media/backup-configure-vault/backup-now.png)
2. I **bekräftelse**, granskar du inställningarna och klicka på **säkerhetskopiera**.
3. Stäng guiden genom att klicka på **Stäng**. Om du gör detta innan säkerhetskopieringen är klar fortsätter guiden att köras i bakgrunden.

När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i säkerhetskopieringskonsolen.

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du](backup-azure-restore-windows-server.md) återställa filer.

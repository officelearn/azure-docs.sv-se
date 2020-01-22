---
title: Säkerhetskopiera Windows-datorer med MARS-agenten
description: Använd MARS-agenten (Azure Backup Microsoft Recovery Services) för att säkerhetskopiera Windows-datorer.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: c6e5ea1ed1ec9dd922793dfc6834238c431ddc38
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "76290877"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Säkerhetskopiera Windows-datorer med Azure Backup MARS-agenten

Den här artikeln beskriver hur du säkerhetskopierar Windows-datorer med hjälp av tjänsten [Azure Backup](backup-overview.md) och Microsoft Azure Recovery Services (mars), även kallat Azure Backup agenten.

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
>
> * Kontrol lera kraven och skapa ett Recovery Services-valv.
> * Ladda ned och installera MARS-agenten
> * Skapa en säkerhets kopierings princip och ett schema.
> * Utföra en säkerhets kopiering på begäran.

## <a name="about-the-mars-agent"></a>Om MARS-agenten

MARS-agenten används av Azure Backup för att säkerhetskopiera filer, mappar och system tillstånd från lokala datorer och virtuella Azure-datorer till ett säkerhets kopierings Recovery Services valv i Azure. Du kan köra agenten på följande sätt:

* Kör agenten direkt på lokala Windows-datorer så att de kan säkerhetskopiera direkt till ett säkerhets kopierings Recovery Services valv i Azure.
* Kör agenten på virtuella Azure-datorer som kör Windows (sida-vid-sida med tillägget Azure VM backup) för att säkerhetskopiera vissa filer och mappar på den virtuella datorn.
* Kör agenten på en Microsoft Azure Backup Server (MABS) eller en System Center Data Protection-Manager-Server (DPM). I det här scenariot säkerhetskopierar datorer och arbets belastningar till MABS/DPM och sedan säkerhetskopierar MABS/DPM upp till ett valv i Azure med MARS-agenten.
Vad du kan säkerhetskopiera beror på var agenten är installerad.

> [!NOTE]
> Den primära metoden för att säkerhetskopiera virtuella Azure-datorer är att använda ett Azure Backup-tillägg på den virtuella datorn. Detta säkerhetskopierar hela den virtuella datorn. Du kanske vill installera och använda MARS-agenten tillsammans med tillägget om du vill säkerhetskopiera vissa filer och mappar på den virtuella datorn. [Läs mer](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Steg i säkerhets kopierings processen](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Innan du börjar

* [Lär dig hur](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Azure Backup säkerhetskopierar Windows-datorer med MARS-agenten.
* [Lär dig mer om](backup-architecture.md#architecture-back-up-to-dpmmabs) säkerhets kopierings arkitekturen som kör mars-agenten på en sekundär Mabs eller DPM-server.
* [Granska](backup-support-matrix-mars-agent.md) vad som stöds och vad som kan säkerhets kopie ras med mars-agenten.
* Verifiera Internet åtkomst på de datorer som du vill säkerhetskopiera.
* Om du vill säkerhetskopiera en server eller klient till Azure behöver du ett Azure-konto. Om du inte har någon kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) på bara några minuter.

### <a name="verify-internet-access"></a>Verifiera Internet åtkomst

Om datorn har begränsad Internet åtkomst kontrollerar du att brand Väggs inställningarna på datorn eller proxyservern tillåter dessa URL: er och IP-adress:

#### <a name="urls"></a>Webbadresser

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>IP-adresser

* 20.190.128.0/18
* 40.126.0.0/18

Åtkomst till alla webb adresser och IP-adresser som anges ovan använder HTTPS-protokollet på port 443.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv lagrar alla säkerhets kopior och återställnings punkter som du skapar med tiden och innehåller den säkerhets kopierings princip som tillämpas på säkerhetskopierade datorer. Skapa ett valv på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med din Azure-prenumeration.

2. Sök efter och välj **Recovery Services valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. Klicka på **+ Lägg till**på menyn **Recovery Services valv** .

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. I **Namn** anger du ett eget namn som identifierar valvet.

   * Namnet måste vara unikt för Azure-prenumerationen.
   * Det kan innehålla 2 till 50 tecken.
   * Det måste börja med en bokstav och får bara innehålla bokstäver, siffror och bindestreck.

5. Välj den Azure-prenumeration, resurs grupp och geografiska region där valvet ska skapas. Säkerhetskopierade data skickas till valvet. Klicka sedan på **Skapa**.

    ![Skapa Recovery Services-valv (steg 3)](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * Det kan ta en stund innan valvet har skapats.
   * Övervaka status meddelanden i det övre högra avsnittet i portalen. Om du inte ser valvet igen efter några minuter klickar du på **Uppdatera**.

     ![Klicka på Uppdatera](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Ange redundans för lagring

Azure Backup hanterar automatiskt lagring för valvet. Du måste ange hur lagringen ska replikeras.

1. På bladet **Recovery Services-valv** klickar du på det nya valvet. Under avsnittet **Inställningar** klickar du på **Egenskaper**.
2. I **Egenskaper**, under **säkerhets kopierings konfiguration**, klickar du på **Uppdatera**.

3. Välj typ av lagrings replikering och klicka på **Spara**.

      ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* Vi rekommenderar att om du använder Azure som primär slut punkt för lagring av säkerhets kopior fortsätter du att använda standardvärdet **Geo-redundant** .
* Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
* Lär dig mer om [geo](../storage/common/storage-redundancy-grs.md) och [lokal](../storage/common/storage-redundancy-lrs.md) redundans.

## <a name="download-the-mars-agent"></a>Hämta MARS-agenten

Ladda ned MARS-agenten för installation på datorer som du vill säkerhetskopiera.

* Om du redan har installerat agenten på alla datorer kontrollerar du att du kör den senaste versionen.
* Den senaste versionen är tillgänglig i portalen eller med en [direkt hämtning](https://aka.ms/azurebackup_agent)

1. I valvet, under **komma igång**, klickar du på **säkerhets kopiering**.

    ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. I **var kommer din arbets belastning att köras?** väljer du **lokalt**. Du bör välja det här alternativet även om du vill installera MARS-agenten på en virtuell Azure-dator.
3. I **vad vill du säkerhetskopiera? väljer du** **filer och mappar** och/eller **system tillstånd**. Det finns ett antal andra alternativ som är tillgängliga, men dessa stöds endast om du kör en sekundär säkerhets kopierings Server. Klicka på **Förbered infrastruktur**.

      ![Konfigurera filer och mappar](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Ladda ned MARS-agenten under **installera Recovery Services agent**på den **förbereda infrastrukturen**.

    ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. På snabbmenyn för hämtningen klickar du på **Spara**. Som standard sparas filen **MARSagentinstaller.exe** i mappen för nedladdningar.

6. Nu kan du kontrol lera **att du redan har laddat ned eller använt den senaste Recovery Services agenten**och sedan hämta autentiseringsuppgifterna för valvet.

    ![hämta autentiseringsuppgifter för valvet](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Klicka på **Spara**. Filen laddas ned till download-mappen. Du kan inte öppna filen med autentiseringsuppgifter för valvet.

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

1. Kör filen **MARSagentinstaller. exe** på datorer som du vill säkerhetskopiera.
2. I installations guiden för MARS agent > **installations inställningar**anger du var du vill installera agenten och en plats som ska användas för cacheminnet. Klicka sedan på **Nästa**.
   * Azure Backup använder cacheminnet för att lagra ögonblicks bilder av data innan de skickas till Azure.
   * Cacheplatsen måste ha ett ledigt utrymme som motsvarar minst 5% av storleken på de data som du säkerhetskopierar.

    ![Installations inställningar för MARS-guiden](./media/backup-configure-vault/mars1.png)

3. I **proxykonfiguration**anger du hur agenten som körs på Windows-datorn ska ansluta till Internet. Klicka sedan på **Nästa**.

   * Om du använder en anpassad proxyserver anger du proxyinställningarna och autentiseringsuppgifter om det behövs.
   * Kom ihåg att agenten behöver åtkomst till [dessa URL: er](#verify-internet-access).

     ![Guiden för MARS-guiden Internet åtkomst](./media/backup-configure-vault/mars2.png)

4. I **Installera** granskar du krav kontrollen och klickar på **Installera**.
5. När agenten har installerats klickar du på **Fortsätt till registreringen**.
6. I **guiden Registrera Server** > **valv identifiering**bläddrar du till och väljer den fil med autentiseringsuppgifter som du laddade ned. Klicka sedan på **Nästa**.

    ![Registrera-valv-autentiseringsuppgifter](./media/backup-configure-vault/register1.png)

7. I **krypterings inställning**anger du en lösen fras som ska användas för att kryptera och dekryptera säkerhets kopior för datorn.

    * Spara krypteringslösenfrasen på en säker plats.
    * Om du tappar bort lösen frasen kan Microsoft inte hjälpa till att återställa säkerhetskopierade data. Spara filen på en säker plats. Du behöver den för att återställa en säkerhets kopia.

8. Klicka på **Slutför**. Nu installeras agenten och datorn registreras i valvet. Nu kan du konfigurera och schemalägga säkerhetskopieringen.

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

Säkerhets kopierings principen anger när ögonblicks bilder av data ska tas för att skapa återställnings punkter och hur länge återställnings punkter ska sparas.

* Du konfigurerar en säkerhets kopierings policy med hjälp av MARS-agenten.
* Azure Backup tar inte automatiskt sommar tid i kontot. Detta kan orsaka en avvikelse mellan den faktiska tiden och den schemalagda säkerhets kopierings tiden.

Skapa en princip på följande sätt:
1. När du har laddat ned och registrerat MARS-agenten startar du agent konsolen. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.  
2. I **åtgärder**klickar du på **Schemalägg säkerhets kopiering**.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)
3. I guiden Schemalägg säkerhets kopiering > **komma igång**klickar du på **Nästa**.
4. I **Välj objekt som ska säkerhets kopie ras klickar du**på **Lägg till objekt**.

    ![Välj objekt som ska säkerhets kopie ras](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. I **Välj objekt**väljer du vad du vill säkerhetskopiera och klickar på **OK**.

    ![Markerade objekt som ska säkerhets kopie ras](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. I sidan **Välj objekt som ska säkerhets kopie ras** klickar du på **Nästa**.
7. På sidan **Ange säkerhets** kopierings schema anger du när du vill göra en daglig eller veckovis säkerhets kopiering. Klicka sedan på **Nästa**.

    - En återställnings punkt skapas när en säkerhets kopia görs.
    - Antalet återställnings punkter som skapats i din miljö beror på ditt schema för säkerhets kopiering.


8. Du kan schemalägga dagliga säkerhets kopieringar, upp till tre gånger per dag. Skärm bilden visar till exempel två dagliga säkerhets kopieringar, en vid midnatt och en på 6:00 PM.

    ![Dags schema](./media/backup-configure-vault/day-schedule.png)


9. Du kan också köra vecko Visa säkerhets kopieringar. Skärm bilden visar till exempel säkerhets kopior som tagits varje alternativ söndag & onsdag kl. 9:30 AM och 1:00 AM.

    ![Vecko schema](./media/backup-configure-vault/week-schedule.png)


10. På sidan **Välj bevarande princip** anger du hur du lagrar historiska kopior av dina data. Klicka sedan på **Nästa**.

    - Inställningarna för kvarhållning anger vilka återställnings punkter som ska lagras och hur länge de ska lagras.
    - Om du till exempel anger en inställning för daglig kvarhållning anger du att vid den tid som anges för daglig kvarhållning, kommer den senaste återställnings punkten att behållas under det angivna antalet dagar. Eller, som ett annat exempel, kan du ange en bevarande princip per månad för att ange att återställnings punkten som skapades den 30 i varje månad ska lagras i 12 månader.
    - Kvarhållning av återställnings punkter per dag och veckovis sammanfaller vanligt vis med schemat för säkerhets kopiering. Det innebär att när säkerhets kopieringen utlöses enligt schemat, lagras återställnings punkten som skapats av säkerhets kopian för den varaktighet som anges i den dagliga eller veckovis bevarande principen.
    - Som exempel, på följande skärm bild:

        -   Dagliga säkerhets kopieringar vid midnatt och 6:00 är sparade i sju dagar.
        -   Säkerhets kopieringar som gjorts på en lördag vid midnatt och 6:00 PM behålls i fyra veckor.
        -   Säkerhets kopieringar som gjorts på lördag den senaste veckan i månaden vid midnatt och 6:00 PM behålls i 12 månader.
        -   Säkerhets kopieringar som gjorts på en lördag under den senaste veckan i mars behålls i 10 år.

    ![Exempel på kvarhållning](./media/backup-configure-vault/retention-example.png)


11. I **Välj typ av första säkerhets kopiering** bestämmer du om du vill ta den första säkerhets kopian över nätverket eller använda säkerhets kopiering offline (mer information om offline-säkerhetskopiering finns i den här [artikeln](backup-azure-backup-import-export.md)). Om du vill ta den första säkerhets kopieringen över nätverket väljer du **automatiskt över nätverket** och klickar på **Nästa**.

    ![första säkerhets kopierings typ](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. Granska informationen i **bekräftelse**och klicka sedan på **Slutför**.

    ![Bekräfta säkerhets kopierings typ](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. När guiden har skapat säkerhetskopieringsschemat klickar du på **Stäng**.

    ![Bekräfta ändring av säkerhets kopierings process](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Du måste skapa en princip på varje dator där agenten är installerad.

### <a name="perform-the-initial-backup-offline"></a>Utför den första säkerhets kopieringen offline

Du kan köra en första säkerhets kopiering automatiskt via nätverket eller offline. Dirigerings-seeding för en första säkerhets kopiering är användbart om du har stora mängder data som kräver mycket nätverks bandbredd för överföring. Du gör en offline-överföring på följande sätt:

1. Du skriver säkerhets kopierings data till en mellanlagringsplats.
2. Du kan använda verktyget AzureOfflineBackupDiskPrep för att kopiera data från mellanlagringsplatsen till en eller flera SATA-diskar.
3. Verktyget skapar ett Azure-importerat jobb. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) om Azure import och export.
4. Du skickar SATA-diskarna till ett Azure-datacenter.
5. På data centret kopieras disk data till ett Azure Storage-konto.
6. Azure Backup kopierar data från lagrings kontot till valvet och stegvisa säkerhets kopieringar schemaläggs.

[Lär dig mer](backup-azure-backup-import-export.md) om seeding offline.

### <a name="enable-network-throttling"></a>Aktivera nätverks begränsning

Du kan styra hur nätverks bandbredden används av MARS-agenten genom att aktivera nätverks begränsning. Begränsning är användbart om du behöver säkerhetskopiera data under arbets tid, men vill styra hur mycket bandbredd som används för säkerhets kopierings-och återställnings aktivitet.

* Azure Backup nätverks begränsning använder [tjänst kvalitet (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) på det lokala operativ systemet.
* Nätverks begränsning för säkerhets kopiering är tillgängligt på Windows Server 2012 och senare och Windows 8 och senare. Operativ system ska köra de senaste Service Pack-versionerna.

Aktivera nätverks begränsning enligt följande:

1. Klicka på **ändra egenskaper**i mars-agenten.
2. På fliken **begränsning** markerar du **Aktivera användnings begränsning för Internet bandbredd för säkerhets kopierings åtgärder**.

    ![Nätverksbegränsning](./media/backup-configure-vault/throttling-dialog.png)
3. Ange tillåten bandbredd under arbets tid och utanför arbets tid. Bandbredds värden börjar på 512 kbit/s och går upp till 1 023 Mbit/s. Klicka på **OK**.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhets kopiering på begäran

1. I MARS-agenten klickar du på **Säkerhetskopiera nu**.

    ![Säkerhetskopiera Windows Server nu](./media/backup-configure-vault/backup-now.png)

2. Om MARS-agentens version är 2.0.9169.0 eller senare kan en anpassad kvarhållning anges. I avsnittet **Kvarhåll säkerhets kopiering till** väljer du ett datum från den visade kalendern:

   ![Behåll säkerhets kopierings kalender](./media/backup-configure-vault/mars-ondemand.png)

3. Granska inställningarna i **bekräftelse**och klicka på **säkerhetskopiera**.
4. Stäng guiden genom att klicka på **Stäng**. Om du gör detta innan säkerhets kopieringen är klar fortsätter guiden att köras i bakgrunden.
5. När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i säkerhetskopieringskonsolen.

## <a name="on-demand-backup-policy-retention-behavior"></a>Kvarhållning av princip för säkerhets kopiering på begäran

>[!NOTE]
>Gäller endast för MARS agent versioner som är äldre än 2.0.9169.0
>

* Mer information finns i steg 8 i [skapa en säkerhets kopierings princip](backup-configure-vault.md#create-a-backup-policy)

| Schema alternativ för säkerhets kopiering | Hur lång tid kommer de säkerhetskopierade data att behållas?
| -- | --
| Schemalägg en säkerhets kopiering varje: * dag | **Standard kvarhållning**: motsvarar "kvarhållning i dagar för dagliga säkerhets kopieringar" <br/><br/> **Undantag**: om en daglig schemalagd säkerhets kopia för långsiktig kvarhållning (veckor, månader, år) Miss lyckas, Miss lyckas en säkerhets kopiering på begäran som utlöses direkt efter att den här schemalagda säkerhets kopieringen anses vara långsiktig kvarhållning. Annars anses nästa schemalagda säkerhets kopiering för långsiktig kvarhållning.<br/><br/> **Exempel**: om den schemalagda säkerhets kopieringen som utfördes på torsdag 8:00 inte fungerar och samma säkerhets kopia skulle beaktas för varje vecka/månad/år-kvarhållning, så kommer den första säkerhets kopieringen på begäran innan nästa schemalagda säkerhets kopiering (t 8:00. ex. fredag) att bli märkt för varje vecka/månad/år enligt vad som är tillämpligt på torsdag 8:00 am-säkerhetskopiering.
| Schemalägg en säkerhets kopiering varje: * varje vecka | **Standard kvarhållning**: 1 dag <br/> Säkerhets kopieringar på begäran som görs för en data källa med en veckovis säkerhets kopierings princip tas bort nästa dag, även om de är de senaste säkerhets kopiorna för data källan. <br/><br/> **Undantag**: om en schemalagd säkerhets kopia för långsiktig kvarhållning (veckor, månader, år) Miss lyckas, Miss lyckas en säkerhets kopiering på begäran som utlöses direkt efter att den här schemalagda säkerhets kopieringen anses vara långsiktig kvarhållning. Annars anses nästa schemalagda säkerhets kopiering för långsiktig kvarhållning. <br/><br/> **Exempel**: om den schemalagda säkerhets kopieringen som utfördes på torsdag 8:00 inte fungerar och samma säkerhets kopia skulle beaktas för månatlig/årlig kvarhållning, så kommer den första säkerhets kopieringen på begäran innan nästa schemalagda säkerhets kopiering (t. ex 8:00.) att bli märkt för månatlig/årlig kvarhållning enligt vad som är tillämpligt på torsdag 8:00 am-säkerhetskopiering

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du återställer filer](backup-azure-restore-windows-server.md).

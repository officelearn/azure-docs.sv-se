---
title: Offlinesäkerhetskopiering för DPM och Azure Backup Server
description: Med Azure Backup kan du skicka data från nätverket med tjänsten Azure import/export. I den här artikeln beskrivs arbets flödet offline-säkerhetskopiering för DPM och Azure Backup Server (MABS).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 259be99efdef29e3f7971632adf76c03175bba01
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996331"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Arbets flöde för offline-säkerhetskopiering för DPM och Azure Backup Server

Azure Backup har flera inbyggda effektivitets vinster som sparar kostnader för nätverk och lagring under de första fullständiga säkerhets kopieringarna av data till Azure. De första fullständiga säkerhets kopieringarna överför ofta stora mängder data och kräver mer nätverks bandbredd jämfört med efterföljande säkerhets kopieringar som bara överför delta/steg. Azure Backup komprimerar de första säkerhets kopiorna. Genom processen för dirigering av dirigering kan Azure Backup använda diskar för att ladda upp komprimerade första säkerhets kopierings data offline till Azure.

Offline-seeding-processen för Azure Backup är nära integrerad med [Azure import/export-tjänsten](../storage/common/storage-import-export-service.md) som gör att du kan överföra data till Azure med hjälp av diskar. Om du har terabyte (TBs) av inledande säkerhets kopierings data som behöver överföras via ett nätverk med hög latens och låg bandbredd, kan du använda arbets flödet offline-seeding för att leverera den första säkerhets kopian på en eller flera hård diskar till ett Azure-datacenter. Den här artikeln innehåller en översikt och ytterligare information om hur du slutför det här arbets flödet för System Center DPM och Azure Backup Server.

> [!NOTE]
> Processen för säkerhets kopiering offline för Microsoft Azure Recovery Services (MARS)-agenten skiljer sig från System Center DPM och Azure Backup Server. Information om hur du använder offline-säkerhetskopiering med MARS-agenten finns i [den här artikeln](backup-azure-backup-import-export.md). Säkerhets kopiering offline stöds inte för säkerhets kopiering av system tillstånd som gjorts med Azure Backup-agenten.
>

## <a name="overview"></a>Översikt

Med funktionen offline-seeding i Azure Backup och Azure import/export är det enkelt att ladda upp data offline till Azure med hjälp av diskar. Säkerhets kopierings processen offline omfattar följande steg:

> [!div class="checklist"]
>
> * Säkerhetskopierade data, i stället för att skickas över nätverket, skrivs till en *mellanlagringsplats*
> * Data på *mellanlagringsplatsen* skrivs sedan till en eller flera SATA-diskar med *AzureOfflineBackupDiskPrep* -verktyget
> * Ett Azure import-jobb skapas automatiskt av verktyget
> * SATA-enheterna skickas sedan till närmaste Azure-datacenter
> * När överföringen av säkerhets kopierings data till Azure är färdig Azure Backup kopierar säkerhetskopierade data till säkerhets kopierings valvet och de stegvisa säkerhets kopiorna schemaläggs.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Offline-säkerhetskopiering stöds för alla distributions modeller av Azure Backup att externt säkerhetskopiera data från lokala platser till Microsoft Cloud. Detta inkluderar

> [!div class="checklist"]
>
> * Säkerhets kopiering av filer och mappar med Microsoft Azure Recovery Services MARS-agenten eller Azure Backup agenten.
> * Säkerhets kopiering av alla arbets belastningar och filer med System Center Data Protection Manager (SC DPM)
> * Säkerhets kopiering av alla arbets belastningar och filer med Microsoft Azure Backup Server

## <a name="prerequisites"></a>Krav

Kontrol lera att följande krav är uppfyllda innan du initierar arbets flödet offline-säkerhetskopiering

* Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) har skapats. Se stegen i [den här artikeln](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) för att skapa en.
* Azure Backup Agent eller Azure Backup Server eller SC DPM har installerats på antingen Windows Server/Windows-klienten, så som det är tillämpligt och datorn har registrerats med Recovery Services-valvet. Se till att endast den [senaste versionen av Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) används.
* [Hämta filen Azure Publish Settings](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) på den dator som du planerar att säkerhetskopiera dina data från. Den prenumeration som du hämtar publicerings inställnings filen från kan skilja sig från den prenumeration som innehåller Recovery Services valvet. Om din prenumeration är på suveräna Azure-moln kan du använda följande länkar för att ladda ned filen med Azures publicerings inställningar.

    | Region för suveräna moln | Fil länk för Azure Publish-inställningar |
    | --- | --- |
    | USA | [Länk](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kina | [Länk](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Ett Azure Storage konto med den *klassiska* distributions modellen har skapats i den prenumeration som du laddade ned publicerings inställnings filen enligt nedan:

  ![Skapa ett klassiskt lagrings konto](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* En mellanlagringsplats, som kan vara en nätverks resurs eller ytterligare en enhet på datorn, intern eller extern, med tillräckligt disk utrymme för att lagra din ursprungliga kopia skapas. Om du till exempel försöker säkerhetskopiera en fil server på 500 GB måste du kontrol lera att mellanlagringsområdet är minst 500 GB. (En mindre mängd används på grund av komprimering.)
* För diskar som ska skickas till Azure ser du till att endast 2,5-tums SSD, eller 2,5-tums eller 3,5-tums interna hård diskar används. Du kan använda hård diskar upp till 10 TB. Läs [dokumentationen för Azure import/export-tjänsten](../storage/common/storage-import-export-requirements.md#supported-hardware) för den senaste uppsättningen enheter som stöds av tjänsten.
* SATA-enheterna måste vara anslutna till en dator (kallas *kopierings dator*) från den plats där kopian av säkerhets kopierings data från *mellanlagringsplatsen* till SATA-enheterna görs. Kontrol lera att BitLocker är aktiverat på *kopierings datorn*

## <a name="workflow"></a>Arbetsflöde

Informationen i det här avsnittet hjälper dig att slutföra arbets flödet för offline-säkerhetskopiering så att dina data kan skickas till ett Azure-datacenter och laddas upp till Azure Storage. Om du har frågor om import tjänsten eller någon del av processen går du till [översikts dokumentationen för import tjänsten](../storage/common/storage-import-export-service.md) som refereras tidigare.

### <a name="initiate-offline-backup"></a>Starta säkerhets kopiering offline

1. När du schemalägger en säkerhets kopia visas följande skärm (i Windows Server, Windows-klient eller System Center Data Protection Manager).

    ![Sidan importera](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Här är motsvarande skärm i System Center Data Protection Manager: <br/>
    ![SC DPM och Azure Backup Server import](./media/backup-azure-backup-import-export/dpmoffline.png)

    Beskrivningen av indata är följande:

   * **Mellanlagringsplats**: den tillfälliga lagrings plats som den första säkerhets kopian skrivs till. Mellanlagrings platsen kan vara en nätverks resurs eller en lokal dator. Om kopierings datorn och käll datorn skiljer sig åt rekommenderar vi att du anger den fullständiga nätverks Sök vägen för mellanlagringsplatsen.
   * **Namn på Azure import-jobb**: det unika namnet som Azure import service och Azure Backup spåra överföringen av data som skickas på diskar till Azure.
   * **Publicerings inställningar för Azure**: Ange den lokala sökvägen till filen med publicerings inställningar.
   * **ID för Azure-prenumeration**: ID för Azure-prenumerationen för prenumerationen från varifrån du laddade ned filen för Azure Publish-inställningar.
   * **Azure Storage konto**: namnet på det lagrings konto i Azure-prenumerationen som är associerat med Azure Publish Settings-filen.
   * **Azure Storage behållare**: namnet på mål lagrings-bloben i Azure Storage-kontot där säkerhetskopierade data importeras.

     Spara *mellanlagringsplatsen* och det *Azure import Job-namn* som du angav som det krävs för att förbereda diskarna.  

2. Slutför arbets flödet och starta säkerhets kopieringen offline genom att klicka på **Säkerhetskopiera nu** i Azure Backup Agent hanterings konsol. Den första säkerhets kopian skrivs till mellanlagringsområdet som en del av det här steget.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow.png)

    För att slutföra motsvarande arbets flöde i System Center Data Protection Manager eller Azure Backup Server, högerklickar du på **skydds gruppen**och väljer sedan alternativet för att **skapa återställnings punkt** . Sedan väljer du alternativet **onlineskydd** .

    ![SC DPM och Azure Backup Server Säkerhetskopiera nu](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    När åtgärden har slutförts är mellanlagringsplatsen redo att användas för disk förberedelse.

    ![Säkerhets kopierings förlopp](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbered SATA-enheter och leverera till Azure

Verktyget *AzureOfflineBackupDiskPrep* används för att förbereda SATA-enheter som skickas till närmaste Azure-datacenter. Det här verktyget finns i installations katalogen för Recovery Services agenten på följande sökväg:

    *\\Microsoft Azure Recovery Services Agent\\Utils\\*

1. Gå till katalogen och kopiera **AzureOfflineBackupDiskPrep** -katalogen till en kopierings dator där de SATA-enheter som ska förberedas är anslutna. Se till att kopiera datorn med avseende på följande:

   * Kopierings datorn kan komma åt mellanlagringsplatsen för arbets flödet offline-seeding med hjälp av samma nätverks Sök väg som angavs i arbets flödet **initiera säkerhets kopiering offline** .
   * BitLocker är aktiverat på kopierings datorn.
   * Kopierings datorn har åtkomst till Azure Portal.

     Vid behov kan kopierings datorn vara samma som käll datorn.

     > [!IMPORTANT]
     > Om käll datorn är en virtuell dator är det obligatoriskt att använda en annan fysisk server eller klient dator som kopierings dator.

2. Öppna en kommando tolk med förhöjd behörighet på Kopiera datorn med katalogen *AzureOfflineBackupDiskPrep* Utility som den aktuella katalogen och kör följande kommando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parameter | Beskrivning |
    | --- | --- |
    | s: *sökväg till&lt;mellanlagrings plats*&gt; |Obligatoriska indatatyper som används för att ange sökvägen till den mellanlagringsplats som du angav i arbets flödet **Starta säkerhets kopiering offline** . |
    | p:&lt;*sökvägen till PublishSettingsFile*&gt; |Valfria indata som används för att ange sökvägen till filen för **Azure Publish-inställningar** som du angav i arbets flödet **Starta säkerhets kopiering offline** . |

    > [!NOTE]
    > Värdet för &lt;sökvägen till AzurePublishSettingFile&gt; är obligatoriskt när kopierings datorn och käll datorn skiljer sig åt.
    >
    >

    När du kör kommandot begär verktyget valet av Azure import-jobb som motsvarar de enheter som måste förberedas. Om bara ett enda import jobb är associerat med den angivna mellanlagringsplatsen visas en skärm som liknar den som följer.

    ![Inmatade Azure disk Preparation-verktyg](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Ange enhets beteckningen utan avslutande kolon för den monterade disk som du vill förbereda för överföring till Azure. Ange en bekräftelse för enhetens formatering vid uppmaning.

    Verktyget börjar sedan förbereda disken och kopiera säkerhetskopierade data. Du kan behöva bifoga ytterligare diskar när du uppmanas att göra det om den angivna disken inte har tillräckligt med utrymme för säkerhets kopierings data. <br/>

    I slutet av lyckad körning av verktyget är en eller flera diskar som du har angett för beredd för leverans till Azure. Dessutom skapas ett import jobb med det namn som du angav under initiera arbets flödet **initiera offline-säkerhetskopiering** i Azure. Slutligen visar verktyget leverans adressen till det Azure-datacenter där diskarna måste skickas.

    ![Förberedelse av Azure-disk slutförd](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. I slutet av kommando körningen visas även alternativet att uppdatera leverans informationen enligt nedan:

    ![Uppdatera leverans information, alternativ](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Du kan ange information direkt. Verktyget vägleder dig genom processen som omfattar en serie indata. Men om du inte har information som spårnings nummer eller annan information relaterad till leverans kan du avsluta sessionen. De steg som krävs för att uppdatera leverans informationen finns i den här artikeln.

6. Leverera diskarna till den adress som verktyget tillhandahåller och behåll spårnings numret för framtida bruk.

   > [!IMPORTANT]
   > Det går inte att ha samma spårnings nummer på två Azure import-jobb. Se till att enheter som förbereds av verktyget under ett enda Azure import-jobb levereras tillsammans i ett enda paket och att det finns ett enda unikt spårnings nummer för paketet. Kombinera inte enheter som har bearbetats som en del av **olika** Azure import-jobb i ett enda paket.

7. När du har information om spårnings numret går du till käll datorn, som väntar på att importen ska slutföras och kör följande kommando i en upphöjd kommando tolk med *AzureOfflineBackupDiskPrep* Utility-katalogen som den aktuella katalogen:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Du kan också köra följande kommando från en annan dator, t. ex. *kopierings datorn*, med *AzureOfflineBackupDiskPrep* verktygs katalog som den aktuella katalogen:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parameter | Beskrivning |
    | --- | --- |
    | u: | Obligatoriskt indata används för att uppdatera skeppnings information för ett Azure-import jobb |
    | s: *sökväg till&lt;mellanlagrings plats*&gt; | Obligatoriska indata när kommandot inte körs på käll datorn. Används för att ange sökvägen till den mellanlagringsplats som du angav i arbets flödet starta arbets flödet för **offline-säkerhetskopiering** . |
    | p:&lt;*sökvägen till PublishSettingsFile*&gt; | Obligatoriska indata när kommandot inte körs på käll datorn. Används för att ange sökvägen till den fil för **Azure Publish-inställningar** som du angav i arbets flödet starta arbets flödet för **säkerhets kopiering offline** . |

    Verktyget identifierar automatiskt det import jobb som käll datorn väntar på eller de import jobb som är kopplade till mellanlagringsplatsen när kommandot körs på en annan dator. Det ger dig möjlighet att uppdatera leverans information via en serie med indata som visas nedan:

    ![Ange leverans information](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

8. När du har angett alla indata kan du läsa informationen noggrant och bekräfta leverans informationen som du angav genom att skriva *Ja*.

    ![Granska skeppnings information](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

9. När leverans informationen har uppdaterats ger verktyget en lokal plats där leverans informationen som anges av dig lagras som visas nedan

    ![Lagra skeppnings information](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Se till att enheterna når Azure-datacentret inom två veckor från att tillhandahålla leverans informationen med hjälp av *AzureOfflineBackupDiskPrep* -verktyget. Om du inte gör det kan det leda till att enheterna inte bearbetas.  

När du har slutfört stegen ovan är Azure-datacentret redo att ta emot enheterna och bearbeta dem ytterligare för att överföra säkerhets kopierings data från enheterna till det klassiska Azure Storage-konto som du skapade.

### <a name="time-to-process-the-drives"></a>Tid för att bearbeta enheterna

Hur lång tid det tar att bearbeta ett Azure-importerat jobb varierar beroende på olika faktorer, till exempel leverans tid, Jobbtyp, typ och storlek för de data som kopieras och storleken på de diskar som tillhandahålls. Tjänsten Azure import/export har inget service avtal, men när diskarna har tagits emot strävar tjänsten efter att slutföra säkerhets kopieringen av säkerhets kopian till ditt Azure Storage-konto på 7 till 10 dagar. I nästa avsnitt beskrivs hur du kan övervaka statusen för Azure import-jobbet.

### <a name="monitoring-azure-import-job-status"></a>Övervakar status för Azure import-jobb

Medan dina enheter överförs eller på Azure-datacenter för att kopieras till lagrings kontot, visar Azure Backup-agenten eller SC DPM eller Azure Backup Server konsolen på käll datorn följande jobb status för dina schemalagda säkerhets kopieringar.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Följ stegen nedan för att kontrol lera status för import jobb.

1. Öppna en kommando tolk med förhöjd behörighet på käll datorn och kör följande kommando:

     `AzureOfflineBackupDiskPrep.exe u:`

2. I resultatet visas import jobbets aktuella status enligt nedan:

    ![Kontrollerar status för import jobb](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Mer information om de olika tillstånden för Azures import jobb finns i [den här artikeln](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Slutför arbets flödet

När import jobbet är klart är de första säkerhets kopierings data tillgängliga i ditt lagrings konto. Vid nästa schemalagda säkerhets kopiering kopierar Azure Backup innehållet i data från lagrings kontot till Recovery Services valvet som visas nedan:

   ![Kopiera data till Recovery Services Vault](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Vid nästa schemalagda säkerhets kopiering Azure Backup utföra stegvis säkerhets kopiering över den första säkerhets kopian.

## <a name="next-steps"></a>Nästa steg

* Om du har frågor om Azures import/export-arbetsflöde läser du [Använd tjänsten Microsoft Azure import/export för att överföra data till Blob Storage](../storage/common/storage-import-export-service.md).


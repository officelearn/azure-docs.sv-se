---
title: Offlinesäkerhetskopiering för DPM och Azure Backup Server
description: Med Azure Backup kan du skicka data från nätverket med hjälp av Azure Import/Export-tjänsten. I den här artikeln beskrivs arbetsflödet för säkerhetskopiering offline för DPM och Azure Backup Server.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197100"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Arbetsflöde för offlinesäkerhetskopior för DPM och Azure Backup Server

Azure Backup har flera inbyggda effektivitetsvinster som sparar nätverks- och lagringskostnader under de första fullständiga säkerhetskopieringarna av data till Azure. Inledande fullständiga säkerhetskopior överför vanligtvis stora mängder data och kräver mer nätverksbandbredd jämfört med efterföljande säkerhetskopior som bara överför deltas/inkrementella. Azure Backup komprimerar de första säkerhetskopieringarna. Genom processen för offline seedning kan Azure Backup använda diskar för att överföra komprimerade initiala säkerhetskopieringsdata offline till Azure.

Offline-seedningsprocessen för Azure Backup är tätt integrerad med [Azure Import/Export-tjänsten](../storage/common/storage-import-export-service.md). Du kan använda den här tjänsten för att överföra data till Azure med hjälp av diskar. Om du har terabyte (TBs) med inledande säkerhetskopieringsdata som måste överföras via ett nätverk med hög latens och låg bandbredd, kan du använda arbetsflödet för offlineutsädning för att leverera den första säkerhetskopian på en eller flera hårddiskar till ett Azure-datacenter. Den här artikeln innehåller en översikt och ytterligare steg som avslutar det här arbetsflödet för System Center Data Protection Manager (DPM) och Microsoft Azure Backup Server (MABS).

> [!NOTE]
> Processen för offline säkerhetskopiering för Microsoft Azure Recovery Services (MARS) Agent skiljer sig från DPM och MABS. Information om hur du använder offlinesäkerhetskopiering med MARS-agenten finns [i Arbetsflöde för säkerhetskopiering offline i Azure Backup](backup-azure-backup-import-export.md). Offline säkerhetskopiering stöds inte för säkerhetskopiering av systemtillstånd som görs med hjälp av Azure Backup Agent.
>

## <a name="overview"></a>Översikt

Med offline-seedning-funktionen för Azure Backup och Azure Import/Export-tjänsten är det enkelt att överföra data offline till Azure med hjälp av diskar. Säkerhetskopieringen offline omfattar följande steg:

> [!div class="checklist"]
>
> * Säkerhetskopieringsdata skrivs till en mellanlagringsplats i stället för att skickas över nätverket.
> * Data på mellanlagringsplatsen skrivs sedan till en eller flera SATA-diskar med hjälp av *AzureOfflineBackupDiskPrep-verktyget.*
> * Ett Azure-importjobb skapas automatiskt av verktyget.
> * SATA-enheterna skickas sedan till närmaste Azure-datacenter.
> * När överföringen av säkerhetskopieringsdata till Azure är klar kopierar Azure Backup säkerhetskopieringsdata till säkerhetskopieringsvalvet och de inkrementella säkerhetskopiorna schemaläggs.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Offline säkerhetskopiering stöds för alla distributionsmodeller av Azure Backup som säkerhetskopierar data från lokalt till Microsoft-molnet. Dessa modeller inkluderar:

> [!div class="checklist"]
>
> * Säkerhetskopiering av filer och mappar med MARS-agenten eller Azure Backup Agent.
> * Säkerhetskopiering av alla arbetsbelastningar och filer med DPM.
> * Säkerhetskopiering av alla arbetsbelastningar och filer med MABS.

## <a name="prerequisites"></a>Krav

Kontrollera att följande förutsättningar uppfylls innan du startar arbetsflödet för säkerhetskopiering offline:

* Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) har skapats. Om du vill skapa en följer du stegen i [Skapa ett recovery services-valv](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* En Azure Backup Agent eller MABS eller DPM har installerats på antingen Windows Server eller en Windows-klient, beroende på vad som är tillämpligt, och datorn är registrerad i Recovery Services-valvet. Kontrollera att endast den [senaste versionen av Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) används.
* [Hämta filen Azure-publiceringsinställningar](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) på den dator som du planerar att säkerhetskopiera dina data från. Prenumerationen som du hämtar filen för publiceringsinställningar från kan skilja sig från prenumerationen som innehåller valvet för Återställningstjänster. Om din prenumeration finns på suveräna Azure-moln använder du följande länkar som är lämpliga för att hämta filen Azure-publiceringsinställningar.

    | Suverän molnregion | Fillänk för Azure-publiceringsinställningar |
    | --- | --- |
    | USA | [Länk](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kina | [Länk](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Ett Azure-lagringskonto med Resurshanterarens distributionsmodell har skapats i prenumerationen som du hämtade filen för publiceringsinställningar från.

  ![Skapa ett lagringskonto med Resurshanterarens utveckling](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* En mellanlagringsplats, som kan vara en nätverksresurs eller ytterligare enhet på datorn, intern eller extern, med tillräckligt med diskutrymme för att hålla den första kopian, skapas. Om du till exempel vill säkerhetskopiera en 500 GB-filserver kontrollerar du att mellanlagringsområdet är minst 500 GB. (En mindre mängd används på grund av komprimering.)
* För diskar som skickas till Azure kontrollerar du att endast 2,5-tums SSD- eller 2,5-tums eller 3,5-tums SATA II/III-interna hårddiskar används. Du kan använda hårddiskar på upp till 10 TB. Kontrollera [Azure Import/Export-tjänstdokumentationen](../storage/common/storage-import-export-requirements.md#supported-hardware) för den senaste uppsättningen enheter som tjänsten stöder.
* SATA-enheterna måste vara anslutna till en dator (kallad *en kopia dator)* från där kopian av säkerhetskopierade data från mellanlagringsplatsen till SATA-enheterna görs. Kontrollera att BitLocker är aktiverat på kopieringsdatorn.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Förbereda servern för säkerhetskopiering av offline

>[!NOTE]
> Om du inte hittar de angivna verktygen, till exempel *AzureOfflineBackupCert.exe*, i din AskAzureBackupTeam@microsoft.com installation av MARS-agenten, skriver du för att få åtkomst till dem.

* Öppna en upphöjd kommandotolk på servern och kör följande kommando:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Verktyget skapar ett Active Directory-program för Azure Offline Backup om det inte finns något.

    Om det redan finns ett program ber den här körbara filen dig att manuellt överföra certifikatet till programmet i klienten. Följ stegen i [det här avsnittet](#manually-upload-an-offline-backup-certificate) för att överföra certifikatet manuellt till programmet.

* *AzureOfflineBackup.exe-verktyget* genererar en *OfflineApplicationParams.xml-fil.* Kopiera filen till servern med MABS eller DPM.
* Installera den [senaste MARS-agenten](https://aka.ms/azurebackup_agent) på DPM-instansen eller Azure Backup-servern.
* Registrera servern till Azure.
* Kör följande kommando:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* Det föregående kommandot skapar `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`filen .

## <a name="manually-upload-an-offline-backup-certificate"></a>Ladda upp ett offlinecertifikat manuellt

Följ dessa steg för att manuellt överföra offline säkerhetskopieringscertifikatet till ett tidigare skapat Azure Active Directory-program avsett för offline säkerhetskopiering.

1. Logga in på Azure Portal.
1. Gå till **Azure Active Directory** > **App registreringar**.
1. Leta reda på ett program med visningsnamnsformatet `AzureOfflineBackup _<Azure User Id`på fliken **Ägda program** .

    ![Leta upp program på fliken Ägda program](./media/backup-azure-backup-import-export/owned-applications.png)

1. Välj programmet. Gå till **Certifikat & hemligheter under** **Hantera** i den vänstra rutan .
1. Kontrollera om det finns redan befintliga certifikat eller offentliga nycklar. Om det inte finns några kan du ta bort programmet på ett säkert sätt genom att välja knappen **Ta bort** på programmets **översiktssida.** Sedan kan du försöka igen stegen för att [förbereda servern för offline säkerhetskopieringsprocessen](#prepare-the-server-for-the-offline-backup-process) och hoppa över följande steg. Annars fortsätter du att följa dessa steg från DPM-instansen eller Azure Backup-servern där du vill konfigurera offlinesäkerhetskopiering.
1. Välj fliken **Hantera datorcertifikatprogram** > **Personligt.** Leta efter `CB_AzureADCertforOfflineSeeding_<ResourceId>`certifikatet med namnet .
1. Markera certifikatet, högerklicka på **Alla aktiviteter**och välj sedan **Exportera**, utan en privat nyckel, i .cer-format.
1. Gå till Azure offline backup-programmet i Azure-portalen.
1. Välj **Hantera** > **certifikat & hemligheter** > **Ladda upp certifikat**. Ladda upp det exporterade certifikatet i föregående steg.

    ![Ladda upp certifikatet](./media/backup-azure-backup-import-export/upload-certificate.png)

1. Öppna registret på servern genom att ange **regedit** i körningsfönstret.
1. Gå till registerposten *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Högerklicka på **CloudBackupProvider**och lägg till ett `AzureADAppCertThumbprint_<Azure User Id>`nytt strängvärde med namnet .

    >[!NOTE]
    > Så här hittar du Azure-användar-ID:
    >
    >* Kör `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` kommandot från Azure-anslutna PowerShell.
    >* Gå till registersökvägen `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`.

1. Högerklicka på strängen som lagts till i föregående steg och välj **Ändra**. I värdet anger du tumavtrycket för det certifikat som du exporterade i steg 7. Välj sedan **OK**.
1. Om du vill hämta tumavtryckets värde dubbelklickar du på certifikatet. Välj fliken **Detaljer** och rulla nedåt tills du ser tumavtrycksfältet. Välj **Tumavtryck**och kopiera värdet.

    ![Kopiera värde från tumavtrycksfältet](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. Fortsätt till avsnittet [Arbetsflöde](#workflow) för att fortsätta med säkerhetskopieringen offline.

## <a name="workflow"></a>Arbetsflöde

Informationen i det här avsnittet hjälper dig att slutföra arbetsflödet för offlinesäkerhetskopiering så att dina data kan levereras till ett Azure-datacenter och överföras till Azure Storage. Om du har frågor om importtjänsten eller någon aspekt av processen läser du [dokumentationen för översikt för importtjänsten](../storage/common/storage-import-export-service.md) som refererades tidigare.

### <a name="initiate-offline-backup"></a>Initiera offline säkerhetskopiering

1. När du schemalägger en säkerhetskopia visas följande sida i Windows Server, en Windows-klient eller DPM.

    ![Sidan Importera](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Här är motsvarande sida i DPM. <br/>
    
    ![Importsida för DPM och Azure Backup Server](./media/backup-azure-backup-import-export/dpmoffline.png)

    Rutorna som du fyller i är:

   * **Mellanlagringsplats**: Den tillfälliga lagringsplatsen som den första säkerhetskopian skrivs till. Mellanlagringsplatsen kan finnas på en nätverksresurs eller en lokal dator. Om kopiatorn och källdatorn är olika anger du den fullständiga nätverkssökvägen för mellanlagringsplatsen.
   * **Azure Publish Settings**: Den lokala sökvägen till filen publicera inställningar.
   * **Azure Import Job Name**: Det unika namn som Azure Import/Export-tjänsten och Azure Backup spårar överföringen av data som skickas på diskar till Azure.
   * **Azure-prenumerations-ID:** Azure-prenumerations-ID för prenumerationen där du hämtade azure-publiceringsinställningsfilen.
   * **Azure Storage Account**: Namnet på lagringskontot i Azure-prenumerationen som är associerad med azure-publiceringsinställningsfilen.
   * **Azure Storage Container**: Namnet på mållagringsbloben i Azure-lagringskontot där säkerhetskopieringsdata importeras.

   Spara **mellanlagringsplatsen** och azure **importjobbnamnsinformationen** som du angav. Det krävs för att förbereda diskarna.

1. Slutför arbetsflödet. Om du vill starta offlinekopian för säkerhetskopiering väljer du **Säkerhetskopiera nu** på hanteringskonsolen för Azure Backup Agent. Den första säkerhetskopian skrivs till mellanlagringsområdet som en del av det här steget.

    ![Backup nu](./media/backup-azure-backup-import-export/backupnow.png)

    Om du vill slutföra motsvarande arbetsflöde i DPM eller Azure Backup Server högerklickar du på **skyddsgruppen**. Välj alternativet **Skapa återställningspunkt.** Välj sedan alternativet **Onlineskydd.**

    ![DPM och MABS Säkerhetskopiera nu](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    När åtgärden är klar är mellanlagringsplatsen klar att användas för diskförberedelse.

    ![Förloppet för säkerhetskopiering](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbereda SATA-enheter och leverera till Azure

*AzureOfflineBackupDiskPrep-verktyget* används för att förbereda SATA-enheter som skickas till närmaste Azure-datacenter. Det här verktyget är tillgängligt i installationskatalogen för Recovery Services Agent i följande sökväg:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Gå till katalogen och kopiera *AzureOfflineBackupDiskPrep-katalogen* till en kopia dator där SATA-enheter som ska förberedas är anslutna. Se till att:

   * Kopieringsdatorn kan komma åt mellanlagringsplatsen för arbetsflödet för offlineutsädning genom att använda samma nätverkssökväg som angavs i arbetsflödet i avsnittet "Initiera offline säkerhetskopiering".
   * BitLocker är aktiverat på kopieringsdatorn.
   * Kopieringsdatorn kan komma åt Azure-portalen. Om det behövs kan kopieringsdatorn vara samma som källdatorn.

     > [!IMPORTANT]
     > Om källdatorn är en virtuell dator är det obligatoriskt att använda en annan fysisk server eller klientdator som kopieringsdator.

1. Öppna en upphöjd kommandotolk på kopieringsdatorn med *azureofflineBackupDiskPrep-verktygskatalogen* som aktuell katalog. Kör följande kommando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parameter | Beskrivning |
    | --- | --- |
    | s:&lt;*Sökväg till mellanlagringsplats*&gt; |Den här obligatoriska indata används för att ange sökvägen till den mellanlagringsplats som du angav i arbetsflödet i avsnittet "Initiera offline säkerhetskopiering". |
    | p:&lt;*Sökväg till PublishSettingsFile*&gt; |Den här valfria indata används för att ange sökvägen till azure-publiceringsinställningsfilen som du angav i arbetsflödet i avsnittet "Initiera offline säkerhetskopiering". |

    > [!NOTE]
    > Värdet &lt;Sökväg till AzurePublishSettingFile&gt; är obligatoriskt när kopieringsdatorn och källdatorn är olika.
    >
    >

    När du kör kommandot begär verktyget valet av Azure-importjobbet som motsvarar de enheter som måste förberedas. Om bara ett enda importjobb är associerat med den angivna mellanlagringsplatsen visas en sida som den här.

    ![Indata för azure-diskförberedelseverktyg](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Ange enhetsbeteckningen utan det efterföljande kolon för den monterade disken som du vill förbereda för överföring till Azure. När du uppmanas att ange en bekräftelse för enhetens formatering.

    Verktyget börjar sedan förbereda disken och kopiera säkerhetskopieringsdata. Du kan behöva ansluta ytterligare diskar när du uppmanas av verktyget om den angivna disken inte har tillräckligt med utrymme för säkerhetskopieringsdata. <br/>

    När verktyget har slutförts förbereds en eller flera diskar som du har angett för leverans till Azure. Ett importjobb med namnet som du angav under arbetsflödet i avsnittet "Initiera offline säkerhetskopiering" skapas också i Azure. Slutligen visar verktyget leveransadressen till Azure-datacentret där diskarna måste levereras.

    ![Azure-diskförberedelse klar](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. I slutet av kommandokörningen visas också alternativet att uppdatera leveransinformation.

    ![Alternativet Uppdatera leveransinformation](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. Du kan ange informationen direkt. Verktyget guidar dig genom processen som involverar en rad indata. Om du inte har information som spårningsnummer eller annan information som rör leverans kan du avsluta sessionen. Stegen för att uppdatera leveransinformation senare finns i den här artikeln.

1. Leverera diskarna till den adress som verktyget angav. Behåll spårningsnumret för framtida referens.

   > [!IMPORTANT]
   > Inga två Azure-importjobb kan ha samma spårningsnummer. Se till att enheter som utarbetats av verktyget under ett enda Azure-importjobb levereras tillsammans i ett enda paket och att det finns ett unikt spårningsnummer för paketet. Kombinera inte enheter som förberetts som en del av olika Azure-importjobb i ett enda paket.

1. När du har information om spårningsnummer går du till källdatorn, som väntar på att importera jobbet slutförs. Kör följande kommando i en upphöjd kommandotolk med *azureofflineBackupDiskPrep-verktygskatalogen* som aktuell katalog.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Du kan också köra följande kommando från en annan dator, till exempel kopiatorn, med *azureofflineBackupDiskPrep-verktygskatalogen* som aktuell katalog.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parameter | Beskrivning |
    | --- | --- |
    | U: | Den här obligatoriska indata används för att uppdatera leveransinformation för ett Azure-importjobb. |
    | s:&lt;*Sökväg till mellanlagringsplats*&gt; | Den här obligatoriska indata används när kommandot inte körs på källdatorn. Den används för att ange sökvägen till den mellanlagringsplats som du angav i arbetsflödet i avsnittet "Initiera offline säkerhetskopiering". |
    | p:&lt;*Sökväg till PublishSettingsFile*&gt; | Den här obligatoriska indata används när kommandot inte körs på källdatorn. Den används för att ange sökvägen till azure-publiceringsinställningsfilen som du angav i arbetsflödet i avsnittet "Initiera offline säkerhetskopiering". |

    Verktyget identifierar automatiskt det importjobb som källdatorn väntar på eller de importjobb som är associerade med mellanlagringsplatsen när kommandot körs på en annan dator. Det ger sedan möjlighet att uppdatera leveransinformation via en serie indata.

    ![Ange leveransinformation](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. När alla indata har lämnats, granska detaljerna noggrant och begår fraktinformationen du angav genom att ange **ja**.

    ![Granska sändningsinformation](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. När leveransinformationen har uppdaterats tillhandahåller verktyget en lokal plats där sändningsinformationen du angav lagras.

    ![Lagra sändningsinformation](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Se till att enheterna når Azure-datacentret inom två veckor efter att leveransinformationen har tillhandahållits med hjälp av *AzureOfflineBackupDiskPrep-verktyget.* Om du inte gör det kan enheterna inte bearbetas. 

När du har avslutat föregående steg är Azure-datacentret redo att ta emot enheterna och bearbeta dem ytterligare för att överföra säkerhetskopierade data från enheterna till det klassiska Azure-lagringskonto som du skapade.

### <a name="time-to-process-the-drives"></a>Dags att bearbeta enheterna

Hur lång tid det tar att bearbeta ett Azure-importjobb varierar. Processtiden beror på faktorer som leveranstid, jobbtyp, typ och storlek på de data som kopieras och storleken på de diskar som tillhandahålls. Azure Import/Export-tjänsten har inget serviceavtal. När diskar har tagits emot strävar tjänsten efter att slutföra säkerhetskopian av data till ditt Azure-lagringskonto inom 7 till 10 dagar. I nästa avsnitt beskrivs hur du kan övervaka status för Azure-importjobbet.

### <a name="monitor-azure-import-job-status"></a>Övervaka Azure-importjobbstatus

Medan dina enheter är under överföring eller i Azure-datacentret som ska kopieras till lagringskontot, visar Azure Backup Agent eller DPM eller MABS-konsolen på källdatorn följande jobbstatus för dina schemalagda säkerhetskopior:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Så här kontrollerar du statusen för importjobbet:

1. Öppna en upphöjd kommandotolk på källdatorn och kör följande kommando:

     `AzureOfflineBackupDiskPrep.exe u:`

1. Utdata visar den aktuella statusen för importjobbet.

    ![Kontrollera status för importjobb](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Mer information om de olika tillstånden för Azure-importjobbet finns i [Visa status för Azure Import/Export-jobb](../storage/common/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Avsluta arbetsflödet

När importjobbet är klart är inledande säkerhetskopieringsdata tillgängliga i ditt lagringskonto. Vid tidpunkten för nästa schemalagda säkerhetskopiering kopierar Azure Backup innehållet i data från lagringskontot till Recovery Services-valvet.

   ![Kopiera data till Valvet för Återställningstjänster](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Vid tidpunkten för nästa schemalagda säkerhetskopiering utför Azure Backup inkrementell säkerhetskopiering över den första säkerhetskopian.

## <a name="next-steps"></a>Nästa steg

* Frågor om Azure Import/Export-tjänsten finns i [Använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob-lagring](../storage/common/storage-import-export-service.md).

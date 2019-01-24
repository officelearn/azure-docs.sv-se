---
title: Azure Backup - säkerhetskopiering Offline för DPM och Azure Backup Server
description: Lär dig hur Azure Backup kan du skicka data från nätverket med hjälp av tjänsten Azure Import/Export. Den här artikeln förklarar offline seeding av den första säkerhetskopiera informationen med hjälp av tjänsten Azure Import Export.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: saurse
ms.openlocfilehash: 01b90d6bb18addd6a0235101f86b9d51953cc096
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818565"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Arbetsflöde för säkerhetskopiering offline för DPM och Azure Backup Server
Azure Backup har flera inbyggda effektiviteten som sparar kostnader för lagring och nätverk under en första fullständig säkerhetskopiering av data till Azure. Inledande fullständiga säkerhetskopieringar vanligtvis överföra stora mängder data och kräver mer bandbredd i nätverket jämfört med efterföljande säkerhetskopieringar som överför bara deltan/varje. Azure Backup komprimerar första säkerhetskopieringarna. Genom processen för att ange startvärden offline, kan Azure Backup använda diskar för att ladda upp den komprimerade första säkerhetskopiera informationen offline till Azure.

Offlineseeding processen med Azure Backup är nära integrerad med den [tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md) som gör att du kan överföra data till Azure med hjälp av diskar. Om du har terabyte (TB) första säkerhetskopierade informationen som ska överföras över ett nätverk med lång svarstid och låg bandbredd kan använda du-offlineseeding arbetsflödet för att leverera den första säkerhetskopian på en eller flera hårddiskar till ett Azure-datacenter. Den här artikeln innehåller en översikt och ytterligare information om steg som utför det här arbetsflödet för System Center DPM och Azure Backup Server.

> [!NOTE]
> Processen för säkerhetskopiering Offline för Microsoft Azure Recovery Services MARS-agenten skiljer sig från System Center DPM och Azure Backup Server. Information om hur du använder Säkerhetskopiering Offline med MARS-agenten finns i [i den här artikeln](backup-azure-backup-import-export.md). Offlinesäkerhetskopiering stöds inte för säkerhetskopior av systemtillståndet göras med hjälp av Azure Backup-agenten. 
>

## <a name="overview"></a>Översikt
Med funktionen offline seeding av Azure Backup och Azure Import/Export är det enkelt att ladda upp data offline till Azure med hjälp av diskar. Offlinesäkerhetskopiering processen omfattar följande steg:

> [!div class="checklist"]
> * Säkerhetskopierade data, i stället för som skickas över nätverket, skrivs till en *mellanlagringsplats*
> * Data på den *mellanlagringsplatsen* skrivs sedan till en eller flera SATA-diskar med hjälp av den *AzureOfflineBackupDiskPrep* verktyget
> * En Azure-importjobbet skapas automatiskt av verktyget
> * SATA-enheter skickas sedan till det närmaste Azure-datacentret
> * När överföringen av data till Azure är klar kan Azure Backup kopierar säkerhetskopierade data till säkerhetskopieringsvalvet och de inkrementella säkerhetskopieringarna schemaläggs.

## <a name="supported-configurations"></a>Konfigurationer som stöds 
Offlinesäkerhetskopiering stöds för alla distributionsmodeller av Azure Backup som externt säkerhetskopierade data från en lokal plats till Microsoft Cloud. Detta inkluderar

> [!div class="checklist"]
> * Säkerhetskopiering av filer och mappar med Microsoft Azure Recovery Services MARS-agenten eller Azure Backup-agenten. 
> * Säkerhetskopiering av alla arbetsbelastningar och filer med System Center Data Protection Manager (DPM SC) 
> * Säkerhetskopiering av alla arbetsbelastningar och filer med Microsoft Azure Backup Server <br/>

## <a name="prerequisites"></a>Förutsättningar
Kontrollera att följande krav är uppfyllda innan du påbörjar arbetsflöde för Offlinesäkerhetskopiering
* En [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) har skapats. Om du vill skapa ett läser du anvisningarna i [i den här artikeln](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Azure Backup-agenten eller Azure Backup Server eller SC DPM har installerats på antingen Windows Server/Windows-klienten, så är tillämpligt och datorn har registrerats med Recovery Services-valvet. Kontrollera att endast den [senaste versionen av Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) används. 
* [Ladda ned filen med Azure publicera](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) på datorn från vilken du planerar att säkerhetskopiera dina data. Den prenumeration som du ladda ned publiceringsinställningsfil kan skilja sig från den prenumeration som innehåller Recovery Services-valvet. Om din prenumeration är i suveräna moln för Azure, använder du följande länkar genom att ladda ned filen publicera i Azure.

    | Nationellt moln region | Azure publicera filen inställningslänken |
    | --- | --- |
    | USA | [Länk](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kina | [Länk](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Ett Azure Storage-konto med *klassiska* distributionsmodellen har skapats i den prenumeration som du har hämtat publiceringsinställningsfil som visas nedan: 

 ![Skapa ett klassiskt lagringskonto](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* En mellanlagringsplats som kan vara en nätverksresurs eller eventuella ytterligare en enhet på datorn, interna eller externa, med tillräckligt med diskutrymme för att rymma den inledande kopian har skapats. Om du vill se till att säkerhetskopiera en filserver med 500 GB är mellanlagringsområdet minst 500 GB. (Färre används på grund av komprimering.)
* Kontrollera som endast 2,5 tums SSD eller 2,5 tum eller 3,5-tums SATA II/III interna hårddiskar som används för diskar som ska skickas till Azure. Du kan använda hårddiskar upp till 10 TB. Kontrollera den [dokumentation om Azure Import/Export service](../storage/common/storage-import-export-requirements.md#supported-hardware) för den senaste uppsättningen av enheter som har stöd för tjänsten.
* SATA-enheter måste vara ansluten till en dator (kallas en *kopia datorn*) varifrån kopia av säkerhetskopieringsdata från den *mellanlagringsplatsen* till SATA-enheter är klar. Kontrollera att BitLocker är aktiverat på den *kopia dator* 

## <a name="workflow"></a>Arbetsflöde
Informationen i det här avsnittet hjälper dig att slutföra arbetsflöde för säkerhetskopiering offline så att dina data kan levereras till ett Azure-datacenter och överförs till Azure Storage. Om du har frågor om tjänsten Import eller någon aspekt av processen, se den [Import tjänstöversikt](../storage/common/storage-import-export-service.md) dokumentationen refererar till tidigare.

### <a name="initiate-offline-backup"></a>Starta Säkerhetskopiering offline
1. När du schemalägger en säkerhetskopia kan se du följande skärm (i Windows Server, Windows-klienten eller System Center Data Protection Manager).

    ![Importskärmen](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Här är motsvarande skärmen i System Center Data Protection Manager: <br/>
    ![SC DPM och Azure Backup server-importskärmen](./media/backup-azure-backup-import-export/dpmoffline.png)

    Beskrivning av indata är följande:

    * **Mellanlagringsplatsen**: Den tillfälliga lagringsplats som den första säkerhetskopian skrivs. Mellanlagringsplatsen kan vara på en nätverksresurs eller en lokal dator. Om kopiera dator och källdatorn skiljer sig, rekommenderar vi att du anger den fullständiga nätverkssökvägen på mellanlagringsplatsen.
    * **Namnet på Azure-importjobbet**: Det unika namnet genom vilka Azure-Import-tjänsten och Azure Backup spåra överföringen av data som skickas på diskar till Azure.
    * **Azure-Publiceringsinställningar**: Ange den lokala sökvägen till filen publicera.
    * **Azure-prenumerations-ID**: Azure prenumerations-ID för prenumerationen från där du laddade ned filen publicera i Azure. 
    * **Azure Storage-konto**: Namnet på storage-konto i Azure-prenumeration som är associerade med filen publicera i Azure.
    * **Azure-lagringsbehållare**: Namnet på målblobben för lagring i Azure storage-konto där dina säkerhetskopierade data har importerats.

     Spara den *mellanlagringsplatsen* och *Azure Importjobbets namn* du angav som det krävs för att förbereda diskarna.  
     
2. Slutföra arbetsflödet och för att starta säkerhetskopiering offline-kopia på **Säkerhetskopiera nu** i konsolen för hantering av Azure Backup-agenten. Den första säkerhetskopieringen skrivs till mellanlagringsområdet som en del av det här steget.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow.png)

    För att slutföra motsvarande arbetsflödet i System Center Data Protection Manager eller Azure Backup server, högerklickar du på den **Skyddsgrupp**, och välj sedan den **Skapa återställningspunkt** alternativet. Du sedan välja den **Onlineskydd** alternativet.

    ![SC DPM och Azure Backup server Säkerhetskopiera nu](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    När åtgärden har slutförts är mellanlagringsplatsen redo att användas för förberedelse av disk.

    ![Säkerhetskopiering pågår](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbered SATA-enheter och skicka till Azure
Den *AzureOfflineBackupDiskPrep* verktyget används för att förbereda SATA-enheter som skickas till den närmaste Azure-Datacenter. Det här verktyget finns i installationskatalogen för Recovery Services-agenten på följande sökväg:

   *\Microsoft* *Azure* *Recovery* *Services* *Agent\Utils\*

1. Gå till katalogen och kopiera den **AzureOfflineBackupDiskPrep** katalogen till en kopia-dator där SATA-enheter att vara förberedd är ansluten. Så här för kopiera datorn:

    * Kopiera-datorn kan komma åt mellanlagringsplatsen för offline-seeding-arbetsflödet med hjälp av samma nätverkssökväg som angavs i den **påbörja offlinesäkerhetskopiering** arbetsflöde.
    * BitLocker har aktiverats på datorn kopia.
    * Kopiera-datorn kan komma åt Azure-portalen.

    Om det behövs, kan kopiera-datorn vara samma som källdatorn. 
    
    > [!IMPORTANT] 
    > Om källdatorn är en virtuell dator, är det obligatoriskt att använda en annan fysisk server eller klientdator som datorn du vill kopiera.
    
    
2. Öppna en upphöjd kommandotolk på Kopiera-dator med den *AzureOfflineBackupDiskPrep* verktyget katalog som den aktuella katalogen och kör sedan följande kommando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parameter | Beskrivning |
    | --- | --- |
    | s:&lt;*sökväg för mellanlagring*&gt; |Obligatorisk indata som används för att ange sökvägen till mellanlagringsplatsen som du angav på det **påbörja offlinesäkerhetskopiering** arbetsflöde. |
    | p:&lt;*sökvägen till PublishSettingsFile*&gt; |Valfri inmatning som används för att ange sökvägen till den **Azure-Publiceringsinställningar** -fil som du angav i den **påbörja offlinesäkerhetskopiering** arbetsflöde. |

    > [!NOTE]
    > Den &lt;sökvägen till AzurePublishSettingFile&gt; värdet är obligatoriskt när kopia datorn och källdatorn är olika.
    >
    >

    När du kör kommandot begär verktyget valet av Azure-importjobbet som motsvarar de enheter som måste förberedas. Om det bara en enda importjobb är associerad med den angivna mellanlagringsplatsen, visas en skärm som liknar den nedan.

    ![Azure Disk förberedelse verktyget indata](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Ange enhetsbeteckningen utan avslutande kolon för den monterade disken som du vill förbereda för överföring till Azure. Ange bekräftelse för att formatera på enheten när du tillfrågas.

    Verktyget börjar sedan att förbereda disken och kopiering av säkerhetskopierade data. Du kan behöva lägga till ytterligare diskar när du uppmanas av verktyget om den angivna disken inte har tillräckligt med utrymme för säkerhetskopierade data. <br/>

    I slutet av verktyget för att utföra förbereds en eller flera diskar som du tillhandahöll för leverans till Azure. Dessutom kan ett importjobb med namnet du angav under den **påbörja offlinesäkerhetskopiering** arbetsflödet har skapats i Azure. Slutligen visas leveransadressen till Azure-datacentret där diskarna måste levereras.

    ![Azure disk förberedelse klar](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. I slutet av Kommandokörningen se du också alternativet för att uppdatera försändelseinformation enligt nedan:

    ![Uppdatera information leveransalternativet](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Du kan ange information direkt. Verktyget vägleder dig genom processen som omfattar en serie indata. Men om du inte har information, till exempel spåra tal eller andra data relaterade till leverans kan du avsluta sessionen. Stegen för att uppdatera leveransinformationen senare finns i den här artikeln. 

6. Skicka tillbaka diskarna till den adress som tillhandahålls av verktyget och hålla spårningsnummer för framtida bruk.

   > [!IMPORTANT] 
   > Inga två Azure-importjobb kan ha samma Spårningsnumret. Se till att enheter som har sammanställts av verktyget under en enda Azure-importjobb levereras tillsammans i ett enda paket och att det finns en enda unika spårningsnummer för paketet. Inte kombinera enheter förberett som en del av **olika** Azure-importjobb i ett enda paket. 

5. När du har spårnings number information går du till källdatorn som väntar på importjobbet slutförande och kör följande kommando i en upphöjd kommandotolk med *AzureOfflineBackupDiskPrep* verktyget katalog som den aktuell katalog: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Du kan också köra följande kommando från en annan dator som den *kopia datorn*, med *AzureOfflineBackupDiskPrep* verktyget katalog som den aktuella katalogen:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parameter | Beskrivning |
    | --- | --- |
    | u: | Obligatorisk indata som används för att uppdatera endash information för ett Azure-importjobb |
    | s:&lt;*sökväg för mellanlagring*&gt; | Obligatorisk indata när kommandot inte körs på källdatorn. Används för att ange sökvägen till mellanlagringsplatsen som du angav på det **påbörja offlinesäkerhetskopiering** arbetsflöde. |
    | p:&lt;*sökvägen till PublishSettingsFile*&gt; | Obligatorisk indata när kommandot inte körs på källdatorn. Används för att ange sökvägen till den **Azure-Publiceringsinställningar** -fil som du angav i den **påbörja offlinesäkerhetskopiering** arbetsflöde. |
    
    Verktyget identifierar automatiskt importjobb som källdatorn väntar eller importera jobb som associeras med mellanlagringsplatsen när kommandot körs på en annan dator. Den tillhandahåller sedan alternativet för att uppdatera leveransinformation följa ett antal indata som visas nedan: 
    
    ![Att ange leveransinformation](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. När alla indata har angetts, bör du granska informationen noggrant och genomför leveransinformation som du har angett genom att skriva *Ja*. 

    ![Granska leveransinformation](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Om hur du uppdaterar leveransinformation har ger verktyget en lokal plats där leveransinformationen angett lagras som visas nedan 

    ![Lagra leveransinformation](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Se till att enheterna når Azure Datacenter inom två veckor efter att tillhandahålla en leverans information med hjälp av den *AzureOfflineBackupDiskPrep* verktyget. I annat fall kan leda till de enheter som inte bearbetas.  

Azure-Datacenter är redo att ta emot enheterna och fortsätta att bearbeta dem för att överföra säkerhetskopieringsdata från enheter till klassisk typ Azure storage-kontot som du skapade när du har slutfört stegen ovan. 

### <a name="time-to-process-the-drives"></a>Tid för att behandla enheterna 
Hur lång tid det tar att bearbeta en Azure-importjobb varierar beroende på olika faktorer, till exempel leveranstid jobb-typ, typ och mängden data som kopieras och storleken på diskarna. Azure Import/Export-tjänsten har inte något serviceavtal men när diskarna har tagits emot av tjänsten strävar efter att Datakopieringen säkerhetskopiering till Azure storage-kontot i 7 till 10 dagar. I nästa avsnitt beskrivs hur du kan övervaka status för Azure-importjobbet. 

### <a name="monitoring-azure-import-job-status"></a>Övervaka status för Azure-importjobb
Visar följande jobbet har statusen för din schemalagda säkerhetskopieringar dina enheter finns under överföring eller i Azure-datacenter som ska kopieras till lagringskontot, Azure Backup-agenten eller SC DPM eller Azure Backup-serverkonsolen på källdatorn. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Följ stegen nedan för att kontrollera jobbstatus för Import. 
1. Öppna en upphöjd kommandotolk på källdatorn och kör följande kommando:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  Utdata visar aktuell status för importjobbet enligt nedan: 

    ![Kontrollera jobbstatus för Import](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Mer information om de olika lägena i Azure-importjobb finns i [i den här artikeln](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Slutföra arbetsflödet
Första säkerhetskopierade informationen är tillgänglig i ditt storage-konto när importjobbet är klar. Vid tidpunkten för nästa schemalagda säkerhetskopiering kopierar med Azure backup innehållet i data från storage-kontot till Recovery Services-valvet enligt nedan: 

   ![Kopiera data till Recovery Services-valv](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Vid tidpunkten för nästa schemalagda säkerhetskopiering utför Azure Backup inkrementell säkerhetskopiering under den första säkerhetskopian.

## <a name="next-steps"></a>Nästa steg
* Frågor om Azure Import/Export-arbetsflöde, se [använder Microsoft Azure Import/Export-tjänsten för att överföra data till Blob storage](../storage/common/storage-import-export-service.md).
* Referera till avsnittet säkerhetskopiering offline i Azure-Backup [vanliga frågor och svar](backup-azure-backup-faq.md) för frågor om arbetsflödet.

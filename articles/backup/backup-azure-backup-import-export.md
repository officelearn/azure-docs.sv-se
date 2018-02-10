---
title: "Azure Backup - offlinesäkerhetskopiering eller inledande seeding med hjälp av tjänsten Azure Import/Export | Microsoft Docs"
description: "Lär dig hur Azure Backup kan användas att skicka data från nätverket med hjälp av tjänsten Azure Import/Export. Den här artikeln förklarar offline seeding av den första säkerhetskopiera informationen med hjälp av tjänsten Azure Import Export."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/6/2018
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 306c4c7498601cf3ab7e918ba6ce6bfef173236a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Arbetsflöde för säkerhetskopiering offline i Azure Backup
Azure-säkerhetskopiering har flera inbyggda effektivitet som sparar kostnader för nätverk och lagring under de första fullständiga säkerhetskopieringarna av data till Azure. Första fullständiga säkerhetskopieringar vanligtvis överför stora mängder data och kräver större nätverksbandbredd jämfört med efterföljande säkerhetskopieringar som överför bara går/varje. Azure-säkerhetskopiering komprimerar inledande säkerhetskopieringar. Genom processen att dirigera offline, kan Azure Backup använda diskar för att ladda upp den komprimerade första säkerhetskopiera informationen offline till Azure.  

Offline-seeding-processen för Azure Backup är nära integrerad med den [Azure Import/Export service](../storage/common/storage-import-export-service.md) som gör att du överför data till Azure med hjälp av diskar. Om du har inledande säkerhetskopieringsdata som måste överföras över ett nätverk med hög fördröjning och låg bandbredd terabyte (TBs) kan använda du offline seeding-arbetsflödet för att leverera den första säkerhetskopian på en eller flera hårddiskar till ett Azure-datacenter. Den här artikeln innehåller en översikt och ytterligare information om åtgärder som slutför det här arbetsflödet.


## <a name="overview"></a>Översikt
Det är enkelt att överföra data offline till Azure med hjälp av diskar med offline seeding-funktioner i Azure Backup och Azure Import/Export. Offline säkerhetskopiering innebär följande steg:

> [!div class="checklist"]
> * Säkerhetskopierade data i stället för som skickas över nätverket, skrivs till en *mellanlagringsplatsen*
> * Data på den *mellanlagringsplatsen* sedan skrivs till en eller flera SATA-diskar med hjälp av den *AzureOfflineBackupDiskPrep* verktyget
> * Azure-importjobbet skapas automatiskt av verktyget
> * SATA-enheter skickas sedan till det närmaste Azure-datacentret
> * När överföringen av säkerhetskopieringsdata till Azure är klar, Azure Backup kopierar den säkerhetskopiera informationen till säkerhetskopieringsvalvet och inkrementella säkerhetskopieringar är schemalagda.

## <a name="supported-configurations"></a>Konfigurationer som stöds 
Offlinesäkerhetskopiering stöds för alla distributionsmodeller av Azure Backup som externt säkerhetskopierade data från lokal till Microsoft Cloud. Detta inkluderar

> [!div class="checklist"]
> * Säkerhetskopiering av filer och mappar med Microsoft Azure Recovery Services MARS-agenten eller Azure Backup-agenten. 
> * Säkerhetskopiering av alla arbetsbelastningar och filer med System Center Data Protection Manager (DPM SC) 
> * Säkerhetskopiering av alla arbetsbelastningar och filer med Microsoft Azure Backup-Server <br/>

   > [!NOTE]
   > Offlinesäkerhetskopiering stöds inte för systemtillståndet görs med Azure Backup-agenten. 

## <a name="prerequisites"></a>Förutsättningar
Se till att följande krav är uppfyllda innan du påbörjar Offline säkerhetskopiering arbetsflödet
* En [Recovery Services-valvet](backup-azure-recovery-services-vault-overview.md) har skapats. Om du vill skapa en följer du stegen i [i den här artikeln](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Azure Backup-agenten eller Azure Backup Server eller SC DPM har installerats på antingen Windows Server och Windows-klient, i tillämpliga fall och datorn har registrerats med Recovery Services-valvet. Kontrollera att endast den [senaste versionen av Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) används. 
* [Hämta filen Azure publicera](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) på den dator från vilken du planerar att säkerhetskopiera dina data. Den prenumeration som du hämtar filen publicera kan skilja sig från den prenumeration som innehåller Recovery Services-valvet. Om din prenumeration finns på statliga Azure-moln, använder du följande länkar efter behov för att hämta filen Azure publicera inställningar.

    | Suveräna molnet region | Azure publiceringsinställningarna filen länk |
    | --- | --- |
    | USA | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kina | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Ett Azure Storage-konto med *klassiska* distributionsmodell har skapats i prenumerationen som du har hämtat den publicera filen enligt nedan: 

 ![Skapa ett klassiska storage-konto](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* En fristående plats, som kan vara en nätverksresurs eller alla ytterligare enheter på datorn, interna eller externa, har tillräckligt med diskutrymme för att rymma den första kopian har skapats. Om du vill se till att säkerhetskopiera en 500 GB filserver är mellanlagringsområdet minst 500 GB. (Färre används på grund av komprimering.)
* Kontrollera som endast 2,5 tum SSD eller 2,5-tums eller 3,5-tums SATA II/III interna hårddiskar som används med avseende på diskar som ska skickas till Azure. Du kan använda hårddiskar upp till 10 TB. Kontrollera den [Azure Import/Export service dokumentationen](../storage/common/storage-import-export-service.md#hard-disk-drives) för den senaste uppsättningen enheter som har stöd för tjänsten.
* SATA-enheter måste vara ansluten till en dator (kallas en *kopiera datorn*) varifrån kopia av säkerhetskopierade data från den *mellanlagringsplatsen* till SATA-enheter är klar. Kontrollera att Bitlocker är aktiverat på den *kopiera dator* 

## <a name="workflow"></a>Arbetsflöde
Informationen i det här avsnittet hjälper dig att slutföra offlinesäkerhetskopiering arbetsflödet så att dina data kan levereras till ett Azure-datacenter och överförs till Azure Storage. Om du har frågor om tjänsten Import eller någon aspekt av processen, se den [översikt över Import](../storage/common/storage-import-export-service.md) dokumentationen refererar till tidigare.

### <a name="initiate-offline-backup"></a>Initiera offlinesäkerhetskopiering
1. När du schemalägger en säkerhetskopia kan se du följande skärm (i Windows Server, Windows-klient eller System Center Data Protection Manager).

    ![Importskärmen](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Här är motsvarande skärmen i System Center Data Protection Manager: <br/>
    ![SC DPM och Azure Backup server importskärmen](./media/backup-azure-backup-import-export/dpmoffline.png)

    Beskrivning av indata är följande:

    * **Plats för mellanlagring**: temporär lagringsplats som den första säkerhetskopian skrivs. Mellanlagring platsen kan vara på en nätverksresurs eller en lokal dator. Om datorn kopia och källdatorn är olika, rekommenderar vi att du anger den fullständiga nätverkssökvägen för mellanlagringsplatsen.
    * **Azure Importjobbets namn**: det unika namnet för vilka Azure Import-tjänsten och Azure Backup spåra överföringen av data som skickas på diskar till Azure.
    * **Azure Publiceringsinställningar**: Ange den lokala sökvägen till filen publicera.
    * **Prenumerations-ID för Azure**: Azure prenumerations-ID för prenumeration från där du hämtade filen Azure publicera. 
    * **Azure Storage-konto**: namnet på storage-konto i Azure-prenumerationen som är associerade med filen Azure publicera.
    * **Azure Storage-behållare**: namnet på målet lagringsblob i Azure storage-konto där den säkerhetskopiera informationen har importerats.

     Spara den *mellanlagringsplatsen* och *Azure Importjobbets namn* du angav som det krävs för att förbereda diskar.  
     
2. Slutför arbetsflödet och för att initiera offlinesäkerhetskopiering kopia klickar du på **Säkerhetskopiera nu** i konsolen för hantering av Azure Backup agent. Den första säkerhetskopian ska skrivas till mellanlagringsområdet som en del av det här steget.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow.png)

    Slutför motsvarande arbetsflödet i System Center Data Protection Manager eller Azure Backup-server genom att högerklicka på den **Skyddsgrupp**, och välj sedan den **Skapa återställningspunkt** alternativet. Du kan sedan välja den **Onlineskydd** alternativet.

    ![SC DPM och Azure Backup server Säkerhetskopiera nu](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Mellanlagringsplatsen är redo att användas för förberedelse av disk när åtgärden har slutförts.

    ![Säkerhetskopiering](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbereda SATA-enheter och levereras till Azure
Den *AzureOfflineBackupDiskPrep* verktyget för att förbereda SATA-enheter som skickas till den närmaste Azure-Datacenter. Det här verktyget finns i installationskatalogen för Recovery Services-agenten på följande sökväg:

   *\Microsoft* *azure* *Recovery* *Services* * Agent\Utils\*

1. Gå till katalogen och kopiera den **AzureOfflineBackupDiskPrep** katalogen till en kopia dator där SATA-enheter att vara förberedd är anslutna. Kontrollera med avseende på Kopiera-datorn:

    * Kopiera-datorn kan komma åt mellanlagringsplatsen för offline-seeding-arbetsflöde med hjälp av samma sökvägen som har angetts i den **initiera offlinesäkerhetskopiering** arbetsflöde.
    * BitLocker har aktiverats på datorn kopia.
    * Kopiera datorn kan komma åt Azure-portalen.

    Om det behövs, kan kopiera datorn vara samma som källdatorn. 
    
    > [!IMPORTANT] 
    > Om källdatorn är en virtuell dator, är det obligatoriskt att använda en annan fysisk server eller klientdator som datorn som kopia.
    
    
2. Öppna en upphöjd kommandotolk på Kopiera-dator med den *AzureOfflineBackupDiskPrep* verktyget katalog som den aktuella katalogen och kör sedan följande kommando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parameter | Beskrivning |
    | --- | --- |
    | s:&lt;*sökvägen för Förproduktion*&gt; |Obligatoriska indata som används för att ange sökvägen till mellanlagringsplatsen som du angav i den **initiera offlinesäkerhetskopiering** arbetsflöde. |
    | p:&lt;*sökvägen till PublishSettingsFile*&gt; |Valfria indata som används för att ange sökvägen till den **Azure Publiceringsinställningar** -fil som du angav i den **initiera offlinesäkerhetskopiering** arbetsflöde. |

    > [!NOTE]
    > Den &lt;sökvägen till AzurePublishSettingFile&gt; värdet är obligatoriskt när kopiera datorn och källdatorn är olika.
    >
    >

    När du kör kommandot begär verktyget valet av Azure-importjobb som motsvarar de enheter som måste förberedas. Om bara en enda importjobbet är associerad med den angivna mellanlagringsplatsen visas en skärm som liknar den som följer.

    ![Azure Disk förberedelse verktyget indata](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Ange enhetsbeteckningen utan avslutande kolon för den monterade disken som du vill förbereda för överföring till Azure. Ange bekräftelse för formatering av enheten när du uppmanas.

    Verktyget börjar sedan förbereda disken och kopiera säkerhetskopieringsdata. Du kan behöva koppla ytterligare diskar när du uppmanas av verktyget om den angivna disken inte har tillräckligt med utrymme för säkerhetskopierade data. <br/>

    I slutet av verktyget har körts förbereds en eller flera diskar som du angav för leverans till Azure. Dessutom kan ett importjobb med namnet du angav under den **initiera offlinesäkerhetskopiering** arbetsflödet har skapats i Azure. Slutligen visas leveransadressen till Azure-datacenter där diskarna måste levereras.

    ![Förberedelse av Azure-disken har slutförts](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. I slutet av Kommandokörningen se du också alternativet för att uppdatera leveransinformation enligt nedan:

    ![Uppdatera leveransalternativet info](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Du kan ange information direkt. Verktyget guidar dig genom processen som involverar en serie indata. Men om du inte har information, till exempel tal eller annan information som rör leverans kan du avsluta sessionen. Stegen för att uppdatera senare leverans information finns i den här artikeln. 

6. Leverera diskar till den adress som tillhandahålls av verktyget och hålla Spårningsnumret för framtida bruk.

   > [!IMPORTANT] 
   > Inga två Azure-importjobb kan ha samma spårningsnummer. Se till att enheter som har förberetts av verktyget under en Azure-importjobb levereras tillsammans i ett paket och att det finns en enda unika spårning för paketet. Enheter som har förberetts som en del av du inte kombinera **olika** Azure-importjobb i ett paket. 

5. När du har spårning number information går du till källdatorn som väntar på att importera jobbet har slutförts och kör följande kommando i en kommandotolk med *AzureOfflineBackupDiskPrep* verktyget katalog som den aktuell katalog: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Du kan du köra följande kommando från en annan dator som den *kopiera datorn*, med *AzureOfflineBackupDiskPrep* verktyget katalog som den aktuella katalogen:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parameter | Beskrivning |
    | --- | --- |
    | u: | Obligatoriska indata som används för att uppdatera leverans information för ett Azure-importjobb |
    | s:&lt;*sökvägen för Förproduktion*&gt; | Obligatoriska indata när kommandot inte körs på måldatorn. Används för att ange sökvägen till mellanlagringsplatsen som du angav i den **initiera offlinesäkerhetskopiering** arbetsflöde. |
    | p:&lt;*sökvägen till PublishSettingsFile*&gt; | Obligatoriska indata när kommandot inte körs på måldatorn. Används för att ange sökvägen till den **Azure Publiceringsinställningar** -fil som du angav i den **initiera offlinesäkerhetskopiering** arbetsflöde. |
    
    Verktyget identifierar automatiskt importjobbet som väntar på källdatorn eller importera jobb som är kopplade till mellanlagringsplatsen när kommandot körs på en annan dator. Det innehåller sedan alternativet för att uppdatera leveransinformation genom en serie av indata som visas nedan: 
    
    ![Ange leveransinformation](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. När alla indata tillhandahålls Läs informationen noggrant och genomför leverans information som du angav genom att skriva *Ja*. 

    ![Granska leveransinformation](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Om hur du uppdaterar informationen som leverans har innehåller verktyget en lokal plats där specifikation för leverans av du lagras som visas nedan 

    ![Lagra leveransinformation](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Se till att enheterna når Azure-Datacenter inom två veckor för att tillhandahålla en leverans information med hjälp av den *AzureOfflineBackupDiskPrep* verktyget. Det gick inte att göra det kan resultera i enheter som inte bearbetas.  

Azure-Datacenter är redo att ta emot enheterna och fortsätta att bearbeta dem för att överföra säkerhetskopierade data från enheter till klassisk typ Azure storage-kontot som du skapade när du har slutfört stegen ovan. 

### <a name="time-to-process-the-drives"></a>Tid för bearbetning av enheterna 
Hur lång tid det tar att bearbeta en Azure-importjobb varierar beroende på olika faktorer, till exempel leveranstid jobbet typ, typen och storleken på data som kopieras och storleken på de diskar som angetts. Azure Import/Export-tjänsten har inte ett SLA men när diskarna har tagits emot tjänsten strävar efter att slutföra kopiera säkerhetskopieringsdata till Azure storage-konto i 7 till 10 dagar. I nästa avsnitt beskrivs hur du kan övervaka statusen för Azure-importjobbet. 

### <a name="monitoring-azure-import-job-status"></a>Övervaka status för Azure-importjobb
Visar följande jobbstatus för schemalagda säkerhetskopieringar när enheterna finns under överföring eller i Azure-datacenter som ska kopieras till lagringskontot, Azure Backup-agenten eller SC DPM eller konsolen i Azure Backup-server på källdatorn. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Följ stegen nedan för att kontrollera status för importjobbet. 
1. Öppna en upphöjd kommandotolk på källdatorn och kör följande kommando:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  Den aktuella statusen för importjobbet visas enligt nedan: 

    ![Kontrollera jobbstatus för Import](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Mer information om de olika lägena i Azure-importjobb finns [i den här artikeln](../storage/common/storage-import-export-service.md#how-does-the-azure-importexport-service-work)

### <a name="complete-the-workflow"></a>Slutföra arbetsflödet
Första säkerhetskopierade informationen är tillgänglig i ditt lagringskonto när importjobbet har slutförts. Vid tidpunkten för nästa schemalagda säkerhetskopiering kopierar Azure-säkerhetskopiering innehållet på data från lagringskontot till Recovery Services-valvet enligt nedan: 

   ![Kopiera data till Recovery Services-valvet](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Vid tidpunkten för nästa schemalagda säkerhetskopiering utför Azure Backup inkrementell säkerhetskopiering över den första säkerhetskopian.

## <a name="next-steps"></a>Nästa steg
* För eventuella frågor om Azure Import/Export-arbetsflöde, se [använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob storage](../storage/common/storage-import-export-service.md).
* Se avsnittet offline säkerhetskopiering av Azure Backup [vanliga frågor och svar](backup-azure-backup-faq.md) för frågor om arbetsflödet.

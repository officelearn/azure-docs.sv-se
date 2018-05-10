---
title: Azure Backup - offlinesäkerhetskopiering eller inledande seeding med hjälp av tjänsten Azure Import/Export | Microsoft Docs
description: Lär dig hur Azure Backup kan användas att skicka data från nätverket med hjälp av tjänsten Azure Import/Export. Den här artikeln förklarar offline seeding av den första säkerhetskopiera informationen med hjälp av tjänsten Azure Import Export.
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shivamg
editor: ''
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 5/8/2018
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 801de343ebb88394f04a65236997f9ec80a2f535
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Arbetsflöde för säkerhetskopiering offline i Azure Backup
Azure-säkerhetskopiering har flera inbyggda effektivitet som sparar kostnader för nätverk och lagring under de första fullständiga säkerhetskopieringarna av data till Azure. Första fullständiga säkerhetskopieringar vanligtvis överför stora mängder data och kräver större nätverksbandbredd jämfört med efterföljande säkerhetskopieringar som överför bara går/varje. Genom processen att dirigera offline, kan Azure Backup använda diskar för att överföra offline säkerhetskopierade data till Azure.

Azure-säkerhetskopiering ska offline seeding processen är nära integrerad med den [Azure Import/Export service](../storage/common/storage-import-export-service.md) som gör att du överför första säkerhetskopierade informationen till Azure med hjälp av diskar. Om du har inledande säkerhetskopieringsdata som måste överföras över ett nätverk med hög fördröjning och låg bandbredd terabyte (TBs) kan använda du offline seeding-arbetsflödet för att leverera den första säkerhetskopian på en eller flera hårddiskar, till ett Azure-datacenter. Följande bild innehåller en översikt över stegen i arbetsflödet.

  ![Översikt över offline importen av arbetsflöde](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Offline säkerhetskopieringsprocessen omfattar följande steg:

1. Skriva säkerhetskopierade data till en fristående plats i stället för att skicka säkerhetskopierade data över nätverket.
2. Använd verktyget AzureOfflineBackupDiskPrep för att skriva data i mellanlagringsplatsen till en eller flera SATA-diskar.
3. Som en del av det förberedande arbetet skapar verktyget AzureOfflineBackupDiskPrep Azure-importjobbet. Skicka SATA-enheter till det närmaste Azure-datacentret och refererar importjobbet för att ansluta aktiviteter.
4. På Azure-datacentret kopieras data på diskarna till ett Azure storage-konto.
5. Azure-säkerhetskopiering kopierar säkerhetskopierade data från storage-konto till Recovery Services-valvet och inkrementella säkerhetskopieringar är schemalagda.

## <a name="supported-configurations"></a>Konfigurationer som stöds 
Följande funktioner i Azure Backup eller arbetsbelastningar som stöd för användning av Offline-säkerhetskopiering.

> [!div class="checklist"]
> * Säkerhetskopiering av filer och mappar med Microsoft Azure Recovery Services MARS-agenten, även kallat Azure Backup-agenten. 
> * Säkerhetskopiering av alla arbetsbelastningar och filer med System Center Data Protection Manager (DPM SC) 
> * Säkerhetskopiering av alla arbetsbelastningar och filer med Microsoft Azure Backup-Server <br/>

   > [!NOTE]
   > Offlinesäkerhetskopiering stöds inte för systemtillståndet görs med Azure Backup-agenten. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Förutsättningar

  > [!NOTE]
  > Följande förutsättningar och arbetsflöde gäller endast för offlinesäkerhetskopiering av filer och mappar med den [senaste MARS-agenten](https://aka.ms/azurebackup_agent). Om du vill utföra offlinesäkerhetskopieringar för arbetsbelastningar med hjälp av System Center DPM eller Azure Backup Server, referera till [i den här artikeln](backup-azure-backup-server-import-export-.md). 

Innan du påbörjar Offline säkerhetskopiering arbetsflödet uppfylla följande krav: 
* Skapa en [Recovery Services-valvet](backup-azure-recovery-services-vault-overview.md). Om du vill skapa ett valv, följer du stegen i [i den här artikeln](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Se till att endast den [senaste versionen av Azure Backup-agenten](https://aka.ms/azurebackup_agent) har installerats på Windows Server och Windows-klienten, i tillämpliga fall och datorn har registrerats med Recovery Services-valvet.
* Azure PowerShell 3.7.0 eller senare krävs på datorn som kör Azure Backup-agenten. Det rekommenderas att du [installera den senaste versionen av Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).
* Kontrollera att Microsoft Edge eller Internet Explorer 11 installeras och JavaScript är aktiverat på datorn som kör Azure Backup-agenten. 
* Skapa ett Azure Storage-konto i samma prenumeration som Recovery Services-valvet. 
* Kontrollera att du har den [behörighet](../azure-resource-manager/resource-group-create-service-principal-portal.md) att skapa Azure Active Directory-programmet. Arbetsflödet Offline säkerhetskopiering skapar ett Azure Active Directory-program i prenumerationen som är associerade med Azure Storage-konto. Syftet med programmet är att ge Azure Backup säker och begränsade åtkomst till tjänsten Azure Import krävs för säkerhetskopiering Offline-arbetsflöde. 
* Registrera Microsoft.ImportExport resursprovidern med prenumerationen som innehåller Azure Storage-konto. Så här registrerar resursprovidern:
    1. Klicka i på huvudmenyn **prenumerationer**.
    2. Om du prenumererar på flera prenumerationer, Välj den prenumeration som du använder för offline-säkerhetskopiering. Om du använder en enda prenumeration visas din prenumeration.
    3. Klicka på menyn prenumeration **Resursproviders** att visa listan med providers.
    4. I listan över providers rulla ned till Microsoft.ImportExport. Om statusen är NotRegistered, klickar du på **registrera**.
    ![registrera resursprovidern](./media/backup-azure-backup-import-export/registerimportexport.png)
* En fristående plats, som kan vara en nätverksresurs eller alla ytterligare enheter på datorn, interna eller externa, har tillräckligt med diskutrymme för att rymma den första kopian har skapats. Om du vill se till att säkerhetskopiera en 500 GB filserver är mellanlagringsområdet minst 500 GB. (Färre används på grund av komprimering.)
* När du skickar diskar till Azure, Använd endast 2,5 tum SSD eller 2,5-tums eller 3,5-tums SATA II/III interna hårddiskar. Du kan använda hårddiskar upp till 10 TB. Kontrollera den [Azure Import/Export service dokumentationen](../storage/common/storage-import-export-service.md#hard-disk-drives) för den senaste uppsättningen enheter som har stöd för tjänsten.
* SATA-enheter måste vara ansluten till en dator (kallas en *kopiera datorn*) varifrån kopia av säkerhetskopierade data från den *mellanlagringsplatsen* till SATA-enheter är klar. Kontrollera att Bitlocker är aktiverat på den *kopiera datorn*.

## <a name="workflow"></a>Arbetsflöde
Det här avsnittet beskrivs arbetsflödet för offline-säkerhetskopiering så att dina data kan levereras till ett Azure-datacenter och överförs till Azure Storage. Om du har frågor om tjänsten Import eller någon aspekt av processen, se den [importera dokumentation för tjänsten översikt](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Initiera offlinesäkerhetskopiering
1. När du schemalägger en säkerhetskopiering på MARS-agenten kan se du följande skärm.

    ![Importskärmen](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

  Beskrivning av indata är följande:

    * **Plats för mellanlagring**: temporär lagringsplats som den första säkerhetskopian skrivs. Mellanlagring platsen kan vara på en nätverksresurs eller en lokal dator. Om datorn kopia och källdatorn är olika, rekommenderar vi att du anger den fullständiga nätverkssökvägen för mellanlagringsplatsen.
    * **Azure Resource Manager lagring för**: namnet på Resource Manager typen av lagringskonto i Azure-prenumeration.
    * **Azure Storage-behållare**: namnet på målet lagringsblob i Azure Storage-konto där säkerhetskopierade data importeras innan den kopieras till Recovery Services-valvet.
    * **Prenumerations-ID för Azure**: ID för Azure-prenumerationen där Azure Storage-konto har skapats.
    * **Azure Importjobbets namn**: det unika namnet för vilka Azure Import-tjänsten och Azure Backup spåra överföringen av data som skickas på diskar till Azure. 
  
  Ange indata på skärmen och klicka på **nästa**. Spara de angivna *mellanlagring plats* och *Azure Importjobbets namn*eftersom den här informationen krävs för att förbereda diskar.

2. När du uppmanas logga in på Azure-prenumerationen. Du måste logga in så att Azure Backup kan skapa Azure Active Directory-program och ange behörigheterna som krävs för att komma åt tjänsten Azure Import.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/azurelogin.png)

3. Slutföra arbetsflödet och i Azure Backup agent-konsolen klickar du på **Säkerhetskopiera nu**.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow.png)

4. På sidan bekräftelse i guiden klickar du på **säkerhetskopiera**. Den första säkerhetskopian ska skrivas till mellanlagringsområdet som en del av installationen.

   ![Bekräfta att du är redo att säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Mellanlagringsplatsen är redo att användas för förberedelse av disk när åtgärden har slutförts.

   ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbereda SATA-enheter och levereras till Azure
Den *AzureOfflineBackupDiskPrep* förbereder du SATA-enheter som skickas till det närmaste Azure-datacentret. Det här verktyget finns i installationskatalogen för Azure Backup agent (på följande sökväg):

   *\Microsoft azure Recovery Services Agent\Utils\\*

1. Gå till katalogen och kopiera den **AzureOfflineBackupDiskPrep** katalogen till en annan dator där SATA-enheter är anslutna. Kontrollera på datorn med anslutna SATA-enheter:

    * Kopiera-datorn kan komma åt mellanlagringsplatsen för offline-seeding-arbetsflöde med hjälp av samma sökvägen som har angetts i den **initiera offlinesäkerhetskopiering** arbetsflöde.
    * BitLocker har aktiverats på datorn kopia.
    * Azure PowerShell 3.7.0, eller nyare är installerad.
    * Senaste kompatibla webbläsare (kant eller Internet Explorer 11) är installerade och JavaScript är aktiverat. 
    * Kopiera datorn kan komma åt Azure-portalen. Om det behövs, kan kopiera datorn vara samma som källdatorn.
    
    > [!IMPORTANT] 
    > Om källdatorn är en virtuell dator, måste kopiera datorn vara en annan fysisk server eller klientdator från källdatorn.

2. Öppna en upphöjd kommandotolk på Kopiera-dator med den *AzureOfflineBackupDiskPrep* verktyget katalog som den aktuella katalogen och kör sedan följande kommando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parameter | Beskrivning |
    | --- | --- |
    | s:&lt;*sökvägen för Förproduktion*&gt; |Obligatoriska indata som används för att ange sökvägen till mellanlagringsplatsen som du angav i den **initiera offlinesäkerhetskopiering** arbetsflöde. |
    | p:&lt;*sökvägen till PublishSettingsFile*&gt; |Valfria indata som används för att ange sökvägen till den **Azure Publiceringsinställningar** -fil som du angav i den **initiera offlinesäkerhetskopiering** arbetsflöde. |

    När du kör kommandot begär verktyget valet av Azure-importjobb som motsvarar de enheter som måste förberedas. Om bara en enda importjobbet är associerad med den angivna mellanlagringsplatsen visas en skärm som liknar den som följer.

    ![Azure Disk förberedelse verktyget indata](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Ange enhetsbeteckningen utan avslutande kolon för den monterade disken som du vill förbereda för överföring till Azure. 
4. Ange bekräftelse för formatering av enheten när du uppmanas.
5. Du uppmanas att logga in på Azure-prenumerationen. Ange dina autentiseringsuppgifter.

    ![Azure Disk förberedelse verktyget indata](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Verktyget börjar sedan förbereda disken och kopiera säkerhetskopieringsdata. Du kan behöva koppla ytterligare diskar när du uppmanas av verktyget om den angivna disken inte har tillräckligt med utrymme för säkerhetskopierade data. <br/>

    I slutet av verktyget har körts innehåller Kommandotolken tre uppgifter:
    1. En eller flera diskar som du angav är förberedda för leverans till Azure. 
    2. Du får en bekräftelse att importera jobbet har skapats. Importjobbet använder det namn du angett.
    3. Verktyget visar leveransadress för Azure-datacenter.

    ![Förberedelse av Azure-disken har slutförts](./media/backup-azure-backup-import-export/console2.png)<br/>

6. I slutet av Kommandokörningen kan du uppdatera informationen om leverans.

7. Leverera diskar till den adress som tillhandahålls av verktyget och hålla Spårningsnumret för framtida bruk.

   > [!IMPORTANT] 
   > Inga två Azure-importjobb kan ha samma spårningsnummer. Se till att enheter som har förberetts av verktyget under en enda Azure-importjobbet levereras tillsammans i ett paket och att det finns en enda unika spårning för paketet. Kombinera inte enheter som har förberetts som en del av olika Azure-importjobb i ett paket.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Uppdatera leveransinformation på Azure-importjobb

Följande procedur uppdaterar Azure Import-jobbinformation leverans. Den här informationen innehåller information om:
* namnet på företaget som levererar diskarna till Azure
* returnera leverans information för diskarna

1. Logga in på Azure-prenumerationen.
2. Klicka i på huvudmenyn **alla tjänster** och Skriv importera i dialogrutan alla tjänster. När du ser **Import/Export jobb**, klickar du på den.
    ![Ange leveransinformation](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Listan över **Import/export jobb** menyn öppnas och visas listan över alla Import/export-jobb i den valda prenumerationen. 

3. Om du har flera prenumerationer måste du markera den prenumeration som används för att importera säkerhetskopian. Välj nyligen skapade importjobbet för att öppna dess information.

    ![Granska leveransinformation](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Klicka på menyn Inställningar för importjobbet **hantera leverans Info** och ange information för returnerade leverans.

    ![Lagra leveransinformation](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. När du har Spårningsnumret från leverans operatör, klickar på banderollen på översiktssidan för Azure Import jobb och ange följande information:

   > [!IMPORTANT] 
   > Kontrollera att informationen om operatör och spårnings-ID uppdateras inom två veckor från Azure-importen jobb skapas. Det gick inte att verifiera den här informationen inom två veckor kan resultera i jobbet tas bort och enheter som inte bearbetas.

   ![Lagra leveransinformation](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Lagra leveransinformation](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tid för bearbetning av enheterna 
Hur lång tid det tar att bearbeta en Azure-importjobb varierar beroende på olika faktorer, till exempel leveranstid jobbet typ, typen och storleken på data som kopieras och storleken på de diskar som angetts. Azure Import/Export-tjänsten har inte ett SLA men när diskarna har tagits emot tjänsten strävar efter att slutföra kopiera säkerhetskopieringsdata till Azure storage-konto i 7 till 10 dagar. 

### <a name="monitoring-azure-import-job-status"></a>Övervaka status för Azure-importjobb
Du kan övervaka statusen för din importjobbet från Azure portal genom att gå till den **Import/Export jobb** sida och välja ditt jobb. Mer information om status för Import-jobb finns i [lagring importera exportera service](../storage/common/storage-import-export-service.md) artikel.

### <a name="complete-the-workflow"></a>Slutföra arbetsflödet
Första säkerhetskopierade informationen är tillgänglig i ditt lagringskonto efter importjobbet har slutförts. Vid tidpunkten för nästa schemalagda säkerhetskopiering kopierar Azure Backup innehållet på data från lagringskontot till Recovery Services-valvet enligt nedan: 

   ![Kopiera data till Recovery Services-valvet](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Tiden för nästa schemalagda säkerhetskopiering utför Azure Backup en inkrementell säkerhetskopia.

### <a name="cleaning-up-resources"></a>Rensa resurser
När den första säkerhetskopieringen är klar, kan du ta bort data som importeras till Azure Storage-behållare och säkerhetskopierade data i Förproduktion plats.

## <a name="next-steps"></a>Nästa steg
* För eventuella frågor om Azure Import/Export-arbetsflöde, se [använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob storage](../storage/common/storage-import-export-service.md).
* Se avsnittet offline säkerhetskopiering av Azure Backup [vanliga frågor och svar](backup-azure-backup-faq.md) för frågor om arbetsflödet.

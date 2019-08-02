---
title: Azure Backup-offline-säkerhetskopiering eller inledande dirigering med tjänsten Azure import/export
description: Lär dig hur Azure Backup ger dig möjlighet att skicka data från nätverket med Azure import/export-tjänsten. I den här artikeln förklaras dirigeringen av de första säkerhets kopierings data med hjälp av Azure import export-tjänsten.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: dacurwin
ms.openlocfilehash: 2c628b94879e54616f294e4c5f349f241fbbb98b
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689480"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Arbetsflöde för säkerhetskopiering offline i Azure Backup
Azure Backup har flera inbyggda effektivitets vinster som sparar kostnader för nätverk och lagring under de första fullständiga säkerhets kopieringarna av data till Azure. De första fullständiga säkerhets kopieringarna överför ofta stora mängder data och kräver mer nätverks bandbredd jämfört med efterföljande säkerhets kopieringar som bara överför delta/steg. Genom processen för dirigering av dirigering kan Azure Backup använda diskar för att ladda upp offline-säkerhetskopierade data till Azure.

Azure Backup offline-seeding-processen är nära integrerad med [Azure import/export-tjänsten](../storage/common/storage-import-export-service.md) som gör att du kan överföra första säkerhets kopierings data till Azure med hjälp av diskar. Om du har terabyte (TBs) av inledande säkerhets kopierings data som behöver överföras via ett nätverk med hög latens och låg bandbredd, kan du använda arbets flödet offline-seeding för att leverera den första säkerhets kopian på en eller flera hård diskar till ett Azure-datacenter. Följande bild ger en översikt över stegen i arbets flödet.

  ![Översikt över arbets flödes processen offline-import](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Säkerhets kopierings processen offline omfattar följande steg:

1. I stället för att skicka säkerhetskopierade data över nätverket skriver du säkerhets kopierings data till en mellanlagringsplats.
2. Använd verktyget AzureOfflineBackupDiskPrep för att skriva data på mellanlagringsplatsen till en eller flera SATA-diskar.
3. Som en del av det förberedande arbetet skapar verktyget AzureOfflineBackupDiskPrep ett Azure-importerat jobb. Skicka SATA-enheterna till närmaste Azure-datacenter och referera till import jobbet för att ansluta aktiviteterna.
4. På Azure-datacentret kopieras data på diskarna till ett Azure Storage-konto.
5. Azure Backup kopierar säkerhetskopierade data från lagrings kontot till Recovery Services-valvet och stegvisa säkerhets kopieringar schemaläggs.

## <a name="supported-configurations"></a>Konfigurationer som stöds 
Följande Azure Backup funktioner eller arbets belastningar har stöd för säkerhets kopiering offline.

> [!div class="checklist"]
> * Säkerhets kopiering av filer och mappar med Microsoft Azure Recovery Services MARS-agenten (MARS), kallas även för Azure Backup agenten. 
> * Säkerhets kopiering av alla arbets belastningar och filer med System Center Data Protection Manager (SC DPM) 
> * Säkerhets kopiering av alla arbets belastningar och filer med Microsoft Azure Backup Server <br/>

   > [!NOTE]
   > Säkerhets kopiering offline stöds inte för säkerhets kopiering av system tillstånd som gjorts med Azure Backup-agenten. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Förutsättningar

  > [!NOTE]
  > Följande nödvändiga komponenter och arbets flöde gäller endast offline-säkerhetskopiering av filer och mappar med hjälp av den [senaste mars](https://aka.ms/azurebackup_agent)-agenten. Om du vill utföra säkerhets kopiering offline för arbets belastningar med System Center DPM eller Azure Backup Server, se [den här artikeln](backup-azure-backup-server-import-export-.md). 

Innan du påbörjar arbets flödet offline måste du uppfylla följande krav: 
* Skapa ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md). Se stegen i [den här artikeln](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) för att skapa ett valv
* Se till att endast den [senaste versionen av Azure Backup](https://aka.ms/azurebackup_agent) -agenten har installerats på Windows Server/Windows-klienten, enligt vad som är tillämpligt och att datorn har registrerats med Recovery Services-valvet.
* Azure PowerShell 3.7.0 krävs på den dator som kör Azure Backup-agenten. Vi rekommenderar att du hämtar och [installerar 3.7.0-versionen av Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* På den dator som kör Azure Backup Agent kontrollerar du att Microsoft Edge eller Internet Explorer 11 är installerat och att Java Script är aktiverat. 
* Skapa ett Azure Storage-konto i samma prenumeration som Recovery Services-valvet. 
* Kontrol lera att du har de [behörigheter som krävs](../active-directory/develop/howto-create-service-principal-portal.md) för att skapa Azure Active Directory-programmet. Arbets flödet offline-säkerhetskopiering skapar ett Azure Active Directory-program i prenumerationen som är kopplad till Azure Storage kontot. Målet med programmet är att tillhandahålla Azure Backup med säker och begränsad åtkomst till Azure import-tjänsten som krävs för arbets flödet offline-säkerhetskopiering. 
* Registrera Microsoft. ImportExport-resurs leverantören med prenumerationen som innehåller det Azure Storage kontot. Så här registrerar du resurs leverantören:
    1. Klicka på prenumerationer i huvudmenyn.
    2. Om du prenumererar på flera prenumerationer väljer du den prenumeration som du använder för säkerhets kopiering offline. Om du bara använder en prenumeration visas din prenumeration.
    3. I menyn prenumeration klickar du på **resurs leverantörer** för att visa listan över leverantörer.
    4. I listan över providers bläddrar du ned till Microsoft. ImportExport. Om statusen är NotRegistered klickar du på **Registrera**.
    ![resurs leverantören registreras](./media/backup-azure-backup-import-export/registerimportexport.png)
* En mellanlagringsplats, som kan vara en nätverks resurs eller ytterligare en enhet på datorn, intern eller extern, med tillräckligt disk utrymme för att lagra din ursprungliga kopia skapas. Om du till exempel försöker säkerhetskopiera en fil server på 500 GB måste du kontrol lera att mellanlagringsområdet är minst 500 GB. (En mindre mängd används på grund av komprimering.)
* När du skickar diskar till Azure använder du endast 2,5-tums SSD eller 2,5-tums eller 3,5-tums interna SATA II/III-hårddiskar. Du kan använda hård diskar upp till 10 TB. Läs [dokumentationen för Azure import/export-tjänsten](../storage/common/storage-import-export-requirements.md#supported-hardware) för den senaste uppsättningen enheter som stöds av tjänsten.
* SATA-enheterna måste vara anslutna till en dator (kallas *kopierings dator*) från den plats där kopian av säkerhets kopierings data från MELLANlagringsplatsen till SATA-enheterna görs. Se till att BitLocker är aktiverat på *kopierings datorn*.

## <a name="workflow"></a>Arbetsflöde
I det här avsnittet beskrivs arbets flödet för offline-säkerhetskopiering så att dina data kan skickas till ett Azure-datacenter och laddas upp till Azure Storage. Om du har frågor om import tjänsten eller någon del av processen går du till översikts [dokumentationen för import tjänsten](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Starta säkerhets kopiering offline
1. När du schemalägger en säkerhets kopiering på MARS-agenten visas följande skärm.

    ![Sidan importera](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   Beskrivningen av indata är följande:

    * **Mellanlagringsplats**: Den tillfälliga lagrings plats som den första säkerhets kopian skrivs till. Mellanlagrings platsen kan vara en nätverks resurs eller en lokal dator. Om kopierings datorn och käll datorn skiljer sig åt rekommenderar vi att du anger den fullständiga nätverks Sök vägen för mellanlagringsplatsen.
    * **Azure Resource Manager lagrings konto**: Namnet på resurs hanterarens typ av lagrings konto (generell användning v1 eller generell användning v2) i alla Azure-prenumerationer.
    * **Azure Storage behållare**: Namnet på mål lagrings-bloben i Azure Storage-kontot där säkerhets kopierings data importeras innan de kopieras till Recovery Services-valvet.
    * **ID för Azure-prenumeration**: ID för den Azure-prenumeration där Azure Storages kontot skapas.
    * **Namn på Azure import-jobb**: Det unika namnet som Azure import service och Azure Backup spåra överföringen av data som skickas på diskar till Azure. 
  
   Ange indata på skärmen och klicka på **Nästa**. Spara den angivna mellanlagringsplatsen och *Azures import jobbnamn*, eftersom den här informationen krävs för att förbereda diskarna.

2. Logga in på din Azure-prenumeration när du uppmanas till det. Du måste logga in så att Azure Backup kan skapa Azure Active Directory programmet och ange de behörigheter som krävs för att få åtkomst till Azure import service.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/azurelogin.png)

3. Slutför arbets flödet och klicka på **Säkerhetskopiera nu**i Azure Backup Agent-konsolen.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow.png)

4. På sidan bekräftelse i guiden klickar du på **säkerhetskopiera**. Den första säkerhets kopian skrivs till mellanlagringsområdet som en del av installationen.

   ![Bekräfta att du är redo att säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    När åtgärden har slutförts är mellanlagringsplatsen redo att användas för disk förberedelse.

   ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbered SATA-enheter och leverera till Azure
*AzureOfflineBackupDiskPrep* -verktyget förbereder de SATA-enheter som skickas till närmaste Azure-datacenter. Det här verktyget är tillgängligt i installations katalogen för Azure Backup Agent (i följande sökväg):

   *\Microsoft Azure Recovery Services-Agent\Utils\\*

1. Gå till katalogen och kopiera **AzureOfflineBackupDiskPrep** -katalogen till en annan dator där SATA-enheterna är anslutna. På datorn med de anslutna SATA-enheterna ser du till att:

   * Kopierings datorn kan komma åt mellanlagringsplatsen för arbets flödet offline-seeding med hjälp av samma nätverks Sök väg som angavs i arbets flödet **initiera säkerhets kopiering offline** .
   * BitLocker är aktiverat på kopierings datorn.
   * Azure PowerShell 3.7.0 har installerats.
   * De senaste kompatibla webbläsarna (Microsoft Edge eller Internet Explorer 11) är installerade och Java Script är aktiverat. 
   * Kopierings datorn har åtkomst till Azure Portal. Vid behov kan kopierings datorn vara samma som käll datorn.
    
     > [!IMPORTANT] 
     > Om käll datorn är en virtuell dator måste kopierings datorn vara en annan fysisk server eller klient dator från käll datorn.

2. Öppna en kommando tolk med förhöjd behörighet på Kopiera datorn med katalogen *AzureOfflineBackupDiskPrep* Utility som den aktuella katalogen och kör följande kommando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parameter | Beskrivning |
    | --- | --- |
    | s:&lt;*sökväg* för mellanlagringsplats&gt; |Obligatoriskt invärden används för att ange sökvägen till den mellanlagringsplats som du angav i arbets flödet **Starta säkerhets kopiering offline** . |
    | p:&lt;*sökväg till PublishSettingsFile*&gt; |Valfria indata som används för att ange sökvägen till filen för **Azure Publish-inställningar** som du angav i arbets flödet **Starta säkerhets kopiering offline** . |

    När du kör kommandot begär verktyget valet av Azure import-jobb som motsvarar de enheter som måste förberedas. Om bara ett enda import jobb är associerat med den angivna mellanlagringsplatsen visas en skärm som liknar den som följer.

    ![Inmatade Azure disk Preparation-verktyg](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Ange enhets beteckningen utan avslutande kolon för den monterade disk som du vill förbereda för överföring till Azure. 
4. Ange en bekräftelse för enhetens formatering vid uppmaning.
5. Du uppmanas att logga in på din Azure-prenumeration. Ange dina autentiseringsuppgifter.

    ![Inmatade Azure disk Preparation-verktyg](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Verktyget börjar sedan förbereda disken och kopiera säkerhetskopierade data. Du kan behöva bifoga ytterligare diskar när du uppmanas att göra det om den angivna disken inte har tillräckligt med utrymme för säkerhets kopierings data. <br/>

    I slutet av lyckad körning av verktyget har kommando tolken tre delar av informationen:
   1. En eller flera diskar som du har angett är för beredda för leverans till Azure. 
   2. Du får en bekräftelse på att ditt import jobb har skapats. Import jobbet använder det namn som du har angett.
   3. Verktyget visar leverans adressen för Azure-datacentret.

      ![Förberedelse av Azure-disk slutförd](./media/backup-azure-backup-import-export/console2.png)<br/>

6. I slutet av kommando körningen kan du uppdatera leverans informationen.

7. Leverera diskarna till den adress som verktyget tillhandahåller och behåll spårnings numret för framtida bruk.

   > [!IMPORTANT] 
   > Det går inte att ha samma spårnings nummer på två Azure import-jobb. Se till att enheter som förbereds av verktyget under ett enda Azure import-jobb levereras tillsammans i ett enda paket och att det finns ett enda unikt spårnings nummer för paketet. Kombinera inte enheter som har bearbetats som en del av separata Azure import-jobb i ett enda paket.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Uppdatera skeppnings information för Azure import-jobbet

I följande procedur uppdateras leverans informationen för Azure import-jobbet. Den här informationen innehåller information om:
* namnet på den operatör som levererar diskarna till Azure
* returnera leverans information för dina diskar

1. Logga in på din Azure-prenumeration.
2. Klicka på **alla tjänster** i huvud menyn och skriv import i dialog rutan alla tjänster. När du ser **import/export-jobb**klickar du på den.
    ![Ange leverans information](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Listan över **import/export-jobb** -menyn öppnas och listan över alla import/export-jobb i den valda prenumerationen visas. 

3. Om du har flera prenumerationer måste du välja den prenumeration som används för att importera säkerhets kopierings data. Välj sedan det nyskapade import jobbet för att öppna dess information.

    ![Granska skeppnings information](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. På menyn Inställningar för import jobbet klickar du på **Hantera skeppnings information** och anger information om retur leveranser.

    ![Lagra skeppnings information](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. När du har spårnings numret från transport företaget klickar du på banderollen på översikts sidan för Azure import jobb och anger följande information:

   > [!IMPORTANT] 
   > Se till att informationen som operatör och spårningsnummer uppdateras inom två veckor efter det att importjobb skapats i Azure. Om du inte verifierar informationen inom två veckor kan det leda till att jobbet tas bort och att enheterna inte bearbetas.

   ![Lagra skeppnings information](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Lagra skeppnings information](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tid för att bearbeta enheterna 
Hur lång tid det tar att bearbeta ett Azure-importerat jobb varierar beroende på olika faktorer, till exempel leverans tid, Jobbtyp, typ och storlek för de data som kopieras och storleken på de diskar som tillhandahålls. Tjänsten Azure import/export har inget service avtal, men när diskarna har tagits emot strävar tjänsten efter att slutföra säkerhets kopieringen av säkerhets kopian till ditt Azure Storage-konto på 7 till 10 dagar. 

### <a name="monitoring-azure-import-job-status"></a>Övervakar status för Azure import-jobb
Du kan övervaka statusen för ditt import jobb från Azure Portal genom att gå till sidan för **import/export-jobb** och välja ditt jobb. Mer information om import jobbens status finns i artikeln om export i [lagrings import](../storage/common/storage-import-export-service.md) .

### <a name="complete-the-workflow"></a>Slutför arbets flödet
När import jobbet har slutförts är de första säkerhets kopierings data tillgängliga i ditt lagrings konto. Vid nästa schemalagda säkerhets kopiering Azure Backup kopierar innehållet i data från lagrings kontot till Recovery Services-valvet som visas nedan: 

   ![Kopiera data till Recovery Services Vault](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Vid nästa schemalagda säkerhets kopiering Azure Backup utföra en stegvis säkerhets kopiering.

### <a name="cleaning-up-resources"></a>Rensar resurser
När den första säkerhets kopieringen är klar kan du på ett säkert sätt ta bort de data som importer ATS till Azure Storage-behållaren och säkerhets kopierings data på mellanlagringsplatsen.

## <a name="next-steps"></a>Nästa steg
* Läs mer om att [använda tjänsten Microsoft Azure import/export för att överföra data till Blob Storage](../storage/common/storage-import-export-service.md)för alla frågor i Azures import/export-arbetsflöde.
* Se avsnittet offline-säkerhets kopiering i Azure Backup [vanliga frågor och svar](backup-azure-backup-faq.md) om arbets flödet.

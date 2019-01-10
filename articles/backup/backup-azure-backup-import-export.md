---
title: Azure Backup - säkerhetskopiering Offline eller inledande seeding med hjälp av tjänsten Azure Import/Export
description: Lär dig hur Azure Backup kan du skicka data från nätverket med hjälp av tjänsten Azure Import/Export. Den här artikeln förklarar offline seeding av den första säkerhetskopiera informationen med hjälp av tjänsten Azure Import Export.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: 9d91ccd04ed06fb6c256a2d9911202d7df6d08a5
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188308"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Arbetsflöde för säkerhetskopiering offline i Azure Backup
Azure Backup har flera inbyggda effektiviteten som sparar kostnader för lagring och nätverk under en första fullständig säkerhetskopiering av data till Azure. Inledande fullständiga säkerhetskopieringar vanligtvis överföra stora mängder data och kräver mer bandbredd i nätverket jämfört med efterföljande säkerhetskopieringar som överför bara deltan/varje. Genom processen för att ange startvärden offline, kan Azure Backup använda diskar för att överföra offline säkerhetskopierade data till Azure.

I Azure Backup-offlineseeding processen är nära integrerad med den [tjänsten Azure Import/Export](../storage/common/storage-import-export-service.md) som gör att du kan överföra första säkerhetskopierade informationen till Azure med hjälp av diskar. Om du har terabyte (TB) första säkerhetskopierade informationen som ska överföras över ett nätverk med lång svarstid och låg bandbredd kan använda du-offlineseeding arbetsflödet för att leverera den första säkerhetskopian på en eller flera hårddiskar, till ett Azure-datacenter. Följande bild innehåller en översikt över stegen i arbetsflödet.

  ![Översikt över offline importen av arbetsflöde](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Offline säkerhetskopieringen omfattar följande steg:

1. Skriva säkerhetskopierade data till en mellanlagringsplats i stället för att skicka säkerhetskopierade data över nätverket.
2. Använd verktyget AzureOfflineBackupDiskPrep för att skriva data i mellanlagringsplatsen till en eller flera SATA-diskar.
3. Som en del av det förberedande arbetet skapar verktyget AzureOfflineBackupDiskPrep en Azure-importjobbet. Skicka SATA-enheter till närmsta Azure-datacentret och referera till importjobbet för att ansluta aktiviteter.
4. Vid Azure-datacentret kopieras data på diskarna till ett Azure storage-konto.
5. Azure Backup kopierar säkerhetskopierade data från storage-kontot till Recovery Services-valvet och inkrementella säkerhetskopieringar är schemalagda.

## <a name="supported-configurations"></a>Konfigurationer som stöds 
Följande funktioner i Azure Backup eller arbetsbelastningar som stöd för användning av säkerhetskopiering Offline.

> [!div class="checklist"]
> * Säkerhetskopiering av filer och mappar med Microsoft Azure Recovery Services MARS-agenten, kallas även Azure Backup-agenten. 
> * Säkerhetskopiering av alla arbetsbelastningar och filer med System Center Data Protection Manager (DPM SC) 
> * Säkerhetskopiering av alla arbetsbelastningar och filer med Microsoft Azure Backup Server <br/>

   > [!NOTE]
   > Offlinesäkerhetskopiering stöds inte för säkerhetskopior av systemtillståndet göras med hjälp av Azure Backup-agenten. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Förutsättningar

  > [!NOTE]
  > Följande nödvändiga komponenter och arbetsflöde gäller endast för offlinesäkerhetskopiering av filer och mappar med hjälp av den [senaste MARS-agenten](https://aka.ms/azurebackup_agent). Om du vill utföra offlinesäkerhetskopieringar för arbetsbelastningar med System Center DPM eller Azure Backup Server, som avser [i den här artikeln](backup-azure-backup-server-import-export-.md). 

Uppfyll följande krav innan du påbörjar arbetsflöde för Offlinesäkerhetskopiering: 
* Skapa en [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md). Om du vill skapa ett valv, läser du anvisningarna i [i den här artikeln](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Se till att endast den [senaste versionen av Azure Backup-agenten](https://aka.ms/azurebackup_agent) har installerats på Windows Server/Windows-klienten, så är tillämpligt och datorn har registrerats med Recovery Services-valvet.
* Azure PowerShell 3.7.0 krävs på den dator som kör Azure Backup-agenten. Vi rekommenderar att du hämtar och [installerar 3.7.0 versionen av Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Kontrollera att Microsoft Edge eller Internet Explorer 11 är installerad och JavaScript är aktiverat på den dator som kör Azure Backup-agenten. 
* Skapa ett Azure Storage-konto i samma prenumeration som Recovery Services-valvet. 
* Kontrollera att du har den [behörighet](../active-directory/develop/howto-create-service-principal-portal.md) att skapa Azure Active Directory-programmet. Arbetsflöde för Offlinesäkerhetskopiering skapar ett Azure Active Directory-program i prenumerationen som är associerade med Azure Storage-kontot. Målet med programmet är att ge Azure Backup säker och begränsad åtkomst till tjänsten Azure Import som krävs för arbetsflöde för Offlinesäkerhetskopiering. 
* Registrera resursprovidern Microsoft.ImportExport med prenumerationen som innehåller Azure Storage-kontot. Registrera resursprovidern:
    1. Klicka på huvudmenyn på **prenumerationer**.
    2. Om du prenumererar på flera prenumerationer väljer du den prenumeration som du använder för säkerhetskopiering offline. Om du använder bara en prenumeration visas din prenumeration.
    3. Klicka på menyn prenumeration **Resursprovidrar** att visa listan med providers.
    4. I listan över leverantörer rulla ned till Microsoft.ImportExport. Om statusen NotRegistered klickar du på **registrera**.
    ![När du registrerar resursprovidern](./media/backup-azure-backup-import-export/registerimportexport.png)
* En mellanlagringsplats som kan vara en nätverksresurs eller eventuella ytterligare en enhet på datorn, interna eller externa, med tillräckligt med diskutrymme för att rymma den inledande kopian har skapats. Om du vill se till att säkerhetskopiera en filserver med 500 GB är mellanlagringsområdet minst 500 GB. (Färre används på grund av komprimering.)
* När du skickar diskar till Azure, använda endast 2,5 tums SSD eller 2,5 tum eller 3,5-tums SATA II/III interna hårddiskar. Du kan använda hårddiskar upp till 10 TB. Kontrollera den [dokumentation om Azure Import/Export service](../storage/common/storage-import-export-requirements.md#supported-hardware) för den senaste uppsättningen av enheter som har stöd för tjänsten.
* SATA-enheter måste vara ansluten till en dator (kallas en *kopia datorn*) varifrån kopia av säkerhetskopieringsdata från den *mellanlagringsplatsen* till SATA-enheter är klar. Kontrollera att Bitlocker är aktiverat på den *kopia datorn*.

## <a name="workflow"></a>Arbetsflöde
Det här avsnittet beskrivs arbetsflödet för säkerhetskopiering offline så att dina data kan levereras till ett Azure-datacenter och överförs till Azure Storage. Om du har frågor om tjänsten Import eller någon aspekt av processen, se den [importera dokumentation om service-översikt](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Starta Säkerhetskopiering offline
1. När du schemalägger en säkerhetskopia på MARS-agenten kan se du följande skärmbild.

    ![Importskärmen](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

  Beskrivning av indata är följande:

    * **Mellanlagringsplatsen**: Den tillfälliga lagringsplats som den första säkerhetskopian skrivs. Mellanlagringsplatsen kan vara på en nätverksresurs eller en lokal dator. Om kopiera dator och källdatorn skiljer sig, rekommenderar vi att du anger den fullständiga nätverkssökvägen på mellanlagringsplatsen.
    * **Azure Resource Manager-Lagringskonto**: Namnet på lagringskontot för Resource Manager typ i alla Azure-prenumerationer.
    * **Azure-lagringsbehållare**: Namnet på målblobben för lagring i Azure Storage-konto där dina säkerhetskopierade data har importerats innan den kopieras till Recovery Services-valvet.
    * **Azure-prenumerations-ID**: ID för Azure-prenumerationen där Azure Storage-kontot skapas.
    * **Namnet på Azure-importjobbet**: Det unika namnet genom vilka Azure-Import-tjänsten och Azure Backup spåra överföringen av data som skickas på diskar till Azure. 
  
  Ange indata på skärmen och klicka på **nästa**. Spara de angivna *mellanlagringsplatsen* och *Azure Importjobbets namn*, enligt den här informationen krävs för att förbereda diskarna.

2. När du uppmanas logga in på din Azure-prenumeration. Du måste logga in så att Azure Backup kan skapa Azure Active Directory-program och ange behörigheterna som krävs för att få åtkomst till tjänsten Azure Import.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/azurelogin.png)

3. Slutföra arbetsflödet och i Azure Backup agent-konsolen klickar du på **Säkerhetskopiera nu**.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow.png)

4. På sidan bekräftelse i guiden klickar du på **säkerhetskopiera**. Den första säkerhetskopieringen skrivs till mellanlagringsområdet som en del av installationen.

   ![Bekräfta att du är redo att säkerhetskopiering nu](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    När åtgärden har slutförts är mellanlagringsplatsen redo att användas för förberedelse av disk.

   ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbered SATA-enheter och skicka till Azure
Den *AzureOfflineBackupDiskPrep* förbereder du SATA-enheter som skickas till närmsta Azure-datacentret. Det här verktyget finns i installationskatalogen för Azure Backup-agenten (i följande sökväg):

   *\Microsoft azure Recovery Services Agent\Utils\\*

1. Gå till katalogen och kopiera den **AzureOfflineBackupDiskPrep** katalogen till en annan dator där SATA-enheter är anslutna. På datorn med anslutna SATA-enheter, kontrollerar du att:

    * Kopiera-datorn kan komma åt mellanlagringsplatsen för offline-seeding-arbetsflödet med hjälp av samma nätverkssökväg som angavs i den **påbörja offlinesäkerhetskopiering** arbetsflöde.
    * BitLocker har aktiverats på datorn kopia.
    * Azure PowerShell 3.7.0 har installerats.
    * De senaste kompatibla webbläsarna (Microsoft Edge eller Internet Explorer 11) har installerats och JavaScript är aktiverat. 
    * Kopiera-datorn kan komma åt Azure-portalen. Om det behövs, kan kopiera-datorn vara samma som källdatorn.
    
    > [!IMPORTANT] 
    > Om källdatorn är en virtuell dator, måste kopiera datorn vara en annan fysisk server eller klientdator från källdatorn.

2. Öppna en upphöjd kommandotolk på Kopiera-dator med den *AzureOfflineBackupDiskPrep* verktyget katalog som den aktuella katalogen och kör sedan följande kommando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parameter | Beskrivning |
    | --- | --- |
    | s:&lt;*sökväg för mellanlagring*&gt; |Obligatorisk indata som används för att ange sökvägen till mellanlagringsplatsen som du angav på det **påbörja offlinesäkerhetskopiering** arbetsflöde. |
    | p:&lt;*sökvägen till PublishSettingsFile*&gt; |Valfri inmatning som används för att ange sökvägen till den **Azure-Publiceringsinställningar** -fil som du angav i den **påbörja offlinesäkerhetskopiering** arbetsflöde. |

    När du kör kommandot begär verktyget valet av Azure-importjobbet som motsvarar de enheter som måste förberedas. Om det bara en enda importjobb är associerad med den angivna mellanlagringsplatsen, visas en skärm som liknar den nedan.

    ![Azure Disk förberedelse verktyget indata](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Ange enhetsbeteckningen utan avslutande kolon för den monterade disken som du vill förbereda för överföring till Azure. 
4. Ange bekräftelse för att formatera på enheten när du tillfrågas.
5. Du uppmanas att logga in på din Azure-prenumeration. Ange dina autentiseringsuppgifter.

    ![Azure Disk förberedelse verktyget indata](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Verktyget börjar sedan att förbereda disken och kopiering av säkerhetskopierade data. Du kan behöva lägga till ytterligare diskar när du uppmanas av verktyget om den angivna disken inte har tillräckligt med utrymme för säkerhetskopierade data. <br/>

    I slutet av lyckad körning av verktyget innehåller tre typer av information i Kommandotolken:
    1. En eller flera diskar som du angav förbereds för leverans till Azure. 
    2. Du fått en bekräftelse att din importjobb har skapats. Importjobbet använder det namn du angett.
    3. Verktyget visar leveransadressen för Azure-datacentret.

    ![Azure disk förberedelse klar](./media/backup-azure-backup-import-export/console2.png)<br/>

6. I slutet av Kommandokörningen, kan du uppdatera leveransadressen.

7. Skicka tillbaka diskarna till den adress som tillhandahålls av verktyget och hålla spårningsnummer för framtida bruk.

   > [!IMPORTANT] 
   > Inga två Azure-importjobb kan ha samma Spårningsnumret. Se till att enheter som har sammanställts av verktyget under en enda Azure-importjobbet levereras tillsammans i ett enda paket och att det finns en enda unika spårningsnummer för paketet. Kombinera inte enheter som har förberetts som en del av separata Azure-importjobb i ett enda paket.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Uppdatera leveransinformation på Azure-importjobb

Följande procedur uppdaterar Azure Import-jobbinformation endash. Denna information innehåller information om:
* namnet på vilken operatör som levererar diskarna till Azure
* returnera endash information om dina diskar

1. Logga in på Azure-prenumerationen.
2. På huvudmenyn klickar du på **alla tjänster** och Skriv importera i dialogrutan där du alla tjänster. När du ser **Import/Export-jobb**, klickar du på den.
    ![Att ange leveransinformation](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Listan över **Import/export-jobb** menyn öppnas och visas i listan över alla Import/export-jobb i den valda prenumerationen. 

3. Om du har flera prenumerationer, måste du markera den prenumeration som används för att importera säkerhetskopian. Välj sedan det nyligen skapade importjobbet att öppna dess egenskaper.

    ![Granska leveransinformation](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Klicka på menyn Inställningar för importjobbet **hantera endash Info** och ange information om returfrakt.

    ![Lagra leveransinformation](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. När du har Spårningsnumret från din transportföretag, klicka på banderollen på översiktssidan för Azure Import-jobb och ange följande information:

   > [!IMPORTANT] 
   > Se till att informationen som operatör och spårningsnummer uppdateras inom två veckor efter jobbskapande för Azure import. Det gick inte att verifiera den här informationen inom två veckor kan resultera i jobbet tas bort och enheter som inte bearbetas.

   ![Lagra leveransinformation](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Lagra leveransinformation](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tid för att behandla enheterna 
Hur lång tid det tar att bearbeta en Azure-importjobb varierar beroende på olika faktorer, till exempel leveranstid jobb-typ, typ och mängden data som kopieras och storleken på diskarna. Azure Import/Export-tjänsten har inte något serviceavtal men när diskarna har tagits emot av tjänsten strävar efter att Datakopieringen säkerhetskopiering till Azure storage-kontot i 7 till 10 dagar. 

### <a name="monitoring-azure-import-job-status"></a>Övervaka status för Azure-importjobb
Du kan övervaka statusen för din importjobb från Azure portal genom att gå till den **Import/Export-jobb** sidan och välja ditt jobb. Mer information om statusen för Import-jobb finns i den [Storage Import-Export-tjänsten](../storage/common/storage-import-export-service.md) artikeln.

### <a name="complete-the-workflow"></a>Slutföra arbetsflödet
Efter importjobbet har slutförts, är första säkerhetskopierade informationen tillgänglig i ditt storage-konto. Vid tidpunkten för nästa schemalagda säkerhetskopiering kopierar Azure Backup innehållet i data från storage-kontot till Recovery Services-valvet som visas nedan: 

   ![Kopiera data till Recovery Services-valv](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Vid tidpunkten för nästa schemalagda säkerhetskopiering utför Azure Backup en inkrementell säkerhetskopia.

### <a name="cleaning-up-resources"></a>Rensa resurser
När den första säkerhetskopieringen är klar kan bort du ta de data som importeras till Azure Storage-behållare och säkerhetskopierade data i den mellanlagringsplatsen.

## <a name="next-steps"></a>Nästa steg
* Frågor om Azure Import/Export-arbetsflöde, se [använder Microsoft Azure Import/Export-tjänsten för att överföra data till Blob storage](../storage/common/storage-import-export-service.md).
* Referera till avsnittet säkerhetskopiering offline i Azure-Backup [vanliga frågor och svar](backup-azure-backup-faq.md) för frågor om arbetsflödet.

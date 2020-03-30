---
title: Dirigera offline-säkerhetskopiering med Azure Import/Export-tjänsten
description: Lär dig hur du kan använda Azure Backup för att skicka data från nätverket med hjälp av Azure Import/Export-tjänsten. I den här artikeln beskrivs offline-seedning av de första säkerhetskopieringsdata med hjälp av Azure Import /Export-tjänsten.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206766"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Arbetsflöde för offlinesäkerhetskopiering i Azure Backup

Azure Backup har flera inbyggda effektivitetsvinster som sparar nätverks- och lagringskostnader under de första fullständiga säkerhetskopieringarna av data till Azure. Inledande fullständiga säkerhetskopior överför vanligtvis stora mängder data och kräver mer nätverksbandbredd jämfört med efterföljande säkerhetskopior som bara överför deltas/inkrementella. Genom processen för offline seedning kan Azure Backup använda diskar för att överföra data för säkerhetskopiering offline till Azure.

Offlinedrösningsprocessen för Azure Backup är tätt integrerad med [Azure Import/Export-tjänsten](../storage/common/storage-import-export-service.md). Du kan använda den här tjänsten för att överföra initiala säkerhetskopierade data till Azure med hjälp av diskar. Om du har terabyte (TBs) med inledande säkerhetskopieringsdata som måste överföras via ett nätverk med hög latens och låg bandbredd, kan du använda arbetsflödet för offlineutsädning för att leverera den första säkerhetskopian på en eller flera hårddiskar till ett Azure-datacenter. Följande bild ger en översikt över stegen i arbetsflödet.

  ![Översikt över arbetsflödesprocessen för offlineimport](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Säkerhetskopieringen offline innebär följande steg:

1. Skriv säkerhetskopieringsdata till en mellanlagringsplats i stället för att skicka säkerhetskopierade data över nätverket.
1. Använd *azureofflineBackupDiskPrep-verktyget* för att skriva data på mellanlagringsplatsen till en eller flera SATA-diskar.
1. Som en del av det förberedande arbetet skapar *AzureOfflineBackupDiskPrep-verktyget* ett Azure-importjobb. Skicka SATA-enheterna till närmaste Azure-datacenter och referera till importjobbet för att ansluta aktiviteterna.
1. På Azure-data kopieras data på diskarna till ett Azure-lagringskonto.
1. Azure Backup kopierar säkerhetskopieringsdata från lagringskontot till Recovery Services-valvet och inkrementella säkerhetskopior schemaläggs.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Följande Azure Backup-funktioner eller arbetsbelastningar stöder användning av offline-säkerhetskopiering för:

> [!div class="checklist"]
>
> * Säkerhetskopiering av filer och mappar med MICROSOFT Azure Recovery Services (MARS)Agent, även kallad Azure Backup Agent.
> * Säkerhetskopiering av alla arbetsbelastningar och filer med System Center Data Protection Manager (DPM).
> * Säkerhetskopiering av alla arbetsbelastningar och filer med Microsoft Azure Backup Server.
 
   > [!NOTE]
   > Offline säkerhetskopiering stöds inte för säkerhetskopiering av systemtillstånd som görs med hjälp av Azure Backup Agent.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Krav

  > [!NOTE]
  > Följande förutsättningar och arbetsflöde gäller endast för offlinesäkerhetskopiering av filer och mappar med den [senaste Azure Recovery Services-agenten](https://aka.ms/azurebackup_agent). Information om hur du utför offlinesäkerhetskopior för arbetsbelastningar med System Center DPM eller Azure Backup Server finns i [Arbetsflödet för offlinesäkerhetskopior för DPM och Azure Backup Server](backup-azure-backup-server-import-export-.md).

Innan du startar arbetsflödet för säkerhetskopiering offline ska du fylla i följande förutsättningar:

* Skapa ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md). Om du vill skapa ett valv följer du stegen i [Skapa ett recovery services-valv](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Kontrollera att endast den [senaste versionen av Azure Backup Agent](https://aka.ms/azurebackup_agent) är installerad på Windows Server eller Windows-klienten, beroende på vad som är tillämpligt, och att datorn är registrerad i Recovery Services-valvet.
* Azure PowerShell 3.7.0 krävs på datorn som kör Azure Backup Agent. Hämta och [installera 3.7.0-versionen av Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Kontrollera att Microsoft Edge eller Internet Explorer 11 är installerat och att JavaScript är aktiverat på datorn som kör Azure Backup Agent.
* Skapa ett Azure-lagringskonto i samma prenumeration som Recovery Services-valvet.
* Kontrollera att du har de [behörigheter som krävs](../active-directory/develop/howto-create-service-principal-portal.md) för att skapa Azure Active Directory-programmet. Arbetsflödet för offlinesäkerhetskopiering skapar ett Azure Active Directory-program i prenumerationen som är associerad med Azure-lagringskontot. Målet med programmet är att ge Azure Backup säker och begränsad åtkomst till Azure Import/Export-tjänsten, som krävs för arbetsflödet för säkerhetskopiering offline.
* Registrera *resursleverantören Microsoft.ImportExport* med prenumerationen som innehåller Azure-lagringskontot. Så här registrerar du resursleverantören:
    1. Välj **Prenumerationer**på huvudmenyn .
    1. Om du prenumererar på flera prenumerationer väljer du den prenumeration som du planerar att använda för offlinesäkerhetskopiningen. Om du bara använder en prenumeration visas prenumerationen.
    1. På prenumerationsmenyn väljer du **Resursleverantörer** för att visa listan över leverantörer.
    1. Bläddra ned till *Microsoft.ImportExport*i listan över leverantörer . Om **statusen** inte **är registrerad**väljer du **Registrera**.

        ![Registrera resursprovidern](./media/backup-azure-backup-import-export/registerimportexport.png)

* En mellanlagringsplats, som kan vara en nätverksresurs eller ytterligare enhet på datorn, intern eller extern, med tillräckligt med diskutrymme för att hålla den första kopian, skapas. Om du till exempel vill säkerhetskopiera en 500 GB-filserver kontrollerar du att mellanlagringsområdet är minst 500 GB. (En mindre mängd används på grund av komprimering.)
* När du skickar diskar till Azure använder du endast 2,5-tums SSD- eller 2,5-tums eller 3,5-tums SATA II/III-interna hårddiskar. Du kan använda hårddiskar på upp till 10 TB. Kontrollera [Azure Import/Export-tjänstdokumentationen](../storage/common/storage-import-export-requirements.md#supported-hardware) för den senaste uppsättningen enheter som tjänsten stöder.
* SATA-enheterna måste vara anslutna till en dator (kallad *en kopia dator)* från där kopian av säkerhetskopierade data från mellanlagringsplatsen till SATA-enheterna görs. Kontrollera att BitLocker är aktiverat på kopieringsdatorn.

## <a name="workflow"></a>Arbetsflöde

I det här avsnittet beskrivs arbetsflödet för säkerhetskopiering offline så att dina data kan levereras till ett Azure-datacenter och överföras till Azure Storage. Om du har frågor om importtjänsten eller någon aspekt av processen läser du [översiktsdokumentationen för Azure Import/Export-tjänsten](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Initiera offline säkerhetskopiering

1. När du schemalägger en säkerhetskopia på Agenten för återställningstjänster visas den här sidan.

    ![Sidan Importera](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Välj alternativet **Överför med mina egna diskar**.

    > [!NOTE]
    > Använd alternativet Azure Data Box för att överföra initiala säkerhetskopierade data offline. Det här alternativet sparar den ansträngning som krävs för att skaffa dina egna Azure-kompatibla diskar. Den levererar Microsoft-proprietära, säkra och manipuleringssäkra Azure Data Box-enheter till vilka säkerhetskopierade data kan skrivas direkt till av Recovery Services Agent.

1. Välj **Nästa**och fyll i rutorna noggrant.

    ![Ange diskuppgifter](./media/backup-azure-backup-import-export/your-disk-details.png)

   Rutorna som du fyller i är:

    * **Mellanlagringsplats**: Den tillfälliga lagringsplatsen som den första säkerhetskopian skrivs till. Mellanlagringsplatsen kan finnas på en nätverksresurs eller en lokal dator. Om kopiatorn och källdatorn är olika anger du den fullständiga nätverkssökvägen för mellanlagringsplatsen.
    * **Azure Resource Manager Storage Account**: Namnet på Resource Manager-typlagringskontot (allmänt ändamål v1 eller allmänt ändamål v2) i en Azure-prenumeration.
    * **Azure Storage Container**: Namnet på mållagringsbloben i Azure-lagringskontot där säkerhetskopieringsdata importeras innan de kopieras till Recovery Services-valvet.
    * **Azure-prenumerations-ID:** ID:et för Azure-prenumerationen där Azure-lagringskontot skapas.
    * **Azure Import Job Name**: Det unika namn som Azure Import/Export-tjänsten och Azure Backup spårar överföringen av data som skickas på diskar till Azure.
  
   När du har fyllt i rutorna väljer du **Nästa**. Spara **mellanlagringsplatsen** och azure **import-jobbnamnsinformationen.** Det krävs för att förbereda diskarna.

1. Logga in på din Azure-prenumeration när du uppmanas att göra det. Du måste logga in så att Azure Backup kan skapa Azure Active Directory-programmet. Ange de behörigheter som krävs för att komma åt Azure Import/Export-tjänsten.

    ![Inloggningssida för Azure-prenumeration](./media/backup-azure-backup-import-export/azure-login.png)

1. Slutför arbetsflödet. På Azure Backup Agent-konsolen väljer du **Säkerhetskopiera nu**.

    ![Backa nu](./media/backup-azure-backup-import-export/backupnow.png)

1. Välj **Säkerhetskopiera**på sidan **Bekräftelse** i guiden . Den första säkerhetskopian skrivs till mellanlagringsområdet som en del av installationen.

   ![Bekräfta att du är redo att säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    När åtgärden är klar är mellanlagringsplatsen klar att användas för diskförberedelse.

   ![Sidan Säkerhetskopiera nu](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Förbereda SATA-enheter och leverera till Azure

*AzureOfflineBackupDiskPrep-verktyget* förbereder SATA-enheter som skickas till närmaste Azure-datacenter. Det här verktyget är tillgängligt i installationskatalogen för Azure Backup Agent i följande sökväg:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Gå till katalogen och kopiera *AzureOfflineBackupDiskPrep-katalogen* till en annan dator där SATA-enheterna är anslutna. Kontrollera att:

   * Kopieringsdatorn kan komma åt mellanlagringsplatsen för arbetsflödet för offlineutsädning genom att använda samma nätverkssökväg som angavs i arbetsflödet i avsnittet "Initiera offline säkerhetskopiering".
   * BitLocker är aktiverat på kopieringsdatorn.
   * Azure PowerShell 3.7.0 är installerat.
   * De senaste kompatibla webbläsarna (Microsoft Edge eller Internet Explorer 11) är installerade och JavaScript är aktiverat.
   * Kopieringsdatorn kan komma åt Azure-portalen. Om det behövs kan kopieringsdatorn vara samma som källdatorn.

     > [!IMPORTANT]
     > Om källdatorn är en virtuell dator måste kopieringsdatorn vara en annan fysisk server eller klientdator från källdatorn.

1. Öppna en upphöjd kommandotolk på kopieringsdatorn med *azureofflineBackupDiskPrep-verktygskatalogen* som aktuell katalog. Kör följande kommando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parameter | Beskrivning |
    | --- | --- |
    | s:&lt;*Sökväg till mellanlagringsplats*&gt; |Den här obligatoriska indata används för att ange sökvägen till den mellanlagringsplats som du angav i arbetsflödet i avsnittet "Initiera offline säkerhetskopiering". |
    | p:&lt;*Sökväg till PublishSettingsFile*&gt; |Den här valfria indata används för att ange sökvägen till azure-publiceringsinställningsfilen som du angav i arbetsflödet i avsnittet "Initiera offline säkerhetskopiering". |

    När du kör kommandot begär verktyget valet av Azure-importjobbet som motsvarar de enheter som måste förberedas. Om bara ett enda importjobb är associerat med den angivna mellanlagringsplatsen visas en sida som den här.

    ![Indata för azure-diskförberedelseverktyg](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Ange enhetsbeteckningen utan det efterföljande kolon för den monterade disken som du vill förbereda för överföring till Azure.
1. Ge bekräftelse på enhetens formatering när du uppmanas att göra det.
1. Du uppmanas att logga in på din Azure-prenumeration. Ange autentiseringsuppgifter.

    ![Inloggning av Azure-prenumeration](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Verktyget börjar sedan förbereda disken och kopiera säkerhetskopieringsdata. Du kan behöva ansluta ytterligare diskar när du uppmanas av verktyget om den angivna disken inte har tillräckligt med utrymme för säkerhetskopieringsdata. <br/>

    I slutet av framgångsrikt utförande av verktyget ger kommandotolken tre delar av informationen:

   1. En eller flera diskar som du angav är förberedda för leverans till Azure.
   1. Du får en bekräftelse på att importjobbet har skapats. Importjobbet använder det namn du angav.
   1. Verktyget visar leveransadressen för Azure-datacentret.

      ![Azure-diskförberedelse klar](./media/backup-azure-backup-import-export/console2.png)<br/>

1. I slutet av kommandokörningen kan du uppdatera leveransinformationen.

1. Leverera diskarna till den adress som verktyget angav. Behåll spårningsnumret för framtida referens.

   > [!IMPORTANT]
   > Inga två Azure-importjobb kan ha samma spårningsnummer. Se till att enheter som utarbetats av verktyget under ett enda Azure-importjobb levereras tillsammans i ett enda paket och att det finns ett unikt spårningsnummer för paketet. Kombinera inte enheter som förberetts som en del av separata Azure-importjobb i ett enda paket.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Uppdatera leveransinformation för Azure-importjobbet

Följande procedur uppdaterar informationen om azure-importjobbet. Denna information innehåller information om:

* Namnet på den operatör som levererar diskarna till Azure.
* Returnera leveransinformation för dina diskar.

1. Logga in på din Azure-prenumeration.
1. Välj **Alla tjänster**på huvudmenyn . I dialogrutan **Alla tjänster** anger du **Importera**. När du ser **Importera/exportera jobb**markerar du det.

    ![Ange leveransinformation](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Menyn **Importera/exportera jobb** öppnas och listan över alla import-/exportjobb i den valda prenumerationen visas.

1. Om du har flera prenumerationer väljer du den prenumeration som används för att importera säkerhetskopierade data. Välj sedan det nyskapade importjobbet för att öppna dess information.

    ![Granska sändningsinformation](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. På **menyn Inställningar** för importjobbet väljer du **Hantera leveransinformation**. Ange returleveransinformation.

    ![Lagra sändningsinformation](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. När du har spårningsnumret från ditt transportföretag väljer du bannern på översiktssidan för Azure-importjobbet och anger följande information.

   > [!IMPORTANT]
   > Se till att informationen som operatör och spårningsnummer uppdateras inom två veckor efter det att importjobb skapats i Azure. Om du inte verifierar den här informationen inom två veckor kan jobbet tas bort och enheter inte bearbetas.

   ![Avisering om uppdatering av spårningsinformation](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Operatörsinformation och spårningsnummer](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Dags att bearbeta enheterna

Hur lång tid det tar att bearbeta ett Azure-importjobb varierar. Processtiden baseras på faktorer som leveranstid, jobbtyp, typ och storlek på de data som kopieras och storleken på de diskar som tillhandahålls. Azure Import/Export-tjänsten har inget serviceavtal. När diskar har tagits emot strävar tjänsten efter att slutföra säkerhetskopian av data till ditt Azure-lagringskonto inom 7 till 10 dagar.

### <a name="monitor-azure-import-job-status"></a>Övervaka Azure-importjobbstatus

Du kan övervaka status för ditt importjobb från Azure-portalen. Gå till sidan **Importera/exportera jobb** och välj ditt jobb. Mer information om status för importjobben finns i [Vad är Azure Import/Export-tjänsten?](../storage/common/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Avsluta arbetsflödet

När importjobbet har slutförts är inledande säkerhetskopieringsdata tillgängliga i ditt lagringskonto. Vid tidpunkten för nästa schemalagda säkerhetskopiering kopierar Azure Backup innehållet i data från lagringskontot till Recovery Services-valvet.

   ![Kopiera data till Valvet för Återställningstjänster](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Vid tidpunkten för nästa schemalagda säkerhetskopiering utför Azure Backup en inkrementell säkerhetskopiering.

### <a name="clean-up-resources"></a>Rensa resurser

När den första säkerhetskopieringen är klar kan du på ett säkert sätt ta bort data som importeras till Azure Storage-behållaren och säkerhetskopieringsdata på mellanlagringsplatsen.

## <a name="next-steps"></a>Nästa steg

* Frågor om Azure Import/Export-tjänsten finns i [Använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob-lagring](../storage/common/storage-import-export-service.md).

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
ms.date: 12/18/2017
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: c58aafda21e02e12984e09ef605f7ea13200e381
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Arbetsflöde för säkerhetskopiering offline i Azure Backup
Azure-säkerhetskopiering har flera inbyggda effektivitet som sparar kostnader för nätverk och lagring under de första fullständiga säkerhetskopieringarna av data till Azure. Första fullständiga säkerhetskopieringar vanligtvis överför stora mängder data och kräver större nätverksbandbredd jämfört med efterföljande säkerhetskopieringar som överför bara går/varje. Azure-säkerhetskopiering komprimerar inledande säkerhetskopieringar. Genom processen att dirigera offline, kan Azure Backup använda diskar för att ladda upp den komprimerade första säkerhetskopiera informationen offline till Azure.  

Offline-seeding-processen för Azure Backup är nära integrerad med den [Azure Import/Export service](../storage/common/storage-import-export-service.md) som gör att du överför data till Azure med hjälp av diskar. Om du har inledande säkerhetskopieringsdata som måste överföras över ett nätverk med hög fördröjning och låg bandbredd terabyte (TBs) kan använda du offline seeding-arbetsflödet för att leverera den första säkerhetskopian på en eller flera hårddiskar till ett Azure-datacenter. Den här artikeln innehåller en översikt över de steg som slutför det här arbetsflödet.

## <a name="overview"></a>Översikt
Det är enkelt att överföra data offline till Azure med hjälp av diskar med offline seeding-funktioner i Azure Backup och Azure Import/Export. I stället för att överföra den ursprungliga fullständiga kopian över nätverket, säkerhetskopierade data skrivs till en *mellanlagringsplatsen*. När alternativet Kopiera till mellanlagringsplatsen har slutförts med hjälp av verktyget Azure Import/Export skrivs data till en eller flera SATA-enheter, beroende på mängden data. Dessa enheter levereras till slut till närmaste Azure-datacenter.

Den [augusti 2016 uppdatera Azure Backup (och senare)](http://go.microsoft.com/fwlink/?LinkID=229525) innehåller den *förberedelseverktyget för Azure Disk*, med namnet AzureOfflineBackupDiskPrep, som:

* Hjälper dig att förbereda dina enheter för Azure Import med hjälp av verktyget Azure Import/Export.
* Skapar automatiskt en Azure-importjobbet för tjänsten Azure Import/Export av [Azure-portalen](https://ms.portal.azure.com).

När överföringen av säkerhetskopieringsdata till Azure är klar, Azure Backup kopierar den säkerhetskopiera informationen till säkerhetskopieringsvalvet och inkrementella säkerhetskopieringar är schemalagda.

> [!NOTE]
> Se till att du har installerat uppdateringen augusti 2016 Azure Backup (eller senare) och utföra alla steg av arbetsflödet med den om du vill använda förberedelseverktyget för Azure Disk. Om du använder en äldre version av Azure Backup kan du förbereda SATA-enhet med hjälp av verktyget Azure Import/Export enligt anvisningarna i senare avsnitt i den här artikeln.
>
>

## <a name="prerequisites"></a>Förutsättningar
* [Bekanta dig med Azure Import/Export-arbetsflödet](../storage/common/storage-import-export-service.md).
* Kontrollera följande innan du påbörjar arbetsflödet:
  * Ett Azure Backup-valvet har skapats.
  * Valvautentiseringsuppgifter som har hämtats.
  * Azure Backup-agenten har installerats på antingen Windows Server/Windows klient- eller System Center Data Protection Manager och datorn har registrerats med Azure Backup-valvet.
* [Hämta Azure filen publiceringsinställningarna](https://manage.windowsazure.com/publishsettings) på den dator från vilken du planerar att säkerhetskopiera dina data.
* Förbered en fristående plats, som kan vara en nätverksresurs eller en ytterligare enhet på datorn. Mellanlagringsplatsen är tillfälliga lagringen och används tillfälligt under det här arbetsflödet. Kontrollera att mellanlagringsplatsen har tillräckligt med diskutrymme för din första kopian. Om du vill se till att säkerhetskopiera en 500 GB filserver är mellanlagringsområdet minst 500 GB. (Färre används på grund av komprimering.)
* Kontrollera att du använder en enhet som stöds. Endast 2,5 tum SSD eller 2,5 eller 3,5-tums SATA II/III interna hårddiskar stöds för användning med Import/Export-tjänsten. Du kan använda hårddiskar upp till 10 TB. Kontrollera den [Azure Import/Export service dokumentationen](../storage/common/storage-import-export-service.md#hard-disk-drives) för den senaste uppsättningen enheter som har stöd för tjänsten.
* Aktivera BitLocker på den dator som skrivaren SATA-enhet är ansluten.
* [Ladda ned verktyget Azure Import/Export](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) till datorn som skrivaren SATA-enhet är ansluten. Det här steget är inte nödvändigt om du har hämtat och installerat augusti 2016 uppdateringen för Azure Backup (eller senare).

## <a name="workflow"></a>Arbetsflöde
Informationen i det här avsnittet hjälper dig att slutföra offlinesäkerhetskopiering arbetsflödet så att dina data kan levereras till ett Azure-datacenter och överförs till Azure Storage. Om du har frågor om tjänsten Import eller någon aspekt av processen, se den [översikt över Import](../storage/common/storage-import-export-service.md) dokumentationen refererar till tidigare.

### <a name="initiate-offline-backup"></a>Initiera offlinesäkerhetskopiering
1. När du schemalägger en säkerhetskopia kan se du följande skärm (i Windows Server, Windows-klient eller System Center Data Protection Manager).

    ![Importskärmen](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Här är motsvarande skärmen i System Center Data Protection Manager: <br/>
    ![DPM importskärmen](./media/backup-azure-backup-import-export/dpmoffline.png)

    Beskrivning av indata är följande:

    * **Plats för mellanlagring**: temporär lagringsplats som den första säkerhetskopian skrivs. Detta kan bero på en nätverksresurs eller en lokal dator. Om datorn kopia och källdatorn är olika, rekommenderar vi att du anger den fullständiga nätverkssökvägen för mellanlagringsplatsen.
    * **Azure Importjobbets namn**: det unika namnet för vilka Azure Import-tjänsten och Azure Backup spåra överföringen av data som skickas på diskar till Azure.
    * **Azure Publiceringsinställningar**: en XML-fil som innehåller information om din prenumeration profil. Den innehåller också säkra referenser som är associerade med prenumerationen. Du kan [hämta filen](https://manage.windowsazure.com/publishsettings). Ange den lokala sökvägen till filen publicera.
    * **Prenumerations-ID för Azure**: Azure prenumerations-ID för den prenumeration där du planerar att starta Azure-importjobbet. Om du har flera Azure-prenumerationer, använder du ID för den prenumeration som du vill associera med importjobbet.
    * **Azure Storage-konto**: storage-konto i Azure-prenumerationen som är associerade med Azure-importjobbet.
    * **Azure Storage-behållare**: namnet på målet lagringsblob i Azure storage-konto där det här jobbet data importeras.

    > [!NOTE]
    > Om du har registrerat din server till en Azure Recovery Services-valvet från den [Azure-portalen](https://portal.azure.com) för dina säkerhetskopieringar och finns inte på en prenumeration som Cloud Solution Providers (CSP) du kan ändå skapa ett lagringskonto från Azure-portalen och Använd den för offline-säkerhetskopiering arbetsflödet.
    >
    >

     Spara den här informationen eftersom du måste ange den igen i följande steg. Endast den *mellanlagringsplatsen* krävs om du använde förberedelseverktyget för Azure Disk för att förbereda diskar.    
2. Slutföra arbetsflödet och välj sedan **Säkerhetskopiera nu** i hanteringskonsolen Azure Backup för att initiera offline säkerhetskopian kopia. Den första säkerhetskopian ska skrivas till mellanlagringsområdet som en del av det här steget.

    ![Säkerhetskopiera nu](./media/backup-azure-backup-import-export/backupnow.png)

    Om du vill slutföra motsvarande arbetsflödet i System Center Data Protection Manager, högerklicka på den **Skyddsgrupp**, och välj sedan den **Skapa återställningspunkt** alternativet. Du kan sedan välja den **Onlineskydd** alternativet.

    ![DPM-säkerhetskopiering nu](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Mellanlagringsplatsen är redo att användas för förberedelse av disk när åtgärden har slutförts.

    ![Säkerhetskopiering](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>Förbereda en SATA-enhet och skapa en Azure-importjobbet med hjälp av förberedelseverktyget för Azure Disk
Förberedelseverktyget för Azure Disk är tillgänglig i installationskatalogen för Recovery Services-agenten (augusti 2016 uppdatera och senare) på följande sökväg.

   *\Microsoft* *azure* *Recovery* *Services* * Agent\Utils\*

1. Gå till katalogen och kopiera den **AzureOfflineBackupDiskPrep** katalogen till en kopia dator där enheter att vara förberedd är monterade. Kontrollera följande avseende kopiera datorn:

    * Kopiera-datorn kan komma åt mellanlagringsplatsen för offline-seeding-arbetsflöde med hjälp av samma sökvägen som har angetts i den **initiera offlinesäkerhetskopiering** arbetsflöde.
    * BitLocker har aktiverats på datorn.
    * Datorn kan komma åt Azure-portalen.

    Om det behövs, kan kopiera datorn vara samma som källdatorn.
2. Öppna en upphöjd kommandotolk på Kopiera-datorn med Azure Disk förberedelse verktyget katalog som den aktuella katalogen och kör följande kommando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | Parameter | Beskrivning |
    | --- | --- |
    | s:&lt;*sökvägen för Förproduktion*&gt; |Obligatoriska indata som används för att ange sökvägen till mellanlagringsplatsen som du angav i den **initiera offlinesäkerhetskopiering** arbetsflöde. |
    | p:&lt;*sökvägen till PublishSettingsFile*&gt; |Valfria indata som används för att ange sökvägen till den **Azure Publiceringsinställningar** -fil som du angav i den **initiera offlinesäkerhetskopiering** arbetsflöde. |

    > [!NOTE]
    > Den &lt;sökvägen till PublishSettingFile&gt; värdet är obligatoriskt när kopiera datorn och källdatorn är olika.
    >
    >

    När du kör kommandot begär verktyget valet av Azure-importjobb som motsvarar de enheter som måste förberedas. Om bara en enda importjobbet är associerad med den angivna mellanlagringsplatsen visas en skärm som liknar den som följer.

    ![Azure Disk förberedelse verktyget indata](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Ange enhetsbeteckningen utan avslutande kolon för den monterade disken som du vill förbereda för överföring till Azure. Ange bekräftelse för formatering av enheten när du uppmanas.

    Verktyget börjar sedan förbereda disken med säkerhetskopierade data. Du kan behöva koppla ytterligare diskar när du uppmanas av verktyget om den angivna disken inte har tillräckligt med utrymme för säkerhetskopierade data. <br/>

    I slutet av verktyget har körts förbereds en eller flera diskar som du angav för leverans till Azure. Dessutom kan ett importjobb med namnet du angav under den **initiera offlinesäkerhetskopiering** arbetsflödet har skapats i Azure-portalen. Slutligen visas leveransadressen till Azure-datacenter där diskarna måste levereras och länken för att hitta importjobbet på Azure-portalen.

    ![Förberedelse av Azure-disken har slutförts](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. Leverera diskar till den adress som tillhandahålls av verktyget och hålla Spårningsnumret för framtida bruk.<br/>

5. När du går till den länk som visas av verktyget kan du se Azure storage-konto som du angav i den **initiera offlinesäkerhetskopiering** arbetsflöde. Här kan du se nyligen skapade importjobbet på den **IMPORT/EXPORT** för lagringskontot.

    ![Skapade importjobb](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. Klicka på **leverans INFO** längst ned på sidan för att uppdatera din kontaktinformation som visas i följande skärmbild. Microsoft använder informationen för att leverera diskarna till dig när importjobbet har slutförts.

    ![Kontaktuppgifter](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. Ange leverans information på nästa skärm. Ange den **leverans operatör** och **spåra antalet** information som motsvarar de diskar som du har levererats till Azure-datacenter.

    ![Leverera info](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>Slutföra arbetsflödet
Första säkerhetskopierade informationen är tillgänglig i ditt lagringskonto när importjobbet har slutförts. Recovery Services-agenten och kopierar data från det här kontot till säkerhetskopieringsvalvet eller återställningstjänster innehåll valvet, beroende på vilket som är tillämpligt. I nästa schemalagda säkerhetskopieringstiden utför Azure Backup-agenten den inkrementella säkerhetskopian över den första säkerhetskopian.

> [!NOTE]
> Följande avsnitt gäller för användare av tidigare versioner av Azure Backup som inte har åtkomst till förberedelseverktyget för Azure Disk.
>
>

### <a name="prepare-a-sata-drive"></a>Förbereda en SATA-enhet
1. Hämta den [verktyget Azure Import/Export](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) på Kopiera-datorn. Kontrollera att mellanlagringsplatsen är tillgängliga från den dator där du planerar att köra en uppsättning kommandon. Om det behövs, kan kopiera datorn vara samma som källdatorn.

2. Packa upp filen WAImportExport.zip. Kör verktyget WAImportExport som formaterar SATA-enheten, skriver den säkerhetskopiera informationen till SATA-enhet och krypterar den. Se till att BitLocker är aktiverat på datorn innan du kör följande kommando. <br/>

    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`

    > [!NOTE]
    > Om du har installerat uppdateringen augusti 2016 Azure Backup (eller senare) måste du se till att mellanlagringsplatsen som du angav är samma som den på den **Säkerhetskopiera nu** skärmen och innehåller AIB och Base Blob-filer.
    >
    >

| Parameter | Beskrivning |
| --- | --- |
| /j: <*JournalFile*> |Sökvägen till journalfilen. Varje enhet måste ha exakt en journal-fil. Journal-fil får inte vara på målenheten. Filnamnstillägget journal är .jrn och skapas som en del av det här kommandot. |
| /ID: <*sessions-ID*> |Sessions-ID identifierar en kopia-session. Den används för att se till att korrekt återställning av en session avbryts kopia. Filer som kopieras i en kopia session lagras i katalogen efter sessions-ID på målenheten. |
| /Sk: <*StorageAccountKey*> |Kontonyckel för lagringskontot som data importeras. Nyckeln måste vara samma som det har angetts under säkerhetskopiering princip/skyddsgrupp skapas. |
| / BlobType |Typ av blob. Det här arbetsflödet fungerar endast om **PageBlob** har angetts. Detta är inte standardalternativet och anges i det här kommandot. |
| / t: <*TargetDriveLetter*> |Enhetsbeteckning utan avslutande kolon av hårddisken mål för den aktuella sessionen kopia. |
| / format |Alternativet för att formatera hårddisken. Ange den här parametern när enheten måste vara formaterad; Annars utelämna den. Innan verktyget loggfilsformat enheten efterfrågar en bekräftelse från konsolen. Ange parametern /silentmode för att ignorera bekräftelsen. |
| / encrypt |Alternativet för att kryptera enheten. Ange den här parametern när enheten har ännu inte har krypterats med BitLocker och måste krypteras av verktyget. Om enheten redan har krypterats med BitLocker utelämna den här parametern anger parametern /bk och tillhandahålla den befintliga nyckeln för BitLocker. Om du anger parametern/format, måste du också ange den / kryptera parametern. |
| /srcdir: <*SourceDirectory*> |Källkatalog som innehåller filer som ska kopieras till målenheten. Kontrollera att det angivna katalognamnet har en fullständig snarare än relativ sökväg. |
| /dstdir: <*DestinationBlobVirtualDirectory*> |Sökvägen till den virtuella målkatalogen i Azure storage-konto. Se till att använda giltig behållarnamn när du anger den virtuella kataloger eller BLOB. Tänk på att behållarnamn måste vara gemener.  Det här behållarnamnet ska vara det som du angav under säkerhetskopiering princip/skyddsgrupp skapas. |

> [!NOTE]
> En journal-fil skapas i mappen WAImportExport som fångar hela information av arbetsflödet. Du behöver den här filen när du skapar ett importjobb i Azure-portalen.
>
>

  ![PowerShell-utdata](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>Skapa ett importjobb i Azure-portalen
1. Gå till ditt lagringskonto i den [Azure-portalen](https://ms.portal.azure.com/), klickar du på **Import/Export**, och sedan **skapa importjobbet** i åtgärdsfönstret.

    ![Import/export-fliken i Azure-portalen](./media/backup-azure-backup-import-export/azureportal.png)

2. I steg 1 i guiden anger du att du har förberett din enhet och att du har enheten journalfil som är tillgänglig.

3. I steg 2 i guiden anger du kontaktinformation för den person som ansvarar för den här importjobbet.

4. Överför journalfiler för enheten som du hämtade i föregående avsnitt i steg 3.

5. I steg 4, anger du ett beskrivande namn för importjobbet som du angav under säkerhetskopiering princip/skyddsgrupp skapas. Namnet som du anger kan innehålla endast små bokstäver, siffror, bindestreck och understreck, måste börja med en bokstav och får inte innehålla blanksteg. Det namn som du väljer används för att spåra dina jobb när de pågår och när de har slutförts.

6. Välj därefter datacenter region i listan. Datacenter-region anger datacenter och adressen som du levererar paketet.

    ![Välj datacenter region](./media/backup-azure-backup-import-export/dc.png)

7. Välj returnerade prefix i listan i steg 5 och ange din operatör kontonummer. Microsoft använder kontot för att leverera enheter tillbaka till dig när importjobbet har slutförts.

8. Skicka disken och ange talet spårning för att spåra status för transporten. När disken anländer i datacentret, kopieras till lagringskontot och status uppdateras.

    ![Slutförd status](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>Slutföra arbetsflödet
När den första säkerhetskopiera informationen är tillgänglig i ditt lagringskonto, Microsoft Azure Recovery Services-agenten kopierar innehållet på data från det här kontot till säkerhetskopieringsvalvet eller Recovery Services-valvet, beroende på vilket som är tillämpligt. I nästa schema tid för säkerhetskopiering utför den inkrementella säkerhetskopian över den första säkerhetskopian i Azure Backup-agenten.

## <a name="next-steps"></a>Nästa steg
* För eventuella frågor om Azure Import/Export-arbetsflöde, se [använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob storage](../storage/common/storage-import-export-service.md).
* Se avsnittet offline säkerhetskopiering av Azure Backup [vanliga frågor och svar](backup-azure-backup-faq.md) för frågor om arbetsflödet.

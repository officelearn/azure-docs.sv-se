---
title: Vanliga frågor och svar för tjänsten Azure Import/Export | Microsoft Docs
description: Läsa svar på vanliga frågor och svar om Azure Import Export-tjänsten.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/13/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 8111d80d0888455fbdf3ccf37e723fe348a62bee
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435012"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Tjänsten Azure Import/Export: vanliga frågor och svar 
Här följer frågor och svar som du kan ha när du använder Azure Import/Export-tjänsten för att överföra data till Azure storage. Frågor och svar är ordnade i följande kategorier:

- Om tjänsten Import/Export
- Förbereda diskarna för import/export
- Import/Export-jobb
- Levererade diskar
- Övrigt 

## <a name="about-importexport-service"></a>Om tjänsten Import/Export

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Kopierar jag Azure File storage med hjälp av tjänsten Azure Import/Export?

Ja. Azure Import/Export-tjänsten stöder importera till Azure File Storage. Det stöder inte export av Azure Files just nu.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Är Azure Import/Export-tjänsten tillgänglig för CSP-prenumerationer?

Ja. Azure Import/Export-tjänsten har stöd för Cloud Solution Providers (CSP) prenumerationer.

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Kan jag använda Azure Import/Export-tjänsten för att kopiera PST postlådor och SharePoint-data till O365?

Ja. Mer information går du till [Importera PST-filer eller SharePoint-data till Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Kan jag använda Azure Import/Export-tjänsten för att kopiera Mina säkerhetskopior offline till Azure Backup-tjänsten?

Ja. Mer information går du till [arbetsflöde för Offlinesäkerhetskopiering i Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Kan jag köpa enheter för import/export-jobb från Microsoft?

Nej. Du måste du skicka dina egna enheter för att importera och exportera jobb.


## <a name="preparing-disks-for-importexport"></a>Förbereda diskar för import/export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kan jag hoppa över enhet förberedelsen för ett importjobb? Kan jag förbereda en enhet utan att kopiera?

Nej. Alla enheter som används för att importera data måste förberedas med hjälp av verktyget Azure WAImportExport. Använd verktyget för att också kopiera data till enheten.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Behöver jag utför alla förberedelse av disk när du skapar ett exportjobb?

Nej. Vissa föruppdateringskontrollen rekommenderas. Använd verktyget WAImportExport PreviewExport kommando för att kontrollera antalet diskar som krävs. Mer information finns i [Förhandsgranska Driv användning för en Export-jobbet](https://msdn.microsoft.com/library/azure/dn722414.aspx). Kommandot kan du förhandsgranska diskanvändning för de valda blobarna, baserat på storleken på de enheter som du tänker använda. Kontrollera också att du kan läsa från och skriva till den hårddisk som levereras för export-jobbet.

## <a name="importexport-jobs"></a>Import/Export-jobb

### <a name="can-i-cancel-my-job"></a>Kan jag säga upp mitt jobb?
Ja. Du kan avbryta ett jobb när dess status är **skapa** eller **levererade**. Det går inte att avbryta jobbet utöver dessa steg och det fortsätter tills det sista steget.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Hur länge kan jag visa status för slutförda jobb i Azure-portalen?
Du kan visa status för slutförda jobb för upp till 90 dagar. Slutförda jobb tas bort efter 90 dagar.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Vad gör jag om jag vill importera eller exportera fler än 10 enheter?
En import eller export-jobbet kan referera till endast 10-enheter i ett enskilt jobb. Om du vill skicka fler än 10 enheter, bör du skapa flera jobb. Enheter som är associerade med samma jobb måste levereras tillsammans i samma paket. Mer information och tips när datakapacitet sträcker sig över flera disk importjobb, kontaktar du Microsoft på bulkimport@microsoft.com. 

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Den uppladdade blobben visar statusen ”lånet har upphört att gälla”. Vad ska jag göra?
Du kan ignorera fältet ”lånet har upphört att gälla”. Import/Export tar lånet på blobben under överföringen kommer att se till att inga andra processer kan uppdatera blob parallellt. Lånet har upphört att gälla innebär att Import/export överförs inte längre till den och blobben är tillgängliga för dig. 

## <a name="shipping-disks"></a>Levererade diskar

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Vad är det maximala antalet HDD för i en leverans?
Det finns ingen gräns för hur många hårddiskar i en leverans. Om diskarna tillhör flera jobb, rekommenderar vi att du: 
- Etikettera diskarna med motsvarande jobbnamn.
- Uppdatera jobb med en spårningsnummer suffix med -1,-2 osv.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Bör jag ta något annat än Hårddisken i min paketet?
Skicka endast dina hårddiskar i paketet levereras. Omfattar inte objekt som anger strömkablar eller USB-kablar.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Måste jag skicka mina enheter med hjälp av FedEx eller DHL?
Du kan leverera enheter till Azure-datacentret med hjälp av några kända operatör som FedEx, DHL, UPS eller oss postgång. Men för returförsändelse enheter till dig från datacentret, måste du ange:

- En FedEx kontonummer i USA och EU, eller
- DHL kontonummer i regionerna östra och Australien.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Finns det några begränsningar med leverans enheten internationellt?
Observera att fysiska media som du levererar kan behöva korsa internationella gränser. Du ansvarar för att säkerställa att dina fysiska media och data har importerats och/eller exporteras i enlighet med tillämpliga lagar. Före leverans kontrollera det fysiska mediet, med din rådgivare att kontrollera att ditt medieinnehåll och data kan vi inte ålagts levereras till identifierade datacenter. Detta hjälper för att säkerställa att den når Microsoft i god tid.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>När du skapar ett jobb är en plats som skiljer sig från min lagringskontoplatsen leveransadressen. Vad ska jag göra?

Vissa lagringsplatser för kontot mappas till alternativa leveranser platser. Tidigare kan tillgängliga endash platser också tillfälligt mappas till andra platser. Kontrollera alltid leveransadressen som angavs under skapande av jobb före leverans av dina enheter.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>När leverans enheten efterfrågar transportföretaget data center adress och telefonnummer kontaktnumret. Vad ska jag ge?

Telefonnumret och DC-adress anges som en del av jobbet skapas.


## <a name="miscellaneous"></a>Övrigt

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Vad händer om jag råkar skickar en Hårddisk som inte följer kraven på stöds?

Azure-datacentret returnerar den enhet som inte följer stöds krav för att du. Om det bara vissa av enheterna i paketet uppfyller kraven för stöd kan dessa enheter kommer att bearbetas och de enheter som inte uppfyller kraven kommer att returneras till dig.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Tjänsten formatera enheter innan den returneras dem?

Nej. Alla enheter är krypterade med BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hur kommer jag åt data som importeras av den här tjänsten?

Använda Azure-portalen eller [Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) åtkomst till data under ditt Azure storage-konto.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>När importen är klar, hur Mina data ut i storage-konto? Bevaras min Kataloghierarki?

När du förbereder en hårddisk för ett importjobb anges målet av DstBlobPathOrPrefix-fält i datamängden CSV. Det här är Målbehållaren i lagringskontot som kopieras data från hårddisken. I den här Målbehållaren skapas virtuella kataloger för mappar från hårddisken och BLOB-objekt skapas för filer. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Om en enhet har filer som redan finns i mitt lagringskonto, tjänsten skriver över befintliga blobar eller filer?

Beror. När du förbereder enheten ska du ange om målfilerna ska skrivas över eller ignoreras med hjälp av fältet i datauppsättningen CSV-filen heter Disposition: < Byt namn på | Nej skriva över | Skriv över >. Som standard tjänsten byter namn på de nya filerna i stället skriva över befintliga blobar eller filer.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Är verktyget WAImportExport kompatibel med 32-bitars operativsystem?
Nej. Verktyget WAImportExport är endast kompatibel med 64-bitars Windows-operativsystem. En fullständig lista över operativsystem som stöds går du till [Supported Operating Systems](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Vad är den högsta Block Blob och Sidblobens storlek som stöds av Azure Import/Export?

Maximalt antal Block Blob är ungefär 4.768TB eller 5 000 000 MB.
Max Page Blob är 1TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Stöder AES-256-kryptering i Azure Import/Export?
Azure Import/Export-tjänsten använder AES-128 bitlocker-kryptering som standard. Du kan ändra detta till AES-256 genom att manuellt krypteras med bitlocker innan data kopieras. 

- Om du använder [WAImportExport V1](https://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), nedan visas ett exempel på kommando
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Om du använder [WAImportExport V2](https://www.microsoft.com/en-us/download/details.aspx?id=55280) ange ”AlreadyEncrypted” och ange nyckeln i driveset CSV.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Import/Export?](storage-import-export-service.md)



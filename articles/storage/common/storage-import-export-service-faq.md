---
title: Vanliga frågor om Azure Import/Export service | Microsoft Docs
description: Läs svar på vanliga frågor och svar om Azure Import exportera service.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: alkohli
ms.openlocfilehash: ed928452946b871ee9192bda82fcbf205b96e6e0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661025"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Tjänsten Azure Import/Export: vanliga frågor och svar 
Följande är frågor och svar som du kan ha när du använder tjänsten Azure Import/Export för att överföra data till Azure storage. Frågor och svar ordnas i följande kategorier:

- Om tjänsten Import/Export
- Förbereda diskar för import/export
- Importera och exportera jobb
- Leverera diskar
- Övrigt 

## <a name="about-importexport-service"></a>Om tjänsten Import/Export

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Kan jag kopiera Azure File storage med hjälp av tjänsten Azure Import/Export?

Ja. Tjänsten stöder Azure Import/Export importera till Azure File Storage. Det stöder inte export av Azure-filer just nu.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Finns tjänsten Azure Import/Export för CSP-prenumerationer?

Ja. Azure Import/Export-tjänsten stöder Cloud Solution Providers (CSP) prenumerationer.

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Kan jag använda tjänsten Azure Import/Export för att kopiera PST postlådor och SharePoint-data till O365?

Ja. Mer information finns på [Importera PST-filer eller SharePoint-data i Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Kan jag använda tjänsten Azure Import/Export för att kopiera Mina säkerhetskopior offline till Azure Backup-tjänsten?

Ja. Mer information finns på [Offline säkerhetskopiering arbetsflöde i Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Kan jag köpa enheter för import/export av jobb från Microsoft?

Nej. Du måste levereras egna enheter för import och export av jobb.


## <a name="preparing-disks-for-importexport"></a>Förbereda diskar för import/export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kan jag hoppa över förberedelsen av enheten för ett importjobb? Kan jag förbereda en enhet utan att kopiera?

Nej. Alla enheter som används för att importera data måste förberedas med verktyget Azure WAImportExport. Använda verktyget för att kopiera data till enheten.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Behöver jag utföra eventuella förberedelse av disk när du skapar ett exportjobb?

Nej. Vissa prechecks rekommenderas. Använd verktyget WAImportExport PreviewExport kommando för att kontrollera antalet diskar som krävs. Mer information finns i [Förhandsgranska enhet användning för ett jobb exportera](https://msdn.microsoft.com/library/azure/dn722414.aspx). Kommandot kan du förhandsgranska diskanvändning för de valda BLOB baserat på storleken på de enheter som du ska använda. Kontrollera också att du kan läsa från och skriva till hårddisken som levereras för exportjobbet.

## <a name="importexport-jobs"></a>Importera och exportera jobb

### <a name="can-i-cancel-my-job"></a>Kan jag avbryts min?
Ja. Du kan avbryta ett jobb när dess status är **skapa** eller **leverans**. Det går inte att avbryta jobbet och fortsätter tills det sista steget utöver dessa steg.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Hur länge kan visa statusen för slutförda jobb i Azure portal?
Du kan visa statusen för slutförda jobb för upp till 90 dagar. Slutförda jobb tas bort efter 90 dagar.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Vad gör jag om jag vill importera eller exportera fler än 10 enheter?
En import eller exportjobb kan referera endast 10-enheter i ett enda jobb. Om du vill skicka fler än 10 enheter, bör du skapa flera jobb. Enheter som är kopplade till samma jobb måste levereras tillsammans i samma paket. Mer information och tips när datakapacitet sträcker sig över flera disk importjobb, kontaktar du Microsoft på bulkimport@microsoft.com.                                                              

## <a name="shipping-disks"></a>Leverera diskar

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Vad är det maximala antalet Hårddisk för i en leverans?
Det finns ingen gräns för hur många hårddiskar i en leverans. Om diskarna tillhör flera jobb, rekommenderar vi att du: 
- etikett diskar med motsvarande namn i jobbet.
- Uppdatera jobb med flera spårning suffixet med -1,-2 osv.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Bör jag ta något annat än Hårddisken i min paketet?
Leverera endast dina hårddiskar i paketet leverans. Inkludera inte ange strömkablar eller USB-kablar.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Måste jag mina enheter som använder FedEx eller DHL levereras?
Du kan leverera enheter till Azure-datacenter med några kända operatör som FedEx DHL, UPS eller oss postgång. Men vid returnerade leverans av enheter till dig från datacentret måste du ange:

- En FedEx kontonummer i USA och EU, eller
- DHL kontonummer i Asien och Australien regioner.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Finns det några begränsningar med leverans internationellt min enhet?
Observera att fysiska media som du levererar kan behöva korsa internationella gränser. Du ansvarar för att säkerställa att din fysiska media och dina data importeras eller exporteras enligt tillämplig lagstiftning. Kontrollera med din rådgivare att kontrollera att dina media och data enligt lag kan levereras till identifierade datacentret innan levereras det fysiska mediet. Detta hjälper för att säkerställa att den når Microsoft inom rimlig tid.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>När du skapar ett jobb är en plats som skiljer sig från min lagringskontoplatsen leveransadressen. Vad ska jag göra?

Vissa konto lagringsplatser mappas till alternativa leverans platser. Tidigare kan tillgängliga leverans platser också tillfälligt mappas till andra platser. Kontrollera alltid leveransadress tillhandahålls när jobbet skapas innan du levererar dina enheter.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>När leverans enheten begär en operatör data center adress och telefonnummer kontaktnumret. Vad bör jag ge?

Telefonnummer och DC-adress anges som en del av jobbet skapas.


## <a name="miscellaneous"></a>Övrigt

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Vad händer om jag av misstag skickar en Hårddisk som inte följer kraven stöds?

Azure-datacentret returneras stöds kravet på att du den enhet som inte överensstämmer. Om endast några av enheterna i paketet uppfyller kraven för stöd dessa enheter kommer att bearbetas och de enheter som inte uppfyller kraven kommer att returneras till dig.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Tjänsten formatera enheter innan du går tillbaka dem?

Nej. Alla enheter krypteras med BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hur kan jag åt data som importeras av den här tjänsten?

Använda Azure Portal eller [Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) åtkomst till data i Azure storage-konto.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>När importen är klar, vad min data ut i storage-konto? Bevaras directory-hierarkin

När du förbereder en hårddisk på en importjobb anges målet av DstBlobPathOrPrefix-fält i datamängden CSV. Detta är målbehållare i storage-konto som kopieras data från hårddisken. I den här målbehållare virtuella kataloger har skapats för mappar från hårddisken och blobbar skapas för filer. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Om en enhet har filer som redan finns i mitt lagringskonto, tjänsten skriva över befintliga blobbar eller filer?

Beror. När du förbereder enheten du kan ange om målfiler ska skrivas över eller ignoreras använda fält i datamängden CSV-fil som kallas Disposition: < Byt namn på | Nej skriva över | skriva över >. Som standard tjänsten byter namn på de nya filerna i stället skriva över befintliga blobbar eller filer.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Är verktyget WAImportExport kompatibel med 32-bitars operativsystem?
Nej. Verktyget WAImportExport är endast kompatibel med 64-bitars Windows-operativsystem. En fullständig lista över operativsystem som stöds går du till [operativsystem som stöds](). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Vad är maximal Blockblob och sidstorlek Blob som stöds av Azure Import/Export?

Max Blockblob är ungefär 4.768TB eller 5,000,000 MB.
Max Page Blob är 1TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Stöder Azure Import/Export AES 256-kryptering?
Azure Import/Export-tjänsten använder AES-128 bitlocker-kryptering som standard. Du kan ändra detta till AES 256 genom att manuellt krypteras med bitlocker innan data kopieras. 

- Om du använder [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), nedan visas ett exempel på kommando
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Om du använder [WAImportExport V2](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) ange ”AlreadyEncrypted” och ange nyckeln i driveset CSV.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Import/Export?](storage-import-export-service.md)



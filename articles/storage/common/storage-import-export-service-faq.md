---
title: Vanliga frågor och svar om Azure Import/Export-tjänsten | Microsoft-dokument
description: Läs svar på vanliga frågor om Azure Import Export-tjänsten.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: dcf9a278bfc0ede844f8eb59e3a256e40a47c57d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282434"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure Import/Export-tjänst: Vanliga frågor och svar

Följande är frågor och svar som du kan ha när du använder din Azure Import/Export-tjänst för att överföra data till Azure-lagring. Frågor och svar är ordnade i följande kategorier:

- Om tjänsten Importera/exportera
- Förbereda diskarna för import/export
- Importera/exportera jobb
- Leverera diskar
- Övrigt

## <a name="about-importexport-service"></a>Om tjänsten Importera/exportera

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Kan jag kopiera Azure File Storage med azure import/export-tjänsten?

Ja. Azure Import/Export-tjänsten stöder import till Azure File Storage. Det stöder inte export av Azure-filer just nu.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Är Azure Import/Export-tjänsten tillgänglig för CSP-prenumerationer?

Ja. Azure Import/Export-tjänsten stöder CSP-prenumerationer (Cloud Solution Providers).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Kan jag använda Azure Import/Export-tjänsten för att kopiera PST-postlådor och SharePoint-data till O365?

Ja. Mer information finns i [Importera PST-filer eller SharePoint-data till Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Kan jag använda Azure Import/Export-tjänsten för att kopiera mina säkerhetskopior offline till Azure Backup Service?

Ja. Mer information finns [i arbetsflödet för offlinesäkerhetskopiering i Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Kan jag köpa enheter för import-/exportjobb från Microsoft?

Nej. Du måste skicka dina egna enheter för import och export jobb.

## <a name="preparing-disks-for-importexport"></a>Förbereda diskar för import/export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kan jag hoppa över förberedelsesteget för ett importjobb? Kan jag förbereda en enhet utan att kopiera?

Nej. Alla enheter som används för att importera data måste förberedas med verktyget Azure WAImportExport. Använd verktyget för att även kopiera data till enheten.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Måste jag utföra några diskförberedelser när jag skapar ett exportjobb?

Nej. Vissa förkontroller rekommenderas. Om du vill kontrollera antalet diskar som krävs använder du kommandot FÖRhandsgranskningsexport för WAImportExport. Mer information finns i [Förhandsgranska enhetsanvändning för ett exportjobb](https://msdn.microsoft.com/library/azure/dn722414.aspx). Kommandot hjälper dig att förhandsgranska enhetsanvändning för de valda blobbar, baserat på storleken på de enheter du ska använda. Kontrollera också att du kan läsa från och skriva till hårddisken som levereras för exportjobbet.

## <a name="importexport-jobs"></a>Importera/exportera jobb

### <a name="can-i-cancel-my-job"></a>Kan jag säga upp mitt jobb?

Ja. Du kan avbryta ett jobb när dess status är **Skapa** eller **Leverera**. Utöver dessa steg kan jobbet inte avbrytas och det fortsätter fram till slutskedet.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Hur länge kan jag visa status för slutförda jobb i Azure-portalen?

Du kan visa status för slutförda jobb i upp till 90 dagar. Slutförda jobb tas bort efter 90 dagar.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Vad ska jag göra om jag vill importera eller exportera fler än 10 enheter?

Ett import- eller exportjobb kan bara referera till 10 enheter i ett enda jobb. Om du vill leverera fler än 10 enheter bör du skapa flera jobb. Enheter som är associerade med samma jobb måste levereras tillsammans i samma paket.
Om du vill ha mer information och vägledning när datakapaciteten sträcker sig över flera jobb för diskimport kontaktar du Microsoft Support.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Den uppladdade blobben visar status som "Lease expired". Vad ska jag göra?

Du kan ignorera fältet "Lånet har upphört att gälla". Import/Export tar lån på blobben under överföringen för att se till att ingen annan process kan uppdatera bloben parallellt. Lease Expired innebär att import/export inte längre överförs till det och blobben är tillgänglig för din användning.

## <a name="shipping-disks"></a>Leverera diskar

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Vad är det maximala antalet hårddiskar för i en sändning?

Det finns ingen gräns för antalet hårddiskar i en sändning. Om diskarna tillhör flera jobb rekommenderar vi att du:

- etikettera diskarna med motsvarande jobbnamn.
- uppdatera jobben med ett spårningsnummer som suffixats med -1, -2 etc.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Ska jag inkludera något annat än hårddisken i mitt paket?

Skicka endast dina hårddiskar i fraktpaketet. Ta inte med saker som nätkablar eller USB-kablar.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Måste jag skicka mina enheter med FedEx eller DHL?

Du kan skicka enheter till Azure-datacentret med alla kända bärare som FedEx, DHL, UPS eller US Postal Service. För returleverans av enheter till dig från datacentret måste du dock ange:

- Ett FedEx-kontonummer i USA och EU, eller
- Ett DHL-kontonummer i Asien och Australien.

> [!NOTE]
> Datacenter i Indien kräver ett deklarationsbrev på ditt brevhuvud (leverans challan) för att returnera enheterna. Om du vill ordna det nödvändiga inträdespasset måste du också boka hämtningen med din valda operatör och dela informationen med datacentret.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Finns det några begränsningar med frakt min enhet internationellt?

Observera att de fysiska medier som du skickar kan behöva passera internationella gränser. Du ansvarar för att dina fysiska medier och data importeras och/eller exporteras i enlighet med gällande lagar. Innan du skickar det fysiska mediet kontrollerar du med dina rådgivare att dina media och data kan skickas lagligt till det identifierade datacentret. Detta kommer att bidra till att säkerställa att den når Microsoft i tid.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Finns det några särskilda krav för att leverera mina diskar till ett datacenter?

Kraven beror på de specifika Azure-datacenterbegränsningarna.

- Det finns några platser som kräver ett Microsoft datacenter-inkommande ID-nummer som ska skrivas på paketet av säkerhetsskäl. Innan du skickar dina enheter eller diskar till datacentret kontaktar du Azure DataBox Operations (adbops@microsoft.com) för att hämta det här numret. Utan detta nummer kommer paketet att avvisas.
- Datacenter i Indien kräver personliga uppgifter om drivrutinen, till exempel myndighets-ID-kortet eller bevisetr. (till exempel PAN, AADHAR, DL), namn, kontakt och bilskyltsnummer för att få ett gate-ingångspass. Informera operatören om dessa krav för att undvika leveransförseningar.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>När du skapar ett jobb är leveransadressen en annan plats än min lagringskontoplats. Vad ska jag göra?

Vissa lagringskontoplatser mappas till alternativa leveransplatser. Tidigare tillgängliga leveransplatser kan också tillfälligt mappas till alternativa platser. Kontrollera alltid den leveransadress som anges när du skapar arbetstillfällen innan du skickar dina enheter.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>När du skickar min enhet frågar transportören efter kontaktadressen för datacentret och telefonnummer. Vad ska jag ge?

Telefonnumret och DC-adressen anges som en del av skapandet av arbetstillfällen.

## <a name="miscellaneous"></a>Övrigt

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Vad händer om jag av misstag skickar en hårddisk som inte uppfyller kraven som stöds?

Azure-datacentret returnerar den enhet som inte uppfyller kraven som stöds till dig. Om bara några av enheterna i paketet uppfyller supportkraven bearbetas dessa enheter och de enheter som inte uppfyller kraven returneras till dig.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Formaterar tjänsten enheterna innan de returneras?

Nej. Alla enheter är krypterade med BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hur kommer jag åt data som importeras av den här tjänsten?

Använd Azure-portalen eller [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) för att komma åt data under ditt Azure-lagringskonto.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>När importen är klar, hur ser mina data ut i lagringskontot? Är min kataloghierarki bevarad?

När du förbereder en hårddisk för ett importjobb anges målet av fältet DstBlobPathOrPrefix i datauppsättningen CSV. Det här är målbehållaren i lagringskontot som data från hårddisken kopieras till. I den här målbehållaren skapas virtuella kataloger för mappar från hårddisken och blobbar skapas för filer.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Om en enhet har filer som redan finns i mitt lagringskonto, skriver tjänsten över befintliga blobbar eller filer?

Beror. När du förbereder enheten kan du ange om målfilerna ska skrivas över eller ignoreras med hjälp av fältet i CSV-fil med datauppsättning som heter Disposition:<byta namn på|ingen överskrivning|skriva över>. Som standard byter tjänsten namn på de nya filerna i stället för att skriva över befintliga blobbar eller filer.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Är verktyget WAImportExport kompatibelt med 32-bitars operativsystem?

Nej. VERKTYGET WAImportExport är endast kompatibelt med 64-bitars Windows-operativsystem. En fullständig lista över operativsystem som stöds finns i [operativsystem som stöds](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Vilken är den maximala Block Blob- och Sidblobbstorleken som stöds av Azure Import/Export?

- Max Block Blob storlek är cirka 4.768TB eller 5.000.000 MB.
- Max sidblobar storlek är 8TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Stöder Azure Import/Export AES-256-kryptering?

Nej. Azure Import/Export-tjänsten använder AES-128 BitLocker-kryptering.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Import/Export för något?](storage-import-export-service.md)

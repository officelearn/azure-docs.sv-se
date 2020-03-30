---
title: Spåra och logga Azure Data Box, Azure Data Box Tunga händelser| Microsoft-dokument
description: Beskriver hur du spårar och loggar händelser i de olika stadierna av din Azure Data Box och Azure Data Box Heavy order.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 72e1d3b0ad72b1e68b88eb0550cbe839ade9d929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260025"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Spårning och händelseloggning för din Azure Data Box och Azure Data Box Heavy

En dataruta eller dataruta Tung ordning går igenom följande steg: ordning, uppsättning, datakopiering, retur, överföring till Azure och verifiera och dataradering. Som motsvarar varje steg i ordern kan du vidta flera åtgärder för att styra åtkomsten till ordern, granska händelserna, spåra ordern och tolka de olika loggar som genereras.

I följande tabell visas en sammanfattning av stegen Data Box eller Data Box Heavy order och de verktyg som finns tillgängliga för att spåra och granska ordern under varje steg.

| Orderfasen för databox       | Verktyg för att spåra och granska                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Skapa order               | [Ställ in åtkomstkontroll på ordern via RBAC](#set-up-access-control-on-the-order)                                                    |
| Ordning bearbetad            | [Spåra ordern](#track-the-order) genom <ul><li> Azure Portal </li><li> Transportföretagets webbplats </li><li>E-postmeddelanden</ul> |
| Konfigurera enhet              | Åtkomst till enhetsuppgifter som loggas i [Aktivitetsloggar](#query-activity-logs-during-setup)                                              |
| Datakopiering till enhet        | [Visa *error.xml-filer* ](#view-error-log-during-data-copy) för datakopiering                                                             |
| Förbereda för att skicka            | [Kontrollera strukturlistefilerna](#inspect-bom-during-prepare-to-ship) eller manifestfilerna på enheten                                      |
| Dataöverföring till Azure       | [Granska kopieringsloggar](#review-copy-log-during-upload-to-azure) för fel under dataöverföring på Azure datacenter                         |
| Dataradering från enhet   | [Visa butikskedjor](#get-chain-of-custody-logs-after-data-erasure) inklusive granskningsloggar och orderhistorik                |

I den här artikeln beskrivs i detalj de olika mekanismer eller verktyg som finns tillgängliga för att spåra och granska Data Box eller Data Box Heavy order. Informationen i den här artikeln gäller både Data Box och Data Box Heavy. I de följande avsnitten gäller alla hänvisningar till Data Box också för Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Ställ in åtkomstkontroll på ordern

Du kan styra vem som kan komma åt din beställning när ordern först skapas. Ställ in rollbaserade åtkomstkontroll (RBAC) roller vid olika scope för att styra åtkomsten till databox-ordern. En RBAC-roll bestämmer typen av åtkomst – läs-och skrivskyddad, skrivskyddad, skrivskyddad till en delmängd av åtgärder.

De två roller som kan definieras för Azure Data Box-tjänsten är:

- **Data Box Reader** - har skrivskyddad åtkomst till en eller de order som definieras av scopet. De kan bara visa information om en order. De kan inte komma åt någon annan information som rör lagringskonton eller redigera orderinformationen, till exempel adress och så vidare.
- **Data Box Contributor** - kan bara skapa en order för att överföra data till ett visst lagringskonto *om de redan har skrivåtkomst till ett lagringskonto*. Om de inte har åtkomst till ett lagringskonto kan de inte ens skapa en databox för att kopiera data till kontot. Den här rollen definierar inte några lagringskontorelaterade behörigheter eller ger åtkomst till lagringskonton.  

Om du vill begränsa åtkomsten till en order kan du:

- Tilldela en roll på ordernivå. Användaren har endast de behörigheter som definieras av rollerna för att interagera med den specifika databoxordningen och inget annat.
- Tilldela en roll på resursgruppsnivå, användaren har åtkomst till alla databoxorder inom en resursgrupp.

Mer information om föreslagen RBAC-användning finns i [Metodtips för RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Spåra beställningen

Du kan spåra din beställning via Azure-portalen och via transportföretagets webbplats. Följande mekanismer finns på plats för att spåra databoxens ordning när som helst:

- Om du vill spåra ordern när enheten finns i Azure-datacenter eller dina lokaler går du till din **databox-beställning > översikt** i Azure-portalen.

    ![Visa orderstatus och spåra nej](media/data-box-logs/overview-view-status-1.png)

- Om du vill spåra ordern medan enheten är i transit går du till den regionala carrier-webbplatsen, till exempel UPS-webbplatsen i USA. Ange spårningsnumret som är kopplat till din beställning.
- Data Box skickar också e-postmeddelanden när som helst orderstatusen ändras baserat på de e-postmeddelanden som angavs när ordern skapades. En lista över alla orderstatus för datarute finns i [Visa orderstatus](data-box-portal-admin.md#view-order-status). Information om hur du ändrar meddelandeinställningarna som är associerade med ordern finns i [Redigera meddelandeinformation](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Frågeaktivitetsloggar under installationen

- Databoxen anländer till din lokal i låst tillstånd. Du kan använda de enhetsautentiseringsuppgifter som finns i Azure-portalen för din beställning.  

    När en dataruta har konfigurerats kan du behöva veta vem som alla har åtkomst till enhetsautentiseringsuppgifterna. Om du vill ta reda på vem som har åtkomst till **autentiseringsuppgifterna** för enhet kan du fråga aktivitetsloggarna.  Alla åtgärder som innebär åtkomst till **enhetsinformation > autentiseringsbladet** loggas `ListCredentials` in i aktivitetsloggarna som åtgärd.

    ![Frågeaktivitetsloggar](media/data-box-logs/query-activity-log-1.png)

- Varje inloggning i datarutan loggas i realtid. Den här informationen är dock endast tillgänglig i [granskningsloggarna](#audit-logs) när ordern har slutförts.

## <a name="view-error-log-during-data-copy"></a>Visa fellogg under datakopiering

Under datakopian till Data Box eller Data Box Heavy genereras en felfil om det finns några problem med de data som kopieras.

### <a name="errorxml-file"></a>Filen Error.xml

Kontrollera att kopieringsjobben har slutförts utan fel. Om det finns fel under kopieringsprocessen hämtar du loggarna från sidan **Anslut och kopiera.**

- Om du kopierade en fil som inte är 512 byte justerad till en hanterad diskmapp i datarutan överförs filen inte som sidblobb till ditt mellanlagringslagringskonto. Du kommer att se ett fel i loggarna. Ta bort filen och kopiera en fil som är 512 byte justerad.
- Om du kopierade en VHDX, en dynamisk virtuell hårddisk eller en varierande virtuell hårddisk (dessa filer stöds inte) visas ett fel i loggarna.

Här är ett exempel på *error.xml* för olika fel vid kopiering till hanterade diskar.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Här är ett exempel på *error.xml* för olika fel vid kopiering till sidblobar.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Här är ett exempel på *error.xml* för olika fel vid kopiering för att blockera blobbar.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Här är ett exempel på *error.xml* för olika fel vid kopiering till Azure-filer.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

I vart och ett av ovanstående fall löser du felen innan du går vidare till nästa steg. Mer information om de fel som tas emot under datakopiering till Data Box via SMB- eller NFS-protokoll finns i [Felsöka dataruta och databoxen tunga problem](data-box-troubleshoot.md). Information om fel som tas emot under datakopiering till Data Box via REST finns i [Felsöka problem med lagring av dataruta blob.](data-box-troubleshoot-rest.md)

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspektera strukturlistan under förberedelserna för att leverera

Under förberedelserna för leverans skapas en lista över filer som kallas strukturlistan (Strukturlista) eller manifestfilen.

- Använd den här filen för att verifiera mot de faktiska namnen och antalet filer som har kopierats till datarutan.
- Använd den här filen för att verifiera mot filernas faktiska storlek.
- Kontrollera att *crc64* motsvarar en sträng som inte är noll. <!--A null value for crc64 indicates that there was a reparse point error)-->

Mer information om de fel som tas emot under förbereda leveransen finns i [Felsöka dataruta och databox tunga problem](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Strukturlista eller manifestfil

Strukturlistan eller manifestfilen innehåller en lista över alla filer som kopieras till databox-enheten. Strukturlistefilen har filnamn och motsvarande storlekar samt kontrollsumman. En separat strukturlistefil skapas för blockblobar, sidblobar, Azure-filer, för kopiering via REST-API:erna och för kopian till hanterade diskar i datarutan. Du kan hämta strukturlistefilerna från enhetens lokala webbgränssnitt under förbereda leveransen.

Dessa filer finns också på Data Box-enheten och överförs till det associerade lagringskontot i Azure-datacentret.

### <a name="bom-file-format"></a>Strukturlisteformat

Strukturliste- eller manifestfil har följande allmänna format:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Här är ett exempel på ett manifest som genererades när data kopierades till blockblolobresursen i datarutan.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Strukturlistan eller manifestfilerna kopieras också till Azure-lagringskontot. Du kan använda strukturlistan eller manifestfilerna för att verifiera att filer som överförs till Azure matchar de data som kopierades till datarutan.

## <a name="review-copy-log-during-upload-to-azure"></a>Granska kopieringsloggen under överföringen till Azure

Under dataöverföringen till Azure skapas en kopieringslogg.

### <a name="copy-log"></a>Kopiera logg

För varje order som bearbetas skapar databox-tjänsten kopieringsloggen i det associerade lagringskontot. Kopieringsloggen har det totala antalet filer som har överförts och antalet filer som felade under datakopian från Data Box till ditt Azure-lagringskonto.

En crc-beräkning (Cyclic Redundancy Check) görs under överföringen till Azure. Crc:erna från datakopian och efter dataöverföringen jämförs. En CRC-fel matchar att motsvarande filer inte kunde överföras.

Som standard skrivs loggar till `copylog`en behållare med namnet . Loggarna lagras med följande namngivningskonvention:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Sökvägen till kopieringsloggen visas också på **bladet Översikt** för portalen.

![Sökväg för att kopiera inloggningsbladet Översikt när den är klar](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Uppladdningen har slutförts 

I följande exempel beskrivs det allmänna formatet för en kopieringslogg för en databoxöverföring som har slutförts:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Uppladdningen har slutförts med fel 

Ladda upp till Azure kan också slutföra med fel.

![Sökväg för att kopiera logga in Översikt blad när du är klar med fel](media/data-box-logs/copy-log-path-2.png)

Här är ett exempel på en kopieringslogg där uppladdningen slutfördes med fel:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Uppladdningen har slutförts med varningar

Ladda upp till Azure slutförs med varningar om dina data hade behållar-/blob/filnamn som inte överensstämde med Azure-namngivningskonventioner och namnen ändrades för att överföra data till Azure.

![Sökväg för att kopiera logga in Översikt blad när den är klar med varningar](media/data-box-logs/copy-log-path-3.png)

Här är ett exempel på en kopieringslogg där behållarna som inte överensstämde med Azure-namngivningskonventioner döptes om under dataöverföringen till Azure.

De nya unika namnen för `DataBox-GUID` behållare är i formatet och data för behållaren läggs i den nya omdöpta behållaren. Kopieringsloggen anger det gamla och det nya behållarnamnet för behållaren.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Här är ett exempel på en kopieringslogg där blobbar eller filer som inte överensstämde med Azure-namngivningskonventioner, döptes om under dataöverföringen till Azure. De nya blob- eller filnamnen konverteras till SHA256-sammansmältning av relativ sökväg till behållare och överförs till sökvägen baserat på måltyp. Målet kan vara blockblobar, sidblobar eller Azure-filer.

Anger `copylog` det gamla och det nya blob- eller filnamnet och sökvägen i Azure.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Få kedja av vårdnadsloggar efter dataradering

När data har raderats från Data Box-diskarna enligt riktlinjerna NIST SP 800-88 Revision 1 finns kedjan av vårdnadsloggar tillgängliga. Dessa loggar inkluderar granskningsloggarna och orderhistoriken. Strukturlistan eller manifestfilerna kopieras också med granskningsloggarna.

### <a name="audit-logs"></a>Granskningsloggar

Granskningsloggar innehåller information om hur du aktiverar och kommer åt resurser i datarutan eller datarutan Tung när den ligger utanför Azure-datacenter. Dessa loggar finns på:`storage-account/azuredatabox-chainofcustodylogs`

Här är ett exempel på granskningsloggen från en dataruta:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Ladda ned beställningshistorik

Orderhistorik är tillgänglig i Azure-portalen. Om ordern är klar och enhetsrensningen (dataradering från diskarna) är klar går du till enhetsordningen och navigerar till **Orderinformation**. Alternativet **Ladda ned beställningshistorik** är tillgängligt. Mer information finns i [Hämta orderhistorik](data-box-portal-admin.md#download-order-history).

Om du bläddrar igenom orderhistoriken ser du:

- Operatörsspårningsinformation för din enhet.
- Händelser med *SecureErase-aktivitet.* Dessa händelser motsvarar radering av data på disken.
- Logglänkar för Data Box. Sökvägarna för *granskningsloggarna,* *kopieringsloggarna*och *strukturlistefilerna* visas.

Här är ett exempel på orderhistorikloggen från Azure Portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Nästa steg

- Läs om hur du [felsöker problem i databoxen och databoxen tung](data-box-troubleshoot.md).

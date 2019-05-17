---
title: Spåra och logga Azure Data Box-händelser | Microsoft Docs
description: Beskriver hur du spårar och loggar händelser i de olika stegen i din Azure Data Box-beställning.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/14/2019
ms.author: alkohli
ms.openlocfilehash: 7a6adc72c1dfbe67311ae2ca98d5b07dfab41719
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806513"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box"></a>Spårning och händelseloggning för din Azure Data Box

En Data Box-beställning går igenom följande steg: ordning ställer in, data kopiera, returnera, överför till Azure och verifiera, och dataradering. Motsvarar varje steg i ordningen som kan du utföra flera åtgärder för att styra åtkomsten till ordningen, granska händelserna, spåra ordningen och tolka flera loggar som genereras.

I följande tabell visas en sammanfattning av stegen för Data Box-beställning och verktyg som finns tillgängliga att spåra och granska ordningen under varje steg.

| Data Box ordning steg       | Verktyg för att spåra och granska                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Skapa order               | [Konfigurera åtkomstkontroll på för via RBAC](#set-up-access-control-on-the-order)                                                    |
| Ordern bearbetas            | [Spåra ordningen](#track-the-order) via <ul><li> Azure Portal </li><li> Levererade operatör webbplats </li><li>E-postmeddelanden</ul> |
| Konfigurera enhet              | Enhetens autentiseringsuppgifter för åtkomst som loggat in [aktivitetsloggar](#query-activity-logs-during-setup)                                              |
| Kopiera data till enheten        | [Visa *error.xml* filer](#view-error-log-during-data-copy-to-data-box) för kopiering av data                                                             |
| Förbered för att skicka            | [Granska BOM filer](#inspect-bom-during-prepare-to-ship) eller manifestfiler på enheten                                      |
| Ladda upp data till Azure       | [Granska *copylogs* ](#review-copy-log-during-upload-to-azure) för fel under data laddar du upp på Azure-datacenter                         |
| Raderingen av data från enhet   | [Visa kedjan av spårbarhet loggar](#get-chain-of-custody-logs-after-data-erasure) inklusive granskningsloggar och ordning historik                                                   |

Den här artikeln beskrivs i detalj olika mekanismer eller verktyg som är tillgängliga för att spåra och granska Data Box-beställning.

## <a name="set-up-access-control-on-the-order"></a>Konfigurera åtkomstkontroll på ordning

Du kan styra vem som kan komma åt din beställning när ordningen som skapas. Ställa in roller för rollbaserad åtkomstkontroll (RBAC) på olika områden att styra åtkomsten till Data Box-beställning. RBAC-roll avgör vilken typ av åtkomst – skrivskyddad, skrivskyddad, Läs-och till en delmängd av åtgärder.

De två Data Box-roller som kan definieras är:

- **Data Box-läsare** -har skrivskyddad åtkomst till en eller flera enligt definitionen i omfånget. De kan bara visa information om en order. De kan inte komma åt andra data som är relaterade till storage-konton eller redigera beställningsinformation som adress och så vidare.
- **Data Box-deltagare** -kan bara skapa en order för att överföra data till ett visst lagringskonto *om de redan har skrivåtkomst till ett lagringskonto*. Om de inte har åtkomst till ett lagringskonto kan inte de även skapa en Data Box-beställning att kopiera data till kontot. Den här rollen definierar inte alla lagringskonton relaterade behörigheter eller beviljar åtkomst till storage-konton.  

Om du vill begränsa åtkomsten till en beställning, kan du:

- Tilldela en roll på en nivå i ordning. Användaren har bara de behörigheterna som definieras av rollerna som kan interagera med den specifika Data Box-beställningen endast och inget annat.
- Tilldela en roll på resursgruppsnivå, användaren har åtkomst till alla Data Box-order i en resursgrupp.

Mer information om den föreslagna RBAC använder Se [bästa praxis för RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Spåra beställningen

Du kan spåra din beställning via Azure-portalen och via webbplatsen endash operatör. Följande metoder är på plats för att spåra Data Box-beställning när som helst:

- Om du vill följa ordningen när enheten är i Azure-datacenter eller ditt lokala nätverk, går du till din **Data Box-beställning > Översikt** i Azure-portalen.

    ![Visa beställningsstatus och spåra Nej](media/data-box-logs/overview-view-status-1.png)

- Om du vill följa ordningen vid överföring, går du till webbplatsen regionala operatör, till exempel UPS webbplats i USA. Ange Spårningsnumret som är associerade med din beställning.
- Data Box skickar också e-postaviseringar när som helst order status ändras, baserat på e-postmeddelanden när ordern har skapats. En lista över alla statusar för Data Box-beställning finns i [Visa beställningsstatus](data-box-portal-admin.md#view-order-status). Om du vill ändra inställningar för meddelanden som är associerade med ordningen som [redigera meddelandeinformation](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Fråga aktivitetsloggar under installationen

- Din Data box-enhet kommer lokalt i ett låst tillstånd. Du kan använda enhetens autentiseringsuppgifter tillgängliga i Azure-portalen för din beställning.  

    När en Data Box har konfigurerats, kan du behöva veta vem som alla åtkomst till enhetens autentiseringsuppgifter. Att ta reda på vem som åtkomst till den **enhetsautentiseringsuppgifterna** bladet kan du fråga aktivitetsloggar.  Alla åtgärder som involverar åtkomst **enhetsinformation > autentiseringsuppgifter** bladet är inloggad i aktivitetsloggarna som `ListCredentials` åtgärd.

    ![Frågeaktivitetsloggar](media/data-box-logs/query-activity-log-1.png)

- Varje logga in på Data Box är loggade realtid. Den här informationen är dock endast tillgängliga i den [granskningsloggar](#audit-logs) när ordningen som har slutförts.

## <a name="view-error-log-during-data-copy-to-data-box"></a>Visa felloggen under Datakopieringen till Data Box

Under Datakopieringen till Data Box skapas en felfilen om det finns några problem med de data som kopieras.

### <a name="errorxml-file"></a>Error.XML fil

Se till att kopiera jobben har slutförts utan fel. Om det uppstår fel under kopieringen kan hämta loggar från den **Anslut och kopiera** sidan.

- Om du har kopierat en fil som är inte 512 byte justeras till en hanterad disk-mapp på din Data Box är inte överföra filen som sidblobb till mellanlagring storage-kontot. Ett fel i loggarna visas. Ta bort filen och kopiera en fil som är 512 byte justerad.
- Om du har kopierat en VHDX eller en dynamisk virtuell Hårddisk eller en differentierande virtuell Hårddisk (dessa filer inte stöds), visas ett fel i loggarna.

Här följer ett exempel på den *error.xml* för olika fel vid kopiering till hanterade diskar.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Här följer ett exempel på den *error.xml* för olika fel när du kopierar till sidblobar.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Här följer ett exempel på den *error.xml* för olika fel när du kopierar till blockblobar.

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

Här följer ett exempel på den *error.xml* för olika fel när du kopierar till Azure Files.

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

Åtgärda felen innan du fortsätter till nästa steg i var och en av de ovanstående fall. Mer information om de fel som togs emot under Datakopieringen till Data Box via SMB- eller NFS-protokoll, går du till [felsöka Data Box utfärdar](data-box-troubleshoot.md). Information om fel togs emot under Datakopieringen till Data Box via REST, går du till [felsöka Blob till Data lagringsproblem](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspektera BOM under Förbered för att skicka

Under Förbered för att skicka en lista över filer som är känd som faktura material (BOM) eller manifestfilen har skapats.

- Använd den här filen för att verifiera mot de faktiska namnen och antalet filer som har kopierats till Data Box.
- Använd den här filen för att verifiera mot de faktiska storleken på filerna.
- Kontrollera att den *crc64* motsvarar en sträng som inte är noll. <!--A null value for crc64 indicates that there was a reparse point error)-->

Mer information om de fel som togs emot under förbereda för att leverera, gå till [felsöka Data Box utfärdar](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>BOM eller manifest-fil

BOM eller Manifestfilen innehåller en lista över alla filer som kopieras till Data Box-enhet. Filen BOM har filnamn och motsvarande storlekar samt kontrollsumman. En separat BOM-fil skapas för blockblobbarna, sidblobar, Azure Files, för kopiering via REST-API: er och för att kopiera till hanterade diskar, på Data Box. Du kan hämta BOM-filer från det lokala webbgränssnittet på enheten under Förbered för att skicka.

Dessa filer kan du också finnas på Data Box-enhet och laddas upp till det associerade lagringskontot i Azure-datacentret.

### <a name="bom-file-format"></a>BOM filformat

BOM eller manifest-fil har följande allmänna format:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Här är ett exempel på ett manifest som genereras när data har kopierats till block blob-resurs på Data Box.

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

BOM eller manifestfiler kopieras även till Azure storage-kontot. Du kan använda Strukturen eller verifiera att filer som överförts till Azure matchar de data som har kopierats till Data Box-manifest.

## <a name="review-copy-log-during-upload-to-azure"></a>Granska kopiera loggen vid överföring till Azure

När data överfördes till Azure, en *copylog* har skapats.

### <a name="copylog"></a>Copylog

För varje order bearbetas Data Box-tjänsten skapar *copylog* i det associerade lagringskontot. Den *copylog* har det totala antalet filer som laddades upp och antalet filer som felaktiga ut under data kopieras Data Box till Azure storage-kontot.

En cyklisk redundans Kontrollera (CRC) beräkning görs under överföringen till Azure. CRC från Datakopieringen och ladda upp data jämförs. Ett CRC-matchningsfel anger att motsvarande filer inte kunde överföra.

Som standard skrivs loggarna till en behållare med namnet copylog. Loggfilerna lagras med följande namngivningskonvention:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Sökvägen copylog visas även på den **översikt** bladet på portalen.

![Sökvägen till copylog i översiktsbladet när du är klar](media/data-box-logs/copy-log-path-1.png)

I följande exempel beskrivs det allmänna formatet för en copylog-fil för en Data Box överför som har slutförts:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Ladda upp till Azure kan även utföra med fel.

![Sökvägen till copylog i översiktsbladet när slutfördes med fel](media/data-box-logs/copy-log-path-2.png)

Här är ett exempel på en copylog där överföringen slutfördes med fel:

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


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Hämta certifikatkedja spårbarhet loggar efter dataradering

När data raderas från Data Box-diskar enligt SP NIST 800-88 Revision 1 riktlinjer, kedjan av spårbarhet loggar är tillgängliga. Dessa loggar är granskningsloggarna och orderhistorik. BOM eller manifestfiler kopieras även med granskningsloggar.

### <a name="audit-logs"></a>Granskningsloggar

Granskningsloggar innehåller information om ström på och dela åtkomst på Data Box när det är utanför Azure-datacenter. Dessa loggar finns på: `storage-account/azuredatabox-chainofcustodylogs`

Här är ett exempel på granskningsloggen från en Data Box:

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

Orderhistorik är tillgänglig i Azure portal. Om den är färdig och enheten rensningen (dataradering från diskarna) är klar, går du till **Data Box-beställning > Order information**. ** Ladda ned orderhistorik** alternativet är tillgängligt. Mer information finns i [hämta orderhistorik](data-box-portal-admin.md#download-order-history).

Om du rullar orderhistorik visas:

- Operatör spårningsinformation för din enhet.
- Händelser med *SecureErase* aktivitet. De här händelserna motsvarar raderingen av alla data på disken.
- Data Box log länkar. Sökvägar för den *granskningsloggar*, *copylogs*, och *BOM* filer visas.

Här är ett exempel på händelseloggen ordning från Azure-portalen:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-pinto                              
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
Copy Logs Path       : databoxcopylog/gus-pinto_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsökning av problem på din Data Box](data-box-troubleshoot.md).

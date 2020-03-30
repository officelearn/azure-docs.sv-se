---
title: Felsöka problem med Azure Files i Linux | Microsoft-dokument
description: Felsöka Problem med Azure Files i Linux
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 95e220102cba290664a32cb6bbebef881ae4ffde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159497"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Felsöka problem med Azure Files i Linux

I den här artikeln visas vanliga problem som är relaterade till Azure-filer när du ansluter från Linux-klienter. Det ger också möjliga orsaker och lösningar på dessa problem. 

Förutom felsökningsstegen i den här artikeln kan du använda [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) för att säkerställa att Linux-klienten har korrekta förutsättningar. AzFileDiagnostics automatiserar upptäckten av de flesta av de symtom som nämns i denna artikel. Det hjälper till att konfigurera din miljö för att få optimal prestanda. Du hittar även den här informationen i [felsökaren för Azure-filer.](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) Felsökaren innehåller steg som hjälper dig med problem med att ansluta, mappa och montera Azure Files-resurser.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Det går inte att ansluta till eller montera en Azure-filresurs

### <a name="cause"></a>Orsak

Vanliga orsaker till det här problemet är:

- Du använder en inkompatibel Linux-distributionsklient. Vi rekommenderar att du använder följande Linux-distributioner för att ansluta till en Azure-filresurs:

|   | SMB 2.1 <br>(Monterar på virtuella datorer inom samma Azure-region) | SMB 3.0 <br>(Fästen från lokala och tvärregionerade) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7,5+ |
| CentOS | 7+ |  7,5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42,3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- CIFS-verktyg (cifs-utils) är inte installerade på klienten.
- Den minsta SMB/CIFS-versionen, 2.1, är inte installerad på klienten.
- SMB 3.0-kryptering stöds inte på klienten. Tabellen föregående innehåller en lista över Linuxdistributioner som stöder montering från lokala och korsregionövergripande med hjälp av kryptering. Andra distributioner kräver kernel 4.11 och senare versioner.
- Du försöker ansluta till ett lagringskonto via TCP-port 445, vilket inte stöds.
- Du försöker ansluta till en Azure-filresurs från en Virtuell Azure-dator och den virtuella datorn är inte i samma region som lagringskontot.
- Om inställningen [för säker överföring]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) som krävs är aktiverad på lagringskontot tillåter Azure-filer endast anslutningar som använder SMB 3.0 med kryptering.

### <a name="solution"></a>Lösning

LÃ¶s problemet genom att [ansã¤ggã¶sa felsÃ¶kningsÃ¶rsÃ¶kning av Azure Files på Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Detta verktyg:

* Hjälper dig att validera klientens körmiljö.
* Identifierar den inkompatibla klientkonfigurationen som skulle orsaka åtkomstfel för Azure-filer.
* Ger normativ vägledning om självfixering.
* Samlar in diagnostikspårningarna.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Montera fel(13): Behörighet nekad" när du monterar en Azure-filresurs

### <a name="cause-1-unencrypted-communication-channel"></a>Orsak 1: Okrypterad kommunikationskanal

Av säkerhetsskäl blockeras anslutningar till Azure-filresurser om kommunikationskanalen inte är krypterad och om anslutningsförsöket inte görs från samma datacenter där Azure-filresurserna finns. Okrypterade anslutningar inom samma datacenter kan också blockeras om inställningen [Säker överföring krävs ](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad på lagringskontot. En krypterad kommunikationskanal tillhandahålls endast om användarens klientoperativsystem stöder SMB-kryptering.

Mer information finns i [Krav för att montera en Azure-filresurs med Linux och paketet cifs-utils](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. Anslut från en klient som stöder SMB-kryptering eller ansluta från en virtuell dator i samma datacenter som Azure-lagringskontot som används för Azure-filresursen.
2. Kontrollera att inställningen för [säker överföring är](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) inaktiverad på lagringskontot om klienten inte stöder SMB-kryptering.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 2: Regler för virtuellt nätverk eller brandvägg är aktiverade på lagringskontot 

Om virtuellt nätverk (VNET) och brandväggsregler har konfigurerats på lagringskontot tillåts inte nätverkstrafik om inte åtkomst tillåts för klientens IP-adress eller virtuella nätverk.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Kontrollera att det virtuella nätverket och brandväggsreglerna har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggsreglerna som orsakar problemet kan du tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[behörighets nekad] Diskkvoten har överskridits" när du försöker öppna en fil

I Linux visas ett felmeddelande som liknar följande:

**\<filnamn> [behörighet nekad] Diskkvoten överskreds**

### <a name="cause"></a>Orsak

Du har nått den övre gränsen för samtidiga öppna referenser som är tillåtna för en fil.

Det finns en kvot på 2 000 öppna handtag på en enda fil. När du har 2 000 öppna handtag visas ett felmeddelande som säger att kvoten har uppnåtts.

### <a name="solution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga vissa referenser och försök sedan igen.

Om du vill visa öppna referenser för en filresurs, katalog eller fil använder du cmdleten [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Om du vill stänga öppna referenser för en filresurs, katalog eller fil använder du cmdleten [Stäng-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell.

> [!Note]  
> Cmdletsna Get-AzStorageFileHandle och Close-AzStorageFileHandle ingår i Az PowerShell-modul version 2.4 eller senare. Information om hur du installerar den senaste Az PowerShell-modulen finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Långsam filkopiering till och från Azure-filer i Linux

- Om du inte har ett specifikt lägsta I/O-storlekskrav rekommenderar vi att du använder 1 MiB som I/O-storlek för optimal prestanda.
- Använd rätt kopieringsmetod:
    - Använd [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för överföring mellan två filresurser.
    - Om du använder cp eller dd med parallell kan kopieringshastigheten förbättras beror antalet trådar på ditt användningsfall och din arbetsbelastning. I följande exempel används sex: 
    - cp exempel (cp kommer att använda standard blockstorlek av `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`filsystemet som segmentstorlek): .
    - dd exempel (det här kommandot anger uttryckligen segmentstorlek till 1 MiB):`find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Verktyg med öppen källkod, till exempel:
        - [GNU Parallell](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) - Sorterar filer och packar dem i partitioner.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) - Använder Fpart och ett kopieringsverktyg för att skapa flera instanser för att migrera data från src_dir till dst_url.
        - [Multi](https://github.com/pkolano/mutil) - Flertrådade cp och md5sum baserade på GNU coreutils.
- Om du ställer in filstorleken i förväg, i stället för att varje skrivning görs till en utökad skrivning, kan du förbättra kopieringshastigheten i scenarier där filstorleken är känd. Om du måste undvika att utöka skrivningar kan du `truncate - size <size><file>` ange en målfilstorlek med kommandot. Därefter kopierar `dd if=<source> of=<target> bs=1M conv=notrunc`kommandot en källfil utan att behöva uppdatera målfilens storlek upprepade gånger. Du kan till exempel ange målfilstorleken för varje fil som du vill kopiera (anta att en resurs är monterad under /mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - och sedan - kopiera filer utan att förlänga skrivningar parallellt:`$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Montera fel(115): Åtgärden pågår nu" när du monterar Azure-filer med hjälp av SMB 3.0

### <a name="cause"></a>Orsak

Vissa Linux-distributioner stöder inte ännu krypteringsfunktionerna i SMB 3.0. Användare kan få felmeddelandet ”115” om de försöker montera Azure Files med hjälp av SMB 3.0 på grund av att en funktion saknas. SMB 3.0 med fullständig kryptering stöds endast om du använder Ubuntu 16.04 eller senare.

### <a name="solution"></a>Lösning

Krypteringsfunktionen för SMB 3.0 för Linux introducerades i kernel 4.11. Den här funktionen möjliggör montering av en Azure-filresurs från en lokal plats eller från en annan Azure-region. Vissa Linux-distributioner kan ha bakåtporterat ändringar från 4.11-kärnan till äldre versioner av Linux-kärnan som de underhåller. Rådgör med [Använd Azure Files med Linux](storage-how-to-use-files-linux.md)för att avgöra om din version av Linux stöder SMB 3.0 med kryptering. 

Om din Linux SMB-klient inte stöder kryptering monterar du Azure Files med SMB 2.1 från en virtuell Linux-dator på Azure som är i samma datacenter som filresursen. Kontrollera att inställningen [Säker överföring krävs]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad för lagringskontot. 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fel "Ingen åtkomst" vid försök att komma åt eller ta bort en Azure-filresurs  
NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att komma åt eller ta bort en Azure-filresurs i portalen kan fÃ¶ ¶ã¥tkomst visas fÃ¶ã¥tkomst:

Ingen åtkomst  
Felkod: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 1: Regler för virtuellt nätverk eller brandvägg är aktiverade på lagringskontot

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

Kontrollera att det virtuella nätverket och brandväggsreglerna har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggsreglerna som orsakar problemet kan du tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Orsak 2: Ditt användarkonto har inte åtkomst till lagringskontot

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Bläddra till lagringskontot där Azure-filresursen finns, klicka på **Åtkomstkontroll (IAM)** och verifiera att ditt användarkonto har åtkomst till lagringskontot. Mer information finns i [Så här skyddar du ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC).](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection)

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Det går inte att ta bort en fil eller katalog i en Azure-filresurs

### <a name="cause"></a>Orsak
Det här problemet uppstår vanligtvis om filen eller katalogen har ett öppet handtag. 

### <a name="solution"></a>Lösning

Om SMB-klienterna har stängt alla öppna referenser och problemet fortsätter att uppstå utför du följande:

- Använd [cmdleten Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell för att visa öppna referenser.

- Använd [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell-cmdlet för att stänga öppna handtag. 

> [!Note]  
> Cmdletsna Get-AzStorageFileHandle och Close-AzStorageFileHandle ingår i Az PowerShell-modul version 2.4 eller senare. Information om hur du installerar den senaste Az PowerShell-modulen finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Dåliga prestanda för en Azure-filresurs monterad i en virtuell Linux-dator

### <a name="cause-1-caching"></a>Orsak 1: Caching

En möjlig orsak till långsam prestanda är inaktiverad cachelagring. Cachelagring kan vara användbart om du använder en fil flera gånger, annars kan det vara en overhead. Kontrollera om du använder cachen innan du inaktiverar den.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

Om du vill kontrollera om cachelagring är inaktiverat letar du efter **cache=-posten.**

**Cache=none** anger att cachelagring är inaktiverat. Sätt tillbaka resursen med hjälp av standardmonteringskommandot eller genom att uttryckligen lägga till alternativet **cache=strikt** i monteringskommandot för att säkerställa att standardcache- eller "strikt" cachelagringsläge är aktiverat.

I vissa fall kan **alternativet serverino** montera orsaka **ls** kommandot att köra stat mot varje katalogpost. Detta resulterar i prestandaförsämring när du listar en stor katalog. Du kan kontrollera monteringsalternativen i din **/etc/fstab-post:**

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Du kan också kontrollera om rätt alternativ används genom att köra kommandot **sudo mount | grep cifs** och kontrollera dess utdata. Följande är exempel på utdata:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Om alternativet **cache=strict** eller **serverino** inte finns, avmontera och montera Azure-filer igen genom att köra kommandot montera från [dokumentationen](../storage-how-to-use-files-linux.md). Kontrollera sedan att **posten /etc/fstab** har rätt alternativ.

### <a name="cause-2-throttling"></a>Orsak 2: Begränsning

Det är möjligt att du upplever begränsning och dina förfrågningar skickas till en kö. Du kan verifiera detta genom att använda [Azure Storage-mått i Azure Monitor](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Se till att din app är inom [Azure Files skala mål](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Tidsstämplar gick förlorade när filer kopierades från Windows till Linux

På Linux/Unix-plattformar misslyckas **cp-p-kommandot** om olika användare äger fil 1 och fil 2.

### <a name="cause"></a>Orsak

Kraftflaggan **f** i COPYFILE resulterar i att **cp -p -f** körs på Unix. Det här kommandot kan inte heller bevara tidsstämpeln för filen som du inte äger.

### <a name="workaround"></a>Lösning

Använd lagringskontoanvändaren för att kopiera filerna:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: kan&lt;inte&gt;komma åt " sökvägen ': Indata-/utdatafel

När du försöker lista filer i en Azure-filresurs med kommandot ls hänger kommandot ls upp när du listar filer. Du får följande felmeddelande:

**ls: kan&lt;inte&gt;komma åt sökvägen ': Indata-/utdatafel**


### <a name="solution"></a>Lösning
Uppgradera Linux-kärnan till följande versioner som har en korrigering för det här problemet:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Alla versioner som är större än eller lika med 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Det går inte att skapa symboliska länkar - ln: det gick inte att skapa symbolisk länk 't': Åtgärden stöds inte

### <a name="cause"></a>Orsak
Som standard aktiveras inte stöd för symboliska länkar (symlänkar) genom att montera Azure-filresurser på Linux med hjälp av CIFS. Du ser ett fel som detta:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Lösning
Linux CIFS-klienten stöder inte skapandet av symboliska länkar i Windows-stil över SMB 2- eller 3-protokollet. För närvarande stöder Linux-klienten en annan typ av symboliska länkar som kallas [Minshall + franska symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) för både skapa och följa operationer. Kunder som behöver symboliska länkar kan använda "mfsymlinks" montera alternativet. Vi rekommenderar "mfsymlinks" eftersom det också är det format som Mac-datorer använder.

Om du vill använda symlinks lägger du till följande i slutet av cifs-monteringskommandot:

```
,mfsymlinks
```

Så kommandot ser ut ungefär som:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Du kan sedan skapa symlinks som föreslås på [wikin](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Mount error(112): Värden är nere" på grund av en time-out för återanslutning

Monteringsfel 112 uppstår på Linux-klienten när klienten har varit inaktiv under en längre tid. Tidsgränsen för anslutningen nås efter en längre tids inaktivitet och klienten kopplas från.  

### <a name="cause"></a>Orsak

Anslutningen kan vara inaktiv av följande skäl:

-   Nätverkskommunikationsfel som förhindrar att en TCP-anslutning till servern återupprättas när standardalternativet ”mjukmontering” används
-   Nya återanslutningskorrigeringar som inte finns i äldre kernlar

### <a name="solution"></a>Lösning

Det här återanslutningsproblemet i Linux-kernel har nu åtgärdats som en del av följande ändringar:

- [Korrigering av återanslutning för att inte skjuta upp smb3-sessionsåteranslutning långt efter socketåteranslutning](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Anrop av ekotjänst omedelbart efter socketåteranslutning](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Åtgärda en eventuell minneskorruption under återanslutning](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Åtgärda en möjlig dubbellåsning av mutex under återanslutning (för kernel v4.9 och senare)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

De här ändringarna kan emellertid inte portas ännu till alla Linux-distributioner. Om du använder en populär Linux-distribution kan du kontrollera [vilken](storage-how-to-use-files-linux.md) version av distributionen som krävs för att se vilken version av distributionen som krävs.

### <a name="workaround"></a>Lösning

Du kan undvika det här problemet genom att ange en hårdmontering. Med en hårdmontering tvingas klienten vänta tills en anslutning har upprättats eller tills den avbryts explicit. Du kan använda den för att undvika fel på grund av att tidsgränsen nås för nätverket. Den här lösningen kan dock orsaka obestämd väntan. Var beredd på att stoppa anslutningar vid behov.

Om du inte kan uppgradera till de senaste kernel-versionerna kan du lösa problemet genom att behålla en fil i Azure-filresursen som du skriver till minst var 30:e sekund. Detta måste vara en skrivåtgärd, till exempel omskrivning av datumet (skapad eller ändrad) i filen. Annars kan du få cachelagrade resultat och åtgärden kanske inte utlöser återanslutningen.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>"CIFS VFS: error -22 on ioctl to get interface list" när du monterar en Azure-filresurs med hjälp av SMB 3.0

### <a name="cause"></a>Orsak
Det här felet loggas eftersom Azure Files [för närvarande inte stöder SMB-flerkanalig](https://docs.microsoft.com/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

### <a name="solution"></a>Lösning
Det här felet kan ignoreras.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.

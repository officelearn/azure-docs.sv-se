---
title: Felsöka problem i Azure Files i Linux | Microsoft Docs
description: Felsökning av problem i Azure Files i Linux
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 97f737c8d1228bd03baf59f2ebe830f715241299
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449848"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Felsöka problem i Azure Files i Linux

Den här artikeln innehåller vanliga problem som rör Azure Files när du ansluter från Linux-klienter. Det ger också möjliga orsaker och lösningar för dessa problem. 

Utöver felsökningsstegen i den här artikeln, kan du använda [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) så att den Linux-klienten har rätt krav. AzFileDiagnostics automatiserar identifiering för de flesta av de problem som nämns i den här artikeln. Det hjälper dig att konfigurera din miljö för att få bästa möjliga prestanda. Du kan också hitta den här informationen i den [Azure Files delar felsökare](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). Felsökaren innehåller steg för att hjälpa dig med problem med att ansluta, mappa och montera Azure-filresurser.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Det går inte att ansluta till eller montera en Azure-filresurs

### <a name="cause"></a>Orsak

Vanliga orsaker till det här problemet är:

- Du använder en inkompatibel klient för Linux-distribution. Vi rekommenderar att du använder följande Linux-distributioner för att ansluta till en Azure-filresurs:

|   | SMB 2.1 <br>(Monterar på virtuella datorer i samma Azure-region) | SMB 3.0 <br>(Monterar från både lokalt och över olika regioner) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- CIFS-verktyg (cfs utils) har inte installerats på klienten.
- Den lägsta SMB/CIFS-versionen 2.1, installeras inte på klienten.
- SMB 3.0-kryptering stöds inte på klienten. Den föregående tabellen innehåller en lista med Linux-distributioner som stöd för montering från både lokalt och över flera regioner med hjälp av kryptering. Andra distributioner kräver kernel 4.11 och senare versioner.
- Du försöker ansluta till ett lagringskonto via TCP-port 445, vilket inte stöds.
- Du försöker ansluta till en Azure-filresurs från en Azure-dator och den virtuella datorn är inte i samma region som lagringskontot.
- Om den [säker överföring krävs]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad på lagringskontot, Azure Files tillåter endast anslutningar som använder SMB 3.0 med kryptering.

### <a name="solution"></a>Lösning

Lös problemet genom att använda den [felsökningsverktyget för Azure Files-monteringsfel på Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Det här verktyget:

* Hjälper dig att validera klienten köra-miljö.
* Identifierar inkompatibla klientkonfigurationen som skulle orsaka fel åtkomst för Azure Files.
* Ger vägledning på att åtgärda själv.
* Samlar in diagnostik-spårningar.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>”Montera error(13): Åtkomst nekad ”när du monterar en Azure-filresurs

### <a name="cause-1-unencrypted-communication-channel"></a>Orsak 1: Okrypterade kommunikationskanalen

Av säkerhetsskäl blockeras anslutningar till Azure-filresurser om kommunikationskanalen inte är krypterad och om anslutningsförsöket inte görs från samma datacenter där de Azure-filresurserna finns. Okrypterade anslutningar inom samma datacenter kan också blockeras om de [säker överföring krävs](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad på lagringskontot. En krypterade kommunikationskanaler tillhandahålls endast om användarens klientoperativsystem stöder SMB-kryptering.

Mer information finns i [krav för att montera en Azure-fil dela med Linux- och cifs-utils-paketet](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package). 

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. Ansluta från en klient som stöder SMB-kryptering eller ansluta från en virtuell dator i samma datacenter som Azure storage-kontot som används för Azure-filresursen.
2. Kontrollera den [säker överföring krävs](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) inställningen är inaktiverad på storage-konto om klienten inte har stöd för SMB-kryptering.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 2: Virtuella nätverk eller brandvägg regler har aktiverats för lagringskontot 

Om virtuella nätverk (VNET) och brandväggsregler har konfigurerats på lagringskontot, nekas nätverkstrafik åtkomst om inte klientens IP-adress eller virtuella nätverk har åtkomst.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Verifiera virtuella nätverk och brandvägg regler har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggen regler som orsakar problemet tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>”[behörighet nekas] diskkvoten har överskridits” när du försöker öppna en fil

I Linux visas ett felmeddelande som liknar följande:

**\<filename > [behörighet nekas] diskkvoten har överskridits**

### <a name="cause"></a>Orsak

Du har nått den övre gränsen för samtidiga öppna referenser som tillåts för en fil.

Det finns en kvot på 2 000 öppna referenser i en enda fil. När du har 2 000 öppna referenser visas ett felmeddelande som säger kvoten har uppnåtts.

### <a name="solution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga några referenser och försök sedan igen.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Långsam filkopieringen till och från Azure Files i Linux

- Om du inte har ett visst minsta i/o-storlek krav, rekommenderar vi att du använder 1 MiB som i/o-storleken för optimala prestanda.
- Om du känner till dess slutliga storlek för en fil som du utvidgar med hjälp av skrivningar och din programvara inte uppleva kompatibilitetsproblem när en oskrivna slutet på filen innehåller nollor, anger du filstorlek i förväg i stället för att varje skrivning en utöka skrivning.
- Använd rätt copy-metoden:
    - Använd [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för alla överföring mellan två filresurser.
    - Med hjälp av cp med parallella kunde förbättra kopia hastighet, antalet trådar som beror på användningsfall och arbetsbelastning. Det här exemplet används sex: `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - Verktyg med öppen källkod från tredje part som:
        - [GNU parallella](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) – sorterar filerna och hanteringspaket dem i partitioner.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) -använder Fpart och en kopieringsverktyget skapa flera instanser att migrera data från src_dir till dst_url.
        - [Flera](https://github.com/pkolano/mutil) -flertrådiga cp och md5sum baserat på GNU coreutils.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>”Montera error(115): Åtgärden pågår ”när du montera Azure Files med hjälp av SMB 3.0

### <a name="cause"></a>Orsak

Vissa Linux-distributioner stöder inte ännu krypteringsfunktionerna i SMB 3.0. Användare kan få felmeddelandet ”115” om de försöker montera Azure Files med hjälp av SMB 3.0 på grund av en funktion som saknas. SMB 3.0 med fullständig kryptering stöds endast när du använder Ubuntu 16.04 eller senare.

### <a name="solution"></a>Lösning

Krypteringsfunktionen för SMB 3.0 för Linux introducerades i 4.11 kerneln. Den här funktionen gör det möjligt för montering av en Azure-filresurs från en lokal plats eller från en annan Azure-region. Den här funktionen ingår i Linux-distributioner som anges i [minsta rekommenderade versioner med motsvarande mount-funktioner (SMB-version 2.1 eller SMB-version 3.0)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30). Andra distributioner kräver kernel 4.11 och senare versioner.

Om din Linux SMB-klienten inte stöder kryptering kan montera en Azure-filer med hjälp av SMB 2.1 från en virtuell Linux-dator som är i samma datacenter som filresursen. Kontrollera att den [säker överföring krävs]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) inställningen är inaktiverad på lagringskontot. 

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>Fel ”Auktoriseringen misslyckades” när du går till en Azure-filresurs i portalen

När du bläddrar till en Azure-filresurs i portalen kan du få följande fel:

Auktoriseringen misslyckades  
Du har inte åtkomst

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Orsak 1: Ditt användarkonto har inte åtkomst till lagringskontot

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

Bläddra till det lagringskonto där Azure-filresursen är placerad, klicka på **åtkomstkontroll (IAM)** och verifiera ditt konto har åtkomst till lagringskontot. Mer information finns i [hur du skyddar ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 2: Virtuella nätverk eller brandvägg regler har aktiverats för lagringskontot

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Verifiera virtuella nätverk och brandvägg regler har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggen regler som orsakar problemet tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Långsam prestanda på en Azure-filresursen monteras på en Linux VM

### <a name="cause-1-caching"></a>Orsak 1: Cachelagring

En möjlig orsak till den dåliga prestandan är inaktiverad cachelagring. Cachelagring kan vara användbart om du använder en fil flera gånger, i annat fall kan det vara en belastning. Kontrollera om du använder cachen innan du inaktiverar den.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

Om du vill kontrollera om cachelagring är inaktiverat, leta efter den **cache =** posten.

**Cache = ingen** anger att cachelagring är inaktiverat. Montera resursen med hjälp av monteringskommandot eller genom att uttryckligen lägga till den **cache = strikt** mount-kommandot för att säkerställa att standard-cachelagring eller ”strikt” cachelagring läge är aktiverat.

I vissa fall kan den **serverino** mount-alternativet kan orsaka den **ls** kommando för att köra stat mot varje katalogposten. Detta resulterar i försämrade prestanda när du en stor katalog. Du hittar alternativ för montering i din **/etc/fstab** post:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Du kan också kontrollera om rätt alternativ som används genom att köra den **sudo montera | grep cifs** kommando och kontroll av dess utdata. Följande är exempel på utdata:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Om den **cache = strikt** eller **serverino** alternativet är inte finns, demontera och montera Azure Files igen genom att köra monteringskommandot från den [dokumentation](../storage-how-to-use-files-linux.md). Kontrollera sedan som den **/etc/fstab** post har rätt alternativ.

### <a name="cause-2-throttling"></a>Orsak 2: Begränsning

Det går du upplever begränsning och dina begäranden skickas till en kö. Du kan kontrollera detta genom att utnyttja [Azure Storage-mått i Azure Monitor](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Kontrollera din app ligger inom den [Azure Files skala mål](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Tidsstämplar förlorades i kopiera filer från Windows till Linux

På Linux/Unix-plattformar, den **cp -p** kommandot misslyckas om olika användare äger filen 1 och 2-filen.

### <a name="cause"></a>Orsak

Den tvingande flaggen **f** i COPYFILE leder till att köra **cp -p -f** på Unix. Det här kommandot genererar ett fel att bevara tidsstämpeln för den fil som du inte äger.

### <a name="workaround"></a>Lösning:

Använd det storage-kontot för att kopiera filerna:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Det går inte att ansluta till eller montera en Azure-filresurs

### <a name="cause"></a>Orsak

Vanliga orsaker till det här problemet är:

- Du använder en inkompatibel klient för Linux-distribution. Vi rekommenderar att du använder följande Linux-distributioner för att ansluta till en Azure-filresurs:

    |   | SMB 2.1 <br>(Monterar på virtuella datorer i samma Azure-region) | SMB 3.0 <br>(Monterar från både lokalt och över olika regioner) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- CIFS-verktyg (cifs-utils) har inte installerats på klienten.
- Den lägsta SMB/CIFS-versionen 2.1, installeras inte på klienten.
- SMB 3.0-kryptering stöds inte på klienten. SMB 3.0-kryptering är tillgänglig i Ubuntu 16,4 tum och senare versioner, tillsammans med SUSE 12,3 och senare versioner. Andra distributioner kräver kernel 4.11 och senare versioner.
- Du försöker ansluta till ett lagringskonto via TCP-port 445, vilket inte stöds.
- Du försöker ansluta till en Azure-filresurs från en Azure-dator och den virtuella datorn är inte i samma region som lagringskontot.
- Om den [säker överföring krävs]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad på lagringskontot, Azure Files tillåter endast anslutningar som använder SMB 3.0 med kryptering.

### <a name="solution"></a>Lösning

Lös problemet genom att använda den [felsökningsverktyget för Azure Files-monteringsfel på Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Det här verktyget:

* Hjälper dig att validera klienten köra-miljö.
* Identifierar inkompatibla klientkonfigurationen som skulle orsaka fel åtkomst för Azure Files.
* Ger vägledning på att åtgärda själv.
* Samlar in diagnostik-spårningar.


## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: Det går inte att komma åt '&lt;sökväg&gt;”: I/o-fel

När du försöker listfiler i en Azure-filresurs med hjälp av kommandot ls avbryter kommandot när filer. Du får följande fel:

**ls: Det går inte att komma åt '&lt;sökväg&gt;”: I/o-fel**


### <a name="solution"></a>Lösning
Uppgradera Linux-kärnan till följande versioner som har en korrigering av det här problemet:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Alla versioner som är större än eller lika med 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Det går inte att skapa symboliska länkar - ln: Det gick inte att skapa symboliska länken 't': Åtgärden stöds inte

### <a name="cause"></a>Orsak
Inte som standard aktivera montera Azure-filresurser på Linux med hjälp av CIFS stöd för symboliska länkar (symlinks). Du ser felmeddelandet Så här:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Lösning
Linux CIFS-klienten stöder inte skapandet av Windows-style symboliska länkar över SMB 2 eller 3-protokollet. För närvarande Linux-klient har en annan typ av symboliska länkar som kallas [Minshall + franska symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) för både skapa och följ åtgärder. Kunder som behöver symboliska länkar kan använda alternativet ”mfsymlinks” monteringspunkter. Vi rekommenderar ”mfsymlinks” eftersom det är också det format som använder Mac-datorer.

Om du vill använda symlinks, lägger du till följande i slutet av din CIFS mount-kommando:

```
,mfsymlinks
```

Så att kommandot ser ut ungefär så:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Du kan sedan skapa symlinks föreslås vanliga ord på den [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>”Montera error(112): Värddatorn är inte tillgänglig ”på grund av en återanslutning timeout

En ”112” mount-fel uppstår på Linux-klient när klienten har varit inaktiv under en längre tid. När du har en utökad inaktivitetstid klienten kopplas och tidsgränsen uppnås för anslutningen.  

### <a name="cause"></a>Orsak

Anslutningen kan vara inaktiv av följande skäl:

-   Kommunikationsfel i nätverket som förhindrar återställning av en TCP-anslutning till servern när alternativet ”soft” montera används
-   Senaste återanslutning korrigeringar som inte finns i äldre kernel

### <a name="solution"></a>Lösning

Problemet återanslutning i Linux-kärnan löses nu som en del av följande ändringar:

- [Korrigeringen återansluta om du vill skjuta upp smb3 session inte återansluta långt efter socket återansluta](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Anropa tjänsten echo omedelbart efter socket återansluta](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Åtgärda ett möjligt minnet skadas under reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Åtgärda ett möjligt dubbla låsning av mutex under reconnect (för kernel v4.9 och senare)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Men kan de här ändringarna inte flyttas ännu till Linux-distributioner. Den här och andra återanslutning korrigeringar finns i den [minsta rekommenderade versioner med motsvarande mount-funktioner (SMB-version 2.1 eller SMB-version 3.0)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30) delen av den [Använd Azure Files med Linux](storage-how-to-use-files-linux.md)artikeln. Den här snabbkorrigeringen får du genom att uppgradera till en av dessa rekommenderade kernel-versioner.

### <a name="workaround"></a>Lösning:

Du kan undvika det här problemet genom att ange en hård montering. En hård montering tvingas klienten att vänta tills en anslutning har upprättats eller uttryckligen avbrott. Du kan använda den för att förhindra fel på grund av timeout för nätverket. Den här lösningen kan dock orsaka obestämd väntar. Var beredd på att stoppa anslutningar vid behov.

Om du inte uppgradera till de senaste kernel-versionerna, kan du undvika problemet genom att lagra en fil i Azure-filresursen som du skriver med 30 sekunders mellanrum eller mindre. Detta måste vara en skrivåtgärd, till exempel skriva om skapade eller ändrade datum för filen. Annars kan du få cachelagrade resultat och åtgärden kan inte utlösa återanslutning.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att snabbt lösa ditt problem.

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
ms.component: files
ms.openlocfilehash: 2ae116649de02c5602aa50d706f6a88ac5872960
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025862"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Felsöka problem i Azure Files i Linux

Den här artikeln innehåller vanliga problem som är relaterade till Microsoft Azure-filer när du ansluter från Linux-klienter. Det ger också möjliga orsaker och lösningar för dessa problem. Förutom felsökningsstegen i den här artikeln, du kan också använda [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) så Linux klienten har rätt krav. AzFileDiagnostics automatiserar identifiering för de flesta av de problem som nämns i den här artikeln och hjälper dig att konfigurera din miljö för att få bästa möjliga prestanda. Du kan också hitta den här informationen i den [Azure Files delar felsökare](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) som innehåller stegen för att hjälpa dig med problem som ansluter/mappning/montera Azure Files delar.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>”[behörighet nekas] diskkvoten har överskridits” när du försöker öppna en fil

I Linux visas ett felmeddelande som liknar följande:

**<filename> [åtkomst nekad] Diskkvoten har överskridits**

### <a name="cause"></a>Orsak

Du har nått den övre gränsen för samtidiga öppna referenser som tillåts för en fil.

### <a name="solution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga några referenser och försök sedan igen. Mer information finns i [checklista för prestanda och skalbarhet i Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Långsam filkopieringen till och från Azure Files i Linux

- Om du inte har ett visst minsta i/o-storlek krav, rekommenderar vi att du använder 1 MiB som i/o-storleken för optimala prestanda.
- Om du känner till dess slutliga storlek för en fil som du utökar med hjälp av skrivningar och din programvara inte uppleva kompatibilitetsproblem när en oskrivna slutet på filen innehåller nollor, anger du filstorlek i förväg i stället för att varje skrivning en utöka skrivning.
- Använd rätt copy-metoden:
    - Använd [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för alla överföring mellan två filresurser.
    - Använd [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) mellan filresurser på den lokala datorn.

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>”Montera error(112): värddatorn är inte tillgänglig” på grund av en återanslutning timeout

En ”112” mount-fel uppstår på Linux-klient när klienten har varit inaktiv under en längre tid. Klienten kopplas efter utökade inaktiva, och anslutningen når sin tidsgräns.  

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

Men kan de här ändringarna inte flyttas ännu till Linux-distributioner. Den här och andra återanslutning korrigeringar görs i följande populära Linux-kernel: 4.4.40 och 4.8.16 4.9.1. Den här snabbkorrigeringen får du genom att uppgradera till en av dessa rekommenderade kernel-versioner.

### <a name="workaround"></a>Lösning

Du kan undvika det här problemet genom att ange en hård montering. Detta gör att klienten ska vänta tills en anslutning har upprättats eller den uttryckligen avbryts och kan användas för att förhindra fel på grund av timeout för nätverket. Den här lösningen kan dock orsaka obestämd väntar. Var beredd på att stoppa anslutningar vid behov.

Om du inte uppgradera till de senaste kernel-versionerna, kan du undvika problemet genom att lagra en fil i Azure-filresursen som du skriver med 30 sekunders mellanrum eller mindre. Detta måste vara en skrivåtgärd, till exempel skriva om skapade eller ändrade datum för filen. Annars kan du få cachelagrade resultat och åtgärden kan inte utlösa återanslutning.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>”Montera error(115): pågår” när du montera Azure Files med hjälp av SMB 3.0

### <a name="cause"></a>Orsak

Vissa Linux-distributioner stöder ännu inte krypteringsfunktionerna i SMB 3.0 och användare kan få felmeddelandet ”115” om de försöker montera Azure Files med hjälp av SMB 3.0 på grund av en funktion som saknas. SMB 3.0 med fullständig kryptering stöds endast för tillfället när du använder Ubuntu 16.04 eller senare.

### <a name="solution"></a>Lösning

Krypteringsfunktionen för SMB 3.0 för Linux introducerades i 4.11 kernel. Den här funktionen gör det möjligt för montering av Azure-filresursen från lokala eller en annan Azure-region. Vid tidpunkten för publiceringen har den här funktionen anpassats till nr 17.04 från Ubuntu och Ubuntu 16,10. Om din Linux SMB-klienten inte har stöd för kryptering, montera Azure filer med hjälp av SMB 2.1 från en virtuell Linux-dator som är i samma datacenter som filen och kontrollera den [säker överföring krävs]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) inställningen är inaktiverad på lagring konto. 

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Långsam prestanda på en Azure-filresursen monteras på en Linux VM

### <a name="cause"></a>Orsak

En möjlig orsak till den dåliga prestandan är inaktiverad cachelagring.

### <a name="solution"></a>Lösning

Om du vill kontrollera om cachelagring är inaktiverat, leta efter den **cache =** posten. 

**Cache = ingen** anger att cachelagring är inaktiverat.  Montera resursen med hjälp av monteringskommandot eller genom att uttryckligen lägga till den **cache = strikt** mount-kommandot för att säkerställa att standard-cachelagring eller ”strikt” cachelagring läge är aktiverat.

I vissa fall kan den **serverino** mount-alternativet kan orsaka den **ls** kommando för att köra stat mot varje katalogposten. Detta resulterar i försämrade prestanda när du en stor katalog. Du hittar alternativ för montering i din **/etc/fstab** post:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Du kan också kontrollera om rätt alternativ som används genom att köra den **sudo montera | grep cifs** kommando och kontroll av utdata, till exempel följande Exempelutdata:

`//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Om den **cache = strikt** eller **serverino** alternativet är inte finns, demontera och montera Azure Files igen genom att köra monteringskommandot från den [dokumentation](../storage-how-to-use-files-linux.md). Kontrollera sedan som den **/etc/fstab** post har rätt alternativ.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Tidsstämplar förlorades i kopiera filer från Windows till Linux

På Linux/Unix-plattformar, den **cp -p** kommandot misslyckas om filen 1 och filen 2 ägs av olika användare.

### <a name="cause"></a>Orsak

Den tvingande flaggen **f** i COPYFILE leder till att köra **cp -p -f** på Unix. Det här kommandot genererar ett fel att bevara tidsstämpeln för den fil som du inte äger.

### <a name="workaround"></a>Lösning

Använd det storage-kontot för att kopiera filerna:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>Det går inte att ansluta eller montera en Azure-filresurs

### <a name="cause"></a>Orsak

Vanliga orsaker till det här problemet är:


- Du använder en inkompatibel klient för Linux-distribution. Vi rekommenderar att du använder följande Linux-distributioner för att ansluta till Azure-filresurs:

* **Minsta rekommenderade versioner med motsvarande mount-funktioner (SMB-version 2.1 eller SMB-version 3.0)**    
    
    |   | SMB 2.1 <br>(Monterar på virtuella datorer i samma Azure-region) | SMB 3.0 <br>(Monterar från både lokalt och över olika regioner) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04 + | 16.04 + |
    | RHEL | 7 + | 7.5 + |
    | CentOS | 7 + |  7.5 + |
    | Debian | 8 + |   |
    | openSUSE | 13.2 + | 42.3 + |
    | SUSE Linux Enterprise Server | 12 | 12 SP3 + |

- CIFS-utils har inte installerats på klienten.
- Minsta SMB/CIFS version 2.1 är inte installerad på klienten.
- SMB 3.0-kryptering stöds inte på klienten. SMB 3.0-kryptering är tillgängligt i Ubuntu 16,4 tum och senare, SUSE 12,3 och senare. Andra distributioner kräver kernel 4.11 och senare.
- Du försöker ansluta till ett lagringskonto via TCP-port 445 som inte stöds.
- Du försöker ansluta till Azure-filresurs från en Azure-dator och den virtuella datorn finns inte i samma region som lagringskontot.
- Om [säker överföring krävs]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad på lagringskontot, Azure Files kan anslutningar som använder SMB 3.0 med kryptering.

### <a name="solution"></a>Lösning

Lös problemet genom att använda den [felsökningsverktyget för Azure Files-monteringsfel på Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Det här verktyget kan du verifiera klienten köra miljö, identifiera inkompatibla klientkonfigurationen som skulle orsaka fel åtkomst för Azure Files, ger vägledning på lokal åtgärda och samla in diagnostik-spårningar.

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: Det går inte att komma åt '&lt;sökväg&gt;': / o-fel

När du försöker att lista filer i en Azure-fil dela med kommandot ls felmeddelande ls kommandot låser sig när lista filer du följande:

**ls: Det går inte att komma åt '&lt;sökväg&gt;': / o-fel**


### <a name="solution"></a>Lösning
Uppgradera Linux-kärnan till följande versioner som har lösa problemet:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Alla versioner som är större än eller lika med 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Det går inte att skapa symboliska länkar - ln: Det gick inte att skapa symboliska länken 't': åtgärden stöds inte

### <a name="cause"></a>Orsak
Montera Azure-filresurser på Linux aktiveras med hjälp av CIFS inte som standard stöd för symlinks. Ett fel länka det här visas:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Lösning
Linux CIFS-klienten stöder inte skapandet av Windows style symboliska länkar över SMB2/3-protokollet. För närvarande Linux-klient har en annan typ av symboliska länkar som kallas [Mishall + franska symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) för både skapa och följ åtgärder. Kunder som behöver symboliska länkar kan använda ”mfsymlinks” mount-alternativet. ”mfsymlinks” rekommenderas vanligtvis eftersom det är också det format som används av Mac-datorer.

Om du vill kunna använda symlinks lägger du till följande i slutet av din CIFS mount-kommando:

```
,mfsymlinks
```

Så att kommandot ska se ut ungefär som:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

När du har lagt till, kommer du att kunna skapa symlinks föreslås vanliga ord på den [Wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att snabbt lösa ditt problem.

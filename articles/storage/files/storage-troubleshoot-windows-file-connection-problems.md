---
title: Felsöka Azure Files-problem i Windows | Microsoft Docs
description: Felsökning av problem i Azure Files i Windows
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: 59eb0ddad72f5e54a23a97a260477f84019eb62c
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386349"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Felsöka Azure Files-problem i Windows

Den här artikeln innehåller vanliga problem som är relaterade till Microsoft Azure-filer när du ansluter från Windows-klienter. Det ger också möjliga orsaker och lösningar för dessa problem. Förutom felsökningsstegen i den här artikeln, du kan också använda [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) så att Windows klientmiljö har rätt krav. AzFileDiagnostics automatiserar identifiering för de flesta av de problem som nämns i den här artikeln och hjälper dig att konfigurera din miljö för att få bästa möjliga prestanda. Du kan också hitta den här informationen i den [Azure Files delar felsökare](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) som innehåller stegen för att hjälpa dig med problem som ansluter/mappning/montera Azure Files delar.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Fel 53 eller fel 67 fel 87 när du montera eller demontera en Azure-filresurs

När du försöker montera en filresurs från en lokal plats eller från ett annat datacenter kan du få följande fel:

- Systemfel 53 har uppstått. Gick inte att hitta nätverkssökvägen.
- Systemfel 67 har uppstått. Nätverksnamnet kan inte hittas.
- Systemfel 87 har uppstått. Parametern är felaktig.

### <a name="cause-1-unencrypted-communication-channel"></a>Orsak 1: Okrypterade kommunikationskanalen

Av säkerhetsskäl blockeras anslutningar till Azure-filresurser om kommunikationskanalen inte är krypterad och om anslutningsförsöket inte görs från samma datacenter där de Azure-filresurserna finns. Okrypterade anslutningar inom samma datacenter kan också blockeras om de [säker överföring krävs](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad på lagringskontot. Kommunikation kanalkrypteringen tillhandahålls endast om användarens klientoperativsystem stöder SMB-kryptering.

Windows 8, Windows Server 2012 och senare versioner av varje system att förhandla begäranden som innehåller SMB 3.0, som stöder kryptering.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. Kontrollera den [säker överföring krävs](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) inställningen är inaktiverad på lagringskontot.
2. Ansluta från en klient som gör något av följande:

    - Uppfyller kraven för Windows 8 och Windows Server 2012 eller senare versioner
    - Ansluter från en virtuell dator i samma datacenter som Azure storage-kontot som används för Azure-filresursen

### <a name="cause-2-port-445-is-blocked"></a>Orsak 2: Port 445 blockeras

Systemfel 53 eller fel 67 kan inträffa om port 445 utgående kommunikation till ett datacenter för Azure Files är blockerad. Om du vill visa en sammanfattning av Internet-leverantörer som Tillåt eller neka åtkomst från port 445, gå till [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

För att förstå om det här är anledningen till meddelandet ”Systemfel 53”, kan du använda Portqry för att fråga TCP:445 slutpunkten. Om TCP:445 slutpunkten är visas som filtrerad är är TCP-port blockerad. Här är en exempelfråga:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Om TCP-port 445 blockeras av en regel längs nätverkssökvägen ser du följande utdata:

  `TCP port 445 (microsoft-ds service): FILTERED`

Mer information om hur du använder Portqry finns i [beskrivningen av kommandoradsverktyget Portqry.exe](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Arbeta med din IT-avdelning öppna port 445 utgående till [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>Orsak 3: NTLMv1 är aktiverat

Systemfel 53 eller systemfel 87 kan inträffa om NTLMv1 kommunikation är aktiverad på klienten. Azure Files stöder endast NTLMv2-autentisering. Att ha aktiverat NTLMv1 skapar en mindre säkra klient. Därför blockeras kommunikation för Azure Files. 

Kontrollera att följande registerundernyckel är inställd på värdet 3 för att avgöra om detta är orsaken till felet:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Mer information finns i den [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) artikeln på TechNet.

### <a name="solution-for-cause-3"></a>Lösning för orsak 3

Återställ den **LmCompatibilityLevel** värdet till standardvärdet 3 i följande registerundernyckel:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Fel 1816 ”inte tillräckligt stor kvot är tillgängliga för att bearbeta det här kommandot” när du kopierar till en Azure-filresurs

### <a name="cause"></a>Orsak

Fel 1816 händer när du når den övre gränsen för samtidiga öppna referenser som tillåts för en fil på datorn där filresursen monteras.

### <a name="solution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga några referenser och försök sedan igen. Mer information finns i [checklista för prestanda och skalbarhet i Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Långsam filkopieringen till och från Azure Files i Windows

Du kan se långsamma prestanda när du försöker överföra filer till Azure-filtjänsten.

- Om du inte har ett visst minsta i/o-storlek krav, rekommenderar vi att du använder 1 MiB som i/o-storleken för optimala prestanda.
-   Om du känner till dess slutliga storlek för en fil som du utökar med skrivningar och din programvara inte har kompatibilitetsproblem när oskrivna slutet på filen innehåller nollor, anger du filstorlek i förväg i stället för att varje skrivning en utöka skrivning.
-   Använd rätt copy-metoden:
    -   Använd [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för alla överföring mellan två filresurser.
    -   Använd [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) mellan filresurser på den lokala datorn.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Överväganden för Windows 8.1 eller Windows Server 2012 R2

För klienter som kör Windows 8.1 eller Windows Server 2012 R2, se till att den [KB3114025](https://support.microsoft.com/help/3114025) snabbkorrigeringen har installerats. Den här snabbkorrigeringen förbättrar prestandan för create och Stäng hanterar.

Du kan köra följande skript för att kontrollera om snabbkorrigeringen har installerats:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Om snabbkorrigeringen har installerats, visas följande utdata:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Windows Server 2012 R2-avbildningar i Azure Marketplace har snabbkorrigering KB3114025 installeras som standard, från och med December 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Ingen mapp med en enhetsbeteckning i **datorn**

Om du mappar en Azure-filresurs som en administratör med hjälp av net Använd verkar resursen saknas.

### <a name="cause"></a>Orsak

Som standard körs Windows Utforskaren inte som en administratör. Om du kör net Använd från en administrativ kommandotolk, mappa nätverksenheten som administratör. Eftersom mappade enheter är användarcentrerad visas inte det användarkonto som är inloggad i enheter om de är monterade under ett annat användarkonto.

### <a name="solution"></a>Lösning
Montera filresursen från en kommandorad för icke-administratör. Du kan också följa [den här TechNet-artikeln](https://technet.microsoft.com/library/ee844140.aspx) att konfigurera den **EnableLinkedConnections** registervärdet.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Det går inte att kommandot net use om lagringskontot innehåller ett snedstreck

### <a name="cause"></a>Orsak

Kommandot net use tolkar ett snedstreck (/) som ett kommandoradsalternativ. Om ditt användarkonto startas med ett snedstreck, misslyckas enhetsmappningen.

### <a name="solution"></a>Lösning

Du kan använda något av följande steg för att undvika problemet:

- Kör följande PowerShell-kommando:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  Du kan köra det här sättet kommandot från en kommandofil:

  `Echo new-smbMapping ... | powershell -command –`

- Placera dubbla citattecken runt på för att undvika det här problemet, om inte snedstreck är det första tecknet. Om den är kan antingen använda det interaktiva läget och ange ditt lösenord separat eller återskapa nycklarna för att få en nyckel som inte börjar med ett snedstreck.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Programmet eller tjänsten kan inte komma åt en monterad Azure Files-enhet

### <a name="cause"></a>Orsak

Enheterna är monterade per användare. Om programmet eller tjänsten körs under ett annat användarkonto än det som monterade enheten, visas inte enheten i programmet.

### <a name="solution"></a>Lösning

Använd någon av följande lösningar:

-   Enheten från samma användarkonto som innehåller programmet. Du kan använda ett verktyg som PsExec.
- Skicka lagringskontonamn och nyckel i användarnamn och lösenordsparametrar av net-kommandot.
- Använd kommandot cmdkey att lägga till autentiseringsuppgifter i Autentiseringshanteraren. Du kan göra detta från kommandoraden under kontokontexten tjänsten via en interaktiv inloggning eller genom att använda runas.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mappa resursen direkt utan att använda en enhetsbokstav. Vissa program kan inte återansluta till enhetsbeteckningen korrekt, så att använda den fullständiga UNC-sökvägen kan vara mer tillförlitlig. 

  `net use * \\storage-account-name.file.core.windows.net\share`

När du har följt de här instruktionerna kan du får följande felmeddelande när du kör net används för system/nätverkstjänstkontot: ”systemfel 1312 har uppstått. En angiven inloggningssession finns inte. Det kanske redan har avslutats ”. Om detta inträffar kan du se till att användarnamnet som skickades till net Använd inkluderar domäninformation (till exempel ”: [lagringskontonamn]. file.core.windows .net”).

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Felet ”du kopierar en fil till ett mål som inte stöder kryptering”

När en fil kopieras över nätverket, är filen dekrypteras på källdatorn, skickas i klartext och krypteras igen vid målet. Men du kan se följande fel när du försöker att kopiera en krypterad fil: ”du kopierar filen till ett mål som inte stöder kryptering”.

### <a name="cause"></a>Orsak
Det här problemet kan inträffa om du använder EFS (ENCRYPTING File System). BitLocker-krypterade filer kan kopieras till Azure Files. Azure Files stöder dock inte NTFS EFS.

### <a name="workaround"></a>Lösning
Om du vill kopiera en fil i nätverket, måste du först dekryptera den. Använd någon av följande metoder:

- Använd den **Kopiera /d** kommando. Det tillåter krypterade filer ska sparas som dekrypterade filer vid målet.
- Ange följande registernyckel:
  - Sökväg = HKLM\Software\Policies\Microsoft\Windows\System
  - Värdetypen = DWORD
  - Namn = CopyFileAllowDecryptedRemoteDestination
  - Värde = 1

Tänk på att ställa in registernyckeln påverkar alla åtgärder i kopia som görs till nätverksresurser.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att snabbt lösa ditt problem.

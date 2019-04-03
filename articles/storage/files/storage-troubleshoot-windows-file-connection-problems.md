---
title: Felsöka Azure Files-problem i Windows | Microsoft Docs
description: Felsökning av problem i Azure Files i Windows
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 1cadf61d7ce6ed48ea2d42b299dede860a505f0d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877439"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Felsöka Azure Files-problem i Windows

Den här artikeln innehåller vanliga problem som är relaterade till Microsoft Azure-filer när du ansluter från Windows-klienter. Det ger också möjliga orsaker och lösningar för dessa problem. Förutom felsökningsstegen i den här artikeln, du kan också använda [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) så att Windows klientmiljö har rätt krav. AzFileDiagnostics automatiserar identifiering för de flesta av de problem som nämns i den här artikeln och hjälper dig att konfigurera din miljö för att få bästa möjliga prestanda. Du kan också hitta den här informationen i den [Azure Files delar felsökare](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) som innehåller stegen för att hjälpa dig med problem som ansluter/mappning/montera Azure Files delar.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fel 5 när du monterar en Azure-filresurs

När du försöker montera en filresurs kan du få följande fel:

- Systemfel 5 har uppstått. Åtkomst nekad.

### <a name="cause-1-unencrypted-communication-channel"></a>Orsak 1: Okrypterade kommunikationskanalen

Av säkerhetsskäl blockeras anslutningar till Azure-filresurser om kommunikationskanalen inte är krypterad och om anslutningsförsöket inte görs från samma datacenter där de Azure-filresurserna finns. Okrypterade anslutningar inom samma datacenter kan också blockeras om de [säker överföring krävs](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad på lagringskontot. En krypterade kommunikationskanaler tillhandahålls endast om användarens klientoperativsystem stöder SMB-kryptering.

Windows 8, Windows Server 2012 och senare versioner av varje system att förhandla begäranden som innehåller SMB 3.0, som stöder kryptering.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. Ansluta från en klient som stöder SMB-kryptering (Windows 8, Windows Server 2012 eller senare) eller ansluta från en virtuell dator i samma datacenter som Azure storage-kontot som används för Azure-filresursen.
2. Kontrollera den [säker överföring krävs](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) inställningen är inaktiverad på storage-konto om klienten inte har stöd för SMB-kryptering.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 2: Virtuella nätverk eller brandvägg regler har aktiverats för lagringskontot 

Om virtuella nätverk (VNET) och brandväggsregler har konfigurerats på lagringskontot, nekas nätverkstrafik åtkomst om inte klientens IP-adress eller virtuella nätverk har åtkomst.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Verifiera virtuella nätverk och brandvägg regler har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggen regler som orsakar problemet tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Fel 53 eller fel 67 fel 87 när du montera eller demontera en Azure-filresurs

När du försöker montera en filresurs från en lokal plats eller från ett annat datacenter kan du få följande fel:

- Systemfel 53 har uppstått. Gick inte att hitta nätverkssökvägen.
- Systemfel 67 har uppstått. Nätverksnamnet kan inte hittas.
- Systemfel 87 har uppstått. Parametern är felaktig.

### <a name="cause-1-port-445-is-blocked"></a>Orsak 1: Port 445 blockeras

Systemfel 53 eller fel 67 kan inträffa om port 445 utgående kommunikation till ett datacenter för Azure Files är blockerad. Om du vill visa en sammanfattning av Internet-leverantörer som Tillåt eller neka åtkomst från port 445, gå till [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

För att kontrollera om din brandvägg eller Internetleverantören blockerar port 445, använda den [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) verktyget eller `Test-NetConnection` cmdlet. 

Du använder den `Test-NetConnection` cmdlet, AzureRM PowerShell-modulen måste vara installerad, se [installera Azure PowerShell-modulen](/powershell/azure/azurerm/install-azurerm-ps) för mer information. Kom ihåg att ersätta `<your-storage-account-name>` och `<your-resource-group-name>` med gällande namn för ditt lagringskonto.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Om en anslutning upprättades bör du se följande utdata:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> Ovanstående kommando returnerar den aktuella IP-adressen för lagringskontot. Det är inte säkert att IP-adressen förblir densamma, och den kan ändras när som helst. Hårdkoda inte den här IP-adressen i några skript eller i en brandväggskonfiguration.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

#### <a name="solution-1---use-azure-file-sync"></a>Lösning 1 – Använd Azure File Sync
Azure File Sync kan omvandlar dina lokala Windows Server till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Azure File Sync fungerar över port 443 och kan därför användas som en lösning för att få åtkomst till Azure Files från klienter som har port 445 blockeras. [Lär dig hur du ställer in Azure File Sync](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Lösning 2 – använda VPN
Genom att konfigurera en VPN-anslutning till specifika Storage-kontot, passerar trafiken en säker tunnel inte via internet. Följ den [instruktioner för att konfigurera VPN](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
) åtkomst till Azure Files från Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Lösning 3 – avblockera port 445 med hjälp av Leverantören / IT-administratören
Arbeta med din IT-avdelningen eller Internetleverantör att öppna port 445 utgående till [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Lösningen 4 – Använd REST API-baserade verktyg som Storage Explorer/Powershell
Azure Files stöder även REST förutom SMB. REST-åtkomst fungerar över port 443 (standard tcp). Det finns olika verktyg som är skrivna med hjälp av REST-API som gör det omfattande användargränssnitt. [Lagringsutforskaren](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) är en av dem. [Ladda ned och installera Lagringsutforskaren](https://azure.microsoft.com/en-us/features/storage-explorer/) och ansluter till filresursen backas upp av Azure Files. Du kan också använda [PowerShell](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-powershell) vilket användaren även REST API.


### <a name="cause-2-ntlmv1-is-enabled"></a>Orsak 2: NTLMv1 är aktiverat

Systemfel 53 eller systemfel 87 kan inträffa om NTLMv1 kommunikation är aktiverad på klienten. Azure Files stöder endast NTLMv2-autentisering. Att ha aktiverat NTLMv1 skapar en mindre säkra klient. Därför blockeras kommunikation för Azure Files. 

Kontrollera att följande registerundernyckel är inställd på värdet 3 för att avgöra om detta är orsaken till felet:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Mer information finns i den [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) artikeln på TechNet.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Återställ den **LmCompatibilityLevel** värdet till standardvärdet 3 i följande registerundernyckel:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Fel 1816 ”inte tillräckligt stor kvot är tillgängliga för att bearbeta det här kommandot” när du kopierar till en Azure-filresurs

### <a name="cause"></a>Orsak

Fel 1816 händer när du når den övre gränsen för samtidiga öppna referenser som tillåts för en fil på datorn där filresursen monteras.

### <a name="solution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga några referenser och försök sedan igen. Mer information finns i [checklista för prestanda och skalbarhet i Microsoft Azure Storage](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>Felet ”Åtkomst nekad” när du går till en Azure-filresurs i portalen

När du bläddrar till en Azure-filresurs i portalen kan du få följande fel:

Åtkomst nekad  
Du saknar åtkomst  
Det verkar som om du inte har åtkomst till det här innehållet. Kontakta ägaren för att få åtkomst.  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Orsak 1: Ditt användarkonto har inte åtkomst till lagringskontot

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

Bläddra till det lagringskonto där Azure-filresursen är placerad, klicka på **åtkomstkontroll (IAM)** och verifiera ditt konto har åtkomst till lagringskontot. Mer information finns i [hur du skyddar ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 2: Virtuella nätverk eller brandvägg regler har aktiverats för lagringskontot

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Verifiera virtuella nätverk och brandvägg regler har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggen regler som orsakar problemet tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

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

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

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

När du har följt de här instruktionerna kan du få följande felmeddelande när du kör net används för system/nätverkstjänstkontot: ”Systemfel 1312 har uppstått. En angiven inloggningssession finns inte. Det kanske redan har avslutats ”. Om detta inträffar kan du se till att användarnamnet som skickades till net Använd inkluderar domäninformation (till exempel ”: [lagringskontonamn]. file.core.windows .net”).

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Felet ”du kopierar en fil till ett mål som inte stöder kryptering”

När en fil kopieras över nätverket, är filen dekrypteras på källdatorn, skickas i klartext och krypteras igen vid målet. Följande fel kan dock uppstå när du försöker att kopiera en krypterad fil: ”Du kopierar filen till ett mål som inte stöder kryptering”.

### <a name="cause"></a>Orsak
Det här problemet kan inträffa om du använder EFS (ENCRYPTING File System). BitLocker-krypterade filer kan kopieras till Azure Files. Azure Files stöder dock inte NTFS EFS.

### <a name="workaround"></a>Lösning
Om du vill kopiera en fil i nätverket, måste du först dekryptera den. Använd någon av följande metoder:

- Använd den **Kopiera /d** kommando. Det tillåter krypterade filer ska sparas som dekrypterade filer vid målet.
- Ange följande registernyckel:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Värdetypen = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Värde = 1

Tänk på att ställa in registernyckeln påverkar alla åtgärder i kopia som görs till nätverksresurser.

## <a name="slow-enumeration-of-files-and-folders"></a>Långsam uppräkning av filer och mappar

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om det finns ingen tillräckligt med cachelagring på klientdatorn för stora kataloger.

### <a name="solution"></a>Lösning

Du löser problemet, justera den **DirectoryCacheEntrySizeMax** registervärde för att tillåta cachelagring av större kataloglistor i klientdatorn:

- Plats: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Värdet mane: DirectoryCacheEntrySizeMax 
- Värdet typ: DWORD
 
 
Du kan till exempel inställd 0x100000 och se om prestanda bli bättre.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Fel AadDsTenantNotFound gör det möjligt för Azure Active Directory-autentisering för Azure Files ”det går inte att hitta aktiva klienter med klient-Id aad-klient-id”

### <a name="cause"></a>Orsak

Fel AadDsTenantNotFound händer när du försöker [aktivera Azure Active Directory (AAD)-autentisering för Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable) på ett lagringskonto där [AAD Domain Service(AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) skapas inte i AAD klient i samma prenumeration.  

### <a name="solution"></a>Lösning

Aktivera AAD DS på AAD-klient för den prenumeration som ditt lagringskonto har distribuerats till. Du måste ha administratörsbehörighet för AAD-klient för att skapa en hanterad domän. Om du inte är administratör för Azure AD-klient, kontakta administratören och följ de stegvisa anvisningarna till [aktivera Azure Active Directory Domain Services med Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att snabbt lösa ditt problem.

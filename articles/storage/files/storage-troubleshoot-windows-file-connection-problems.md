---
title: Felsöka problem med Azure Files i Windows | Microsoft-dokument
description: Felsöka problem med Azure Files i Windows
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: b4e1ef4fbc3ade38b55fc06f8e4e9a119938581b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383894"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Felsöka Azure Files-problem i Windows

I den här artikeln visas vanliga problem som är relaterade till Microsoft Azure-filer när du ansluter från Windows-klienter. Det ger också möjliga orsaker och lösningar på dessa problem. Förutom felsökningsstegen i den här artikeln kan du också använda [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) för att säkerställa att Windows-klientmiljön har korrekta förutsättningar. AzFileDiagnostics automatiserar upptäckt av de flesta av de symtom som nämns i den här artikeln och hjälper till att ställa in din miljö för att få optimal prestanda. Du kan också hitta den här informationen i [Felsökaren för Azure Files-resurser](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) som innehåller steg som hjälper dig med problem med att ansluta/mappa/montera Azure Files-resurser.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fel 5 när du monterar en Azure-filresurs

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att montera en filresurs kan fÃ¶1sÃ¶k visas fÃ¶ndesÃ¤nde:

- Systemfel 5 har uppstått. Åtkomst nekad.

### <a name="cause-1-unencrypted-communication-channel"></a>Orsak 1: Okrypterad kommunikationskanal

Av säkerhetsskäl blockeras anslutningar till Azure-filresurser om kommunikationskanalen inte är krypterad och om anslutningsförsöket inte görs från samma datacenter där Azure-filresurserna finns. Okrypterade anslutningar inom samma datacenter kan också blockeras om inställningen [Säker överföring krävs ](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad på lagringskontot. En krypterad kommunikationskanal tillhandahålls endast om användarens klientoperativsystem stöder SMB-kryptering.

Windows 8, Windows Server 2012 och senare versioner av respektive system förhandlar begäranden som innehåller SMB 3.0, som stöder kryptering.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. Anslut från en klient som stöder SMB-kryptering (Windows 8, Windows Server 2012 eller senare) eller anslut från en virtuell dator i samma datacenter som Azure-lagringskontot som används för Azure-filresursen.
2. Kontrollera att inställningen för [säker överföring är](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) inaktiverad på lagringskontot om klienten inte stöder SMB-kryptering.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 2: Regler för virtuellt nätverk eller brandvägg är aktiverade på lagringskontot 

Om virtuellt nätverk (VNET) och brandväggsregler har konfigurerats på lagringskontot tillåts inte nätverkstrafik om inte åtkomst tillåts för klientens IP-adress eller virtuella nätverk.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Kontrollera att det virtuella nätverket och brandväggsreglerna har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggsreglerna som orsakar problemet kan du tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Orsak 3: Behörigheter på resursnivå är felaktiga när identitetsbaserad autentisering

Om användare har åtkomst till Azure-filresursen med Active Directory (AD) eller Azure Active Directory Domain Services (Azure AD DS) misslyckas åtkomsten till filresursen med felet "Åtkomst nekas" om behörigheterna på delningsnivå är felaktiga. 

### <a name="solution-for-cause-3"></a>Lösning för orsak 3

Information om hur du uppdaterar behörigheterna på resursnivå finns i [Tilldela åtkomstbehörigheter till en identitet](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Fel 53, Fel 67 eller Fel 87 när du monterar eller avmonterar en Azure-filresurs

NÃ¤r du fÃ¶rsÃ¶ks fÃ¶rsÃ¶k att Ã¶r en filresurs frÃ¶r lokala resurser eller från ett annat datacenter kan fÃ¶ ¶ã¶ssã¶k visas fÃ¶ã¶ss fÃ¶ã¶r:

- Systemfel 53 har uppstått. Det gick inte att hitta nätverkssökvägen.
- Systemfel 67 har uppstått. Nätverksnamnet kan inte hittas.
- Systemfel 87 har uppstått. Parametern är felaktig.

### <a name="cause-1-port-445-is-blocked"></a>Orsak 1: Port 445 är blockerad

Systemfel 53 eller systemfel 67 kan uppstå om port 445 utgående kommunikation till ett Azure Files-datacenter blockeras. En översikt över Internetleverantörer som tillåter och inte tillåter åtkomst från port 445 finns på [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Om du vill kontrollera om brandväggen eller Internet-leverantören blockerar port 445 `Test-NetConnection` använder du verktyget [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) eller cmdlet. 

Om du `Test-NetConnection` vill använda cmdlet måste Azure PowerShell-modulen installeras, se [Installera Azure PowerShell-modulen](/powershell/azure/install-Az-ps) för mer information. Kom ihåg att ersätta `<your-storage-account-name>` och `<your-resource-group-name>` med gällande namn för ditt lagringskonto.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
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
Azure File Sync kan omvandla din lokala Windows Server till en snabb cache av din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Azure File Sync fungerar över port 443 och kan därför användas som en lösning för att komma åt Azure-filer från klienter som har port 445 blockerad. [Lär dig hur du konfigurerar Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Lösning 2 – Använd VPN
Genom att konfigurera en VPN till ditt specifika lagringskonto kommer trafiken att gå igenom en säker tunnel i motsats till över internet. Följ [instruktionerna för att konfigurera VPN](storage-files-configure-p2s-vpn-windows.md) för att komma åt Azure-filer från Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Lösning 3 – Avblockera port 445 med hjälp av Internetleverantören/IT-administratören
Arbeta med IT-avdelningen eller Isp för att öppna port 445 utgående till [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Lösning 4 – Använd REST API-baserade verktyg som Storage Explorer/Powershell
Azure Files stöder också REST utöver SMB. REST-åtkomst fungerar över port 443 (standard tcp). Det finns olika verktyg som skrivs med REST API som möjliggör omfattande UI-upplevelse. [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) är en av dem. [Hämta och installera Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) och anslut till filresursen som backas upp av Azure Files. Du kan också använda [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) som också användare REST API.

### <a name="cause-2-ntlmv1-is-enabled"></a>Orsak 2: NTLMv1 är aktiverat

Systemfel 53 eller systemfel 87 kan uppstå om NTLMv1-kommunikation är aktiverad på klienten. Azure Files stöder endast NTLMv2-autentisering. När NTLMv1 är aktiverad är klienten mindre säker. Därför blockeras kommunikation för Azure Files. 

Kontrollera att följande registerundernyckel är inställd på värdet 3 för att avgöra om detta är orsaken till felet:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Mer information finns i ämnet [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) på TechNet.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Återställ värdet **LmCompatibilityLevel** till standardvärdet 3 i följande registerundernyckel:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Fel 1816 "Det finns inte tillräckligt med kvot för att bearbeta det här kommandot" när du kopierar till en Azure-filresurs

### <a name="cause"></a>Orsak

Fel 1816 inträffar när du når den övre gränsen för samtidiga öppna referenser som tillåts för en fil på datorn där filresursen monteras.

### <a name="solution"></a>Lösning

Minska antalet samtidiga öppna handtag genom att stänga vissa handtag och försök sedan igen. Mer information finns i [Microsoft Azure Storages checklista för prestanda och skalbarhet](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Om du vill visa öppna referenser för en filresurs, katalog eller fil använder du cmdleten [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell.  

Om du vill stänga öppna referenser för en filresurs, katalog eller fil använder du cmdleten [Stäng-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell.

> [!Note]  
> Cmdletsna Get-AzStorageFileHandle och Close-AzStorageFileHandle ingår i Az PowerShell-modul version 2.4 eller senare. Information om hur du installerar den senaste Az PowerShell-modulen finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

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
NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ta bort en fil kan fÃ¶1sÃ¶k visas fÃ¶ndesÃ¤nde

Den angivna resursen markeras för borttagning av en SMB-klient.

### <a name="cause"></a>Orsak
Det här problemet uppstår vanligtvis om filen eller katalogen har ett öppet handtag. 

### <a name="solution"></a>Lösning

Om SMB-klienterna har stängt alla öppna referenser och problemet fortsätter att uppstå utför du följande:

- Använd [cmdleten Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell för att visa öppna referenser.

- Använd [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell-cmdlet för att stänga öppna handtag. 

> [!Note]  
> Cmdletsna Get-AzStorageFileHandle och Close-AzStorageFileHandle ingår i Az PowerShell-modul version 2.4 eller senare. Information om hur du installerar den senaste Az PowerShell-modulen finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Långsam filkopiering till och från Azure Files i Windows

Du kan se långsam prestanda när du försöker överföra filer till Azure File-tjänsten.

- Om du inte har ett specifikt lägsta I/O-storlekskrav rekommenderar vi att du använder 1 MiB som I/O-storlek för optimal prestanda.
-   Om du vet den slutliga storleken på en fil som du utökar med skrivningar, och programvaran inte har kompatibilitetsproblem när den oskrivna svansen på filen innehåller nollor, ställ sedan in filstorleken i förväg istället för att göra varje skrivning till en förlängningsskrivning.
-   Använd rätt kopieringsmetod:
    -   Använd [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för överföring mellan två filresurser.
    -   Använd [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) mellan filresurser på en lokal dator.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Överväganden för Windows 8.1 eller Windows Server 2012 R2

För klienter som kör Windows 8.1 eller Windows Server 2012 R2 kontrollerar du att snabbkorrigeringen [KB3114025](https://support.microsoft.com/help/3114025) är installerad. Den här snabbkorrigeringen förbättrar prestanda för att skapa och stänga referenser.

Du kan köra följande skript för att kontrollera om snabbkorrigeringen har installerats:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Om snabbkorrigeringen är installerad visas följande utdata:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Windows Server 2012 R2-avbildningar på Azure Marketplace har snabbkorrigering KB3114025 installerat som standard, med början i december 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Ingen mapp med en enhetsbeteckning i "Den här datorn" eller "Den här datorn"

Om du mappar en Azure-filresurs som administratör med hjälp av nettoanvändning, verkar resursen saknas.

### <a name="cause"></a>Orsak

Som standard körs inte Utforskaren som administratör. Om du kör net användning från en administrativ kommandotolk, du mappa nätverksenheten som administratör. Eftersom mappade enheter är användarcentrerade visar användarkontot som är inloggad inte enheterna om de är monterade under ett annat användarkonto.

### <a name="solution"></a>Lösning
Montera resursen från en kommandorad som inte är administratör. Du kan också följa [det här TechNet-avsnittet](https://technet.microsoft.com/library/ee844140.aspx) för att konfigurera registervärdet **EnableLinkedConnections.**

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Kommandot Net use misslyckas om lagringskontot innehåller ett snedstreck

### <a name="cause"></a>Orsak

Kommandot Net Use tolkar ett snedstreck (/) som ett kommandoradsalternativ. Om ditt användarkontonamn börjar med ett snedstreck misslyckas enhetsmappningen.

### <a name="solution"></a>Lösning

Du kan använda något av följande steg för att lösa problemet:

- Kör följande PowerShell-kommando:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Från en kommandofil kan du köra kommandot så här:

  `Echo new-smbMapping ... | powershell -command –`

- Placera dubbla citattecken runt nyckeln för att komma runt det här problemet – om inte det främre snedstrecket är det första tecknet. Om så är fallet, antingen använda interaktivt läge och ange ditt lösenord separat eller återskapa dina nycklar för att få en nyckel som inte börjar med en framåt snedstreck.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Programmet eller tjänsten kan inte komma åt en monterad Azure Files-enhet

### <a name="cause"></a>Orsak

Enheter monteras per användare. Om ditt program eller din tjänst körs under ett annat användarkonto än det som monterade enheten visas inte enheten i programmet.

### <a name="solution"></a>Lösning

Använd någon av följande lösningar:

-   Montera enheten från samma användarkonto som innehåller programmet. Du kan använda ett verktyg som PsExec.
- Skicka namnet och nyckeln för lagringskontot i användarnamns- och lösenordsparametrarna för kommandot net use.
- Använd kommandot cmdkey för att lägga till autentiseringsuppgifterna i Autentiseringshanteraren. Utför detta från en kommandorad under servicekontokontexten, `runas`antingen genom en interaktiv inloggning eller genom att använda .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mappa resursen direkt utan att använda en mappad enhetsbeteckning. Vissa program kanske inte återansluter till enhetsbeteckningen korrekt, så det kan vara mer tillförlitligt att använda den fullständiga UNC-sökvägen. 

  `net use * \\storage-account-name.file.core.windows.net\share`

När du har följt dessa instruktioner kan följande felmeddelande visas när du kör nettoanvändning för system-/nätverkstjänstkontot: "Systemfel 1312 har inträffat. Det finns ingen angiven inloggningssession. Det kan redan ha avslutats." Om detta inträffar kontrollerar du att användarnamnet som skickas till net användning innehåller domäninformation (till exempel: "[lagringskontonamn].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Fel "Du kopierar en fil till ett mål som inte stöder kryptering"

När en fil kopieras över nätverket dekrypteras filen på källdatorn, överförs i klartext och krypteras på nytt vid målet. Du kan dock se följande fel när du försöker kopiera en krypterad fil: "Du kopierar filen till ett mål som inte stöder kryptering."

### <a name="cause"></a>Orsak
Det här problemet kan uppstå om du använder Krypterande filsystem (EFS). BitLocker-krypterade filer kan kopieras till Azure-filer. Azure Files stöder dock inte NTFS EFS.

### <a name="workaround"></a>Lösning
Om du vill kopiera en fil över nätverket måste du först dekryptera den. Använd någon av följande metoder:

- Använd kommandot **kopiera /d.** Det gör att de krypterade filerna kan sparas som dekrypterade filer på målet.
- Ange följande registernyckel:
  - Sökväg = HKLM\Software\Policies\Microsoft\Windows\System
  - Värdetyp = DWORD
  - Namn = CopyFileAllowDecryptedRemoteDestination
  - Värde = 1

Tänk på att inställningen av registernyckeln påverkar alla kopieringsåtgärder som görs till nätverksresurser.

## <a name="slow-enumeration-of-files-and-folders"></a>Långsam uppräkning av filer och mappar

### <a name="cause"></a>Orsak

Det här problemet kan uppstå om det inte finns tillräckligt med cache på klientdator för stora kataloger.

### <a name="solution"></a>Lösning

LÃ¶s problemet genom att justera **registervärdet DirectoryCacheEntrySizeMax** fÃ¶r att tillåta cachelagring av större kataloglistor i klientdatorn:

- Plats: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Värdeman: DirectoryCacheEntrySizeMax 
- Värdetyp:DWORD
 
 
Du kan till exempel ställa in den på 0x100000 och se om prestanda blir bättre.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Fel AadDsTenantNotFound vid aktivering av AAD DS-autentisering (Azure Active Directory Domain Service) för Azure Files "Det gick inte att hitta aktiva klienter med klient-ID aad-tenant-id"

### <a name="cause"></a>Orsak

Fel AadDsTenantNotFound inträffar när du försöker [aktivera Azure Active Directory Domain Services (Azure AD DS) autentisering på Azure-filer](storage-files-identity-auth-active-directory-domain-service-enable.md) på ett lagringskonto där [AAD Domain Service(AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) inte skapas på AAD-klienten för den associerade prenumerationen.  

### <a name="solution"></a>Lösning

Aktivera AAD DS på AAD-klienten för prenumerationen som ditt lagringskonto distribueras till. Du behöver administratörsbehörighet för AAD-klienten för att skapa en hanterad domän. Om du inte är administratör för Azure AD-klienten kontaktar du administratören och följer steg-för-steg-anvisningarna för att [aktivera Azure Active Directory Domain Services med Azure-portalen](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Systemfel 1359 uppstod. Ett internt fel har tagits emot via SMB-åtkomst till filresurser med Azure Active Directory Domain Service (AAD DS) autentisering aktiverad

### <a name="cause"></a>Orsak

Systemfel 1359 uppstod. Ett internt fel inträffar när du försöker ansluta till filresursen med AAD DS-autentisering aktiverad mot ett AAD DS med domännamn som börjar med ett numeriskt tecken. Om ditt AAD DS-domännamn är "1domän" får du det här felet när du försöker montera filresursen med AAD-autentiseringsuppgifter. 

### <a name="solution"></a>Lösning

För närvarande kan du överväga att distribuera om din AAD DS med ett nytt DNS-domännamn som gäller med reglerna nedan:
- Namn kan inte börja med ett numeriskt tecken.
- Namnen måste vara mellan 3 och 63 tecken långa.

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Det går inte att montera Azure-filer med AD-autentiseringsuppgifter 

### <a name="self-diagnostics-steps"></a>Steg för självdiagnostik
Kontrollera först att du har följt alla fyra stegen för att [aktivera Azure Files AD Authentication](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable).

Försök för det andra [att montera Azure-filresurs med lagringskontonyckel](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows). Om du inte kunde montera, ladda ner [AzFileDiagnostics.ps1](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) för att hjälpa dig att validera klientens körmiljö, identifiera den inkompatibla klientkonfigurationen som skulle orsaka åtkomstfel för Azure-filer, ger föreskrivande vägledning om självkorrigering och samlar in diagnostikspårningar.

För det tredje kan du köra cmdleten Debug-AzStorageAccountAuth för att utföra en uppsättning grundläggande kontroller av AD-konfigurationen med den inloggade AD-användaren. Denna cmdlet stöds på [AzFilesHybrid v0.1.2+ version](https://github.com/Azure-Samples/azure-files-samples/releases). Du måste köra den här cmdleten med en AD-användare som har ägarbehörighet för mållagringskontot.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
Cmdlet utför dessa kontroller nedan i följd och ger vägledning för fel:
1. CheckPort445Anslutning: kontrollera att port 445 är öppen för SMB-anslutning
2. CheckDomainJoined: verifiera att klientdatorn är domän ansluten till AD
3. CheckADObject: bekräfta att den inloggade användaren har en giltig representation i AD-domänen att lagringskontot är kopplat till
4. CheckGetKerberosTicket: försök att få en Kerberos-biljett för att ansluta till lagringskontot 
5. CheckADObjectPasswordIsCorrect: se till att lösenordet som konfigurerats på AD-identiteten som representerar lagringskontot matchar nyckeln för lagringskontots trottoarkanter
6. CheckSidHasAadUser: kontrollera att den inloggade AD-användaren är synkroniserad med Azure AD

Vi arbetar aktivt med att utöka denna diagnostik cmdlet för att ge bättre felsökningsvägledning.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.

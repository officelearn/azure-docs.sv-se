---
title: Felsöka Azure Files problem i Windows | Microsoft Docs
description: Felsöka Azure Files problem i Windows
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 2b49598d51fb785872fccec966ac11a95ef3cede
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84657726"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Felsöka Azure Files-problem i Windows

Den här artikeln innehåller vanliga problem som är relaterade till Microsoft Azure filer när du ansluter från Windows-klienter. Den innehåller också möjliga orsaker och lösningar på problemen. Förutom fel söknings stegen i den här artikeln kan du också använda [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)   för att kontrol lera att Windows-klientens miljö uppfyller rätt krav. AzFileDiagnostics automatiserar identifiering av de flesta av de symtom som nämns i den här artikeln och hjälper dig att konfigurera din miljö för att få bästa möjliga prestanda. Du kan också hitta den här informationen i [fel sökaren Azure Files-resurser](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) som innehåller steg som hjälper dig med problem med att ansluta/mappa/montera Azure Files resurser.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fel 5 när du monterar en Azure-filresurs

När du försöker montera en fil resurs kan du få följande fel meddelande:

- Systemfel 5 har uppstått. Åtkomst nekad.

### <a name="cause-1-unencrypted-communication-channel"></a>Orsak 1: okrypterad kommunikations kanal

Av säkerhetsskäl blockeras anslutningar till Azure-filresurser om kommunikationskanalen inte är krypterad och om anslutningsförsöket inte görs från samma datacenter där Azure-filresurserna finns. Okrypterade anslutningar inom samma datacenter kan också blockeras om inställningen [Säker överföring krävs ](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är aktiverad på lagringskontot. En krypterad kommunikationskanal tillhandahålls endast om användarens klientoperativsystem stöder SMB-kryptering.

Windows 8, Windows Server 2012 och senare versioner av respektive system förhandlar begäranden som innehåller SMB 3.0, som stöder kryptering.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. Anslut från en klient som har stöd för SMB-kryptering (Windows 8, Windows Server 2012 eller senare) eller Anslut från en virtuell dator i samma data Center som det Azure Storage-konto som används för Azure-filresursen.
2. Kontrol lera att inställningen för [säker överföring](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) är inaktive rad på lagrings kontot om klienten inte stöder SMB-kryptering.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 2: virtuella nätverk eller brand Väggs regler är aktiverade på lagrings kontot 

Om virtuellt nätverk (VNET) och brandväggsregler har konfigurerats på lagringskontot tillåts inte nätverkstrafik om inte åtkomst tillåts för klientens IP-adress eller virtuella nätverk.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Kontrollera att det virtuella nätverket och brandväggsreglerna har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggsreglerna som orsakar problemet kan du tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Orsak 3: behörigheter på resurs nivå är felaktiga vid användning av Identity-baserad autentisering

Om användarna har åtkomst till Azure-filresursen med hjälp av Active Directory (AD) eller Azure Active Directory Domain Services (Azure AD DS) kommer åtkomst till fil resursen att Miss Miss kan fel meddelandet "åtkomst nekas" om behörigheter på resurs nivå är felaktiga. 

### <a name="solution-for-cause-3"></a>Lösning för orsak 3

Information om hur du uppdaterar behörigheter på resurs nivå finns i [tilldela åtkomst behörigheter till en identitet](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Fel 53, fel 67 eller fel 87 när du monterar eller demonterar en Azure-filresurs

När du försöker montera en fil resurs från en lokal plats eller från ett annat data Center kan följande fel uppstå:

- Systemfel 53 har uppstått. Det gick inte att hitta nätverkssökvägen.
- Systemfel 67 har uppstått. Nätverksnamnet kan inte hittas.
- Systemfel 87 har uppstått. Parametern är felaktig.

### <a name="cause-1-port-445-is-blocked"></a>Orsak 1: port 445 är blockerad

Systemfel 53 eller systemfel 67 kan uppstå om Port 445 utgående kommunikation till ett Azure Files Data Center blockeras. En översikt över Internetleverantörer som tillåter och inte tillåter åtkomst från port 445 finns på [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Om du vill kontrol lera om brand väggen eller Internet leverantören blockerar port 445 använder du [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) -verktyget eller- `Test-NetConnection` cmdleten. 

Om du vill använda `Test-NetConnection` cmdleten måste Azure PowerShell-modulen installeras, se [Installera Azure PowerShell-modulen](/powershell/azure/install-Az-ps) för mer information. Kom ihåg att ersätta `<your-storage-account-name>` och `<your-resource-group-name>` med gällande namn för ditt lagringskonto.

   
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
Azure File Sync kan omvandla din lokala Windows Server till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Azure File Sync fungerar över port 443 och kan därför användas som en lösning för att komma åt Azure Files från klienter där port 445 blockeras. [Lär dig att konfigurera Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Lösning 2 – Använd VPN
Genom att konfigurera en VPN-anslutning till ditt lagrings konto går trafiken via en säker tunnel i stället för via Internet. Följ [anvisningarna för att konfigurera VPN](storage-files-configure-p2s-vpn-windows.md) för att få åtkomst till Azure Files från Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Lösning 3 – Avblockera port 445 med hjälp av Internetleverantören/IT-administratören
Arbeta med IT-avdelningen eller Internet leverantören för att öppna port 445 utgående till [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Lösning 4 – Använd REST API-baserade verktyg som Storage Explorer/Powershell
Azure Files stöder också REST utöver SMB. REST-åtkomst fungerar över port 443 (standard-TCP). Det finns olika verktyg som skrivs med REST API som möjliggör omfattande GRÄNSSNITTs upplevelser. [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) är en av dem. [Hämta och installera Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) och Anslut till fil resursen med Azure Files. Du kan också använda [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) som även användar REST API.

### <a name="cause-2-ntlmv1-is-enabled"></a>Orsak 2: NTLMv1 har Aktiver ATS

Systemfel 53 eller systemfel 87 kan uppstå om NTLMv1-kommunikation är aktiverat på klienten. Azure Files stöder endast NTLMv2-autentisering. När NTLMv1 är aktiverad är klienten mindre säker. Därför blockeras kommunikation för Azure Files. 

Kontrollera att följande registerundernyckel är inställd på värdet 3 för att avgöra om detta är orsaken till felet:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Mer information finns i ämnet [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) på TechNet.

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Återställ värdet **LmCompatibilityLevel** till standardvärdet 3 i följande registerundernyckel:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Fel 1816 "det finns inte tillräckligt med kvot för att bearbeta det här kommandot" när du kopierar till en Azure-filresurs

### <a name="cause"></a>Orsak

Fel 1816 uppstår när du når den övre gränsen för samtidiga öppna referenser som tillåts för en fil på den dator där fil resursen monteras.

### <a name="solution"></a>Lösning

Minska antalet samtidiga öppna referenser genom att stänga några referenser och försök sedan igen. Mer information finns i [Check lista för Microsoft Azure Storage prestanda och skalbarhet](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Om du vill visa öppna referenser för en fil resurs, katalog eller fil använder du PowerShell-cmdleten [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) .  

Om du vill stänga öppna referenser för en fil resurs, katalog eller fil använder du PowerShell-cmdleten [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) .

> [!Note]  
> Cmdletarna get-AzStorageFileHandle och Close-AzStorageFileHandle ingår i AZ PowerShell-modul version 2,4 eller senare. Information om hur du installerar den senaste AZ PowerShell-modulen finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fel "ingen åtkomst" när du försöker komma åt eller ta bort en Azure-filresurs  
När du försöker komma åt eller ta bort en Azure-filresurs i portalen kan du få följande fel meddelande:

Ingen åtkomst  
Felkod: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Orsak 1: virtuella nätverk eller brand Väggs regler är aktiverade på lagrings kontot

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

Kontrollera att det virtuella nätverket och brandväggsreglerna har konfigurerats korrekt på lagringskontot. Om du vill testa om det virtuella nätverket eller brandväggsreglerna som orsakar problemet kan du tillfälligt ändra inställningen på lagringskontot för att **tillåta åtkomst från alla nätverk**. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Orsak 2: ditt användar konto har inte åtkomst till lagrings kontot

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Bläddra till det lagrings konto där Azure-filresursen finns, klicka på **åtkomst kontroll (IAM)** och kontrol lera att ditt användar konto har åtkomst till lagrings kontot. Mer information finns i [så här skyddar du ditt lagrings konto med hjälp av rollbaserad Access Control (RBAC)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Det går inte att ta bort en fil eller katalog i en Azure-filresurs
När du försöker ta bort en fil kan du få följande fel meddelande:

Den angivna resursen har markerats för borttagning av en SMB-klient.

### <a name="cause"></a>Orsak
Det här problemet uppstår vanligt vis om filen eller katalogen har en öppen referens. 

### <a name="solution"></a>Lösning

Om SMB-klienterna har stängt alla öppna referenser och problemet fortsätter att inträffa, gör du följande:

- Använd PowerShell [-cmdleten Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) för att visa öppna referenser.

- Använd PowerShell [-cmdleten Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) för att stänga öppna referenser. 

> [!Note]  
> Cmdletarna get-AzStorageFileHandle och Close-AzStorageFileHandle ingår i AZ PowerShell-modul version 2,4 eller senare. Information om hur du installerar den senaste AZ PowerShell-modulen finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Långsam filkopiering till och från Azure Files i Windows

Du kan se långsamma prestanda när du försöker överföra filer till Azure File Service.

- Om du inte har en speciell minimi krav på I/O-storlek rekommenderar vi att du använder 1 MiB som I/O-storlek för bästa prestanda.
-   Om du känner till den slutliga storleken på en fil som du utökar med skrivningar, och program varan inte har kompatibilitetsproblem när den skrivna filen i filen innehåller nollor, ställer du in fil storleken i förväg i förväg i stället för att göra varje Skriv en utökad skrivning.
-   Använd rätt kopierings metod:
    -   Använd [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för överföring mellan två fil resurser.
    -   Använd [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) mellan fil resurser på en lokal dator.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Överväganden för Windows 8,1 eller Windows Server 2012 R2

För klienter som kör Windows 8,1 eller Windows Server 2012 R2 kontrollerar du att snabb korrigeringen [KB3114025](https://support.microsoft.com/help/3114025) är installerad. Den här snabb korrigeringen förbättrar prestanda i Create-och Close-handtag.

Du kan köra följande skript för att kontrol lera om snabb korrigeringen har installerats:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Om snabb korrigeringen har installerats visas följande utdata:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Windows Server 2012 R2-avbildningar i Azure Marketplace har snabb korrigeringar KB3114025 installerade som standard, från och med december 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Ingen mapp med en enhets beteckning i "Min Dator" eller "den här datorn"

Om du mappar en Azure-filresurs som administratör med hjälp av net use verkar resursen saknas.

### <a name="cause"></a>Orsak

Som standard körs inte Windows Utforskaren som administratör. Om du kör net use från en administrativ kommando tolk mappar du nätverks enheten som administratör. Eftersom mappade enheter är användarvänliga visar användar kontot som är inloggad inte enheterna om de monteras under ett annat användar konto.

### <a name="solution"></a>Lösning
Montera resursen från en kommando rad som inte är administratör. Du kan också följa [det här TechNet-avsnittet](https://technet.microsoft.com/library/ee844140.aspx) om du vill konfigurera registervärdet **EnableLinkedConnections** .

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Net use-kommandot Miss lyckas om lagrings kontot innehåller ett snedstreck

### <a name="cause"></a>Orsak

Kommandot net use tolkar ett snedstreck (/) som kommando rads alternativ. Om ditt användar konto namn börjar med ett snedstreck, Miss lyckas enhets mappningen.

### <a name="solution"></a>Lösning

Du kan använda något av följande steg för att lösa problemet:

- Kör följande PowerShell-kommando:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Du kan köra kommandot på det här sättet från en kommando fil:

  `Echo new-smbMapping ... | powershell -command –`

- Undvik det här problemet genom att omge det med dubbla citat tecken – om inte snedstrecket är det första tecknet. Om det är det använder du antingen det interaktiva läget och anger ditt lösen ord separat eller återskapar nycklar för att få en nyckel som inte börjar med ett snedstreck.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Program eller tjänst kan inte komma åt en monterad Azure Files enhet

### <a name="cause"></a>Orsak

Enheter monteras per användare. Om programmet eller tjänsten körs under ett annat användar konto än det som monterade enheten, kommer programmet inte att se enheten.

### <a name="solution"></a>Lösning

Använd någon av följande lösningar:

-   Montera enheten från samma användar konto som innehåller programmet. Du kan använda ett verktyg som PsExec.
- Överför lagrings kontots namn och nyckel i parametrarna för användar namn och lösen ord för kommandot net use.
- Använd kommandot cmdkey för att lägga till autentiseringsuppgifterna i Autentiseringshanteraren. Utför detta från en kommando rad under tjänst konto kontexten, antingen via en interaktiv inloggning eller med hjälp av `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mappa resursen direkt utan att använda en mappad enhets beteckning. Vissa program kan inte återansluta till enhets beteckningen korrekt, så det kan vara mer tillförlitligt att använda den fullständiga UNC-sökvägen. 

  `net use * \\storage-account-name.file.core.windows.net\share`

När du har följt de här anvisningarna kan du få följande fel meddelande när du kör net use för system-eller nätverks tjänst kontot: "systemfel 1312 har inträffat. Det finns ingen angiven inloggningssession. Det kanske redan har avbrutits. " Om detta inträffar kontrollerar du att det användar namn som skickas till net use innehåller domän information (till exempel: "[lagrings kontots namn]. File. Core. Windows. net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Fel "du kopierar en fil till ett mål som inte stöder kryptering"

När en fil kopieras över nätverket dekrypteras filen på käll datorn, skickas i klartext och krypteras på nytt vid målet. Du kan dock se följande fel när du försöker kopiera en krypterad fil: "du kopierar filen till ett mål som inte stöder kryptering."

### <a name="cause"></a>Orsak
Det här problemet kan uppstå om du använder krypterande filsystem (EFS). BitLocker-krypterade filer kan kopieras till Azure Files. Azure Files stöder dock inte NTFS EFS.

### <a name="workaround"></a>Lösning
Om du vill kopiera en fil över nätverket måste du först dekryptera den. Använd någon av följande metoder:

- Använd kommandot **copy/d** . Det gör att de krypterade filerna kan sparas som dekrypterade filer vid målet.
- Ange följande register nyckel:
  - Sökväg = HKLM\Software\Policies\Microsoft\Windows\System
  - Värdetyp = DWORD
  - Namn = CopyFileAllowDecryptedRemoteDestination
  - Värde = 1

Tänk på att om du anger register nyckeln påverkar det alla kopierings åtgärder som görs till nätverks resurser.

## <a name="slow-enumeration-of-files-and-folders"></a>Långsam uppräkning av filer och mappar

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om det inte finns tillräckligt med cache på klient datorn för stora kataloger.

### <a name="solution"></a>Lösning

Lös problemet genom att justera register värdet **DirectoryCacheEntrySizeMax** så att det tillåter cachelagring av större katalog listor på klient datorn:

- Plats: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Värde Mane: DirectoryCacheEntrySizeMax 
- Värdetyp: DWORD
 
 
Du kan till exempel ställa in den på 0x100000 och se om prestandan blir bättre.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Fel vid AadDsTenantNotFound aktivering av AAD DS-autentisering (Azure Active Directory Domain Service) för Azure Files "Det gick inte att hitta aktiva klienter med klient-ID AAD-Tenant-ID"

### <a name="cause"></a>Orsak

Fel AadDsTenantNotFound inträffar när du försöker [aktivera Azure Active Directory Domain Services (Azure AD DS)-autentisering på Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) på ett lagrings konto där [AAD-domän tjänsten (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) inte skapas på AAD-klienten för den associerade prenumerationen.  

### <a name="solution"></a>Lösning

Aktivera AAD DS på AAD-klienten för den prenumeration som ditt lagrings konto har distribuerats till. Du behöver administratörs behörighet för AAD-klienten för att skapa en hanterad domän. Om du inte är administratör för Azure AD-klienten kontaktar du administratören och följer steg-för-steg-vägledningen för att [aktivera Azure Active Directory Domain Services att använda Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Fel: system fel 1359 har uppstått. Ett internt fel som har tagits emot via SMB-åtkomst till fil resurser med autentisering med Azure Active Directory Domain Service (AAD DS) aktiverat

### <a name="cause"></a>Orsak

Fel: system fel 1359 har uppstått. Ett internt fel inträffar när du försöker ansluta till fil resursen med AAD DS-autentisering aktive rad mot en AAD DS med domän-DNS-namn som börjar med ett numeriskt tecken. Om ditt DNS-namn för AAD DS-domänen till exempel är "1domain", får du det här felet när du försöker montera fil resursen med AAD-autentiseringsuppgifter. 

### <a name="solution"></a>Lösning

För närvarande kan du överväga att omdistribuera AAD DS med ett nytt domän-DNS-namn som gäller för reglerna nedan:
- Namn får inte börja med ett numeriskt tecken.
- Namn måste vara mellan 3 och 63 tecken.

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Det gick inte att montera Azure Files med AD-autentiseringsuppgifter 

### <a name="self-diagnostics-steps"></a>Själv diagnos steg
Kontrol lera först att du har följt alla fyra stegen för att [aktivera Azure Files AD-autentisering](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable).

Försök sedan att [montera Azure-filresursen med lagrings konto nyckeln](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows). Om du inte kunde montera hämtar du [AzFileDiagnostics.ps1](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) för att hjälpa dig att validera klienten som kör miljön, identifiera inkompatibel klient konfiguration som skulle orsaka åtkomst fel för Azure Files, ger vägledning om själv korrigering och samlar in diagnostiska spårningar.

För det tredje kan du köra cmdleten debug-AzStorageAccountAuth för att utföra en uppsättning grundläggande kontroller på din AD-konfiguration med den inloggade AD-användaren. Den här cmdleten stöds i [versionen AzFilesHybrid v0.1.2+](https://github.com/Azure-Samples/azure-files-samples/releases). Du måste köra denna cmdlet med en AD-användare som har ägarbehörighet på mållagringskontot.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
Cmdleten utför dessa kontroller nedan i följd och ger vägledning för felen:
1. CheckPort445Connectivity: kontrol lera att port 445 är öppen för SMB-anslutning
2. CheckDomainJoined: verifiera att klient datorn är domänansluten till AD
3. CheckADObject: bekräfta att den inloggade användaren har en giltig representation i den AD-domän som lagrings kontot är associerat med
4. CheckGetKerberosTicket: försöker hämta en Kerberos-biljett för att ansluta till lagrings kontot 
5. CheckADObjectPasswordIsCorrect: kontrol lera att lösen ordet som har kon figurer ATS på den AD-identitet som representerar lagrings kontot matchar lagrings kontots kerb-nyckel
6. CheckSidHasAadUser: kontrol lera att den inloggade AD-användaren är synkroniserad med Azure AD. Om du vill se om en särskild AD-användare är synkroniserad med Azure AD kan du ange parametern-UserName och-Domain i indataparametrarna.
7. CheckAadUserHasSid: kontrol lera om en Azure AD-användare har ett SID i AD, kräver användaren att ange objekt-ID: t för Azure AD-användaren med-ObjectId. 
8. CheckStorageAccountDomainJoined: kontrol lera om du har registrerat en identitet i AD som representerar lagrings kontot. 

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Det gick inte att konfigurera katalog-/fil nivå behörigheter (Windows ACL: er) med Utforskaren i Windows

### <a name="symptom"></a>Symptom

Du kan uppleva antingen symptom som beskrivs nedan när du försöker konfigurera Windows ACL: er med Utforskaren på en monterad fil resurs:
- När du har klickat på Redigera behörighet på fliken säkerhet läser inte behörighets guiden in. 
- När du försöker välja en ny användare eller grupp visar inte domän platsen rätt AD DS-domän. 

### <a name="solution"></a>Lösning

Vi rekommenderar att du använder [icacls-verktyget](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) för att konfigurera behörigheter för katalogen/fil nivån som en lösning. 

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.

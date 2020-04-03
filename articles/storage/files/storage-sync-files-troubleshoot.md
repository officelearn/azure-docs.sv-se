---
title: Felsöka Synkronisering av Azure-filer | Microsoft-dokument
description: Felsöka vanliga problem med Azure File Sync.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: d46f513fccf9921d4cf47835bc9d5be4c6ffe241
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607498"
---
# <a name="troubleshoot-azure-file-sync"></a>Felsök Azure File Sync
Använd Azure File Sync för att centralisera organisationens filresurser i Azure Files, samtidigt som flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver bevaras. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cachar som du behöver över hela världen.

Den här artikeln är utformad för att hjälpa dig att felsöka och lösa problem som kan uppstå med din Azure File Sync-distribution. Vi beskriver också hur du samlar in viktiga loggar från systemet om det krävs en djupare undersökning av problemet. Om du inte ser svaret på din fråga kan du kontakta oss via följande kanaler (i eskalerande ordning):

1. [Azure Storage Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Azure-filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft-support. Om du vill skapa en ny supportbegäran väljer du hjälp **+ supportknappen** på fliken Azure på **fliken Hjälp** + och väljer sedan Ny **supportbegäran**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Jag har problem med Azure File Sync på min server (synkronisering, molnnivådelning osv.). Ska jag ta bort och återskapa serverslutpunkten?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Agentinstallation och serverregistrering
<a id="agent-installation-failures"></a>**Felsöka agentinstallationsfel**  
Om installationen av Azure File Sync-agenten misslyckas kör du följande kommando vid en upphöjd kommandotolk för att aktivera loggning under agentinstallationen:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Granska installer.log för att fastställa orsaken till installationsfelet.

<a id="agent-installation-on-DC"></a>**Agentinstallationen misslyckas på Active Directory Domain Controller**  
Om du försöker installera synkroniseringsagenten på en Active Directory-domänkontrollant där PDC-rollägaren finns på en Windows Server 2008 R2 eller under OS-versionen kan du lösa problemet där synkroniseringsagenten inte installeras.

Lös problemet genom att överföra PDC-rollen till en annan domänkontrollant som kör Windows Server 2012 R2 eller senare och sedan installera synkronisering.

<a id="parameter-is-incorrect"></a>**Det går inte att komma åt en volym på Windows Server 2012 R2 med fel: Parametern är felaktig**  
När du har skapat en serverslutpunkt på Windows Server 2012 R2 uppstår följande fel vid åtkomst till volymen:

enhetslett:\ inte är tillgänglig.  
Parametern är felaktig.

LÃ¶s problemet genom att installera de senaste uppdateringarna fÃ¶r Windows Server 2012 R2 och starta om servern.

<a id="server-registration-missing-subscriptions"></a>**Serverregistreringen listar inte alla Azure-prenumerationer**  
När du registrerar en server med ServerRegistration.exe saknas prenumerationer när du klickar på listrutan Azure-prenumeration.

Det hÃ¤r problemet uppstÃ¥r pÃ¥ grund av att ServerRegistration.exe för närvarande inte stöder miljöer med flera innehavare. Det här problemet kommer att åtgärdas i en framtida Azure File Sync-agentuppdatering.

Om du vill lösa problemet använder du följande PowerShell-kommandon för att registrera servern:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Serverregistrering visar följande meddelande: "Förutsättningar saknas"**  
Det här meddelandet visas om Az- eller AzureRM PowerShell-modulen inte är installerad på PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe stöder inte PowerShell 6.x. Du kan använda cmdleten Register-AzStorageSyncServer på PowerShell 6.x för att registrera servern.

Så här installerar du Az- eller AzureRM-modulen på PowerShell 5.1:

1. Skriv **powershell** från en upphöjd kommandotolk och tryck enter.
2. Installera den senaste Az- eller AzureRM-modulen genom att följa dokumentationen:
    - [Az modul (kräver .NET 4.7.2)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [AzureRM-modul]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Kör ServerRegistration.exe och slutför guiden för att registrera servern med en storage sync-tjänst.

<a id="server-already-registered"></a>**Serverregistrering visar följande meddelande: "Den här servern är redan registrerad"** 

![En skärmbild av dialogrutan Serverregistrering med felmeddelandet "servern är redan registrerad"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Det här meddelandet visas om servern tidigare har registrerats med en storage sync-tjänst. Om du vill avregistrera servern från den aktuella lagringssynkroniseringstjänsten och sedan registrera dig med en ny Storage Sync-tjänst slutför du stegen som beskrivs i [Avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Om servern inte visas under **Registrerade servrar** i storage sync-tjänsten kör du följande PowerShell-kommandon på den server som du vill avregistrera:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Om servern ingår i ett kluster kan du använda parametern *Reset-StorageSyncServer -CleanClusterRegistration* (tillval) för att även ta bort klusterregistreringen.

<a id="web-site-not-trusted"></a>**När jag registrerar en server, ser jag många "webbplats inte betrodda" svar. Varför?**  
Det hÃ¤r problemet uppstÃ¥r nÃ¤r **sÃ¤kert fÃ¶rbÃ¤nded Internet Explorer-säkerhetsprincipen** är aktiverad vid serverregistrering. Mer information om hur du inaktiverar **säkerhetsprincipen Förbättrad Internet Explorer** korrekt finns i [Förbereda Windows Server som ska användas med Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) och [hur du distribuerar Azure File Sync](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Servern visas inte under registrerade servrar i Azure-portalen**  
Om en server inte visas under **Registrerade servrar** för en storage sync-tjänst:
1. Logga in på den server som du vill registrera.
2. Öppna Utforskaren och gå sedan till installationskatalogen för Lagringssynkroniseringsagenten (standardplatsen är C:\Program\Azure\StorageSyncAgent). 
3. Kör ServerRegistration.exe och slutför guiden för att registrera servern med en storage sync-tjänst.

## <a name="sync-group-management"></a>Hantering av synkronisera grupp
<a id="cloud-endpoint-using-share"></a>**Cloud slutpunkt skapande misslyckas, med det här felet: "Den angivna Azure FileShare används redan av en annan CloudEndpoint"**  
Det här felet uppstår om Azure-filresursen redan används av en annan molnslutpunkt. 

Om det här meddelandet visas och Azure-filresursen för närvarande inte används av en molnslutpunkt, slutför du följande steg för att rensa Azure File Sync-metadata på Azure-filresursen:

> [!Warning]  
> Om du tar bort metadata på en Azure-filresurs som för närvarande används av en molnslutpunkt misslyckas Azure File Sync-åtgärder. 

1. Gå till din Azure-filresurs i Azure-portalen.  
2. Högerklicka på Azure-filresursen och välj sedan **Redigera metadata**.
3. Högerklicka på **SyncService**och välj sedan **Ta bort**.

<a id="cloud-endpoint-authfailed"></a>**Cloud slutpunkt skapande misslyckas, med det här felet: "AuthorizationFailed"**  
Det här felet uppstår om ditt användarkonto inte har tillräckliga rättigheter för att skapa en molnslutpunkt. 

Om du vill skapa en molnslutpunkt måste ditt användarkonto ha följande behörigheter för Microsoft-auktorisering:  
* Läs: Få rolldefinition
* Skriva: Skapa eller uppdatera anpassad rolldefinition
* Läs: Få rolltilldelning
* Skriv: Skapa rolltilldelning

Följande inbyggda roller har de nödvändiga Microsoft Authorization-behörigheterna:  
* Ägare
* Administratör för användaråtkomst

Så här avgör du om din användarkontoroll har de behörigheter som krävs:  
1. I Azure-portalen väljer du **Resursgrupper**.
2. Välj den resursgrupp där lagringskontot finns och välj sedan **Åtkomstkontroll (IAM)**.
3. Välj fliken **Rolltilldelningar.**
4. Välj **roll** (till exempel Ägare eller Deltagare) för ditt användarkonto.
5. Välj **Microsoft Authorization**i listan **Resursprovider** . 
    * **Rolltilldelning** bör ha **läs-** och skrivbehörighet. **Write**
    * **Rolldefinitionen** bör ha **läs-** och skrivbehörighet. **Write**

<a id="-2134375898"></a>**Det går inte att skapa serverslutpunkter med det här felet: "MgmtServerJobFailed" (Felkod: -2134375898 eller 0x80c80226)**  
Felet uppstår om sökvägen till serverslutpunkten finns på systemvolymen och molnnivåindelning är aktiverat. Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.

<a id="-2147024894"></a>**Det går inte att skapa serverslutpunkter med det här felet: "MgmtServerJobFailed" (Felkod: -2147024894 eller 0x80070002)**  
Det här felet uppstår om den angivna sökvägen till serverslutpunkten är ogiltig. Kontrollera att sökvägen till serverslutpunkten är en lokalt ansluten NTFS-volym. Azure File Sync stöder inte mappade enheter som en serverslutpunktssökväg.

<a id="-2134375640"></a>**Det går inte att skapa serverslutpunkter med det här felet: "MgmtServerJobFailed" (Felkod: -2134375640 eller 0x80c80328)**  
Det här felet uppstår om den angivna serverslutpunktssökvägen inte är en NTFS-volym. Kontrollera att sökvägen till serverslutpunkten är en lokalt ansluten NTFS-volym. Azure File Sync stöder inte mappade enheter som en serverslutpunktssökväg.

<a id="-2134347507"></a>**Det går inte att skapa serverslutpunkter med det här felet: "MgmtServerJobFailed" (Felkod: -2134347507 eller 0x80c8710d)**  
Det här felet beror på att Azure File Sync inte stöder serverslutpunkter på volymer som har en komprimerad System Volume Information-mapp. Lös problemet genom att expandera mappen System Volume Information. Om mappen System Volume information är den enda mappen som komprimeras på volymen utför du följande steg:

1. Ladda ner [PsExec-verktyget.](https://docs.microsoft.com/sysinternals/downloads/psexec)
2. Kör följande kommando från en upphöjd kommandotolk för att starta en kommandotolk som körs under systemkontot: **PsExec.exe -i -s -d cmd**
3. Från kommandotolken som körs under systemkontot skriver du följande kommandon och trycker på RETUR:   
    **cd /d "enhetsbeteckning:\Information om systemvolym"**  
    **kompakt /u /s**

<a id="-2134376345"></a>**Det går inte att skapa serverslutpunkter med det här felet: "MgmtServerJobFailed" (Felkod: -2134376345 eller 0x80C80067)**  
Det här felet uppstår om gränsen för serverslutpunkter per server uppnås. Azure File Sync stöder för närvarande upp till 30 serverslutpunkter per server. Mer information finns i [Azure File Sync-skalningsmål](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Det går inte att skapa serverslutpunkter med det här felet: "MgmtServerJobFailed" (Felkod: -2134376427 eller 0x80c80015)**  
Det här felet uppstår om en annan serverslutpunkt redan synkroniserar den angivna serversökvägen. Azure File Sync har inte stöd för att flera serverslutpunkter synkroniserar samma katalog eller volym.

<a id="-2160590967"></a>**Det går inte att skapa serverslutpunkter med det här felet: "MgmtServerJobFailed" (Felkod: -2160590967 eller 0x80c80077)**  
Det här felet uppstår om serverns slutpunktssökväg innehåller överblivna nivåindelade filer. Om en serverslutpunkt nyligen har tagits bort väntar du tills rensningen av de överblivna nivåindelad filerna har slutförts. Ett händelse-ID 6662 loggas till telemetrihändelseloggen när den överblivna nivåindelade filrensningen har startat. Ett händelse-ID 6661 loggas när den överblivna nivåindelade filrensningen har slutförts och en serverslutpunkt kan återskapas med hjälp av sökvägen. Om serverslutspunkten misslyckas efter att ett händelse-ID 6661 har loggats tar du bort de överblivna nivåindelade filerna genom att utföra de steg som dokumenteras i [de nivåindelade filerna inte är tillgängliga på servern när du har tagit bort ett serverslutpunktsavsnitt.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)

<a id="-2134347757"></a>**Det går inte att ta bort serverslutpunkten med det här felet: "MgmtServerJobExpired" (Felkod: -2134347757 eller 0x80c87013)**  
Det här felet uppstår om servern är offline eller inte har någon nätverksanslutning. Om servern inte längre är tillgänglig avregistrerar du servern i portalen, vilket tar bort serverslutpunkterna. Om du vill ta bort serverslutpunkterna följer du stegen som beskrivs i [Avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Det går inte att öppna sidan egenskaper för serverslutpunktsegenskaper eller uppdatera molnnivådelningsprincipen**  
Det hÃ¤r problemet kan uppstÃ¥ om en hanteringsåtgärd på serverslutpunkten misslyckas. Om egenskapssidan för serverslutpunkt inte öppnas i Azure-portalen kan det här problemet åtgärdas genom att uppdatera serverslutpunkten med PowerShell-kommandon från servern. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Serverslutpunkten har en hälsostatus för "Ingen aktivitet" eller "Väntande" och servertillståndet på bladet för registrerade servrar är "Visas offline"**  

Det hÃ¤r problemet kan uppstÃ¥ om Storage Sync Monitor-processen (AzureStorageSyncMonitor.exe) inte kÃ¤r kÃ¤rs eller om servern inte kan komma åt Azure File Sync-tjänsten.

På servern som visas som "Visas offline" i portalen tittar du på händelse-ID 9301 i telemetrihändelseloggen (finns under Program och tjänster\Microsoft\FileSync\Agent i Loggboken) för att avgöra varför servern inte kan komma åt Azure File Sync-tjänsten. 

- Om **GetNextJob har slutförts med status: 0** loggas kan servern kommunicera med Azure File Sync-tjänsten. 
    - Öppna Aktivitetshanteraren på servern och kontrollera att Storage Sync Monitor-processen (AzureStorageSyncMonitor.exe) körs. Om processen inte körs provar du först att starta om servern. Om det inte går att lösa problemet genom att starta om servern uppgraderar du till den senaste [agentversionen](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) för Azure File Sync. 

- Om **GetNextJob har slutförts med status: -2134347756** loggas, kan servern inte kommunicera med Azure File Sync-tjänsten på grund av en brandvägg eller proxy. 
    - Om servern finns bakom en brandvägg kontrollerar du att port 443 för utgående trafik tillåts. Om brandväggen begränsar trafiken till specifika domäner bekräftar du att de domäner som anges i [brandväggsdokumentationen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) är tillgängliga.
    - Om servern ligger bakom en proxy konfigurerar du proxyinställningarna för hela datorn eller appspecifikt genom att följa stegen i [proxydokumentationen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy).
    - Använd cmdleten Test-StorageSyncNetworkConnectivity för att kontrollera nätverksanslutningen till tjänstslutpunkterna. Mer information finns i [Testa nätverksanslutning till tjänstslutpunkter](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).

- Om **GetNextJob har slutförts med status: -2134347764** loggas, kan servern inte kommunicera med Azure File Sync-tjänsten på grund av ett utgånget eller borttaget certifikat.  
    - Kör följande PowerShell-kommando på servern för att återställa certifikatet som används för autentisering:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Serverslutpunkten har en hälsostatus för "Ingen aktivitet" och servertillståndet på bladet för registrerade servrar är "Online"**  

En hälsostatus för serverslutpunkten för "Ingen aktivitet" innebär att serverns slutpunkt inte har loggat synkroniseringsaktivitet under de senaste två timmarna.

Information om hur du kontrollerar aktuell synkroniseringsaktivitet på en server finns i [Hur övervakar jag förloppet för en aktuell synkroniseringssession?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

En serverslutpunkt kanske inte loggar synkroniseringsaktivitet i flera timmar på grund av ett fel eller otillräckliga systemresurser. Kontrollera att den senaste Azure File [Sync-agentversionen](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) är installerad. Om problemet kvarstår öppnar du en supportbegäran.

> [!Note]  
> Om servertillståndet på bladet för registrerade servrar är "Visas offline" utför du stegen som dokumenteras i [serverslutpunkten har en hälsostatus för "Ingen aktivitet" eller "Väntande" och servertillståndet på bladet registrerade servrar är avsnittet "Visas offline".](#server-endpoint-noactivity)

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Om jag har skapat en fil direkt i min Azure-filresurs via SMB eller via portalen, hur lång tid tar det för filen att synkroniseras med servrar i synkroniseringsgruppen?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Serverslutpunktshälsa är i väntande tillstånd i flera timmar**  
Det här problemet förväntas om du skapar en molnslutpunkt och använder en Azure-filresurs som innehåller data. Ändringsuppräkningsjobbet som söker efter ändringar i Azure-filresursen måste slutföras innan filer kan synkroniseras mellan molnet och serverns slutpunkter. Tiden för att slutföra jobbet är beroende av storleken på namnområdet i Azure-filresursen. Serverns slutpunktshälsa bör uppdateras när ändringsuppräkningsjobbet har slutförts.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Hur övervakar jag synkroniseringens status?
# <a name="portal"></a>[Portalen](#tab/portal1)
Inom varje synkroniseringsgrupp kan du öka detaljnivån i dess enskilda serverslutpunkter för att se statusen för de senaste slutförda synkroniseringssessionerna. En grön hälsokolumn och värdet För fil som inte synkroniseras på 0 anger att synkroniseringen fungerar som förväntat. Om så inte är fallet läser du nedan för en lista över vanliga synkroniseringsfel och hur du hanterar filer som inte synkroniseras. 

![En skärmbild av Azure-portalen](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
Gå till serverns telemetriloggar, som finns i Loggboken `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`på . Händelse 9102 motsvarar en slutförd synkroniseringssession. För den senaste statusen för synkronisering, leta efter den senaste händelsen med ID 9102. SyncDirection talar om för dig om det var en uppladdning eller nedladdning av den här sessionen. Om HResult är 0 lyckades synkroniseringssessionen. En HResult som inte är noll betyder att det uppstod ett fel under synkroniseringen. se nedan för en lista över vanliga fel. Om PerItemErrorCount är större än 0 betyder det att vissa filer eller mappar inte synkroniserades korrekt. Det är möjligt att ha en HResult på 0 men en PerItemErrorCount som är större än 0.

Nedan är ett exempel på en lyckad uppladdning. För enkelhetens skull anges endast några av de värden som finns i varje 9102-händelse nedan. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Omvänt kan en misslyckad uppladdning se ut så här:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Ibland misslyckas synkroniseringssessioner totalt sett eller har en peritemErrorCount som inte är noll men gör fortfarande framåt, med vissa filer synkroniserade. Detta kan ses i fälten Applied* (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes), som talar om hur mycket av sessionen som lyckas. Om du ser flera synkroniseringssessioner i en rad som misslyckas men har ett ökande tillämpat* antal, bör du ge synkroniseringstid att försöka igen innan du öppnar en supportbiljett.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hur övervakar jag förloppet för en pågående synkronisering?
# <a name="portal"></a>[Portalen](#tab/portal1)
I synkroniseringsgruppen går du till serverslutpunkten i fråga och tittar på avsnittet Synkroniseringsaktivitet för att se antalet filer som laddats upp eller hämtats i den aktuella synkroniseringssessionen. Observera att den här statusen kommer att fördröjas med cirka 5 minuter, och om synkroniseringssessionen är tillräckligt liten för att slutföras inom den här perioden kanske den inte rapporteras i portalen. 

# <a name="server"></a>[Server](#tab/server)
Titta på den senaste 9302-händelsen i telemetriloggen på servern (i Loggboken går du till Program- och tjänstloggar\Microsoft\FileSync\Agent\Telemetri). Den här händelsen anger tillståndet för den aktuella synkroniseringssessionen. TotalItemCount anger hur många filer som ska synkroniseras, AppliedItemCount antalet filer som har synkroniserats hittills, och PerItemErrorCount antalet filer som inte synkroniseras (se nedan för hur man hanterar detta).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Hur vet jag att servrarna är synkroniserade med varandra?
# <a name="portal"></a>[Portalen](#tab/portal1)
Kontrollera att du måste se till att
- Tidsstämplarna för den senaste synkroniseringen för både uppladdning och nedladdning är nya.
- Statusen är grön för både uppladdning och nedladdning.
- I fältet Synkroniseringsaktivitet visas mycket få eller inga filer kvar att synkronisera.
- Fältet Filer som inte synkroniseras är 0 för både uppladdning och nedladdning.

# <a name="server"></a>[Server](#tab/server)
Titta på de slutförda synkroniseringssessionerna, som markeras med 9102 händelser i telemetrihändelseloggen för varje server (i Loggboken går du till `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. På en viss server vill du se till att de senaste uppladdnings- och nedladdningssessionerna har slutförts. För att göra detta, kontrollera att HResult och PerItemErrorCount är 0 för både ladda upp och ladda ner (SyncDirection fältet anger om en viss session är en uppladdning eller nedladdning session). Observera att om du inte ser en nyligen slutförd synkroniseringssession är det troligt att en synkroniseringssession pågår, vilket kan förväntas om du just har lagt till eller ändrat en stor mängd data.
2. När en server är helt uppdaterad med molnet och inte har några ändringar att synkronisera i någon riktning visas tomma synkroniseringssessioner. Dessa indikeras av uppladdnings- och nedladdningshändelser där alla Sync*-fält (SyncFileCount, SyncDirCount, SyncTombstoneCount och SyncSizeBytes) är noll, vilket innebär att det inte fanns något att synkronisera. Observera att dessa tomma synkroniseringssessioner kanske inte sker på servrar med hög omsättning eftersom det alltid finns något nytt att synkronisera. Om det inte finns någon synkroniseringsaktivitet bör de inträffa var 30:e minut. 
3. Om alla servrar är uppdaterade med molnet, vilket innebär att deras senaste uppladdnings- och nedladdningssessioner är tomma synkroniseringssessioner, kan du med rimlig säkerhet säga att systemet som helhet är synkroniserat. 
    
Observera att om du har gjort ändringar direkt i din Azure-filresurs identifierar Azure File Sync inte den här ändringen förrän ändringens uppräkning körs, vilket sker en gång var 24:e timme. Det är möjligt att en server kommer att säga att det är uppdaterat med molnet när det i själva verket saknas de senaste ändringarna som gjorts direkt i Azure-filresursen. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hur gör jag för att se om det finns vissa filer eller mappar som inte synkroniseras?
Om din PerItemErrorCount på servern eller Antal filer som inte synkroniseras i portalen är större än 0 för en viss synkroniseringssession betyder det att vissa objekt inte synkroniseras. Filer och mappar kan ha egenskaper som hindrar dem från att synkroniseras. Dessa egenskaper kan vara beständiga och kräva explicita åtgärder för att återuppta synkroniseringen, till exempel ta bort tecken som inte stöds från filen eller mappnamnet. De kan också vara tillfälliga, vilket innebär att filen eller mappen automatiskt återupptar synkroniseringen. Filer med öppna referenser återupptas till exempel automatiskt synkronisering när filen stängs. När Azure File Sync-motorn upptäcker ett sådant problem skapas en fellogg som kan tolkas för att lista de objekt som för närvarande inte synkroniseras korrekt.

Om du vill se dessa fel kör du **PowerShell-skriptet FileSyncErrorsReport.ps1** (finns i agentinstallationskatalogen för Azure File Sync-agenten) för att identifiera filer som inte kunde synkroniseras på grund av öppna referenser, tecken som inte stöds eller andra problem. I fältet ItemPath visas platsen för filen i förhållande till rotsynkroniseringskatalogen. Se listan över vanliga synkroniseringsfel nedan för reparationssteg.

> [!Note]  
> Om fileSyncErrorsReport.ps1-skriptet returnerar "Det fanns inga filfel hittades" eller inte listar fel per objekt för synkroniseringsgruppen, är orsaken antingen:
>
>- Orsak 1: Den senast slutförda synkroniseringssessionen hade inga fel per objekt. Portalen bör uppdateras snart för att visa 0 filer som inte synkroniseras. 
>   - Kontrollera [händelse-ID 9102](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) i telemetrihändelseloggen för att bekräfta att PerItemErrorCount är 0. 
>
>- Orsak 2: Händelseloggen ItemResults på servern som radbryts på grund av för många fel per objekt och händelseloggen innehåller inte längre fel för den här synkroniseringsgruppen.
>   - För att förhindra det här problemet ökar du händelseloggstorleken ItemResults. Händelseloggen ItemResults finns under "Program- och tjänstloggar\Microsoft\FileSync\Agent" i Loggboken. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Felsöka synkroniseringsfel per fil/katalog
**ItemResults-logg - synkroniseringsfel per objekt**  

| Hresult | HRESULT (decimal) | Felsträng | Problem | Åtgärder |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Den nivåindelad filen på servern är inte tillgänglig. Det här problemet uppstår om den nivåindelade filen inte återkallades innan du tog bort en serverslutpunkt. | LÃ¶s problemet genom att se [Nivåindelade filer inte är tillgängliga på servern efter att en serverslutpunkt har tagits bort](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Det går inte att synkronisera fil- eller katalogändringen ännu eftersom en beroende mapp ännu inte är synkroniserad. Det här objektet synkroniseras när de beroende ändringarna har synkroniserats. | Ingen åtgärd krävs. Om felet kvarstår i flera dagar använder du Skriptet FileSyncErrorsReport.ps1 för att ta reda på varför den beroende mappen ännu inte är synkroniserad. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Det går inte att synkronisera fil- eller katalogändringen ännu eftersom en beroende mapp ännu inte är synkroniserad och synkroniseringssessionen misslyckades. Det här objektet synkroniseras när de beroende ändringarna har synkroniserats. | Ingen åtgärd krävs. Om felet kvarstår undersöker du synkroniseringssessionsfelet. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Filens eller katalognamnet är ogiltigt. | Byt namn på filen eller katalogen i fråga. Mer information [finns i Hantera tecken som inte stöds.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Filens eller katalognamnet är ogiltigt. | Byt namn på filen eller katalogen i fråga. Mer information [finns i Hantera tecken som inte stöds.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Det går inte att synkronisera filen eftersom den används. Filen synkroniseras när den inte längre används. | Ingen åtgärd krävs. Azure File Sync skapar en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Filen har ändrats, men ändringen har ännu inte upptäckts av synkronisering. Synkroniseringen återställs när den här ändringen har identifierats. | Ingen åtgärd krävs. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Filen togs bort och synkroniseringen är inte medveten om ändringen. | Ingen åtgärd krävs. Synkroniseringen slutar logga det här felet när ändringsidentifieringen identifierar filen har tagits bort. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Det går inte att synkronisera borttagning av en fil eller katalog eftersom objektet redan har tagits bort i målet och synkroniseringen inte är medveten om ändringen. | Ingen åtgärd krävs. Synkroniseringen slutar logga det här felet när ändringsidentifieringen körs på målet och synkroniseringen identifierar objektet har tagits bort. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Filen eller katalogen hoppades över men synkroniserades under nästa synkroniseringssession. Om det här felet rapporteras när objektet hämtas är fil- eller katalognamnet mer än sannolikt ogiltigt. | Ingen åtgärd krävs om det här felet rapporteras när filen överförs. Om felet rapporteras när filen hämtas byter du namn på filen eller katalogen i fråga. Mer information [finns i Hantera tecken som inte stöds.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Det går inte att synkronisera skapandet av en fil eller katalog eftersom objektet redan finns i målet och synkroniseringen inte är medveten om ändringen. | Ingen åtgärd krävs. Synkroniseringen slutar logga det här felet när ändringsidentifieringen körs på målet och synkroniseringen är medveten om det nya objektet. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Det går inte att synkronisera filen eftersom gränsen för Azure-filresursen har nåtts. | LÃ¶s problemet genom att [lÃ¤sa du nått](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) avsnittet azure file share storage limit i felsökningsguiden. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Filen krypteras av en lösning som inte stöds (som NTFS EFS). | Dekryptera filen och använd en krypteringslösning som stöds. En lista över lösningar som stöds finns i avsnittet [Krypteringslösningar](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption) i planeringsguiden. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Filen finns på en DFS-R-skrivskyddad replikeringsmapp. | Filen finns på en DFS-R-skrivskyddad replikeringsmapp. Azure Files Sync stöder inte serverslutpunkter i skrivskyddade DFS-R-replikeringsmappar. Mer information finns i [planeringsguiden.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Filen har ett väntande tillstånd för borttagning. | Ingen åtgärd krävs. Filen tas bort när alla öppna filreferenser har stängts. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Det går inte att synkronisera filen eftersom brandväggs- och virtuella nätverksinställningarna på lagringskontot är aktiverade och servern inte har åtkomst till lagringskontot. | Lägg till serverns IP-adress eller virtuella nätverk genom att följa stegen som dokumenteras i avsnittet [Konfigurera brandvägg och inställningar](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) för virtuella nätverk i distributionsguiden. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Det går inte att synkronisera filen eftersom säkerhetsbeskrivningsstorleken överskrider gränsen på 64 KiB. | Lös problemet genom att ta bort filens åtkomstkontrollposter så att säkerhetsbeskrivningen minskar i storlek. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Det går inte att synkronisera filen på grund av ett oväntat fel. | Om felet kvarstår i flera dagar öppnar du ett supportärende. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Det går inte att synkronisera filen eftersom den används. Filen synkroniseras när den inte längre används. | Ingen åtgärd krävs. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Filen ändrades under synkroniseringen, så den måste synkroniseras igen. | Ingen åtgärd krävs. |
| 0x80070017 | -2147024873 | ERROR_CRC | Det går inte att synkronisera filen på grund av CRC-fel. Det här felet kan uppstå om en nivåindelad fil inte återkallades innan en serverslutpunkt eller om filen är skadad. | LÃ¶s problemet genom att se [Nivåindelade filer inte är tillgängliga på servern efter att en serverslutpunkt har tagits bort](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) för att ta bort nivåindelade filer som är överblivna. Om felet kvarstår när oprhaned-nivåindelade filer har tagit bort dem kör du [chkdsk](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) på volymen. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Det går inte att synkronisera filen eftersom det maximala antalet konfliktfiler har nåtts. Azure File Sync stöder 100 konfliktfiler per fil. Mer information om filkonflikter finns i [vanliga frågor och svar om](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution)Azure File Sync . | Lös problemet genom att minska antalet konfliktfiler. Filen synkroniseras när antalet konfliktfiler är mindre än 100. |

#### <a name="handling-unsupported-characters"></a>Hantera tecken som inte stöds
Om **Skriptet FileSyncErrorsReport.ps1** visar fel på grund av tecken som inte stöds (felkod 0x8007007b eller 0x80c80255) bör du ta bort eller byta namn på tecknen som ska vara felaktiga från respektive filnamn. PowerShell kommer sannolikt att skriva ut dessa tecken som frågetecken eller tomma rektanglar eftersom de flesta av dessa tecken inte har någon standard visuell kodning. [Utvärderingsverktyget](storage-sync-files-planning.md#evaluation-cmdlet) kan användas för att identifiera tecken som inte stöds.

Tabellen nedan innehåller alla unicode-tecken som Azure File Sync ännu inte stöder.

| Teckenuppsättning | Antal tecken |
|---------------|-----------------|
| <ul><li>0x0000009D (osc operativsystem kommando)</li><li>0x00000090 (dcs enhetskontrollsträng)</li><li>0x0000008F (ss3 enda skift tre)</li><li>0x00000081 (hög oktett förinställd)</li><li>0x0000007F (del ta bort)</li><li>0x0000008D (ri omvänd linje matning)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (arabiska presentationsformulär-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (specials) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (icke-karaktärstecken)</li><li>0x0002FFFE - 0x0002FFFF = 2 (icke-karaktärstecken)</li><li>0x0003FFFE - 0x0003FFFF = 2 (icke-karaktärstecken)</li><li>0x0004FFFE - 0x0004FFFF = 2 (icke-karaktärstecken)</li><li>0x0005FFFE - 0x0005FFFF = 2 (icke-karaktärstecken)</li><li>0x0006FFFE - 0x0006FFFF = 2 (icke-karaktärstecken)</li><li>0x0007FFFE - 0x0007FFFF = 2 (icke-karaktärstecken)</li><li>0x0008FFFE - 0x0008FFFF = 2 (icke-karaktärstecken)</li><li>0x0009FFFE - 0x0009FFFF = 2 (icke-karaktärstecken)</li><li>0x000AFFFE - 0x000AFFFF = 2 (icke-karaktärstecken)</li><li>0x000BFFFE - 0x000BFFFF = 2 (icke-karaktärstecken)</li><li>0x000CFFFE - 0x000CFFFF = 2 (icke-karaktärstecken)</li><li>0x000DFFFE - 0x000DFFFF = 2 (icke-karaktärstecken)</li><li>0x000EFFFE - 0x000EFFFF = 2 (odefinierad)</li><li>0x000FFFFE - 0x000FFFFF = 2 (extra privat bruk)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Vanliga synkroniseringsfel
<a id="-2147023673"></a>**Synkroniseringssessionen avbröts.**  

| | |
|-|-|
| **Hresult** | 0x800704c7 |
| **HRESULT (decimal)** | -2147023673 | 
| **Felsträng** | ERROR_CANCELLED |
| **Reparation krävs** | Inga |

Synkroniseringssessioner kan misslyckas av olika skäl, inklusive servern som startas om eller uppdateras, VSS ögonblicksbilder, etc. Även om det här felet ser ut som det kräver uppföljning, är det säkert att ignorera det här felet om det inte kvarstår under en period av flera timmar.

<a id="-2147012889"></a>**Det gick inte att upprätta en anslutning till tjänsten.**    

| | |
|-|-|
| **Hresult** | 0x80072ee7 |
| **HRESULT (decimal)** | -2147012889 | 
| **Felsträng** | WININET_E_NAME_NOT_RESOLVED |
| **Reparation krävs** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Användarbegäran begränsades av tjänsten.**  

| | |
|-|-|
| **Hresult** | 0x80c8004c |
| **HRESULT (decimal)** | -2134376372 |
| **Felsträng** | ECS_E_USER_REQUEST_THROTTLED |
| **Reparation krävs** | Inga |

Ingen åtgärd krävs. servern kommer att försöka igen. Om felet kvarstår i flera timmar kan du skapa en supportförfrågan.

<a id="-2134364043"></a>**Synkroniseringen blockeras tills ändringsidentifieringen är klar efter återställning**  

| | |
|-|-|
| **Hresult** | 0x80c83075 |
| **HRESULT (decimal)** | -2134364043 |
| **Felsträng** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Reparation krävs** | Inga |

Ingen åtgärd krävs. När en fil- eller filresurs (molnslutpunkt) återställs med Azure Backup blockeras synkroniseringen tills ändringsidentifieringen är klar på Azure-filresursen. Den här identifieringen körs direkt när återställningen är färdig och hur lång tid det tar beror på antalet filer i filresursen.

<a id="-2147216747"></a>**Synkroniseringen misslyckades eftersom synkroniseringsdatabasen togs bort.**  

| | |
|-|-|
| **Hresult** | 0x80041295 |
| **HRESULT (decimal)** | -2147216747 |
| **Felsträng** | SYNC_E_METADATA_INVALID_OPERATION |
| **Reparation krävs** | Inga |

Det här felet uppstår vanligtvis när ett säkerhetskopieringsprogram skapar en VSS-ögonblicksbild och Sync-databasen tas bort från minnet. Om felet kvarstår i flera timmar kan du skapa en supportförfrågan.

<a id="-2134364065"></a>**Synkronisering kan inte komma åt Azure-filresursen som anges i molnslutpunkten.**  

| | |
|-|-|
| **Hresult** | 0x80c8305f |
| **HRESULT (decimal)** | -2134364065 |
| **Felsträng** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Reparation krävs** | Ja |

Det här felet beror på att Azure File Sync-agenten inte kommer åt Azure-filresursen, vilket kan bero på att Azure-filresursen eller lagringskontot som är värd inte längre finns. Du kan felsöka det här felet genom att utföra följande steg:

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att Azure-filresursen finns.](#troubleshoot-azure-file-share)
3. [Kontrollera att Azure File Sync har åtkomst till lagringskontot.](#troubleshoot-rbac)
4. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Synkroniseringen misslyckades eftersom begäran inte har behörighet att utföra den här åtgärden.**  

| | |
|-|-|
| **Hresult** | 0x80c86044 |
| **HRESULT (decimal)** | -2134351804 |
| **Felsträng** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Reparation krävs** | Ja |

Det här felet beror på att Azure File Sync-agenten inte har behörighet att komma åt Azure-filresursen. Du kan felsöka det här felet genom att utföra följande steg:

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att Azure-filresursen finns.](#troubleshoot-azure-file-share)
3. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Kontrollera att Azure File Sync har åtkomst till lagringskontot.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Det gick inte att matcha det lagringskontonamn som användes.**  

| | |
|-|-|
| **Hresult** | 0x80C83060 |
| **HRESULT (decimal)** | -2134364064 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Reparation krävs** | Ja |

1. Kontrollera att du kan matcha DNS-namnet för lagring från servern.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
3. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Ett okänt fel uppstod när lagringskontot skulle komma åt.**  

| | |
|-|-|
| **Hresult** | 0x80c8308a |
| **HRESULT (decimal)** | -2134364022 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Reparation krävs** | Ja |

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Synkroniseringen misslyckades på grund av att lagringskontot var låst.**  

| | |
|-|-|
| **Hresult** | 0x80c83092 |
| **HRESULT (decimal)** | -2134364014 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Reparation krävs** | Ja |

Det här felet beror på att lagringskontot har ett skrivskyddat [resurslås](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources). Lös problemet genom att ta bort det skrivskyddade resurslåset på lagringskontot. 

<a id="-1906441138"></a>**Synkroniseringen misslyckades på grund av ett problem med synkroniseringsdatabasen.**  

| | |
|-|-|
| **Hresult** | 0x8e5e044e |
| **HRESULT (decimal)** | -1906441138 |
| **Felsträng** | JET_errWriteConflict |
| **Reparation krävs** | Ja |

Det här felet uppstår när det finns ett problem med den interna databasen som används av Azure File Sync. När det här problemet uppstår skapar du en supportbegäran så kontaktar vi dig för att lösa problemet.

<a id="-2134364053"></a>**Azure File Sync agent-versionen som är installerad på servern stöds inte.**  

| | |
|-|-|
| **Hresult** | 0x80C8306B |
| **HRESULT (decimal)** | -2134364053 |
| **Felsträng** | ECS_E_AGENT_VERSION_BLOCKED |
| **Reparation krävs** | Ja |

Det här felet inträffar om den Azure File Sync-agentversion som är installerad på servern inte stöds. LÃ¶s problemet genom att [uppgradera]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) till en [agentversion som stöds]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Du har nått lagringsgränsen för Azure-filresurs.**  

| | |
|-|-|
| **Hresult** | 0x80c8603e |
| **HRESULT (decimal)** | -2134351810 |
| **Felsträng** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Reparation krävs** | Ja |

Det här felet uppstår när du överskrider lagringsgränsen för Azure-filresursen, och det kan hända om en kvot används för en Azure-filresurs eller om användningen överskrider gränsen för en Azure-filresurs. Mer information finns i de [aktuella gränserna för en Azure-filresurs](storage-files-scale-targets.md).

1. Navigera till synkroniseringsgruppen i tjänsten Lagringssynkronisering.
2. Välj molnslutpunkten i synkroniseringsgruppen.
3. Observera Azure-filresursnamnet i det öppnade fönstret.
4. Välj det länkade lagringskontot. Om den här länken misslyckas har det refererade lagringskontot tagits bort.

    ![En skärmbild som visar detaljfönstret för slutpunkt i molnet med en länk till lagringskontot.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Välj **Filer** om du vill visa listan över filresurser.
6. Klicka på de tre punkterna i slutet av raden för Azure-filresursen som refereras av molnslutpunkten.
7. Kontrollera att **Användning** är lägre än **Kvot**. Om inte en alternativ kvot har angetts matchar kvoten den [maximala storleken på Azure-filresursen](storage-files-scale-targets.md).

    ![En skärmbild av egenskaperna för Azure-filresurs.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Om resursen är full och ingen kvot har angetts kan du åtgärda problemet genom att göra varje undermapp vid den aktuella serverslutpunkten till en egen serverslutpunkt i en egen separat synkroniseringsgrupp. På så vis synkroniseras varje undermapp till enskilda Azure-filresurser.

<a id="-2134351824"></a>**Det går inte att hitta Azure-filresursen.**  

| | |
|-|-|
| **Hresult** | 0x80c86030 |
| **HRESULT (decimal)** | -2134351824 |
| **Felsträng** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Reparation krävs** | Ja |

Felet uppstår när Azure-filresursen inte är tillgänglig. Så här felsöker du:

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att Azure-filresursen finns.](#troubleshoot-azure-file-share)

Om Azure-filresursen togs bort måste du skapa en ny filresurs och sedan återskapa synkroniseringsgruppen. 

<a id="-2134364042"></a>**Synkroniseringen pausas medan den här Azure-prenumerationen är avstängd.**  

| | |
|-|-|
| **Hresult** | 0x80C83076 |
| **HRESULT (decimal)** | -2134364042 |
| **Felsträng** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Reparation krävs** | Ja |

Det här felet uppstår vid uppehåll i Azure-prenumerationen. Synkroniseringen aktiveras igen när Azure-prenumerationen återställs. Se [Varför är min Azure-prenumeration inaktiverad och hur återaktiverar jag den?](../../cost-management-billing/manage/subscription-disabled.md)

<a id="-2134364052"></a>**Lagringskontot har en brandvägg eller virtuella nätverk konfigurerade.**  

| | |
|-|-|
| **Hresult** | 0x80c8306c |
| **HRESULT (decimal)** | -2134364052 |
| **Felsträng** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Reparation krävs** | Ja |

Felet uppstår när det inte går att nå Azure-filresursen på grund av en brandvägg för lagringskontot eller om lagringskontot tillhör ett virtuellt nätverk. Kontrollera att brandväggen och inställningarna för virtuella nätverk på lagringskontot är korrekt konfigurerade. Mer information finns i [Konfigurera brandväggs- och inställningar för virtuella nätverk](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Synkroniseringen misslyckades på grund av ett problem med synkroniseringsdatabasen.**  

| | |
|-|-|
| **Hresult** | 0x80c80219 |
| **HRESULT (decimal)** | -2134375911 |
| **Felsträng** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Reparation krävs** | Inga |

Felet brukar lösas av sig självt och inträffa om det finns:

* Ett stort antal filändringar på servrarna i synkroniseringsgruppen.
* Ett stort antal fel på enskilda filer och kataloger.

Om det här felet kvarstår längre än några timmar skapar du en supportbegäran så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2146762487"></a>**Servern kunde inte upprätta en säker anslutning. Molntjänsten fick ett oväntat certifikat.**  

| | |
|-|-|
| **Hresult** | 0x800b0109 |
| **HRESULT (decimal)** | -2146762487 |
| **Felsträng** | CERT_E_UNTRUSTEDROOT |
| **Reparation krävs** | Ja |

Det här felet kan inträffa om din organisation använder en TLS-avslutande proxy eller om en skadlig enhet fångar upp trafiken mellan servern och Azure File Sync-tjänsten. Om du är säker på att detta är förväntat (eftersom din organisation använder en TLS-avslutande proxy) hoppar du över certifikatverifiering med en registersidosättning.

1. Skapa registervärdet SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Starta om synkroniseringstjänsten på den registrerade servern.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Genom att ange det här registervärdet accepterar Azure File Sync-agenten alla lokalt betrodda TLS/SSL-certifikat när data överförs mellan servern och molntjänsten.

<a id="-2147012894"></a>**Det gick inte att upprätta en anslutning till tjänsten.**  

| | |
|-|-|
| **Hresult** | 0x80072ee2 |
| **HRESULT (decimal)** | -2147012894 |
| **Felsträng** | WININET_E_TIMEOUT |
| **Reparation krävs** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synkroniseringen misslyckades på grund av ett problem med autentisering.**  

| | |
|-|-|
| **Hresult** | 0x80c80300 |
| **HRESULT (decimal)** | -2134375680 |
| **Felsträng** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Reparation krävs** | Ja |

Det här felet uppstår vanligtvis på grund av att servertiden är fel. Om servern körs på en virtuell dator kontrollerar du att tiden på värden är korrekt.

<a id="-2134364040"></a>**Synkroniseringen misslyckades på grund av certifikatets förfallodatum.**  

| | |
|-|-|
| **Hresult** | 0x80c83078 |
| **HRESULT (decimal)** | -2134364040 |
| **Felsträng** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Reparation krävs** | Ja |

Det här felet beror på att det certifikat som används för autentisering har upphört att gälla.

Bekräfta att certifikatet har upphört med hjälp av följande steg:  
1. Öppna MMC-snapin-modulen Certifikat, välj Datorkonto och navigera till Certifikat (lokal dator)\Personal\Certifikat.
2. Kontrollera om klientautentiseringscertifikatet har upphört att gälla.

Om certifikatet för klientautentisering har upphört att gälla löser du problemet genom att utföra följande steg:

1. Verifiera Azure File Sync agent version 4.0.1.0 eller senare är installerad.
2. Kör följande PowerShell-kommando på servern: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Synkroniseringen misslyckades på grund av att det inte gick att hitta autentiseringscertifikatet.**  

| | |
|-|-|
| **Hresult** | 0x80c80228 |
| **HRESULT (decimal)** | -2134375896 |
| **Felsträng** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Reparation krävs** | Ja |

Det här felet beror på att det inte går att hitta certifikatet som används för autentisering.

Åtgärda problemet genom att följa dessa steg:

1. Verifiera Azure File Sync agent version 4.0.1.0 eller senare är installerad.
2. Kör följande PowerShell-kommando på servern: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Synkroniseringen misslyckades på grund av att autentiseringsidentiteten inte hittades.**  

| | |
|-|-|
| **Hresult** | 0x80c83079 |
| **HRESULT (decimal)** | -2134364039 |
| **Felsträng** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Reparation krävs** | Ja |

Felet beror på att borttagningen av serverslutpunkten misslyckades och att slutpunkten nu är i ett delvis borttaget tillstånd. Lös problemet genom att försöka ta bort serverslutpunkten.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Volymen där serverslutpunkten finns har ont om diskutrymme.**  

| | |
|-|-|
| **Hresult** | 0x8e5e0211 |
| **HRESULT (decimal)** | -1906441711 |
| **Felsträng** | JET_errLogDiskFull |
| **Reparation krävs** | Ja |
| | |
| **Hresult** | 0x80c8031a |
| **HRESULT (decimal)** | -2134375654 |
| **Felsträng** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Reparation krävs** | Ja |

Det här felet beror på att volymen är full. Felet uppstår vanligtvis när filer utanför serverslutpunkten använder utrymme på volymen. Frigör utrymme på volymen genom att lägga till ytterligare serverslutpunkter, flytta filer till en annan volym eller öka storleken på volymen som serverslutpunkten är på.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Tjänsten är ännu inte klar att synkroniseras med den här serverslutpunkten.**  

| | |
|-|-|
| **Hresult** | 0x80c8300f |
| **HRESULT (decimal)** | -2134364145 |
| **Felsträng** | ECS_E_REPLICA_NOT_READY |
| **Reparation krävs** | Inga |

Det här felet beror på att molnslutpunkten skapades med innehåll som redan finns på Azure-filresursen. Azure File Sync måste söka igenom Azure-filresursen för allt innehåll innan serverslutpunkten kan fortsätta med den första synkroniseringen.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synkroniseringen misslyckades på grund av problem med många enskilda filer.**  

| | |
|-|-|
| **Hresult** | 0x80c8023b |
| **HRESULT (decimal)** | -2134375877 |
| **Felsträng** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Reparation krävs** | Ja |
| | |
| **Hresult** | 0x80c8021c |
| **HRESULT (decimal)** | -2134375908 |
| **Felsträng** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Reparation krävs** | Ja |
| | |
| **Hresult** | 0x80c80253 |
| **HRESULT (decimal)** | -2134375853 |
| **Felsträng** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Reparation krävs** | Ja |

I de fall där det finns många synkroniseringsfel per fil kan synkroniseringssessioner börja misslyckas. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure File Sync skapar en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser.

<a id="-2134376423"></a>**Synkroniseringen misslyckades på grund av ett problem med serverns slutpunktssökväg.**  

| | |
|-|-|
| **Hresult** | 0x80c80019 |
| **HRESULT (decimal)** | -2134376423 |
| **Felsträng** | ECS_E_SYNC_INVALID_PATH |
| **Reparation krävs** | Ja |

Kontrollera att sökvägen finns, finns på en lokal NTFS-volym och är inte en referenspunkt eller befintlig serverslutpunkt.

<a id="-2134375817"></a>**Synkroniseringen misslyckades eftersom filterdrivrutinsversionen inte är kompatibel med agentversionen**  

| | |
|-|-|
| **Hresult** | 0x80C80277 |
| **HRESULT (decimal)** | -2134375817 |
| **Felsträng** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Reparation krävs** | Ja |

Det här felet beror på att den version av filterdrivrutinen för molnnivåindelning (StorageSync.sys) som lästs in inte är kompatibel med Storage Sync Agent-tjänsten (FileSyncSvc). Om Azure File Sync-agenten har uppgraderats startar du om servern för att slutföra installationen. Om felet fortsätter att inträffa avinstallerar du agenten, startar om servern och installerar om Azure File Sync-agenten.

<a id="-2134376373"></a>**Tjänsten är inte tillgänglig för tillfället.**  

| | |
|-|-|
| **Hresult** | 0x80c8004b |
| **HRESULT (decimal)** | -2134376373 |
| **Felsträng** | ECS_E_SERVICE_UNAVAILABLE |
| **Reparation krävs** | Inga |

Felet beror på att tjänsten Azure File Sync-tjänsten inte är tillgänglig. Det här felet löses automatiskt när Azure File Sync-tjänsten är tillgänglig igen.

<a id="-2146233088"></a>**Synkroniseringen misslyckades på grund av ett undantag.**  

| | |
|-|-|
| **Hresult** | 0x80131500 |
| **HRESULT (decimal)** | -2146233088 |
| **Felsträng** | COR_E_EXCEPTION |
| **Reparation krävs** | Inga |

Felet beror på att synkroniseringen misslyckades på grund av ett undantag. Om felet kvarstår i flera timmar skapar du en supportbegäran.

<a id="-2134364045"></a>**Synkroniseringen misslyckades eftersom lagringskontot har misslyckats med en annan region.**  

| | |
|-|-|
| **Hresult** | 0x80c83073 |
| **HRESULT (decimal)** | -2134364045 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Reparation krävs** | Ja |

Felet beror på att lagringskontot har redundansväxlat till en annan region. Azure File Sync har inte stöd för redundansväxling av lagringskonton. Lagringskonton med Azure-filresurser som används som molnslutpunkter i Azure File Sync får inte redundansväxlas. Om du gör det slutar synkroniseringen att fungera och det kan leda till oväntade dataförluster för nyligen nivåbaserade filer. Lös problemet genom att flytta lagringskontot till den primära regionen.

<a id="-2134375922"></a>**Synkroniseringen misslyckades på grund av ett tillfälligt problem med synkroniseringsdatabasen.**  

| | |
|-|-|
| **Hresult** | 0x80c8020e |
| **HRESULT (decimal)** | -2134375922 |
| **Felsträng** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Reparation krävs** | Inga |

Det här felet uppstår på grund av ett internt problem med Sync-databasen. Det här felet löses automatiskt när Sync försöker igen. Om det här felet fortsätter under en längre tid skapar du en supportbegäran så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2134364024"></a>**Synkroniseringen misslyckades på grund av ändring i Azure Active Directory-klient**  

| | |
|-|-|
| **Hresult** | 0x80c83088 |
| **HRESULT (decimal)** | -2134364024 | 
| **Felsträng** | ECS_E_INVALID_AAD_TENANT |
| **Reparation krävs** | Ja |

Det här felet beror på att Azure File Sync för närvarande saknar stöd för att flytta prenumerationen till en annan klientorganisation i Azure Active Directory.
 
Gör så här för att åtgärda problemet:

- **Alternativ 1 (rekommenderas):** Flytta tillbaka prenumerationen till den ursprungliga Azure Active Directory-klienten
- **Alternativ 2**: Ta bort och återskapa den aktuella synkroniseringsgruppen. Om molnnivåindelning är aktiverad på serverslutpunkten tar du bort synkroniseringsgruppen och utför sedan stegen som beskrivs i avsnittet [Molnnivåindelning]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) för att ta bort de överblivna filerna innan du skapar synkroniseringsgrupperna igen. 

<a id="-2134364010"></a>**Synkroniseringen misslyckades på grund av att brandväggen och det virtuella nätverksundantaget inte har konfigurerats**  

| | |
|-|-|
| **Hresult** | 0x80c83096 |
| **HRESULT (decimal)** | -2134364010 | 
| **Felsträng** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Reparation krävs** | Ja |

Det här felet uppstår om brandväggen och inställningarna för virtuella nätverk är aktiverade på lagringskontot och undantaget "Tillåt betrodda Microsoft-tjänster att komma åt det här lagringskontot" är inte markerat. Lös problemet genom att följa de steg som beskrivs i avsnittet [Konfigurera inställningar för brandvägg och virtuellt nätverk](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) i distributionsguiden.

<a id="-2147024891"></a>**Synkroniseringen misslyckades eftersom behörigheterna för mappen Systemvolyminformation är felaktiga.**  

| | |
|-|-|
| **Hresult** | 0x80070005 |
| **HRESULT (decimal)** | -2147024891 |
| **Felsträng** | ERROR_ACCESS_DENIED |
| **Reparation krävs** | Ja |

Det här felet kan inträffa om kontot NT AUTHORITY\SYSTEM inte har behörighet till mappen System Volume Information på den volym där serverslutpunkten finns. Om enskilda filer inte synkroniseras med ERROR_ACCESS_DENIED utför du stegen som dokumenteras i avsnittet [Felsöka per fil/katalogsynkronisering.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Åtgärda problemet genom att följa dessa steg:

1. Ladda ned verktyget [Psexec](https://docs.microsoft.com/sysinternals/downloads/psexec).
2. Kör följande kommando från en upphöjd kommandotolk för att starta en kommandotolk med hjälp av systemkontot: **PsExec.exe -i -s -d cmd** 
3. Kör den kommandotolk som körs under systemkontot kör du följande kommando för att bekräfta att kontot NT AUTHORITY\SYSTEM inte har åtkomst till mappen System Volume Information: **cacls "partitionsbokstav:\system volume information" /T /C**
4. Om kontot NT AUTHORITY\SYSTEM inte har åtkomst till mappen System Volume Information kör du följande kommando: **cacls  "partitionsbokstav:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Om steg 4 misslyckas med nekad åtkomst kör du följande kommando för att bli ägare till mappen System Volume Information och upprepar sedan steg 4: **takeown /A /R /F "partitionsbokstav:\System Volume Information"**

<a id="-2134375810"></a>**Synkroniseringen misslyckades eftersom Azure-filresursen togs bort och återskapades.**  

| | |
|-|-|
| **Hresult** | 0x80c8027e |
| **HRESULT (decimal)** | -2134375810 |
| **Felsträng** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Reparation krävs** | Ja |

Felet beror på att Azure File Sync inte har stöd för borttagning och återskapande av en Azure-filresurs i samma synkroniseringsgrupp. 

Lös problemet genom att ta bort och återskapa synkroniseringsgruppen med hjälp av följande steg:

1. Ta bort alla serverslutpunkter i synkroniseringsgruppen.
2. Ta bort molnslutpunkten. 
3. Ta bort synkroniseringsgruppen.
4. Om molnnivådelning har aktiverats på en serverslutpunkt tar du bort de överblivna nivåindelade filerna på servern genom att utföra de steg som dokumenteras i [de nivåindelade filerna inte är tillgängliga på servern när du har tagit bort ett serverslutpunktsavsnitt.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)
5. Återskapa synkroniseringsgruppen.

<a id="-2145844941"></a>**Synkroniseringen misslyckades eftersom HTTP-begäran omdirigerades**  

| | |
|-|-|
| **Hresult** | 0x80190133 |
| **HRESULT (decimal)** | -2145844941 |
| **Felsträng** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Reparation krävs** | Ja |

Det här felet beror på att Azure File Sync inte stöder HTTP-omdirigering (3xx-statuskod). LÃ¶s problemet genom att inaktivera HTTP-omdirigering på proxyservern eller nätverksenheten.

<a id="-2134364027"></a>**En timeout inträffade under dataöverföring offline, men den pågår fortfarande.**  

| | |
|-|-|
| **Hresult** | 0x80c83085 |
| **HRESULT (decimal)** | -2134364027 |
| **Felsträng** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Reparation krävs** | Inga |

Det här felet uppstår när en datainmatningsåtgärd överskrider tidsgränsen. Det här felet kan ignoreras om synkroniseringen gör framsteg (AppliedItemCount är större än 0). Se [Hur övervakar jag förloppet för en aktuell synkroniseringssession?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

### <a name="common-troubleshooting-steps"></a>Vanliga felsökningssteg
<a id="troubleshoot-storage-account"></a>**Kontrollera att lagringskontot finns.**  
# <a name="portal"></a>[Portalen](#tab/azure-portal)
1. Navigera till synkroniseringsgruppen i tjänsten Lagringssynkronisering.
2. Välj molnslutpunkten i synkroniseringsgruppen.
3. Observera Azure-filresursnamnet i det öppnade fönstret.
4. Välj det länkade lagringskontot. Om den här länken misslyckas har det refererade lagringskontot tagits bort.
    ![En skärmbild som visar detaljfönstret för slutpunkt i molnet med en länk till lagringskontot.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Kontrollera att Azure-filresursen finns.**  
# <a name="portal"></a>[Portalen](#tab/azure-portal)
1. Klicka på **Översikt** i den vänstra innehållsförteckningen för att återgå till huvudkontosidan för lagring.
2. Välj **Filer** om du vill visa listan över filresurser.
3. Kontrollera att filresursen som refereras av molnslutpunkten visas i listan över filresurser (du bör ha noterat detta i steg 1 ovan).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Kontrollera att Azure File Sync har åtkomst till lagringskontot.**  
# <a name="portal"></a>[Portalen](#tab/azure-portal)
1. Klicka på **Åtkomstkontroll (IAM)** i den vänstra innehållsförteckningen.
1. Klicka på fliken **Rolltilldelningar** i listan de användare och program (*tjänstens huvudnamn*) som har åtkomst till ditt lagringskonto.
1. Kontrollera att **Microsoft.StorageSync** eller **Hybrid File Sync Service** (gammalt programnamn) visas i listan med rollen **Läsar- och dataåtkomst.** 

    ![En skärmbild av tjänsten Hybrid File Sync Service-tjänstens huvudnamn på fliken Åtkomstkontroll på lagringskontot](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Om Synkroniseringstjänsten för **Microsoft.StorageSync** eller **Hybrid File** inte visas i listan gör du följande:

    - Klicka på **Lägg till**.
    - Välj **Läsar- och dataåtkomst**i fältet **Roll** .
    - Skriv **Microsoft.StorageSync**i fältet **Välj** , välj rollen och klicka på **Spara**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Hur förhindrar jag att användare skapar filer som innehåller tecken som inte stöds på servern?
Du kan använda [FSRM-filskärmar (File Server Resource Manager)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) för att blockera filer med tecken som inte stöds i deras namn från att skapas på servern. Du kan behöva göra detta med PowerShell eftersom de flesta av de tecken som inte stöds inte kan skrivas ut och därför måste du casta sina hexadecimala representationer som tecken först.

Skapa först en FSRM-filgrupp med [cmdleten New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). I det här exemplet definieras gruppen så att den bara innehåller två av tecknen som inte stöds, men du kan inkludera så många tecken som behövs i filgruppen.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

När du har definierat en FSRM-filgrupp kan du skapa en FSRM-filskärm med cmdleten New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Observera att filgallringar endast bör användas för att blockera skapandet av tecken som inte stöds av Azure File Sync. Om filgallringar används i andra scenarier försöker synkroniseringen kontinuerligt hämta filerna från Azure-filresursen till servern och blockeras på grund av filskärmen, vilket resulterar i hög datautgång. 

## <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Det finns två sökvägar för fel i molnnivådelning:

- Filer kan misslyckas med att nivå, vilket innebär att Azure File Sync utan framgång försöker nivå en fil till Azure-filer.
- Filer kan inte komma ihåg, vilket innebär att filsystemet Azure File Sync (StorageSync.sys) inte kan hämta data när en användare försöker komma åt en fil som har nivåats.

Det finns två huvudklasser av fel som kan inträffa via någon av felsökvägen:

- Molnlagringsfel
    - *Tillgänglighetsproblem för tillfälliga lagringstjänster*. Mer information finns i [servicenivåavtalet (SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Otillgänglig Azure-filresurs*. Det här felet inträffar vanligtvis när du tar bort Azure-filresursen när det fortfarande är en molnslutpunkt i en synkroniseringsgrupp.
    - *Oåtkomligt lagringskonto*. Det här felet inträffar vanligtvis när du tar bort lagringskontot medan det fortfarande har en Azure-filresurs som är en molnslutpunkt i en synkroniseringsgrupp. 
- Serverfel 
  - *Filsystemfilfilter för Azure File Sync (StorageSync.sys) har inte lästs in*. För att kunna svara på begäranden om nivåindelning/återkallande måste filsystemet Azure File Sync läsas in. Filtret som inte läses in kan ske av flera skäl, men den vanligaste orsaken är att en administratör lossade det manuellt. Filsystemet Azure File Sync måste alltid läsas in för att Azure File Sync ska fungera korrekt.
  - *Saknad, skadad eller på annat sätt bruten förarpunkt*. En reparse punkt är en särskild datastruktur på en fil som består av två delar:
    1. En reparse-tagg, som anger för operativsystemet att filsystemet Azure File Sync (StorageSync.sys) kan behöva utföra vissa åtgärder på IO till filen. 
    2. Reparse data, som anger att filsystemet filtrerar URI för filen på den associerade molnslutpunkten (Azure-filresursen). 
        
       Det vanligaste sättet en reparse punkt kan bli skadad är om en administratör försöker ändra antingen taggen eller dess data. 
  - *Problem med nätverksanslutningen*. För att nivå eller återkalla en fil måste servern ha internetanslutning.

I följande avsnitt beskrivs hur du felsöker problem med molnnivådelning och avgör om ett problem är ett problem med molnlagring eller ett serverproblem.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Övervaka nivåindelning på en server  
Om du vill övervaka nivåindelningsaktivitet på en server använder du Händelse-ID 9003, 9016 och 9029 i händelseloggen för Telemetri (finns under Program och tjänster\Microsoft\FileSync\Agent i Loggboken).

- Händelse-ID 9003 ger feldistribution för en serverslutpunkt. Till exempel totalt antal fel, ErrorCode, etc. Observera att en händelse loggas per felkod.
- Händelse-ID 9016 ger spökresultat för en volym. Till exempel, Ledigt utrymme procent är, Antal filer spökat i session, Antal filer misslyckades att spöke, etc.
- Händelse-ID 9029 innehåller spöksessionsinformation för en serverslutpunkt. Antal filer som du försöker göra i sessionen, Antal filer som nivåindelats i sessionen, Antal filer som redan har nivåindelats osv.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Övervaka återkallning på en server
Om du vill övervaka återkallningsaktivitet på en server använder du Händelse-ID 9005, 9006, 9009 och 9059 i händelseloggen för telemetri (finns under Program och tjänster\Microsoft\FileSync\Agent i Loggboken).

- Händelse-ID 9005 ger återkallningstillförlitlighet för en serverslutpunkt. Till exempel totalt antal unika filer som används, Totalt unika filer med misslyckad åtkomst, etc.
- Händelse-ID 9006 ger distribution av återkallningsfel för en serverslutpunkt. Till exempel totalt antal misslyckade begäranden, ErrorCode, etc. Observera att en händelse loggas per felkod.
- Händelse-ID 9009 innehåller information om återkallningssession för en serverslutpunkt. Till exempel DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
- Händelse-ID 9059 ger programåterkallningsdistribution för en serverslutpunkt. ShareId, Programnamn och TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Felsöka filer som inte kan nivå
Om filer inte kan nivå till Azure-filer:

1. I Loggboken granskar du de telemetri-, drifts- och diagnostikhändelseloggar som finns under Applications and Services\Microsoft\FileSync\Agent. 
   1. Kontrollera att filerna finns i Azure-filresursen.

      > [!NOTE]
      > En fil måste synkroniseras till en Azure-filresurs innan den kan nivåindelad.

   2. Kontrollera att servern har internetanslutning. 
   3. Verifiera att filtrarna för Azure File Sync (StorageSync.sys och StorageSyncGuard.sys) körs:
       - Kör i en upphöjd `fltmc`kommandotolk . Kontrollera att filfilterdrivrutinerna StorageSync.sys och StorageSyncGuard.sys visas.

> [!NOTE]
> Ett händelse-ID 9003 loggas en gång i timmen i telemetrihändelseloggen om en fil inte kan nivå (en händelse loggas per felkod). Kontrollera avsnittet [Nivåindelningsfel och reparation](#tiering-errors-and-remediation) för att se om reparationssteg visas för felkoden.

### <a name="tiering-errors-and-remediation"></a>Nivåindelar fel och reparation

| Hresult | HRESULT (decimal) | Felsträng | Problem | Åtgärder |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Filen kunde inte nivå eftersom den används. | Ingen åtgärd krävs. Filen kommer att nivåindelad när den inte längre används. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Filen kunde inte nivå eftersom den är utesluten av synkronisering. | Ingen åtgärd krävs. Det går inte att nivåindelad filer i undantagslistan för synkronisering. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Det gick inte att nivåvisa filen eftersom den inte hittades på servern. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du om filen finns på servern. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Filen kunde inte nivå eftersom den togs bort i Azure-filresursen. | Ingen åtgärd krävs. Filen ska tas bort på servern när nästa synkroniseringssession för hämtning körs. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Filen kunde inte nivå på grund av ett nätverksproblem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Filen kunde inte nivå på grund av ett nätverksproblem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Filen kunde inte nivå på grund av åtkomst nekade fel. Det här felet kan uppstå om filen finns på en DFS-R-skrivskyddad replikeringsmapp. | Azure Files Sync stöder inte serverslutpunkter i skrivskyddade DFS-R-replikeringsmappar. Mer information finns i [planeringsguiden.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Filen kunde inte nivå på grund av ett nätverksproblem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Filen kunde inte nivå eftersom filstorleken är mindre än den storlek som stöds. | Om agentversionen är mindre än 9,0 är den minsta filstorleken som stöds 64 kb. Om agentversionen är 9.0 och nyare baseras den minsta filstorlek som stöds på filsystemets klusterstorlek (dubbelt filsystemklusterstorlek). Om filsystemets klusterstorlek till exempel är 4kb är den minsta filstorleken 8kb. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Filen kunde inte nivå på grund av ett Azure-lagringsproblem. | Om felet kvarstår öppnar du en supportbegäran. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Filen kunde inte nivå eftersom den återkallades samtidigt. | Ingen åtgärd krävs. Filen kommer att nivåindelad när återkallelsen är klar och filen inte längre används. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Filen kunde inte nivå eftersom den inte har synkroniserats till Azure-filresursen. | Ingen åtgärd krävs. Filen kommer att nivå när den har synkroniserats till Azure-filresursen. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Filen kunde inte nivå eftersom molnnivåfiltningsdrivrutinen (storagesync.sys) inte körs. | LÃ¶s problemet genom att lÃ¶sa en upphöjd kommandotolk och lÃ¶sa följande:`fltmc load storagesync`<br>Om storagesync-filterdrivrutinen inte kan läsas in när fltmc-kommandot körs avinstallerar du Azure File Sync-agenten, startar om servern och installerar om Azure File Sync-agenten. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Filen kunde inte nivå på grund av otillräckligt diskutrymme på volymen där serverns slutpunkt finns. | LÃ¶s problemet genom att frigöra minst 100 MB diskutrymme på volymen där serverslutpunkten finns. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Filen kunde inte nivå eftersom den inte har synkroniserats till Azure-filresursen. | Ingen åtgärd krävs. Filen kommer att nivå när den har synkroniserats till Azure-filresursen. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Filen kunde inte nivå eftersom det är en reparse-punkt som inte stöds. | Om filen är en reparspunkt för datadyplikation följer du stegen i [planeringsguiden](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication) för att aktivera stöd för datadyplikation. Filer med andra reparse-punkter än datadedioxidation stöds inte och kommer inte att nivåieras.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Filen kunde inte nivå eftersom den har ändrats. | Ingen åtgärd krävs. Filen kommer att nivå när den ändrade filen har synkroniserats till Azure-filresursen. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Filen kunde inte nivå eftersom den inte har synkroniserats till Azure-filresursen. | Ingen åtgärd krävs. Filen kommer att nivå när den har synkroniserats till Azure-filresursen. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | Filen kunde inte nivå på grund av ett nätverksproblem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Filen kunde inte nivå eftersom den har ändrats. | Ingen åtgärd krävs. Filen kommer att nivå när den ändrade filen har synkroniserats till Azure-filresursen. |
| 0x800705aaa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Filen kunde inte nivå på grund av otillräckliga systemresurser. | Om felet kvarstår undersöker du vilken drivrutin för program eller kernel-läge som uttömmer systemresurser. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Felsöka filer som inte kan återkallas  
Om filer inte återkallas:
1. I Loggboken granskar du de telemetri-, drifts- och diagnostikhändelseloggar som finns under Applications and Services\Microsoft\FileSync\Agent.
    1. Kontrollera att filerna finns i Azure-filresursen.
    2. Kontrollera att servern har internetanslutning. 
    3. Öppna snapin-modulen Tjänster MMC och kontrollera att tjänsten Storage Sync Agent (FileSyncSvc) körs.
    4. Verifiera att filtrarna för Azure File Sync (StorageSync.sys och StorageSyncGuard.sys) körs:
        - Kör i en upphöjd `fltmc`kommandotolk . Kontrollera att filfilterdrivrutinerna StorageSync.sys och StorageSyncGuard.sys visas.

> [!NOTE]
> Ett händelse-ID 9006 loggas en gång per timme i telemetrihändelseloggen om en fil inte återkallas (en händelse loggas per felkod). Kontrollera avsnittet [Återkalla fel och reparation](#recall-errors-and-remediation) för att se om reparationssteg anges för felkoden.

### <a name="recall-errors-and-remediation"></a>Återkalla fel och reparation

| Hresult | HRESULT (decimal) | Felsträng | Problem | Åtgärder |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Det gick inte att återkalla filen på grund av en I/O-timeout. Det här problemet kan uppstå av flera skäl: begränsningar för serverresurser, dålig nätverksanslutning eller ett Azure-lagringsproblem (t.ex. begränsning). | Ingen åtgärd krävs. Om felet kvarstår i flera timmar öppnar du ett supportärende. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Filen kunde inte återkallas på grund av ett nätverksproblem.  | Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Det gick inte att återkalla filen eftersom serverslutpunkten togs bort. | LÃ¶s problemet genom att se [Nivåindelade filer inte är tillgängliga på servern efter att en serverslutpunkt har tagits bort](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Det gick inte att återkalla filen på grund av ett nekat fel. Det hÃ¤r problemet kan uppstÃ¥ om brandväggs- och virtuella nätverksinställningarna fÃ¥r i lagringskontot är aktiverade och servern inte har åtkomst till lagringskontot. | LÃ¶s problemet genom att lÃ¤gt server-IP-adressen eller det virtuella nätverket genom att följa stegen som dokumenteras i avsnittet [Konfigurera brandvägg och inställningar](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) för virtuella nätverk i distributionsguiden. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Filen kunde inte återkallas eftersom den inte är tillgänglig i Azure-filresursen. | LÃ¶s problemet genom att kontrollera att filen finns i Azure-filresursen. Om filen finns i Azure-filresursen uppgraderar du till den senaste Azure File [Sync-agentversionen](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions). |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Filen kunde inte återkallas på grund av auktoriseringsfel i lagringskontot. | LÃ¶s problemet genom att verifiera [att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Det gick inte att komma ihåg filen eftersom Azure-filresursen inte är tillgänglig. | Kontrollera att filresursen finns och är tillgänglig. Om filresursen togs bort och återskapades, utför stegen som dokumenteras i [Synkroniseringen misslyckades eftersom Azure-filresursen togs bort och återskapade](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810) avsnittet för att ta bort och återskapa synkroniseringsgruppen. |
| 0x800705aaa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Filen kunde inte återkallas på grund av otillräckliga systemresurser. | Om felet kvarstår undersöker du vilken drivrutin för program eller kernel-läge som uttömmer systemresurser. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Filen kunde inte återkallas på grund av insuffcient minne. | Om felet kvarstår undersöker du vilken drivrutin för program eller kernel-läge som orsakar tillståndet för lite minne. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Det gick inte att återkalla filen på grund av otillräckligt diskutrymme. | Lös problemet genom att frigöra utrymme på volymen genom att flytta filer till en annan volym, öka volymens storlek eller tvinga filer till nivå med hjälp av cmdleten Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Nivåindelade filer är inte tillgängliga på servern efter att en serverslutpunkt har tagits bort
Nivåindelad filer på en server blir otillgängliga om filerna inte återkallas innan du tar bort en serverslutpunkt.

Loggade fel om nivåindelad filer inte är tillgängliga
- Vid synkronisering av en fil loggas felkoden -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) i händelseloggen ItemResults
- När du återkallar en fil loggas felkoden -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) i händelseloggen RecallResults

Du kan återställa åtkomsten till dina nivåindelade filer om följande villkor uppfylls:
- Serverslutpunkten har tagits bort inom de senaste 30 dagarna
- Molnslutpunkten har inte tagits bort 
- Filresursen har inte tagits bort
- Sync-gruppen har inte tagits bort

Om ovanstående villkor är uppfyllda kan du återställa åtkomsten till filerna på servern genom att återskapa serverslutpunkten på samma sökväg på servern inom samma Sync-grupp inom 30 dagar. 

Om ovanstående villkor inte uppfylls, kan du inte återställa åtkomsten, eftersom dessa nivåindelade filer nu är överblivna på servern. Följ instruktionerna nedan för att ta bort de överblivna nivåindelade filerna.

**Obs!**
- När nivåindelad filer inte är tillgängliga på servern bör den fullständiga filen fortfarande vara tillgänglig om du kommer åt Azure-filresursen direkt.
- Om du vill förhindra överblivna nivåindelade filer i framtiden följer du stegen som dokumenteras i [Ta bort en serverslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) när du tar bort en serverslutpunkt.

<a id="get-orphaned"></a>**Så här hämtar du listan över överblivna nivåindelade filer** 

1. Verifiera Azure File Sync agent version v5.1 eller senare är installerad.
2. Kör följande PowerShell-kommandon för att lista överblivna nivåindelade filer:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Spara utdatafilen OrphanTieredFiles.txt om filer måste återställas från säkerhetskopian när de har tagits bort.

<a id="remove-orphaned"></a>**Ta bort överblivna nivåindelade filer** 

*Alternativ 1: Ta bort överblivna nivåindelade filer*

Det här alternativet tar bort de överblivna nivåindelade filerna på Windows Server men kräver att serverslutpunkten tas bort om den finns på grund av rekreation efter 30 dagar eller är ansluten till en annan synkroniseringsgrupp. Filkonflikter uppstår om filer uppdateras på Filresursen i Windows Server eller Azure innan serverslutpunkten återskapas.

1. Verifiera Azure File Sync agent version v5.1 eller senare är installerad.
2. Säkerhetskopiera Azure-filresursen och serverslutpunktens plats.
3. Ta bort serverslutpunkten i synkroniseringsgruppen (om det finns) genom att följa stegen som dokumenteras i [Ta bort en serverslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint).

> [!Warning]  
> Om serverslutpunkten inte tas bort innan du använder cmdleten Remove-StorageSyncOrphanedTieredFiles tas den överblivna nivåindelade filen på servern bort hela filen i Azure-filresursen. 

4. Kör följande PowerShell-kommandon för att lista överblivna nivåindelade filer:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Spara utdatafilen OrphanTieredFiles.txt om filer måste återställas från säkerhetskopian när de har tagits bort.
6. Kör följande PowerShell-kommandon för att ta bort överblivna nivåindelade filer:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Obs!** 
- Nivåindelad filer som ändras på servern som inte är synkroniserade med Azure-filresursen tas bort.
- Nivåindelad filer som är tillgängliga (inte överblivna) tas inte bort.
- Filer som inte är nivåer finns kvar på servern.

7. Valfritt: Återskapa serverslutpunkten om den tas bort i steg 3.

*Alternativ 2: Montera Azure-filresursen och kopiera filerna lokalt som är överblivna på servern*

Det här alternativet kräver inte att serverslutpunkten tas bort, men det krävs tillräckligt med diskutrymme för att kopiera de fullständiga filerna lokalt.

1. [Montera](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) Azure-filresursen på Windows Server som har överblivna nivåindelade filer.
2. Kör följande PowerShell-kommandon för att lista överblivna nivåindelade filer:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Använd utdatafilen OrphanTieredFiles.txt för att identifiera överblivna nivåindelade filer på servern.
4. Skriv över de överblivna nivåindelade filerna genom att kopiera hela filen från Azure-filresursen till Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Felsöka filer som oväntat återkallats på en server  
Antivirus, säkerhetskopiering och andra program som läser ett stort antal filer orsakar oavsiktliga återkallelser om de inte respekterar attributet skip offline och hoppar över att läsa innehållet i dessa filer. Genom att hoppa över filer för produkter som stöder det här alternativet kan oönskad återkallning undvikas i samband med aktiviteter som virusgenomsökningar eller säkerhetskopieringsjobb.

Kontakta programleverantören för information om hur du konfigurerar deras lösning så att offlinefiler ignoreras.

Oavsiktliga återkallelser kan också förekomma i andra scenarier, till exempel när du bläddrar i filer i Utforskaren. Om en mapp med filer på molnnivå öppnas i Utforskaren på servern kan det leda till oväntad återkallning. Det är ännu mer troligt om en antiviruslösning är aktiverad på servern.

> [!NOTE]
>Använd händelse-ID 9059 i telemetrihändelseloggen för att avgöra vilka program som orsakar återkallelser. Den här händelsen ger programåterkallningsdistribution för en serverslutpunkt och loggas en gång i timmen.

## <a name="general-troubleshooting"></a>Allmän felsökning
Om du stöter på problem med Azure File Sync på en server börjar du med att utföra följande steg:
1. I Loggboken granskar du telemetri-, drifts- och diagnostikhändelseloggarna.
    - Synkroniserings-, nivåindelnings- och återkallelseproblem loggas i telemetri-, diagnostik- och drifthändelseloggarna under Program och tjänster\Microsoft\FileSync\Agent.
    - Problem som är relaterade till hantering av en server (till exempel konfigurationsinställningar) loggas i händelseloggarna för drift och diagnostik under Program och tjänster\Microsoft\FileSync\Management.
2. Kontrollera att Azure File Sync-tjänsten körs på servern:
    - Öppna MMC-snapin-modulen Tjänster och kontrollera att tjänsten Storage Sync Agent (FileSyncSvc) körs.
3. Verifiera att filtrarna för Azure File Sync (StorageSync.sys och StorageSyncGuard.sys) körs:
    - Kör i en upphöjd `fltmc`kommandotolk . Kontrollera att filfilterdrivrutinerna StorageSync.sys och StorageSyncGuard.sys visas.

Om problemet inte är löst kör du AFSDiag-verktyget:
1. Skapa en katalog där AFSDiag-utdata sparas (till exempel C:\Output).
    > [!NOTE]
    >AFSDiag tar bort allt innehåll i utdatakatalogen innan du samlar in loggar. Ange en utdataplats som inte innehåller data.
2. Öppna ett upphöjt PowerShell-fönster och kör sedan följande kommandon (tryck på Retur efter varje kommando):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. För spårningsnivån för Azure File Sync kernel mode anger du **1** (om inget annat anges, för att skapa fler utförliga spårningar) och trycker sedan på Retur.
4. För spårningsnivån för Azure File Sync anger du **1** (om inget annat anges, för att skapa fler utförliga spårningar) och trycker sedan på Retur.
5. Återskapa problemet. När du är klar anger du **D**.
6. En ZIP-fil som innehåller loggar och spårningsfiler sparas i utdatakatalogen som du angav.

## <a name="see-also"></a>Se även
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
- [Vanliga frågor och svar i Azure-filer](storage-files-faq.md)
- [Felsöka Azure Files-problem i Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsöka problem med Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md)

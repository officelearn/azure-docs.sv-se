---
title: Viktig information för Azure File Sync-agenten | Microsoft-dokument
description: Viktig information för Azure File Sync-agenten.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 578d00d4bd65b3ffbfb6cdac439762344604e6b8
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804885"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Viktig information om Azure File Sync-agenten
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Dina Windows Server-installationer omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande versioner av Azure File Sync-agenten stöds:

| Milstolpe | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| Samlad uppdatering i december 2019 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | den 12 december 2019 | Stöds |
| V9 Release - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | den 2 december 2019 | Stöds |
| V8 Release - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | den 8 oktober 2019 | Stöds |
| Samlad uppdatering i juli 2019 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | den 24 juli 2019 | Stöds |
| Samlad uppdatering i juli 2019 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 juli 2019 | Stöds |
| V7 Release - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | den 19 juni 2019 | Stöds |
| Samlad uppdatering i juni 2019 - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | den 27 juni 2019 | Stöds - Agentversionen upphör att gälla den 21 april 2020 |
| Samlad uppdatering i juni 2019 - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | den 13 juni 2019 | Stöds - Agentversionen upphör att gälla den 21 april 2020 |
| Samlad uppdatering i maj 2019 - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | den 7 maj 2019 | Stöds - Agentversionen upphör att gälla den 21 april 2020 |
| V6 Release - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | den 21 april 2019 | Stöds - Agentversionen upphör att gälla den 21 april 2020 |
| V5 Släpp | 5.0.2.0 - 5.2.0.0 | Ej tillämpligt | Stöds inte - Agentversionerna upphörde att gälla den 18 mars 2020 |
| V4 Släpp | 4.0.1.0 - 4.3.0.0 | Ej tillämpligt | Stöds inte - Agentversionerna upphörde att gälla den 6 november 2019 |
| V3-släpp | 3.1.0.0 - 3.4.0.0 | Ej tillämpligt | Stöds inte - Agentversionerna upphörde att gälla den 19 augusti 2019 |
| Pre-GA agenter | 1.1.0.0 - 3.0.13.0 | Ej tillämpligt | Stöds inte - Agentversionerna upphörde att gälla den 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-9100"></a>Agent version 9.1.0.0
Följande versionsfakturor gäller version 9.1.0.0 av Azure File Sync-agenten som släpptes 12 december 2019. Dessa anteckningar är utöver de viktig information som anges för version 9.0.0.0.

Problem som åtgärdas i den här versionen:  
- Synkroniseringen misslyckas med något av följande fel efter uppgradering till Azure File Sync agent version 9.0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Agent version 9.0.0.0
Följande versionsfakturor gäller version 9.0.0.0 av Azure File Sync-agenten (släpptes 2 december 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som är åtgärdade

- Stöd för återställning av självbetjäning
    - Användare kan nu återställa sina filer med hjälp av den tidigare versionsfunktionen. Före v9-versionen stöddes inte den tidigare versionsfunktionen på volymer som hade aktiverat molnnivådelning. Den här funktionen måste aktiveras för varje volym separat, där en slutpunkt med molnnivåbaserad aktiverad finns. Mer information finns i  
[Självbetjäningsåterställning via tidigare versioner och VSS (Volume Shadow Copy Service)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Stöd för större filresursstorlekar 
    - Azure File Sync stöder nu upp till 64TiB och 100 miljoner filer i ett enda, synkroniserande namnområde.  
 
- Stöd för dataduplicering på Server 2019 
    - Datadeduplicering stöds nu med molnnivåbaserad aktivering aktiverad på Windows Server 2019. För att stödja dataduplicering på volymer med molnnivådelning måste Windows update [KB4520062](https://support.microsoft.com/help/4520062) installeras. 
 
- Förbättrad minsta filstorlek för en fil till nivå 
    - Den minsta filstorleken för en fil till nivå baseras nu på filsystemets klusterstorlek (dubbla filsystemets klusterstorlek). Som standard är ntfs-filsystemets klusterstorlek till exempel 4KB, den resulterande minsta filstorleken för en fil till nivå är 8KB. 
 
- Nätverksanslutning test cmdlet 
    - Som en del av Azure File Sync-konfigurationen måste flera tjänstslutpunkter kontaktas. De har var sitt eget DNS-namn som måste vara tillgängliga för servern. Dessa webbadresser är också specifika för den region som en server är registrerad på. När en server har registrerats kan anslutningstestet cmdlet (PowerShell och Server Registration Utility) användas för att testa kommunikation med alla webbadresser som är specifika för den här servern. Den här cmdleten kan hjälpa till att felsöka när ofullständig kommunikation förhindrar att servern fungerar fullt ut med Azure File Sync och den kan användas för att finjustera proxy- och brandväggskonfigurationer.  
 
        Kör följande PowerShell-kommandon om du vill köra nätverksanslutningstestet: 
 
        Importmodul "C:\Program\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Ta bort förbättring av serverslutpunkter när molnnivådelning är aktiverat 
    - Precis som tidigare leder det inte till att filer tas bort i Azure-filresursen. Beteendet för reparse-punkter på den lokala servern har dock ändrats. Reparse-punkter (pekare till filer som inte är lokala på servern) tas nu bort när en serverslutpunkt tas bort. De helt cachade filerna finns kvar på servern. Den här förbättringen gjordes för att förhindra [överblivna nivåindelade filer](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) när du tar bort en serverslutpunkt. Om serverslutpunkten återskapas återskapas reparsepunkterna för de nivåindelade filerna på servern.  
 
- Prestanda- och tillförlitlighetsförbättringar 
    - Minskade återkallningsfel. Återkallningsstorleken justeras nu automatiskt baserat på nätverkets bandbredd. 
    - Förbättrade hämtningsprestanda när du lägger till en ny server i en synkroniseringsgrupp. 
    - Reducerade filer som inte synkroniseras på grund av begränsningskonflikter. 
    - Filer kan inte nivå eller oväntat återkallas i vissa scenarier om serverns slutpunktssökväg är en volymmonteringspunkt.
    
### <a name="evaluation-tool"></a>Verktyg för utvärdering
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med ditt system med hjälp av utvärderingsverktyget för Azure File Sync. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Instruktioner för installation och användning finns i avsnittet [Utvärderingsverktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md) och hur du [distribuerar Azure File Sync](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjda (admin) behörigheter.
- Agenten stöds inte på nanoserverdistributionsalternativet.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat bör den virtuella datorn konfigureras med minst 2048 MiB minne.
- Tjänsten Storage Sync Agent (FileSyncSvc) stöder inte serverslutpunkter som finns på en volym som har SVI-katalogen komprimerad (System Volume Information). Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- FSRM-filskärmar (File Server Resource Manager) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av filskärmen.
- Köra sysprep på en server som har Azure File Sync agent installerad stöds inte och kan leda till oväntade resultat. Azure File Sync-agenten bör installeras när serveravbildningen har distribuerats och systeminförinstallationen har slutförts.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [Felsökningsguide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista över tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- DACL-delen av en säkerhetsbeskrivning om den är större än 2 kB. (Det här är endast ett problem om du har mer än ca 40 åtkomstkontrollposter på ett enskilt objekt.)
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Serverslutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåindelad filer blir otillgängliga om filerna inte återkallas innan serverslutpunkten tas bort. Om du vill återställa åtkomsten till filerna återskapar du serverslutpunkten. Om det har gått 30 dagar sedan serverslutpunkten togs bort eller om molnslutpunkten togs bort, kommer nivåindelade filer som inte återkallades att vara oersättliga. Mer information finns i [Nivåindelade filer är inte tillgängliga på servern när du har tagit bort en serverslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativsystemets eller programväxlingsfil på en serverslutpunktsplats.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Slutpunkt för molnet
- Azure File Sync stöder att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett identifieringsjobb för ändring av Azure File Sync. Ett ändringsidentifieringsjobb initieras för en molnslutpunkt en gång var 24:e timme. För att omedelbart synkronisera filer som ändras i Azure-filresursen kan [PowerShell-cmdletn Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) användas för att manuellt initiera identifiering av ändringar i Azure-filresursen. Dessutom kommer ändringar som gjorts i en Azure-filresurs över REST-protokollet inte att uppdatera SMB senast ändrade tiden och kommer inte att ses som en ändring av synkronisering.
- Lagringssynkroniseringstjänsten och/eller lagringskontot kan flyttas till en annan resursgrupp eller prenumeration inom den befintliga Azure AD-klienten. Om lagringskontot flyttas måste du ge Hybrid File Sync Service åtkomst till lagringskontot (se [Se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte att flytta prenumerationen till en annan Azure AD-klientorganisation.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med robocopy använder du alternativet /MIR för att bevara filtidsstämplar. Detta säkerställer att äldre filer är nivåindelade tidigare än nyligen använda filer.
- Filer kan misslyckas med att nivå om pagefile.sys finns på en volym som har molnnivåbaserad aktiverad. Pagefile.sys ska finnas på en volym som har molnnivåaktiverad.

## <a name="agent-version-8000"></a>Agent version 8.0.0.0
Följande versionsfakturor gäller version 8.0.0.0 av Azure File Sync-agenten (släpptes 8 oktober 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som är åtgärdade

- Återställa prestandaförbättringar
    - Snabbare återställningstider för återställning som görs via Azure Backup. Återställda filer synkroniseras tillbaka till Azure File Sync-servrar mycket snabbare. 
- Förbättrad portalupplevelse för molnnivåivision  
    - Om du har nivåindelad filer som inte kommer ihåg kan du nu visa återkallningsfelen i serverslutpunktsegenskaperna. Dessutom visar serverslutpunktshälsan nu ett fel och begränsningssteg om filterdrivrutinen för molnnivådelning inte läses in på servern.
- Enklare agentinstallation
    - Az\AzureRM PowerShell-modulen krävs inte längre för att registrera servern vilket gör installationen enklare och snabb.
- Diverse prestanda- och tillförlitlighetsförbättringar

### <a name="evaluation-tool"></a>Verktyg för utvärdering
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med ditt system med hjälp av utvärderingsverktyget för Azure File Sync. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Instruktioner för installation och användning finns i avsnittet [Utvärderingsverktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md) och hur du [distribuerar Azure File Sync](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjda (admin) behörigheter.
- Agenten stöds inte på nanoserverdistributionsalternativet.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat bör den virtuella datorn konfigureras med minst 2048 MiB minne.
- Tjänsten Storage Sync Agent (FileSyncSvc) stöder inte serverslutpunkter som finns på en volym som har SVI-katalogen komprimerad (System Volume Information). Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- FSRM-filskärmar (File Server Resource Manager) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av filskärmen.
- Köra sysprep på en server som har Azure File Sync agent installerad stöds inte och kan leda till oväntade resultat. Azure File Sync-agenten bör installeras när serveravbildningen har distribuerats och systeminförinstallationen har slutförts.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [Felsökningsguide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista över tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- DACL-delen av en säkerhetsbeskrivning om den är större än 2 kB. (Det här är endast ett problem om du har mer än ca 40 åtkomstkontrollposter på ett enskilt objekt.)
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Serverslutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåindelad filer blir otillgängliga om filerna inte återkallas innan serverslutpunkten tas bort. Om du vill återställa åtkomsten till filerna återskapar du serverslutpunkten. Om det har gått 30 dagar sedan serverslutpunkten togs bort eller om molnslutpunkten togs bort, kommer nivåindelade filer som inte återkallades att vara oersättliga. Mer information finns i [Nivåindelade filer är inte tillgängliga på servern när du har tagit bort en serverslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativsystemets eller programväxlingsfil på en serverslutpunktsplats.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Slutpunkt för molnet
- Azure File Sync stöder att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett identifieringsjobb för ändring av Azure File Sync. Ett ändringsidentifieringsjobb initieras för en molnslutpunkt en gång var 24:e timme. För att omedelbart synkronisera filer som ändras i Azure-filresursen kan [PowerShell-cmdletn Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) användas för att manuellt initiera identifiering av ändringar i Azure-filresursen. Dessutom kommer ändringar som gjorts i en Azure-filresurs över REST-protokollet inte att uppdatera SMB senast ändrade tiden och kommer inte att ses som en ändring av synkronisering.
- Lagringssynkroniseringstjänsten och/eller lagringskontot kan flyttas till en annan resursgrupp eller prenumeration inom den befintliga Azure AD-klienten. Om lagringskontot flyttas måste du ge Hybrid File Sync Service åtkomst till lagringskontot (se [Se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte att flytta prenumerationen till en annan Azure AD-klientorganisation.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med robocopy använder du alternativet /MIR för att bevara filtidsstämplar. Detta säkerställer att äldre filer är nivåindelade tidigare än nyligen använda filer.

## <a name="agent-version-7200"></a>Agent version 7.2.0.0
Följande versionsfakturor gäller version 7.2.0.0 av Azure File Sync-agenten som släpptes 24 juli 2019. Dessa anteckningar är utöver de viktig information som anges för version 7.0.0.0.

Lista över problem som åtgärdats i den här versionen:  
- FileSyncSvc (Storage Sync Agent) kraschar om proxykonfigurationen är null.
- Serverslutpunkten startar BCDR (fel 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) om flera slutpunkter på servern har samma namn.
- Förbättringar av tillförlitligheten i molnnivå.

## <a name="agent-version-7100"></a>Agent version 7.1.0.0
Följande versionsfakturor gäller version 7.1.0.0 av Azure File Sync-agenten som släpptes 12 juli 2019. Dessa anteckningar är utöver de viktig information som anges för version 7.0.0.0.

Lista över problem som åtgärdats i den här versionen:  
- Det går långsamt att komma åt eller bläddra på en serverslutpunktsplats via SMB på Windows Server 2012 R2. 
- Ökad CPU-användning efter installation av Azure File Sync v6-agenten.
- Förbättringar av telemetri för molnnivå.
- Diverse tillförlitlighetsförbättringar för molnnivådelning och synkronisering.

## <a name="agent-version-7000"></a>Agent version 7.0.0.0
Följande versionsfakturor gäller version 7.0.0.0 av Azure File Sync-agenten (släpptes 19 juni 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som är åtgärdade

- Stöd för större filresursstorlekar
    - Med förhandsgranskningen av större Azure-filresurser ökar vi våra supportgränser även för filsynkronisering. I det här första steget stöder Azure File Sync nu upp till 25 TB och 50 miljoner filer i ett enda, synkroniserande namnområde. Om du vill ansöka om förhandsgranskning https://aka.ms/azurefilesatscalesurveyav stora filresurser fyller du i det här formuläret . 
- Stöd för brandvägg och inställning för virtuella nätverk på lagringskonton
    - Azure File Sync stöder nu brandväggen och inställningen för virtuella nätverk på lagringskonton. Mer om hur du konfigurerar distributionen så att den fungerar med brandväggen och inställningen för virtuella nätverk finns i [Konfigurera inställningar för brandvägg och virtuella nätverk](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- PowerShell-cmdlet för att omedelbart synkronisera filer som ändrats i Azure-filresursen
    - För att omedelbart synkronisera filer som ändras i Azure-filresursen kan PowerShell-cmdletn Invoke-AzStorageSyncChangeDetection användas för att manuellt initiera identifiering av ändringar i Azure-filresursen. Den här cmdleten är avsedd för scenarier där någon typ av automatiserad process gör ändringar i Azure-filresursen eller om ändringarna görs av en administratör (som att flytta filer och kataloger till resursen). För ändringar av slutanvändaren är rekommendationen att installera Azure File Sync-agenten i en virtuell virtuell IaaS-dator och låta slutanvändare komma åt filresursen via IaaS VM. På så sätt synkroniseras alla ändringar snabbt med andra agenter utan att du behöver använda cmdleten Invoke-AzStorageSyncChangeDetection. Mer information finns i dokumentationen [Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
- Förbättrad portalupplevelse om du stöter på filer som inte synkroniseras
    - Om du har filer som inte synkroniseras skiljer vi nu mellan tillfälliga och beständiga fel i portalen. Tillfälliga fel löser sig vanligtvis utan att du behöver vidta administratörsåtgärder. En fil som används synkroniseras till exempel inte förrän filreferensen har stängts. För beständiga fel visar vi nu antalet filer som påverkas av varje fel. Det beständiga antalet fel visas också i kolumnen filer som inte synkroniserar alla serverslutpunkter i en synkroniseringsgrupp.
- Förbättrad återställning på Azure Backup-filnivå
    - Enskilda filer som återställs med Azure Backup identifieras och synkroniseras nu snabbare till serverslutpunkten.
- Förbättrad molnnivåhantering återkalla cmdlet tillförlitlighet 
    - Med cmdlet invoke-StorageSyncFileRecall kan kunder ange antalet försök per fil och fördröjning per filförsök som liknar robocopy. Tidigare skulle den här cmdleten återkalla alla nivåindelade filer under en viss sökväg i slumpmässig ordning. Med den nya parametern -Order återkallar den här cmdleten de hetaste data först och respekterar molnnivådelningsprincipen (sluta återkalla om datumprincipen uppfylls eller volymfritt utrymme uppfylls, beroende på vilket som inträffar först).
- Stöd endast för TLS 1.2 (TLS 1.0 och 1.1 är inaktiverat)
    - Azure File Sync stöder nu endast att använda TLS 1.2 på servrar som har TLS 1.0 och 1.1 inaktiverade. Innan den här förbättringen skulle serverregistrering misslyckas om TLS 1.0 och 1.1 inaktiverades på servern.
- Diverse prestanda- och tillförlitlighetsförbättringar för synkronisering och molnnivådelning
    - Det finns flera tillförlitlighets- och prestandaförbättringar i den här versionen. Vissa av dem är inriktade på att göra molnnivåhanteringen effektivare och Azure File Sync som helhet fungerar bättre i de situationer då du har ett bandbreddsbegränsningsschema inställt.

### <a name="evaluation-tool"></a>Verktyg för utvärdering
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med ditt system med hjälp av utvärderingsverktyget för Azure File Sync. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Instruktioner för installation och användning finns i avsnittet [Utvärderingsverktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md) och hur du [distribuerar Azure File Sync](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjda (admin) behörigheter.
- Agenten stöds inte på nanoserverdistributionsalternativet.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat bör den virtuella datorn konfigureras med minst 2048 MiB minne.
- Tjänsten Storage Sync Agent (FileSyncSvc) stöder inte serverslutpunkter som finns på en volym som har SVI-katalogen komprimerad (System Volume Information). Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- FSRM-filskärmar (File Server Resource Manager) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av filskärmen.
- Köra sysprep på en server som har Azure File Sync agent installerad stöds inte och kan leda till oväntade resultat. Azure File Sync-agenten bör installeras när serveravbildningen har distribuerats och systeminförinstallationen har slutförts.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [Felsökningsguide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista över tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- DACL-delen av en säkerhetsbeskrivning om den är större än 2 kB. (Det här är endast ett problem om du har mer än ca 40 åtkomstkontrollposter på ett enskilt objekt.)
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Serverslutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåindelad filer blir otillgängliga om filerna inte återkallas innan serverslutpunkten tas bort. Om du vill återställa åtkomsten till filerna återskapar du serverslutpunkten. Om det har gått 30 dagar sedan serverslutpunkten togs bort eller om molnslutpunkten togs bort, kommer nivåindelade filer som inte återkallades att vara oersättliga.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativsystemets eller programväxlingsfil på en serverslutpunktsplats.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Slutpunkt för molnet
- Azure File Sync stöder att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett identifieringsjobb för ändring av Azure File Sync. Ett ändringsidentifieringsjobb initieras för en molnslutpunkt en gång var 24:e timme. Dessutom kommer ändringar som gjorts i en Azure-filresurs över REST-protokollet inte att uppdatera SMB senast ändrade tiden och kommer inte att ses som en ändring av synkronisering.
- Lagringssynkroniseringstjänsten och/eller lagringskontot kan flyttas till en annan resursgrupp eller prenumeration inom den befintliga Azure AD-klienten. Om lagringskontot flyttas måste du ge Hybrid File Sync Service åtkomst till lagringskontot (se [Se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte att flytta prenumerationen till en annan Azure AD-klientorganisation.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med robocopy använder du alternativet /MIR för att bevara filtidsstämplar. Detta säkerställer att äldre filer är nivåindelade tidigare än nyligen använda filer.

## <a name="agent-version-6300"></a>Agent version 6.3.0.0
Följande versionsfakturor gäller version 6.3.0.0 av Azure File Sync-agenten som släpptes den 27 juni 2019. Dessa anteckningar är utöver de viktig information som anges för version 6.0.0.0.

Lista över problem som åtgärdats i den här versionen:  
- Det går långsamt att komma åt eller bläddra på en serverslutpunktsplats via SMB på Windows Server 2012 R2 
- Ökad CPU-användning efter installation av Azure File Sync v6-agenten
- Förbättringar av telemetri i molnet

## <a name="agent-version-6200"></a>Agent version 6.2.0.0
Följande versionsfakturor gäller version 6.2.0.0 av Azure File Sync-agenten som släpptes den 13 juni 2019. Dessa anteckningar är utöver de viktig information som anges för version 6.0.0.0.

Lista över problem som åtgärdats i den här versionen:  
- När du har skapat en serverslutpunkt kan hög CPU-användning uppstå när bakgrundsinnning hämtar filer till servern
- Synkroniserings- och molnnivådelningsåtgärder kan misslyckas med fel ECS_E_SERVER_CREDENTIAL_NEEDED på grund av tokenförfallodatum
- Det kan misslyckas att återkalla en fil om url:en för att hämta filen innehåller reserverade tecken 

## <a name="agent-version-6100"></a>Agent version 6.1.0.0
Följande versionsfakturor gäller version 6.1.0.0 av Azure File Sync-agenten som släpps den 6 maj 2019. Dessa anteckningar är utöver de viktig information som anges för version 6.0.0.0.

Lista över problem som åtgärdats i den här versionen:  
- Det går inte att visa agentversionen och serverslutpunktskonfigurationen på servrar som har Azure File Sync agent version 6.0 installerad.

## <a name="agent-version-6000"></a>Agent version 6.0.0.0
Följande versionsfakturor gäller version 6.0.0.0 av Azure File Sync-agenten (släpptes den 22 april 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som är åtgärdade

- Support för automatisk uppdatering av agent
  - Vi har hört din feedback och lagt till en funktion för automatisk uppdatering i Azure File Sync-serveragenten. Mer information finns i [uppdateringsprincipen för Azure File Sync agent](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Stöd för ACL-filer för Azure-fildelning
  - Azure File Sync har alltid stött synkronisering av ACL mellan serverslutpunkter, men ACL:erna synkroniserades inte med molnslutpunkten (Azure-filresurs). Den här versionen lägger till stöd för synkronisering av ACL:er mellan server- och molnslutpunkter.
- Parallella uppladdnings- och nedladdningssynkroniseringssessioner för en serverslutpunkt 
  - Serverslutpunkter stöder nu att ladda upp och hämta filer samtidigt. Ingen mer väntan på att en nedladdning ska slutföras så att filer kan överföras till Azure-filresursen. 
- Nya cmdlets för molnnivådelning för att hämta volym- och nivåindelningsstatus
  - Två nya, serverlokala PowerShell-cmdlets kan nu användas för att hämta information om molnnivåindelning och filåterkallelse. De gör loggningsinformation från två händelsekanaler på servern tillgänglig:
    - Get-StorageSyncFileTieringResult listar alla filer och deras sökvägar som inte har nivåindelats och rapporterar om orsaken.
    - Get-StorageSyncFileRecallResult rapporterar alla filåterkallningshändelser. Den listar varje fil återkallas och dess väg samt framgång eller fel för att återkalla.
  - Som standard kan båda händelsekanalerna lagra upp till 1 MB vardera – du kan öka mängden filer som rapporteras genom att öka händelsekanalstorleken.
- Stöd för FIPS-läge
  - Azure File Sync stöder nu aktivering av FIPS-läge på servrar som har Azure File Sync-agenten installerad.
    - Innan fips-läget aktiveras på servern installerar du Modulen Azure File Sync agent och [PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) på servern. Om FIPS redan är aktiverat på servern [hämtar du](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) [modulen PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) manuellt till servern.
- Diverse tillförlitlighetsförbättringar för molnnivådelning och synkronisering

### <a name="evaluation-tool"></a>Verktyg för utvärdering
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med ditt system med hjälp av utvärderingsverktyget för Azure File Sync. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Instruktioner för installation och användning finns i avsnittet [Utvärderingsverktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md) och hur du [distribuerar Azure File Sync](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjda (admin) behörigheter.
- Agenten stöds inte på nanoserverdistributionsalternativet.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat bör den virtuella datorn konfigureras med minst 2048 MiB minne.
- Tjänsten Storage Sync Agent (FileSyncSvc) stöder inte serverslutpunkter som finns på en volym som har SVI-katalogen komprimerad (System Volume Information). Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- FSRM-filskärmar (File Server Resource Manager) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av filskärmen.
- Köra sysprep på en server som har Azure File Sync agent installerad stöds inte och kan leda till oväntade resultat. Azure File Sync-agenten bör installeras när serveravbildningen har distribuerats och systeminförinstallationen har slutförts.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [Felsökningsguide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista över tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- DACL-delen av en säkerhetsbeskrivning om den är större än 2 kB. (Det här är endast ett problem om du har mer än ca 40 åtkomstkontrollposter på ett enskilt objekt.)
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Serverslutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåindelad filer blir otillgängliga om filerna inte återkallas innan serverslutpunkten tas bort. Om du vill återställa åtkomsten till filerna återskapar du serverslutpunkten. Om det har gått 30 dagar sedan serverslutpunkten togs bort eller om molnslutpunkten togs bort, kommer nivåindelade filer som inte återkallades att vara oersättliga.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativsystemets eller programväxlingsfil på en serverslutpunktsplats.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Slutpunkt för molnet
- Azure File Sync stöder att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett identifieringsjobb för ändring av Azure File Sync. Ett ändringsidentifieringsjobb initieras för en molnslutpunkt en gång var 24:e timme. Dessutom kommer ändringar som gjorts i en Azure-filresurs över REST-protokollet inte att uppdatera SMB senast ändrade tiden och kommer inte att ses som en ändring av synkronisering.
- Lagringssynkroniseringstjänsten och/eller lagringskontot kan flyttas till en annan resursgrupp eller prenumeration inom den befintliga Azure AD-klienten. Om lagringskontot flyttas måste du ge Hybrid File Sync Service åtkomst till lagringskontot (se [Se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte att flytta prenumerationen till en annan Azure AD-klientorganisation.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med robocopy använder du alternativet /MIR för att bevara filtidsstämplar. Detta säkerställer att äldre filer är nivåindelade tidigare än nyligen använda filer.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

## <a name="agent-version-5200"></a>Agent version 5.2.0.0
Följande versionsfakturor gäller version 5.2.0.0 av Azure File Sync-agenten som släpps den 4 april 2019. Dessa anteckningar är utöver de viktig information som anges för version 5.0.2.0.

Lista över problem som åtgärdats i den här versionen:  
- Tillförlitlighetsförbättringar för funktioner för offlinedataöverföring och återupptar dataöverföring
- Förbättringar av synkroniseringstelemetri

## <a name="agent-version-5100"></a>Agent version 5.1.0.0
Följande versionsfakturor gäller version 5.1.0.0 av Azure File Sync-agenten som släpps den 7 mars 2019. Dessa anteckningar är utöver de viktig information som anges för version 5.0.2.0.

Lista över problem som åtgärdats i den här versionen:  
- Filer kan misslyckas med att synkronisera med felet 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) om ändringsuppräkningen misslyckas på servern
- Om en synkroniseringssession eller -fil får ett fel 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), försöker synkroniseringen nu igen
- Filer kan misslyckas med att synkronisera med felet 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Hög minnesanvändning kan uppstå när du återkallar filer
- Förbättringar av telemetri i molnet 

## <a name="agent-version-5020"></a>Agent version 5.0.2.0
Följande versionsfakturor gäller version 5.0.2.0 av Azure File Sync-agenten (släpptes 12 februari 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som är åtgärdade

- Stöd för Azure Government-molnet
  - Vi har lagt till förhandsversionsstöd för Azure Government-molnet. Detta kräver en prenumeration i vit listad och en specialagenthämtning från Microsoft. För att få tillgång till förhandsgranskningen, vänligen maila oss direkt på [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Stöd för dataduplicering
    - Dataduplicering stöds nu fullt ut med molnnivåbaserad aktivering på Windows Server 2016 och Windows Server 2019. Om du aktiverar deduplicering på en volym med molnnivåaktivering aktiverat kan du cachelagra fler filer lokalt utan att etablera mer lagringsutrymme.
- Stöd för dataöverföring offline (t.ex. via Data Box)
    - Migrera enkelt stora mängder data till Azure File Sync på något sätt du väljer. Du kan välja Azure Data Box, AzCopy och till och med migreringstjänster från tredje part. Du behöver inte använda enorma mängder bandbredd för att få dina data till Azure, när det gäller Data Box - helt enkelt skicka den dit! Mer information finns i [Offlinedataöverföringsdokument](https://aka.ms/AFS/OfflineDataTransfer).
- Förbättrad synkroniseringsprestanda
    - Kunder med flera serverslutpunkter på samma volym kan ha haft långsam synkroniseringsprestanda före den här versionen. Azure File Sync skapar en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser. Sync stöder nu flera serverslutpunkter som synkroniseras på en volym när en VSS-synkroniseringssession är aktiv. Ingen mer väntan på att en VSS-synkroniseringssession ska slutföras så att synkroniseringen kan återupptas på andra serverslutpunkter på volymen.
- Förbättrad övervakning i portalen
    - Diagram har lagts till i storage sync-tjänstportalen för att visa:
        - Antal synkroniserade filer
        - Storleken på överförda data
        - Antal filer som inte synkroniseras
        - Storleken på återkallade data
        - Status för serveranslutning
    - Mer information finns i [Övervaka Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Förbättrad skalbarhet och tillförlitlighet
    - Maximalt antal filsystemobjekt (kataloger och filer) i en katalog har ökat till 1 000 000. Tidigare gräns var 200 000.
    - Synkroniseringen försöker återuppta dataöverföringen i stället för att återsända när en överföring avbryts för stora filer 

### <a name="evaluation-tool"></a>Verktyg för utvärdering
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med ditt system med hjälp av utvärderingsverktyget för Azure File Sync. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Instruktioner för installation och användning finns i avsnittet [Utvärderingsverktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md) och hur du [distribuerar Azure File Sync](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjda (admin) behörigheter.
- Agenten stöds inte i distributionsalternativen för Windows Server Core eller Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat bör den virtuella datorn konfigureras med minst 2048 MiB minne.
- Tjänsten Storage Sync Agent (FileSyncSvc) stöder inte serverslutpunkter som finns på en volym som har SVI-katalogen komprimerad (System Volume Information). Den här konfigurationen leder till oväntade resultat.
- FIPS-läge stöds inte och måste inaktiveras. 

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- FSRM-filskärmar (File Server Resource Manager) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av filskärmen.
- Köra sysprep på en server som har Azure File Sync agent installerad stöds inte och kan leda till oväntade resultat. Azure File Sync-agenten bör installeras när serveravbildningen har distribuerats och systeminförinstallationen har slutförts.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [Felsökningsguide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista över tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- DACL-delen av en säkerhetsbeskrivning om den är större än 2 kB. (Det här är endast ett problem om du har mer än ca 40 åtkomstkontrollposter på ett enskilt objekt.)
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Serverslutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåindelad filer blir otillgängliga om filerna inte återkallas innan serverslutpunkten tas bort. Om du vill återställa åtkomsten till filerna återskapar du serverslutpunkten. Om det har gått 30 dagar sedan serverslutpunkten togs bort eller om molnslutpunkten togs bort, kommer nivåindelade filer som inte återkallades att vara oersättliga.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativsystemets eller programväxlingsfil på en serverslutpunktsplats.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Slutpunkt för molnet
- Azure File Sync stöder att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett identifieringsjobb för ändring av Azure File Sync. Ett ändringsidentifieringsjobb initieras för en molnslutpunkt en gång var 24:e timme. Dessutom kommer ändringar som gjorts i en Azure-filresurs över REST-protokollet inte att uppdatera SMB senast ändrade tiden och kommer inte att ses som en ändring av synkronisering.
- Lagringssynkroniseringstjänsten och/eller lagringskontot kan flyttas till en annan resursgrupp eller prenumeration inom den befintliga Azure AD-klienten. Om lagringskontot flyttas måste du ge Hybrid File Sync Service åtkomst till lagringskontot (se [Se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte att flytta prenumerationen till en annan Azure AD-klientorganisation.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med robocopy använder du alternativet /MIR för att bevara filtidsstämplar. Detta säkerställer att äldre filer är nivåindelade tidigare än nyligen använda filer.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

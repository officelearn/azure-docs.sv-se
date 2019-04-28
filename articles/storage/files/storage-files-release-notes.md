---
title: Viktig information för Azure File Sync-agenten | Microsoft Docs
description: Viktig information om Azure File Sync-agenten.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 4/22/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 9a8fe1c083ab4e241cf236fd6f731fba1aa67f87
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112722"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Viktig information för Azure File Sync-agenten
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Dina Windows Server-installationer omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande versioner av Azure File Sync-agenten stöds:

| Milstolpe | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| V6 Release - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | Den 21 april 2019 | Stöds (rekommenderad version) |
| Samlad - uppdatering i april 2019 [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 april 2019 | Stöds |
| Samlad - uppdatering i mars 2019 [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | Den 7 mars 2019 | Stöds |
| V5 Release - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 februari 2019 | Stöds |
| Samlad - uppdatering i januari 2019 [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 januari 2019 | Stöds |
| Samlad - uppdatering för december 2018 [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 december 2018 | Stöds |
| Samlad uppdatering för december 2018 | 4.1.0.0 | 4 december 2018 | Stöds |
| V4-version | 4.0.1.0 | Den 13 november 2018 | Stöds |
| Samlad uppdatering september 2018 | 3.3.0.0 | 24 september 2018 | Stöd för – upphör agentversion att gälla 19 juli 2019 |
| Samlad uppdatering augusti 2018 | 3.2.0.0 | 15 augusti 2018 | Stöd för – upphör agentversion att gälla 19 juli 2019 |
| Allmän tillgänglighet | 3.1.0.0 | 19 juli 2018 | Stöd för – upphör agentversion att gälla 19 juli 2019 |
| Agenter som har upphört att gälla | 1.1.0.0 - 3.0.13.0 | Gäller inte | Inte stöd för – agentversioner har upphört att gälla den 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-6000"></a>Agentversion 6.0.0.0
Följande viktiga information gäller 6.0.0.0 av Azure File Sync-agenten (gavs ut den 22 April 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Stöd för automatisk uppdatering av Agent
  - Vi har hört er feedback och lagt till en funktion för automatisk uppdatering i Azure File Sync server-agenten. Mer information finns i [uppdateringsprincip för Azure File Sync-agenten](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Stöd för Azure file dela ACL: er
  - Azure File Sync har alltid haft stöd synkronisering ACL: er mellan serverslutpunkter men ACL: er har inte synkroniserats till molnslutpunkten (Azure-filresurs). Den här versionen lägger till stöd för ACL: er för synkronisering mellan servern och molnslutpunkter.
- Parallell ladda upp och ladda ned synkroniseringssessioner för en serverslutpunkt 
  - Serverslutpunkter har nu stöd för att överföra och hämta filer på samma gång. Du behöver inte vänta efter en komponent att slutföra så filer kan överföras till Azure-filresursen. 
- Ny Molnlagringsnivå cmdlets för att hämta volym och lagringsnivåer status
  - Två nya, server-lokala PowerShell-cmdlets kan nu användas för att hämta molnet lagringsnivåer och filen återkallande. De tillgängliggöra loggningsinformation från två händelse kanaler på servern:
    - Get-StorageSyncFileTieringResult visar en lista över alla filer och deras sökvägar som inte har nivåindelade och rapporter på orsaken till varför.
    - Get-StorageSyncFileRecallResult rapporterar alla händelser för återkallande av filen. Den visas varje fil återställdes och dess sökväg samt – slutförd eller misslyckades för den återkallanden.
  - Som standard både händelse kanaler kan lagra upp till 1MB varje – du kan öka mängden filer som rapporteras genom att öka kanal händelsestorleken.
- Stöd för FIPS-läge
  - Azure File Sync har nu stöd för att aktivera FIPS-läge på servrar som har Azure File Sync-agenten installerad.
    - Innan du kan aktivera FIPS-läge på din server, installerar du Azure File Sync-agenten och [PackageManagement modulen](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) på servern. Om FIPS redan är aktiverad på servern, [hämta manuellt](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) den [PackageManagement modulen](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) till servern.
- Diverse tillförlitlighet förbättringar för molntjänster lagringsnivåer och synkronisering

### <a name="evaluation-tool"></a>Utvärderingsverktyg för
Innan du distribuerar Azure File Sync, bör du utvärdera om den är kompatibel med ditt system med hjälp av verktyget Azure File Sync-utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med ditt filsystem och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Anvisningarna för installation och användning, finns i [Evaluation Tool](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) avsnitt i Planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [planera för distribution av Azure File Sync](storage-sync-files-planning.md) och [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjd behörighet (admin) behörigheter.
- Agenten stöds inte på Nano Server-distributionsalternativet.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, kommer den virtuella datorn ska konfigureras med en minsta 2048 MiB minne.
- Storage Sync-agenten (FileSyncSvc)-tjänsten stöder inte serverslutpunkter som finns på en volym med system volume information (SVI) directory komprimeras. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Hanteraren för filserverresurser (FSRM) filgaller kan orsaka oändliga synkroniseringsfel om filer blockeras på grund av filgallret.
- Kör sysprep på en server som har Azure File Sync-agenten installerad stöds inte och kan leda till oväntade resultat. Azure File Sync-agenten ska installeras efter distribution av server-avbildning och slutföra sysprep mini-installationen.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [felsökningsguide för](storage-sync-files-troubleshoot.md#handling-unsupported-characters) lista över tecken som inte stöds.
- Filer eller kataloger som avslutas med en punkt.
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
 
### <a name="server-endpoint"></a>Server-slutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåindelade filer kommer att bli otillgängliga om filerna som inte hämtas innan du tar bort Serverslutpunkten. Återskapa Serverslutpunkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Serverslutpunkten har tagits bort eller om molnslutpunkten har tagits bort, kommer du att oanvändbara nivåindelade filer som inte har återkallats.
- Molnet lagringsnivåer stöds inte på systemvolymen. Inaktivera molnlagringsnivåer när du skapar Serverslutpunkten för att skapa en serverslutpunkt på systemvolymen.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte ett operativsystem eller ett programs växlingsfil i en serverplats för slutpunkten.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Molnslutpunkt
- Azure File Sync stöder gör ändringar i Azure-filresursen direkt. Ändringar som görs på Azure-filresursen måste dock först identifieras av ett jobb med Azure File Sync ändra identifiering. Ett jobb för identifiering av ändring initieras för en molnslutpunkt en gång per dygn. Dessutom kommer inte att uppdatera SMB tid för senaste ändring ändringar som gjorts i en Azure-filresurs via REST-protokollet och kan inte ses som en ändring av synkronisering.
- Storage sync-tjänsten och/eller storage-konto kan flyttas till en annan resursgrupp eller prenumeration inom de befintliga Azure AD-klienten. Om lagringskontot har flyttats, måste du ge Hybrid Filsynkroniseringstjänstens åtkomst till lagringskontot (se [se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med hjälp av robocopy, kan du använda alternativet/MIR för att bevara filen tidsstämplar. Detta säkerställer att äldre filer nivåindelas tidigare än nyligen öppnade filer.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

## <a name="agent-version-5200"></a>Agentversion 5.2.0.0
Följande viktiga information gäller 5.2.0.0 av Azure File Sync-agenten gavs ut den 4 April 2019. Detta är viktig för version 5.0.2.0.

Lista över problem som åtgärdas i den här versionen:  
- Förbättrad tillförlitlighet för överföring av data offline och dataöverföring återuppta funktioner
- Synkronisera telemetri förbättringar

## <a name="agent-version-5100"></a>Agentversion 5.1.0.0
Följande viktiga information gäller 5.1.0.0 av Azure File Sync-agenten släpps den 7 mars 2019. Detta är viktig för version 5.0.2.0.

Lista över problem som åtgärdas i den här versionen:  
- Filer kan misslyckas med att synkronisera med fel 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) om ändringen uppräkning fungerar inte på servern
- Om en synkroniseringssessionen eller filen får ett fel 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), synkronisera nu försök sedan igen
- Filer kan misslyckas med att synkronisera med fel 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Hög minnesanvändning kan uppstå vid återställning av filer
- Förbättringar av lagringsnivåer telemetri i molnet 

## <a name="agent-version-5020"></a>Agentversion 5.0.2.0
Följande viktiga information gäller 5.0.2.0 av Azure File Sync-agenten (gavs ut den 12 februari 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Stöd för Azure Government-molnet
  - Vi har lagt till förhandsversion av stöd för Azure Government-molnet. Detta kräver en vit angiven prenumeration och en särskild agent-hämtning från Microsoft. För att få åtkomst till förhandsversionen kan du maila direkt [ AzureFiles@microsoft.com ](mailto:AzureFiles@microsoft.com).
- Stöd för Datadeduplicering
    - Datadeduplicering stöds nu helt med molnlagringsnivåer aktiverade på Windows Server 2016 och Windows Server 2019. Aktivera deduplicering på en volym med molnlagringsnivåer aktiverad kan du Cachelagra flera filer på plats utan att behöva etablera mer lagringsutrymme.
- Stöd för offline-dataöverföring (t.ex. via Data Box)
    - Migrera enkelt stora mängder data till Azure File Sync något sätt som du väljer. Du kan välja Azure Data Box, AzCopy och även migreringstjänsterna från tredje part. Inte behöver använda stora mängder bandbredd för att få dina data i Azure, när det gäller Data Box – helt enkelt skicka ut den det! Mer information finns i [Offline Data Transfer Docs](https://aka.ms/AFS/OfflineDataTransfer).
- Förbättrad synkronisering
    - Kunder med flera serverslutpunkter på samma volym kan det ha uppstått långsam synkroniseringsprestanda innan den här versionen. Azure File Sync skapas en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser. Synkronisering har nu stöd för flera serverslutpunkter synkronisera på en volym när en VSS-synkroniseringssessionen är aktiv. Ingen mer väntan på ett VSS synkronisera session för att slutföra så synkronisering kan återuppta på andra serverslutpunkter på volymen.
- Förbättrad övervakning i portalen
    - Diagram har lagts till i den Lagringssynkroniseringstjänst-portalen för att visa:
        - Antal filer som har synkroniserats
        - Storleken på data som överförs
        - Antal filer som inte synkroniserar
        - Storleken på data som har återkallats
        - Status för anslutning
    - Mer information finns i [övervaka Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Förbättrad skalbarhet och tillförlitlighet
    - Maxantalet filsystemsobjekt (kataloger och filer) i en katalog har ökat till 1 000 000. Gränsen för tidigare var 200 000.
    - Synkronisering försöker återuppta dataöverföring i stället för återöverföring sker när en överföring avbryts för stora filer 

### <a name="evaluation-tool"></a>Utvärderingsverktyg för
Innan du distribuerar Azure File Sync, bör du utvärdera om den är kompatibel med ditt system med hjälp av verktyget Azure File Sync-utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med ditt filsystem och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Anvisningarna för installation och användning, finns i [Evaluation Tool](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) avsnitt i Planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [planera för distribution av Azure File Sync](storage-sync-files-planning.md) och [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjd behörighet (admin) behörigheter.
- Agenten stöds inte på distributionsalternativen för Windows Server Core eller Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, kommer den virtuella datorn ska konfigureras med en minsta 2048 MiB minne.
- Storage Sync-agenten (FileSyncSvc)-tjänsten stöder inte serverslutpunkter som finns på en volym med system volume information (SVI) directory komprimeras. Den här konfigurationen leder till oväntade resultat.
- FIPS-läge stöds inte och måste inaktiveras. 

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Hanteraren för filserverresurser (FSRM) filgaller kan orsaka oändliga synkroniseringsfel om filer blockeras på grund av filgallret.
- Kör sysprep på en server som har Azure File Sync-agenten installerad stöds inte och kan leda till oväntade resultat. Azure File Sync-agenten ska installeras efter distribution av server-avbildning och slutföra sysprep mini-installationen.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [felsökningsguide för](storage-sync-files-troubleshoot.md#handling-unsupported-characters) lista över tecken som inte stöds.
- Filer eller kataloger som avslutas med en punkt.
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
 
### <a name="server-endpoint"></a>Server-slutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåindelade filer kommer att bli otillgängliga om filerna som inte hämtas innan du tar bort Serverslutpunkten. Återskapa Serverslutpunkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Serverslutpunkten har tagits bort eller om molnslutpunkten har tagits bort, kommer du att oanvändbara nivåindelade filer som inte har återkallats.
- Molnet lagringsnivåer stöds inte på systemvolymen. Inaktivera molnlagringsnivåer när du skapar Serverslutpunkten för att skapa en serverslutpunkt på systemvolymen.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte ett operativsystem eller ett programs växlingsfil i en serverplats för slutpunkten.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Molnslutpunkt
- Azure File Sync stöder gör ändringar i Azure-filresursen direkt. Ändringar som görs på Azure-filresursen måste dock först identifieras av ett jobb med Azure File Sync ändra identifiering. Ett jobb för identifiering av ändring initieras för en molnslutpunkt en gång per dygn. Dessutom kommer inte att uppdatera SMB tid för senaste ändring ändringar som gjorts i en Azure-filresurs via REST-protokollet och kan inte ses som en ändring av synkronisering.
- Storage sync-tjänsten och/eller storage-konto kan flyttas till en annan resursgrupp eller prenumeration inom de befintliga Azure AD-klienten. Om lagringskontot har flyttats, måste du ge Hybrid Filsynkroniseringstjänstens åtkomst till lagringskontot (se [se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med hjälp av robocopy, kan du använda alternativet/MIR för att bevara filen tidsstämplar. Detta säkerställer att äldre filer nivåindelas tidigare än nyligen öppnade filer.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

## <a name="agent-version-4300"></a>Agentversion 4.3.0.0
Följande viktiga information gäller 4.3.0.0 av Azure File Sync-agenten gavs ut den 14 januari 2019. Detta är viktig för version 4.0.1.0.

Lista över problem som åtgärdas i den här versionen:  
- Filer inte nivåindelas efter Azure File Sync-agenten uppgraderas till version 4.x.
- AfsUpdater.exe stöds nu på Windows Server 2019.
- Diverse tillförlitlighet förbättringar för synkronisering. 

## <a name="agent-version-4200"></a>Agentversion 4.2.0.0
Följande viktiga information gäller 4.2.0.0 av Azure File Sync-agenten är 10 December 2018. Detta är viktig för version 4.0.1.0.

Lista över problem som åtgärdas i den här versionen:  
- Stoppfel 0x3B eller stoppfelet 0x1E kan uppstå när en VSS-ögonblicksbilden har skapats.  
- En minnesläcka kan inträffa när molnet lagringsnivåer är aktiverat  

## <a name="agent-version-4100"></a>Agentversion 4.1.0.0
Följande viktiga information gäller 4.1.0.0 av Azure File Sync-agenten gavs ut den 4 December 2018. Detta är viktig för version 4.0.1.0.

Lista över problem som åtgärdas i den här versionen:  
- Servern kan bli svarar inte på grund av en molnlagringsnivå minnesläcka.  
- Agentinstallationen misslyckas med följande fel: Fel vid 1921. Det gick inte att stoppa tjänsten 'Storage Sync-agenten ”(FileSyncSvc).  Kontrollera att du har behörighet att stoppa systemtjänster.  
- Tjänsten Storage Sync-agenten (FileSyncSvc) kan krascha när minnesanvändningen är hög.  
- Diverse tillförlitlighet förbättringar för molnbaserad lagringsnivåer och synkronisering.

## <a name="agent-version-4010"></a>Agentversion 4.0.1.0
Följande viktiga information gäller 4.0.1.0 av Azure File Sync-agenten (gavs ut den 13 November 2018).

### <a name="evaluation-tool"></a>Utvärderingsverktyg för
Innan du distribuerar Azure File Sync, bör du utvärdera om den är kompatibel med ditt system med hjälp av verktyget Azure File Sync-utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med ditt filsystem och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Anvisningarna för installation och användning, finns i [Evaluation Tool](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) avsnitt i Planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [planera för distribution av Azure File Sync](storage-sync-files-planning.md) och [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjd behörighet (admin) behörigheter.
- Agenten stöds inte på distributionsalternativen för Windows Server Core eller Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, kommer den virtuella datorn ska konfigureras med en minsta 2048 MiB minne.
- Storage Sync-agenten (FileSyncSvc)-tjänsten stöder inte serverslutpunkter som finns på en volym med system volume information (SVI) directory komprimeras. Den här konfigurationen leder till oväntade resultat.
- FIPS-läge stöds inte och måste inaktiveras. 
- Stoppfel 0x3B eller stoppfelet 0x1E kan uppstå när en VSS-ögonblicksbilden har skapats.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Hanteraren för filserverresurser (FSRM) filgaller kan orsaka oändliga synkroniseringsfel om filer blockeras på grund av filgallret.
- Kör sysprep på en server som har Azure File Sync-agenten installerad stöds inte och kan leda till oväntade resultat. Azure File Sync-agenten ska installeras efter distribution av server-avbildning och slutföra sysprep mini-installationen.
- Datadeduplicering och lagringsnivåer för moln stöds inte på samma volym.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [felsökningsguide för](storage-sync-files-troubleshoot.md#handling-unsupported-characters) lista över tecken som inte stöds.
- Filer eller kataloger som avslutas med en punkt.
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
 
### <a name="server-endpoint"></a>Server-slutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåindelade filer kommer att bli otillgängliga om filerna som inte hämtas innan du tar bort Serverslutpunkten. Återskapa Serverslutpunkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Serverslutpunkten har tagits bort eller om molnslutpunkten har tagits bort, kommer du att oanvändbara nivåindelade filer som inte har återkallats.
- Molnet lagringsnivåer stöds inte på systemvolymen. Inaktivera molnlagringsnivåer när du skapar Serverslutpunkten för att skapa en serverslutpunkt på systemvolymen.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte ett operativsystem eller ett programs växlingsfil i en serverplats för slutpunkten.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Molnslutpunkt
- Azure File Sync stöder gör ändringar i Azure-filresursen direkt. Ändringar som görs på Azure-filresursen måste dock först identifieras av ett jobb med Azure File Sync ändra identifiering. Ett jobb för identifiering av ändring initieras för en molnslutpunkt en gång per dygn. Dessutom kommer inte att uppdatera SMB tid för senaste ändring ändringar som gjorts i en Azure-filresurs via REST-protokollet och kan inte ses som en ändring av synkronisering.
- Storage sync-tjänsten och/eller storage-konto kan flyttas till en annan resursgrupp eller prenumeration inom de befintliga Azure AD-klienten. Om lagringskontot har flyttats, måste du ge Hybrid Filsynkroniseringstjänstens åtkomst till lagringskontot (se [se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Den datumbaserade lagringsnivåer för moln principinställningen används för att ange filer som ska cachelagras om öppnas i ett angivet antal dagar. Mer information finns i [översikt över lagringsnivåer Cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med hjälp av robocopy, kan du använda alternativet/MIR för att bevara filen tidsstämplar. Detta säkerställer att äldre filer nivåindelas tidigare än nyligen öppnade filer.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

## <a name="agent-version-3300"></a>Agentversion 3.3.0.0
Följande viktiga information gäller 3.3.0.0 av Azure File Sync-agenten gavs ut den 24 September 2018. Detta är viktig för version 3.1.0.0.

Lista över problem som åtgärdas i den här versionen:
- Registrerad server status är ”visas som offline” när du har i Azure File Sync-agenten har uppgraderats till version 3.1 eller 3.2.
- Storage Sync-agenten (FileSyncSvc)-tjänsten kraschar på grund av filer som har långa sökvägar.
- Registrera servern misslyckas med fel: Det gick inte att läsa in filen eller sammansättningen Kailani.Afs.StorageSyncProtocol.V3.

## <a name="agent-version-3200"></a>Agent-version 3.2.0.0
Följande viktiga information gäller 3.2.0.0 av Azure File Sync-agenten gavs ut den 15 augusti 2018. Detta är viktig för version 3.1.0.0.

Den här versionen innehåller följande korrigeringen:
- Synkronisering misslyckas med minnesfel-nummer (0x8007000e) på grund av minnesläcka

## <a name="agent-version-3100"></a>Agentversion 3.1.0.0
Följande viktiga information gäller 3.1.0.0 av Azure File Sync-agenten (gavs ut den 19 juli 2018).

### <a name="evaluation-tool"></a>Utvärderingsverktyg för
Innan du distribuerar Azure File Sync, bör du utvärdera om den är kompatibel med ditt system med hjälp av verktyget Azure File Sync-utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med ditt filsystem och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Anvisningarna för installation och användning, finns i [Evaluation Tool](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) avsnitt i Planeringsguiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [planera för distribution av Azure File Sync](storage-sync-files-planning.md) och [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet måste installeras med förhöjd behörighet (admin) behörigheter.
- Agenten stöds inte på distributionsalternativen för Windows Server Core eller Nano Server.
- Agenten stöds endast på Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GB fysiskt minne.
- Storage Sync-agenten (FileSyncSvc)-tjänsten stöder inte serverslutpunkter som finns på en volym med system volume information (SVI) directory komprimeras. Den här konfigurationen leder till oväntade resultat.
- FIPS-läge stöds inte och måste inaktiveras. 

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Använd inte hanteraren för filserverresurser eller andra filgaller. Filgaller kan orsaka oändliga synkroniseringsfel om filer blockeras på grund av filgallret.
- Kör sysprep på en server som har Azure File Sync-agenten installerad stöds inte och kan leda till oväntade resultat. Agentregistreringen för installation och server ska inträffa efter distribution av server-avbildning och slutföra sysprep mini-installationen.
- Datadeduplicering och lagringsnivåer för moln stöds inte på samma volym.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
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
 
### <a name="server-endpoint"></a>Server-slutpunkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåindelade filer blir oanvändbara om filerna inte återställas innan du tar bort Serverslutpunkten.
- Molnet lagringsnivåer stöds inte på systemvolymen. Inaktivera molnlagringsnivåer när du skapar Serverslutpunkten för att skapa en serverslutpunkt på systemvolymen.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte ett operativsystem eller ett programs växlingsfil i en serverslutpunkt.
- Servernamnet i portalen uppdateras inte om servern har bytt namn.

### <a name="cloud-endpoint"></a>Molnslutpunkt
- Azure File Sync stöder gör ändringar i Azure-filresursen direkt. Ändringar som görs på Azure-filresursen måste dock först identifieras av ett jobb med Azure File Sync ändra identifiering. Ett jobb för identifiering av ändring initieras för en molnslutpunkt en gång per dygn. Dessutom kommer inte att uppdatera SMB tid för senaste ändring ändringar som gjorts i en Azure-filresurs via REST-protokollet och kan inte ses som en ändring av synkronisering.
- Storage sync-tjänsten och/eller storage-konto kan flyttas till en annan resursgrupp eller prenumeration inom de befintliga Azure AD-klienten. Om lagringskontot har flyttats, måste du ge Hybrid Filsynkroniseringstjänstens åtkomst till lagringskontot (se [se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

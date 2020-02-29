---
title: Viktig information för Azure File Sync agenten | Microsoft Docs
description: Viktig information för Azure File Sync agenten.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 51b7b37466ae8a6988ee4bfa85a054fb42d23124
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920764"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Viktig information för Azure File Sync agent
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Dina Windows Server-installationer omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande versioner av Azure File Sync-agenten stöds:

| Gränser | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| Samlad uppdatering från december 2019 – [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 december 2019 | Stöds |
| V9-version – [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 december 2019 | Stöds |
| V8-version – [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 oktober 2019 | Stöds |
| Samlad uppdatering 2019 – [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 juli 2019 | Stöds |
| Samlad uppdatering 2019 – [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 juli 2019 | Stöds |
| V7-version – [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 juni 2019 | Stöds |
| Samlad uppdatering för 2019 juni – [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 juni 2019 | Stöds |
| Samlad uppdatering för 2019 juni – [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 juni 2019 | Stöds |
| Samlad uppdatering för 2019 maj – [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 maj 2019 | Stöds |
| V6-version – [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 april 2019 | Stöds |
| Samlad uppdatering från april 2019 – [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 april 2019 | Agent versionen som stöds upphör att gälla den 18 mars 2020 |
| Samlad uppdatering från mars 2019 – [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 mars 2019 | Agent versionen som stöds upphör att gälla den 18 mars 2020 |
| V5-version – [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 februari 2019 | Agent versionen som stöds upphör att gälla den 18 mars 2020 |
| V4-version | 4.0.1.0 - 4.3.0.0 | Saknas | Stöds inte-agent versioner upphörde att gälla den 6 november 2019 |
| V3-version | 3.1.0.0 - 3.4.0.0 | Saknas | Stöds inte-agent versioner har upphört att gälla den 19 augusti 2019 |
| För GA-agenter | 1.1.0.0 – 3.0.13.0 | Saknas | Stöds inte-agent versioner har upphört att gälla den 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-9100"></a>9\.1.0.0 för agent version
Följande viktiga information gäller version 9.1.0.0 av Azure File Sync agent som lanserades den 12 december 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 9.0.0.0.

Problem korrigerat i den här versionen:  
- Synkroniseringen Miss lyckas med ett av följande fel efter uppgraderingen till Azure File Sync agent version 9,0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>9\.0.0.0 för agent version
Följande viktig information gäller version 9.0.0.0 av Azure File Sync agent (lanserades 2 december 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Support för självbetjänings återställning
    - Användarna kan nu återställa sina filer med hjälp av funktionen för tidigare versioner. Innan v9-versionen stöds inte den tidigare versions funktionen på volymer där moln skiktning är aktiverat. Den här funktionen måste vara aktive rad för varje volym separat, på vilken en slut punkt med aktive rad moln nivå finns. Mer information finns i  
[Återställning via självbetjäning genom tidigare versioner och VSS (tjänsten Volume Shadow Copy)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Stöd för större fil resurs storlekar 
    - Azure File Sync stöder nu upp till 64TiB-och 100 000 000-filer i ett enda, syncing-namnområde.  
 
- Stöd för datadeduplicering på Server 2019 
    - Datadeduplicering stöds nu med moln nivåer som är aktiverade på Windows Server 2019. Windows Update- [KB4520062](https://support.microsoft.com/help/4520062) måste vara installerat för att stödja datadeduplicering på volymer med moln nivåer. 
 
- Förbättrad minsta fil storlek för en fil till nivå 
    - Den minsta fil storleken för en fil till-nivån baseras nu på fil systemets kluster storlek (dubbelt fil systemets kluster storlek). Som standard är till exempel fil systemets NTFS-storlek 4KB, den resulterande minsta fil storleken för en fil till nivån är 8 KB. 
 
- Test-cmdlet för nätverks anslutning 
    - Som en del av Azure File Sync konfiguration måste flera tjänst slut punkter kontaktas. De har sina egna DNS-namn som måste vara tillgängliga för-servern. Dessa URL: er är också speciella för regionen som en server är registrerad på. När en server har registrerats kan anslutnings test cmdlet (PowerShell och Server registrerings verktyg) användas för att testa kommunikationen med alla URL: er som är kopplade till den här servern. Den här cmdleten kan hjälpa till att felsöka när ofullständig kommunikation förhindrar att servern fungerar fullt ut med Azure File Sync och kan användas för att finjustera konfigurationer för proxy och brand väggar.  
 
        Kör följande PowerShell-kommandon för att köra testet för nätverks anslutning: 
 
        Importera-modul "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Ta bort Server slut punkts förbättring när moln skiktning är aktiverat 
    - Som tidigare resulterar det inte i att ta bort filer i Azure-filresursen om du tar bort en server slut punkt. Beteendet för referens punkter på den lokala servern har dock ändrats. Referens punkter (pekare till filer som inte är lokala på servern) tas nu bort när du tar bort en server slut punkt. De fullständigt cachelagrade filerna är kvar på servern. Den här förbättringen gjordes för att förhindra [överblivna filer på nivån](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) när en server slut punkt togs bort. Om Server slut punkten återskapas, kommer referens punkterna för de nivåbaserade filerna att återskapas på servern.  
 
- Förbättringar av prestanda och tillförlitlighet 
    - Minskat återställnings problem. Åter kallelse storleken justeras nu automatiskt baserat på nätverks bandbredden. 
    - Förbättrad nedladdnings prestanda när du lägger till en ny server i en Sync-grupp. 
    - Mindre filer synkroniseras inte på grund av begränsnings konflikter. 
    - Filerna går inte att flytta till nivån eller återkallas i vissa fall, om Server slut punkten är en volym monterings punkt.
    
### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte för distributions alternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
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
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåbaserade filer blir otillgängliga om filerna inte återkallas innan Server slut punkten tas bort. Återskapar Server slut punkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Server slut punkten togs bort, eller om moln slut punkten togs bort, kommer filer som inte återkallats att bli oanvändbara. Mer information finns i [skiktade filer är inte tillgängliga på servern när du har tagit bort en server slut punkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagrings kontot kan flyttas till en annan resurs grupp eller prenumeration i den befintliga Azure AD-klienten. Om lagrings kontot har flyttats måste du ge hybrid File Sync tjänst åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync har inte stöd för att flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.
- Filer kan Miss lyckas på nivån om pagefile. sys finns på en volym som har aktiverat moln skiktning. Pagefile. sys bör finnas på en volym där moln nivån är inaktive rad.

## <a name="agent-version-8000"></a>8\.0.0.0 för agent version
Följande viktig information gäller version 8.0.0.0 av Azure File Sync agent (lanserades den 8 oktober 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Återställa prestanda förbättringar
    - Snabbare återställnings tider för återställning klar genom Azure Backup. Återställda filer synkroniseras tillbaka till Azure File Sync servrar mycket snabbare. 
- Förbättrad Portal upplevelse för moln nivåer  
    - Om du har på nivå av filer som inte kan återkallas kan du nu Visa återställnings felen i egenskaperna för Server slut punkten. Dessutom visar Server slut punkt hälsan ett fel och en minskning av stegen om moln skikts filter driv rutinen inte har lästs in på servern.
- Enklare agent installation
    - Az\AzureRM PowerShell-modulen krävs inte längre för att registrera servern som gör installationen enklare och snabbt.
- Diverse förbättringar av prestanda och tillförlitlighet

### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte för distributions alternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
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
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåbaserade filer blir otillgängliga om filerna inte återkallas innan Server slut punkten tas bort. Återskapar Server slut punkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Server slut punkten togs bort, eller om moln slut punkten togs bort, kommer filer som inte återkallats att bli oanvändbara. Mer information finns i [skiktade filer är inte tillgängliga på servern när du har tagit bort en server slut punkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagrings kontot kan flyttas till en annan resurs grupp eller prenumeration i den befintliga Azure AD-klienten. Om lagrings kontot har flyttats måste du ge hybrid File Sync tjänst åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync har inte stöd för att flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.

## <a name="agent-version-7200"></a>7\.2.0.0 för agent version
Följande viktiga information gäller version 7.2.0.0 av Azure File Sync agent som lanserades den 24 juli 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 7.0.0.0.

Lista med problem som korrigeras i den här versionen:  
- FileSyncSvc (Storage Sync agent) kraschar om proxykonfigurationen är null.
- Server slut punkten startar BCDR (Error 0x80c80257-ECS_E_BCDR_IN_PROGRESS) om flera slut punkter på servern har samma namn.
- Förbättringar av distribution av moln nivåer.

## <a name="agent-version-7100"></a>7\.1.0.0 för agent version
Följande viktig information gäller version 7.1.0.0 av den Azure File Sync agenten som publicerades den 12 juli 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 7.0.0.0.

Lista med problem som korrigeras i den här versionen:  
- Det går långsamt att komma åt eller bläddra i en server slut punkt via SMB i Windows Server 2012 R2. 
- Ökad processor användning när du har installerat Azure File Sync V6-agenten.
- Förbättringar av telemetri för moln nivåer.
- Diverse förbättringar av tillförlitlighet för moln nivåer och synkronisering.

## <a name="agent-version-7000"></a>7\.0.0.0 för agent version
Följande viktiga information gäller version 7.0.0.0 av Azure File Sync agent (återutgiven 19 juni 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Stöd för större fil resurs storlekar
    - Med för hands versionen av större Azure-filresurser ökar vi även våra support begränsningar för filsynkronisering. I det första steget har Azure File Sync nu stöd för upp till 25 TB-och 50 000 000-filer i ett enda, syncing-namnområde. Om du vill använda för för hands versionen av den stora fil resursen fyller du i det här formuläret https://aka.ms/azurefilesatscalesurvey. 
- Stöd för inställningar för brand vägg och virtuellt nätverk på lagrings konton
    - Azure File Sync stöder nu inställningen brand vägg och virtuellt nätverk på lagrings konton. Om du vill konfigurera distributionen så att den fungerar med inställningen brand vägg och virtuellt nätverk, se [Konfigurera inställningar för brand vägg och virtuellt nätverk](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- PowerShell-cmdlet för att omedelbart synkronisera filer som ändrats i Azure-filresursen
    - Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten Invoke-AzStorageSyncChangeDetection för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Denna cmdlet är avsedd för scenarier där någon typ av automatiserad process gör ändringar i Azure-filresursen eller ändringarna utförs av en administratör (t. ex. att flytta filer och kataloger till resursen). För slut användar ändringar är rekommendationen att installera Azure File Sync agenten i en virtuell IaaS-dator och har slutanvändarna åtkomst till fil resursen via den virtuella datorn IaaS. På så sätt synkroniseras alla ändringar snabbt till andra agenter utan att du behöver använda cmdleten Invoke-AzStorageSyncChangeDetection. Mer information finns i [Invoke-AzStorageSyncChangeDetection-](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) dokumentationen.
- Förbättrad Portal upplevelse om du stöter på filer som inte synkroniseras
    - Om du har filer som inte kan synkroniseras skiljer vi nu mellan tillfälliga och beständiga fel i portalen. Tillfälliga fel löses vanligt vis utan att administratören behöver göra något. En fil som används för tillfället kommer till exempel inte att synkroniseras förrän fil handtaget stängs. För beständiga fel visar vi nu antalet filer som påverkas av varje fel. Antalet beständiga fel visas också i kolumnen filer som inte synkroniseras för alla Server slut punkter i en Sync-grupp.
- Förbättrad Azure Backup Återställning på fil nivå
    - Enskilda filer som återställs med hjälp av Azure Backup identifieras och synkroniseras nu till Server slut punkten snabbare.
- Förbättrad återställnings-cmdlet för åter kallelse av moln nivåer 
    - Cmdleten Invoke-StorageSyncFileRecall gör det nu möjligt för kunder att ange antal per fil och försök igen per fil, ungefär som Robocopy. Tidigare skulle denna cmdlet återkalla alla skiktade filer under en specifik sökväg i slumpmässig ordning. Med parametern New-order kommer den här cmdleten att återkalla de hetaste data först och följa principen för molnnivå (stoppa återanrop om datum principen är uppfylld eller om volymens lediga utrymme är uppfyllt, beroende på vilket som sker först).
- Stöd för TLS 1,2 (TLS 1,0 och 1,1 är inaktiverat)
    - Azure File Sync stöder nu endast TLS 1,2 på servrar med TLS 1,0 och 1,1 inaktiverade. Innan den här förbättringen skulle Server registreringen bli misslyckad om TLS 1,0 och 1,1 har inaktiverats på servern.
- Diverse förbättringar av prestanda och tillförlitlighet för synkronisering och moln nivåer
    - Det finns flera förbättringar av tillförlitlighet och prestanda i den här versionen. En del av dem är avsedda att göra moln nivåer mer effektiva och Azure File Sync hela tiden bättre i dessa situationer när du har en schema uppsättning för bandbredds begränsning.

### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte för distributions alternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
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
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåbaserade filer blir otillgängliga om filerna inte återkallas innan Server slut punkten tas bort. Återskapar Server slut punkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Server slut punkten togs bort, eller om moln slut punkten togs bort, kommer filer som inte återkallats att bli oanvändbara.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagrings kontot kan flyttas till en annan resurs grupp eller prenumeration i den befintliga Azure AD-klienten. Om lagrings kontot har flyttats måste du ge hybrid File Sync tjänst åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync har inte stöd för att flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.

## <a name="agent-version-6300"></a>6\.3.0.0 för agent version
Följande viktig information gäller version 6.3.0.0 av Azure File Sync agent som publicerades 27 juni 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 6.0.0.0.

Lista med problem som korrigeras i den här versionen:  
- Det går långsamt att komma åt eller bläddra i en server slut punkt via SMB i Windows Server 2012 R2 
- Ökad processor användning efter installation av Azure File Sync V6-agenten
- Förbättringar av telemetri för moln nivåer

## <a name="agent-version-6200"></a>6\.2.0.0 för agent version
Följande viktiga information gäller version 6.2.0.0 av Azure File Sync agent som publicerades 13 juni 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 6.0.0.0.

Lista med problem som korrigeras i den här versionen:  
- När du har skapat en server slut punkt kan hög processor användning uppstå när bakgrunds återkallande laddar ned filer till servern
- Synkroniserings-och moln nivå åtgärder kan Miss lyckas med fel ECS_E_SERVER_CREDENTIAL_NEEDED på grund av att token upphör att gälla
- Det går inte att återställa en fil om URL: en för att hämta filen innehåller reserverade tecken 

## <a name="agent-version-6100"></a>6\.1.0.0 för agent version
Följande viktig information gäller version 6.1.0.0 av den Azure File Sync agent som lanserades den 6 maj 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 6.0.0.0.

Lista med problem som korrigeras i den här versionen:  
- Windows administrations Center kan inte Visa agent versionen och Server slut punkts konfigurationen på servrar som har Azure File Sync agent version 6,0 installerad.

## <a name="agent-version-6000"></a>6\.0.0.0 för agent version
Följande viktig information gäller version 6.0.0.0 av Azure File Sync agent (lanserades den 22 april 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Stöd för automatisk uppdatering av agent
  - Vi har hört din feedback och lagt till en funktion för automatisk uppdatering i Azure File Sync Server agenten. Mer information finns i [Azure File Sync agent uppdaterings princip](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Stöd för ACL: er för Azure-filresurs
  - Azure File Sync har alltid stöd för att synkronisera ACL: er mellan server slut punkter, men ACL: er har inte synkroniserats till moln slut punkten (Azure-filresurs). Den här versionen lägger till stöd för att synkronisera ACL: er mellan server-och moln slut punkter.
- Parallell uppladdning och hämtning av Sync-sessioner för en server slut punkt 
  - Server slut punkter stöder nu överföring och hämtning av filer på samma gång. Ingen mer väntan på att nedladdningen ska slutföras så att filer kan överföras till Azure-filresursen. 
- Nya cmdletar för moln nivåer för att hämta volym-och nivå status
  - Två nya, Server-lokala PowerShell-cmdlets kan nu användas för att hämta information om moln skiktning och återställning av filer. De gör det möjligt att logga information från två händelse kanaler på servern:
    - Get-StorageSyncFileTieringResult visar en lista över alla filer och deras sökvägar som inte har skiktat och rapporterar på orsaken till varför.
    - Get-StorageSyncFileRecallResult rapporterar alla fil åter kallelse händelser. Den visar en lista över alla filer som har återkallats och dess sökväg samt lyckad eller fel som återkallas.
  - Som standard kan båda händelse kanalerna lagra upp till 1 MB – du kan öka mängden filer som rapporteras genom att öka händelse kanal storleken.
- Stöd för FIPS-läge
  - Azure File Sync har nu stöd för att aktivera FIPS-läge på servrar där Azure File Sync-agenten är installerad.
    - Innan du aktiverar FIPS-läge på servern installerar du Azure File Sync-agenten och [PackageManagement-modulen](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) på servern. Om FIPS redan är aktiverat på servern laddar du [ned](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) PackageManagement- [modulen](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) manuellt till servern.
- Diverse förbättringar av tillförlitlighet för moln nivåer och synkronisering

### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte för distributions alternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
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
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåbaserade filer blir otillgängliga om filerna inte återkallas innan Server slut punkten tas bort. Återskapar Server slut punkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Server slut punkten togs bort, eller om moln slut punkten togs bort, kommer filer som inte återkallats att bli oanvändbara.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagrings kontot kan flyttas till en annan resurs grupp eller prenumeration i den befintliga Azure AD-klienten. Om lagrings kontot har flyttats måste du ge hybrid File Sync tjänst åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync har inte stöd för att flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

## <a name="agent-version-5200"></a>5\.2.0.0 för agent version
Följande viktig information gäller version 5.2.0.0 av Azure File Sync agent som lanserades 4 april 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 5.0.2.0.

Lista med problem som korrigeras i den här versionen:  
- Tillförlitlighets förbättringar för funktionerna för data överföring offline och data överföring
- Förbättringar av synkronisering av telemetri

## <a name="agent-version-5100"></a>5\.1.0.0 för agent version
Följande viktiga information gäller version 5.1.0.0 av Azure File Sync agent som lanserades 7 mars 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 5.0.2.0.

Lista med problem som korrigeras i den här versionen:  
- Filer kan Miss lyckas med att synkronisera med fel 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) om ändrings uppräkningen Miss lyckas på servern
- Om en Sync-session eller-fil får ett fel 0x80072F78 (WININET_E_INVALID_SERVER_RESPONSE) försöker synkroniseringen nu utföras igen
- Det går inte att synkronisera filer med fel 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Hög minnes användning kan uppstå när filer anropas
- Förbättringar av telemetri för moln nivåer 

## <a name="agent-version-5020"></a>5\.0.2.0 för agent version
Följande viktiga information gäller version 5.0.2.0 av Azure File Sync agent (lanserades 12 februari 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Stöd för Azure Government Cloud
  - Vi har lagt till för hands versions stöd för Azure Government molnet. Detta kräver en prenumeration med en vit lista och en särskild agent hämtning från Microsoft. För att få åtkomst till för hands versionen kan du skicka e-post till oss direkt på [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Stöd för datadeduplicering
    - Datadeduplicering stöds nu fullt ut med moln nivåer som är aktiverade på Windows Server 2016 och Windows Server 2019. Genom att aktivera deduplicering på en volym med aktive rad moln nivå kan du cachelagra fler filer lokalt utan att tillhandahålla mer lagrings utrymme.
- Stöd för data överföring offline (t. ex. via Data Box-enhet)
    - Migrera enkelt stora mängder data till Azure File Sync via det sätt du väljer. Du kan välja Azure Data Box, AzCopy och andra migrerings tjänster från tredje part. Du behöver inte använda enorma mängder bandbredd för att hämta dina data till Azure, när det gäller Data Box-enhet – skicka det dit! Mer information finns i [Offline dataöverföring dokument](https://aka.ms/AFS/OfflineDataTransfer).
- Förbättrad prestanda för synkronisering
    - Kunder med flera Server slut punkter på samma volym kan ha drabbats av långsam synkronisering före den här versionen. Azure File Sync skapar en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser. Sync stöder nu flera Server slut punkter som synkroniseras på en volym när en VSS-Sync-session är aktiv. Det går inte att vänta på att VSS-synkroniseringen ska slutföras, så synkronisering kan återupptas på andra server slut punkter på volymen.
- Förbättrad övervakning i portalen
    - Diagram har lagts till i portalen för Storage Sync-tjänsten för att visa:
        - Antal filer som har synkroniserats
        - Storlek på överförda data
        - Antal filer som inte synkroniseras
        - Storlek på data som återkallas
        - Status för Server anslutning
    - Mer information finns i [övervaka Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Förbättrad skalbarhet och tillförlitlighet
    - Maximalt antal fil system objekt (kataloger och filer) i en katalog har ökat till 1 000 000. Den tidigare gränsen var 200 000.
    - Synkronisering kommer att försöka återuppta data överföringen i stället för att skicka om en överföring avbryts för stora filer 

### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte på distributions alternativ för Windows Server Core eller Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.
- FIPS-läge stöds inte och måste inaktive ras. 

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
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
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåbaserade filer blir otillgängliga om filerna inte återkallas innan Server slut punkten tas bort. Återskapar Server slut punkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Server slut punkten togs bort, eller om moln slut punkten togs bort, kommer filer som inte återkallats att bli oanvändbara.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagrings kontot kan flyttas till en annan resurs grupp eller prenumeration i den befintliga Azure AD-klienten. Om lagrings kontot har flyttats måste du ge hybrid File Sync tjänst åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync har inte stöd för att flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

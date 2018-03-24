---
title: Viktig information om Azure File Sync-agenten (förhandsversionen) | Microsoft Docs
description: Viktig information om Azure filsynkronisering agent (förhandsversion).
services: storage
author: wmgries
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/12/2018
ms.author: wgries
ms.openlocfilehash: bb7fa68809341b5132d551ff1cab187bd4d7eeac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="release-notes-for-the-azure-file-sync-agent-preview"></a>Viktig information om Azure File Sync-agenten (förhandsversionen)
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Dina Windows Server-installationer omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande versioner av Azure File Sync-agenten stöds:

| Milstolpe | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| Samlad uppdatering för mars | 2.2.0.0 | 12 mars 2018 | Stöds (rekommenderad version) |
| Samlad uppdatering för februari | 2.1.0.0 | 28 februari 2018 | Stöds |
| Uppdatera 1 | 2.0.11.0 | 8 februari 2018 | Stöds |
| Samlad uppdatering i januari | 1.4.0.0 | 8 januari 2018 | Stöd till 8 maj 2018<sup>1</sup> |
| Samlad uppdatering i november | 1.3.0.0 | 30 november 2017 | Stöd till 8 maj 2018<sup>1</sup> |
| Samlad uppdatering för oktober | 1.2.0.0 | 31 oktober 2017 | Stöd till 8 maj 2018<sup>1</sup> |
| Inledande förhandsversion | 1.1.0.0 | 26 september 2017 | Stöd till 8 maj 2018<sup>1</sup> |

\[1\]: versioner av Azure filsynkronisering agenten under förhandsgranskningen avsiktligt inte överensstämmer med principen för uppdateringen. Uppdatera principen tillämpas från och med den första versionen av agenten när Azure filsynkronisering deklareras allmänt tillgänglig.

### <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-2200"></a>Agentversionen 2.2.0.0
Följande information gäller version av 2.2.0.0 av Azure filsynkronisering agent 12 mars 2018.  Dessa anteckningar är förutom viktig information för version 2.1.0.0 och 2.0.11.0

Installation av v2.1.0.0 för vissa kunder misslyckas på grund av FileSyncSvc inte stoppas. Den här uppdateringen löser problemet.

## <a name="agent-version-2100"></a>Agentversion 2.1.0.0
Följande viktiga information gäller agentversion 2.1.0.0 av Azure File Sync som gavs ut den 28 februari 2018. Detta är kompletterande information till den viktiga informationen för version 2.0.11.0.

Den här versionen innehåller följande ändringar:
- Förbättring av hantering av klusterredundans.
- Förbättring av den tillförlitliga hanteringen av nivåindelade filer.
- Stöd för agentinstallation på domänkontrollantdatorer tillagt till en Windows Server 2008 R2-domänmiljö.
- Åtgärdat i den här versionen: för hög diagnostikgenerering på servrar med många filer.
- Förbättring av felhantering för sessionsfel.
- Förbättring av felhantering för filöverföringsfel.
- Ändrat i den här versionen: standardintervallet för att köra lagringsnivåer för moln när det är aktiverat på en serverslutpunkt är nu en timme. 
- Tillfälligt blockeringsproblem: flyttning av Azure File Sync-resurser (tjänst för synkronisering av lagring) till en ny Azure-prenumeration.

## <a name="agent-version-20110"></a>Agentversion 2.0.11.0
Följande viktiga information gäller agentversion 2.0.11.0 av Azure File Sync som gavs ut den 9 februari 2018. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för distribution av Azure File Sync (förhandsversionen)](storage-sync-files-planning.md) och [Så här distribuerar du Azure File Sync (förhandsversionen)](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet (MSI) måste installeras med förhöjda behörigheter (admin).
- Agenten stöds inte för distributionsalternativen för Windows Server Core eller Nano Server.
- Agenten stöds endast på Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GB fysiskt minne.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync (förhandsversionen)](storage-sync-files-troubleshoot.md).
- Den här versionen har även stöd för DFS-R. Mer information finns i [planeringsguiden](storage-sync-files-planning.md#distributed-file-system-dfs).
- Använd inte hanteraren för filserverresurser eller andra filgaller. Filgaller kan orsaka oändliga synkroniseringsfel om filer blockeras på grund av filgallret.
- Duplicering av registrerade servrar (inklusive kloning av virtuella datorer) kan leda till oväntade resultat. Det kan särskilt uppstå problem med konvergering av synkronisering.
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
 
### <a name="server-endpoints"></a>Serverslutpunkter
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- En serverslutpunkt kan inte finnas på systemvolymen. Till exempel är C:\MyFolder inte en giltig sökväg om inte C:\MyFolder är en monteringspunkt.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Den här versionen har stöd för att synkroniseringsroten är roten av en volym.
- Lagra inte ett operativsystem eller ett programs växlingsfil i en serverslutpunkt.
- Ändrat i den här versionen: vi har lagt till nya händelser för att spåra den totala körningstiden för lagringsnivåer för moln (EventID 9016), uppladdningsförloppet för synkronisering (EventID 9302) och filer som inte synkroniserades (EventID 9900).
- Förbättrade i den här versionen: 
- Snabb DR-namnområdet sync prestanda ökas avsevärt.
- Om du tar bort stort antal (över 10 000-tal) kataloger behöver inte göras i batchar med v2 *.
 
### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Ändrat jämfört med den tidigare versionen: nya filer nivåindelas på en timme och omfattas av principinställningen för nivåindelning (tidigare 32 timmar). Vi tillhandahåller en PowerShell-cmdlet för nivåindelning på begäran. Med denna cmdlet kan du utvärdera nivåindelningen effektivare utan att behöva vänta på bakgrundsåtgärden.
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.
- Ändrat jämfört med den tidigare versionen: filer laddas nu ned som nivåindelade filer på andra servrar förutsatt att filen är ny eller var en nivåindelad fil redan från början.

## <a name="agent-version-1100"></a>Agentversion 1.1.0.0
Följande viktiga information gäller agentversion 1.1.0.0 av Azure File Sync som gavs ut den 9 september 2017 (första förhandsversionen). 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för distribution av Azure File Sync (förhandsversionen)](storage-sync-files-planning.md) och [Så här distribuerar du Azure File Sync (förhandsversionen)](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet (MSI) måste installeras med förhöjda behörigheter (admin).
- Agenten stöds inte för distributionsalternativen för Windows Server Core eller Nano Server.
- Agenten stöds endast på Windows Server 2016 och 2012 R2.
- Agenten kräver minst 2 GB fysiskt minne.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync (förhandsversionen)](storage-sync-files-troubleshoot.md).
- Använd inte hanteraren för filserverresurser eller andra filgaller. Filgaller kan orsaka oändliga synkroniseringsfel om filer blockeras på grund av filgallret.
- Duplicering av registrerade servrar (inklusive kloning av virtuella datorer) kan leda till oväntade resultat. Det kan särskilt uppstå problem med konvergering av synkronisering.
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
 
### <a name="server-endpoints"></a>Serverslutpunkter
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- En serverslutpunkt kan inte finnas på systemvolymen. Till exempel är C:\MyFolder inte en giltig sökväg om inte C:\MyFolder är en monteringspunkt.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Om ett stort antal kataloger tas bort från en server på en gång (över 10 000) kan det orsaka synkroniseringsfel. Ta bort kataloger i omgångar om färre än 10 000 kataloger. Kontrollera att åtgärdssynkroniseringen har slutförts innan du tar bort fler.
- En serverslutpunkt stöds inte för närvarande på en rots volym.
- Lagra inte ett operativsystem eller ett programs växlingsfil i en serverslutpunkt.
 
### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- I syfte att säkerställa att filer kan återställas korrekt kanske systemet inte automatiskt delar in nya eller ändrade filer i lagringsnivåer under upp till 32 timmar. Den här processen omfattar den första indelningen efter konfiguration av en ny serverslutpunkt. Vi tillhandahåller en PowerShell-cmdlet för nivåindelning på begäran. Med denna cmdlet kan du utvärdera nivåindelningen effektivare utan att behöva vänta på bakgrundsåtgärden.
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

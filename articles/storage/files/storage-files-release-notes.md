---
title: Viktig information för Azure File Sync-agenten | Microsoft Docs
description: Viktig information om Azure File Sync-agenten.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 1/14/2019
ms.author: wgries
ms.component: files
ms.openlocfilehash: 245314f0b7460cce270c97307e0719beebee472a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478645"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Viktig information för Azure File Sync-agenten
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Dina Windows Server-installationer omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande versioner av Azure File Sync-agenten stöds:

| Milstolpe | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| Samlad - uppdatering i januari 2019 [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 januari 2019 | Stöds (rekommenderad version) |
| Samlad - uppdatering för december 2018 [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 december 2018 | Stöds |
| Samlad uppdatering för december 2018 | 4.1.0.0 | 4 december 2018 | Stöds |
| V4-version | 4.0.1.0 | Den 13 november 2018 | Stöds |
| Samlad uppdatering september 2018 | 3.3.0.0 | 24 september 2018 | Stöds |
| Samlad uppdatering augusti 2018 | 3.2.0.0 | 15 augusti 2018 | Stöds |
| Allmän tillgänglighet | 3.1.0.0 | 19 juli 2018 | Stöds |
| Agenter som har upphört att gälla | 1.1.0.0 - 3.0.13.0 | Gäller inte | Inte stöd för – agentversioner har upphört att gälla den 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

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
- Servernamnet i portalen uppdateras inte om servern har bytt namn. Uppdatera servernamnet i portalen genom att avregistrera och registrera om servern.

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
- Servernamnet i portalen uppdateras inte om servern har bytt namn. Uppdatera servernamnet i portalen genom att avregistrera och registrera om servern.

### <a name="cloud-endpoint"></a>Molnslutpunkt
- Azure File Sync stöder gör ändringar i Azure-filresursen direkt. Ändringar som görs på Azure-filresursen måste dock först identifieras av ett jobb med Azure File Sync ändra identifiering. Ett jobb för identifiering av ändring initieras för en molnslutpunkt en gång per dygn. Dessutom kommer inte att uppdatera SMB tid för senaste ändring ändringar som gjorts i en Azure-filresurs via REST-protokollet och kan inte ses som en ändring av synkronisering.
- Storage sync-tjänsten och/eller storage-konto kan flyttas till en annan resursgrupp eller prenumeration inom de befintliga Azure AD-klienten. Om lagringskontot har flyttats, måste du ge Hybrid Filsynkroniseringstjänstens åtkomst till lagringskontot (se [se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.

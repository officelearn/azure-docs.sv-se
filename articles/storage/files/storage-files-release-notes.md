---
title: Viktig information om Azure File Sync-agenten | Microsoft Docs
description: Viktig information om Azure File Sync
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: tamram
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 1db3fef17e24022bff59665558f4a354f8c37d1d
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="azure-file-sync-agent-release-notes"></a>Viktig information om Azure File Sync-agenten
Med Azure File Sync (förhandsversionen) kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Detta möjliggörs genom att Windows-servern omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande versioner av Azure File Sync stöds:

| Agentversionsnummer | Utgivningsdatum | Stöds till och med |
|----------------------|--------------|------------------|
| 1.1.0.0 | 2017-09-26 | Aktuell version |

### <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-1100"></a>Agentversion 1.1.0.0
Följande information gäller agentversion 1.1.0.0 som gavs ut den 9 september 2017. Den här versionen är den första förhandsversionen av Azure File Sync!

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för distribution av Azure File Sync (förhandsgranskning)](storage-sync-files-planning.md) och [Så här distribuerar du Azure File Sync (förhandsgranskning)](storage-sync-files-deployment-guide.md).

- Agentinstallationspaketet (MSI) måste installeras med förhöjda behörigheter (admin).
- Stöds inte på distributionsalternativen för Windows Server Core eller Nano Server.
- Stöds endast på Windows Server 2016 och 2012 R2.
- Agenten kräver minst 2 GB fysiskt minne.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [Felsöka Azure File Sync (förhandsgranskning)](storage-sync-files-troubleshoot.md)
- Använd inte filgaller för hanteraren för filserverresurser (eller andra filgaller): filgaller kan orsaka oändliga synkroniseringsfel om filer blockeras på grund av filgallret.
- Duplicering av en registrerad server (inklusive kloning av virtuella datorer) kan leda till oväntade resultat (det kan särskilt uppstå problem med konvergering av synkronisering).
- Datadeduplicering och lagringsnivåer för moln stöds inte på samma volym.
 
### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Sökvägar som är längre än 2048 tecken
- DACL-delen av en säkerhetsbeskrivning om den är större än 2K (det här är endast ett problem om du har mer än runt 40 åtkomstkontrollposter på ett enskilt objekt)
- SACL-delen av en säkerhetsbeskrivning (används för granskning)
- Utökade attribut
- Alternativa dataströmmar
- Referenspunkter
- Hårda länkar
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att vår tjänst läser data 
    
    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring och vilande data kan vara krypterade i Azure.
 
### <a name="server-endpoints"></a>Serverslutpunkter
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- En serverslutpunkt kan inte finnas på systemvolymen (till exempel är C:\MyFolder inte en giltig sökväg om inte C:\MyFolder är en monteringspunkt).
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade, men de får finnas på samma volym parallellt med varandra.
- Att ta bort ett stort antal kataloger från en server på en gång (över 10 000) kan orsaka synkroniseringsfel. Ta bort kataloger i omgångar om färre än 10 000 kataloger och kontrollera att åtgärdssynkroniseringen har slutförts innan du tar bort fler.
- Stöds inte på en volyms rot.
- Lagra inte ett operativsystem eller ett programs växlingsfil i en serverslutpunkt.
 
### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- För att säkerställa att filer kan återställas korrekt kanske systemet inte automatiskt delar in nya eller ändrade filer i lagringsnivåer under upp till 32 timmar, inklusive efter att den första indelningen i lagringsnivåer sker efter konfiguration av en ny serverslutpunkt. Vi erbjuder en PowerShell-cmdlet för nivåindelning på begäran så att du kan utvärdera nivåindelningen mer effektivt utan att behöva vänta på bakgrundsprocesser.
- Om en nivåindelad fil är kopierad till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad men offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du visar filegenskaper på en SMB-klient kan det verka som att offline-attributet har konfigurerats felaktigt på grund av SMB-cachelagring av filens metadata.
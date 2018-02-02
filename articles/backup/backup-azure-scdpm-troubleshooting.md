---
title: "Felsöka System Center Data Protection Manager med Azure Backup | Microsoft Docs"
description: "Felsöka problem i System Center Data Protection Manager."
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Felsöka System Center Data Protection Manager

Du hittar den senaste viktig information för SC DPM [här](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016).
Skydd stöd Matrix kan hittas [här](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="replica-is-inconsistent"></a>Repliken är inkonsekvent

Det här felet kan inträffa av olika skäl, till exempel - replikeringsjobb misslyckades, problem med att ändra journalen volym nivån bitmappen filterfel, källdatorn stängdes av oväntat, spill synkroniseringsloggen eller repliken är inkonsekvent verkligen. Följ dessa steg för att lösa problemet:
- Kör konsekvenskontroll manuellt eller schemalägga en daglig konsekvenskontroll för att ta bort inkonsekvent status.
- Se till att du är den senaste versionen av MAB Server eller System Center DPM
- Kontrollera att automatiska konsekvenskontroller är aktiverat
- Försök att starta om tjänsterna från Kommandotolken (”net stop dpmra” följt av ”net start dpmra”)
- Kontrollera anslutning och bandbredd krav uppfylls
- Kontrollera om källdatorn stängdes av oväntat
- Se till att disken är felfri och det finns tillräckligt med diskutrymme för replik
- Se till att inga dubbla säkerhetskopieringsjobb körs samtidigt

## <a name="online-recovery-point-creation-failed"></a>Skapa en online-återställningspunkt

Följ dessa steg för att lösa problemet:
- Se till att du är den senaste versionen av Azure Backup Agent
- Försök att skapa en återställningspunkt manuellt i skyddsaktivitetsområdet
- Se till att du kör konsekvenskontroll på datakällan
- Kontrollera anslutning och bandbredd krav uppfylls
- Replikdata är i inkonsekvent tillstånd. Skapa en återställningspunkt för disk av denna datakälla
- Se till att replikeringen är närvarande och inte saknas
- Repliken har tillräckligt med utrymme för att skapa USN-journalen

## <a name="unable-to-configure-protection"></a>Det gick inte att konfigurera skydd

Det här felet uppstår när DPM-servern inte kan kontakta den skyddade servern. Följ dessa steg för att lösa problemet:
- Kontrollera att du är på den senaste versionen av Azure Backup-agenten
- Se till att det finns en anslutning (proxy-nätverk/brandvägg) mellan DPM-servern och skyddade servern
- Om du skyddar SQL Server, kontrollera NT AUTHORITY\SYSTEM har sysadmin aktiverat från inloggningsegenskaper

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>Den här servern är inte registrerad på valvet som är angivet av valvautentiseringen

Det här felet uppstår när valvautentiseringsfilen som valts inte tillhör Recovery Services-valvet som är associerade med System Center DPM / Azure Backup-Server där återställningen utförs. Följ dessa steg för att lösa problemet:
- Hämta valvautentiseringsfilen från Recovery Services-valvet som System Center DPM / Azure Backup Server är registrerad.
- Prova att registrera servern med valvet med senaste hämtade valvautentiseringsfilen.

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>Återställningsbara data är inte tillgänglig eller den valda servern är inte en DPM-server
Det här felet visas när det finns inga andra System Center DPM / Azure Backup-servrar registrerade Recovery Services-valvet servrar har ännu inte överföra metadata eller den valda servern inte är en System Center DPM / Azure Backup Server.
- Om det finns andra System Center DPM / Azure Backup-servrar registrerade Recovery Services-valvet, se till att den senaste Azure Backup-agenten är installerad.
- Om andra System Center DPM eller Azure Backup-servrar registrerade Recovery Services-valv, vänta på en dag efter installationen för att starta återställningsprocessen. Nattetid filöverföringar metadata för alla skyddade säkerhetskopieringar till molnet. Data är tillgängliga för återställning.

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>Den angivna krypteringslösenfrasen matchar inte lösenfrasen som associeras med följande server

> [!NOTE]
>Om du har glömt/förlorat krypteringslösenfrasen kan sedan går det inte att återställa data. Det enda alternativet är att återskapa lösenfrasen och använder för att kryptera framtida säkerhetskopierade data.
>
>

Det här felet visas när krypteringslösenfrasen används håller på att kryptera data från System Center DPM eller den angivna krypteringslösenfrasen matchar inte i Azure Backup Server-data som återställs. Agenten är det gick inte att dekryptera data. Därför misslyckas återställningen. Följ dessa steg för att lösa problemet:
- Ange exakt samma krypteringslösenfrasen som är associerade med System Center DPM / Azure Backup Server vars data återställs. 

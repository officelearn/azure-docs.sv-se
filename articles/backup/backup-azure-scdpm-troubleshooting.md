---
title: Felsöka System Center Data Protection Manager med Azure Backup | Microsoft Docs
description: Felsöka problem i System Center Data Protection Manager.
services: backup
documentationcenter: ''
author: adigan
manager: shreeshd
editor: ''
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 8f8117b216dcbda217bc433e643090e8eb47cf57
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Felsöka System Center Data Protection Manager

Den här artikeln beskrivs lösningar på problem som kan uppstå när du använder Data Protection Manager.

Senaste viktig information för System Center Data Protection Manager, finns det [dokumentation för System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Du kan lära dig mer om stöd för Data Protection Manager i [matrisen](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Fel: Repliken är inkonsekvent

En replik blir inkonsekvent av följande skäl:
- Det går inte att skapa replikeringar.
- Det finns problem med att ändra journalen.
- Volymen nivån filter bitmappen innehåller fel.
- Källdatorn avslutas oväntat.
- Synkroniseringsloggen är full.
- Repliken är inkonsekvent verkligen.

Lös problemet genom att utföra följande åtgärder:
- Om du vill ta bort inkonsekvent status, kör en konsekvenskontroll manuellt eller schemalägga en daglig konsekvenskontroll.
- Se till att du använder den senaste versionen av Microsoft Azure Backup Server och Data Protection Manager.
- Se till att den **automatisk konsekvenskontroll** inställningen är aktiverad.
- Försök att starta om tjänsterna från Kommandotolken. Använd den `net stop dpmra` kommandot följt av `net start dpmra`.
- Se till att uppnå nätverkskraven för anslutningar och bandbredd.
- Kontrollera om källdatorn stängdes av oväntat.
- Se till att disken är felfri och att det finns tillräckligt med diskutrymme för repliken.
- Se till att det inte finns några dubbletter säkerhetskopieringsjobb som körs samtidigt.

## <a name="error-online-recovery-point-creation-failed"></a>Fel: Det gick inte att skapa onlineåterställningspunkt

Lös problemet genom att utföra följande åtgärder:
- Se till att du använder den senaste versionen av Azure Backup-agenten.
- Försök att manuellt skapa en återställningspunkt i avsnittet skyddsaktivitet.
- Se till att du kör en konsekvenskontroll på datakällan.
- Se till att uppnå nätverkskraven för anslutningar och bandbredd.
- När replikdata är i ett inkonsekvent tillstånd, skapa en återställningspunkt för disk av denna datakälla.
- Se till att replikeringen finns och inte saknas.
- Kontrollera att repliken har tillräckligt med utrymme för att skapa journalen update sekvens nummer (USN).

## <a name="error-unable-to-configure-protection"></a>Fel: Det gick inte att konfigurera skydd

Det här felet uppstår när Data Protection Manager-servern inte kan kontakta den skyddade servern. 

Lös problemet genom att utföra följande åtgärder:
- Se till att du använder den senaste versionen av Azure Backup-agenten.
- Se till att det finns anslutning (proxy-nätverk/brandvägg) mellan Data Protection Manager-servern och den skyddade servern.
- Om du skyddar en SQLServer, se till att den **inloggningsegenskaper** > **NT AUTHORITY\SYSTEM** egenskapen visar den **sysadmin** inställningen är aktiverad.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Fel: Servern inte registrerad som anges i valvautentiseringsfilen

Detta fel uppstår under återställningen för Data Protection Manager/Azure Backup server-data. Valvautentiseringsfilen som används i återställningsprocessen hör inte till Recovery Services-valvet för Data Protection Manager/Azure Backup-servern.

Utför de här stegen för att lösa problemet:
1. Hämta valvautentiseringsfilen från Recovery Services-valvet som Data Protection Manager/Azure Backup-servern är registrerad.
2. Försök att registrera servern med valvet med hjälp av nyligen hämtade valvautentiseringsfilen.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Fel: Ingen återställningsbara data eller valda servern inte Data Protection Manager-server

Det här felet uppstår av följande skäl:
- Inga andra Data Protection Manager/Azure Backup-servrar är registrerade på Recovery Services-valvet.
- Servrarna som inte har överförts metadata ännu.
- Den valda servern är inte en säkerhetskopiering av Data Protection Manager-/ Azure-server.

När andra säkerhetskopiering av Data Protection Manager-/ Azure-servrar är registrerade på Recovery Services-valvet, utföra dessa steg för att lösa problemet:
1. Kontrollera att den senaste Azure Backup-agenten är installerad.
2. När du har kontrollerat att den senaste agenten är installerad, vänta en dag innan du startar återställningsprocessen. Säkerhetskopiering nattetid Överför metadata för alla skyddade säkerhetskopieringar till molnet. Säkerhetskopierade data är sedan tillgängliga för återställning.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Fel: Angivna krypteringslösenfrasen matchar inte lösenfrasen för server

Det här felet inträffar under krypteringsprocessen vid återställning av Data Protection Manager/Azure Backup server-data. Den krypteringslösenfras som används i återställningsprocessen matchar inte serverns krypteringslösenfrasen. Detta innebär att agenten kan inte dekryptera data och återställningen misslyckas.

> [!IMPORTANT]
> Om du glömmer bort krypteringslösenfrasen, finns inga andra metoder för att återställa data. Det enda alternativet är att återskapa lösenfrasen. Du kan använda den nya lösenfrasen för att kryptera framtida säkerhetskopierade data.
>
> När du återställer data, alltid ge samma krypteringslösenfrasen som är associerad med Data Protection Manager/Azure Backup-servern. 
>

---
title: Felsöka System Center Data Protection Manager med Azure Backup
description: Felsöka problem i System Center Data Protection Manager.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 4108616e3ae41e2c88b74bb08d5f846c0035101f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236197"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Felsöka System Center Data Protection Manager

Den här artikeln beskriver lösningar på problem som kan uppstå när du använder Data Protection Manager.

De senaste viktig information för System Center Data Protection Manager, finns det [dokumentation för System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Du kan läsa mer om stöd för Data Protection Manager i [matrisen](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Fel: Replikeringen är inkonsekvent

En replik kan vara inkonsekvent av följande skäl:
- Det går inte att skapa replikeringar.
- Det finns problem med ändringsjournalen.
- Volymen filtret på sidnivå bitmappen innehåller fel.
- Källdatorn är oväntat stängs av.
- Synkroniseringsloggen-värdet.
- Repliken är inkonsekvent verkligen.

Lös problemet genom att utföra följande åtgärder:
- Om du vill ta bort inkonsekvent status, kör en konsekvenskontroll manuellt eller schemalägga en daglig konsekvenskontroll.
- Se till att du använder den senaste versionen av Microsoft Azure Backup Server och Data Protection Manager.
- Se till att den **automatisk konsekvenskontroll** inställningen är aktiverad.
- Försök att starta om tjänsterna från Kommandotolken. Använd den `net stop dpmra` -kommandot följt av `net start dpmra`.
- Se till att du uppfyller nätverkskraven för anslutning och bandbredd.
- Kontrollera om källdatorn stängdes oväntat.
- Se till att disken är felfri och att det finns tillräckligt med diskutrymme för repliken.
- Se till att det finns inga dubbla säkerhetskopieringsjobb som körs samtidigt.

## <a name="error-online-recovery-point-creation-failed"></a>Fel: Det gick inte att skapa onlineåterställningspunkt

Lös problemet genom att utföra följande åtgärder:
- Se till att du använder den senaste versionen av Azure Backup-agenten.
- Försök att manuellt skapa en återställningspunkt i avsnittet skyddsaktivitet.
- Se till att du kör en konsekvenskontroll på datakällan.
- Se till att du uppfyller nätverkskraven för anslutning och bandbredd.
- När data är i ett inkonsekvent tillstånd kan du skapa en diskåterställningspunkt av den här datakällan.
- Kontrollera att repliken är närvarande och inte saknas.
- Kontrollera att repliken har tillräckligt med utrymme för att skapa update sequence number (USN) journalen.

## <a name="error-unable-to-configure-protection"></a>Fel: Det gick inte att konfigurera skydd

Det här felet inträffar när Data Protection Manager-servern inte kan kontakta den skyddade servern. 

Lös problemet genom att utföra följande åtgärder:
- Se till att du använder den senaste versionen av Azure Backup-agenten.
- Se till att det finns en anslutning (nätverk/Brandvägg/proxy) mellan dina Data Protection Manager-servern och den skyddade servern.
- Om du skyddar en SQL-server kontrollerar du att den **inloggningsegenskaper** > **NT AUTHORITY\SYSTEM** egenskapen visar den **sysadmin** inställningen är aktiverad.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Fel: Server som är inte registrerad som anges i valvautentiseringsfilen

Det här felet uppstår under återställningen för Data Protection Manager/Azure Backup server-data. Valvautentiseringsfilen som används i återställningsprocessen hör inte till Recovery Services-valv för säkerhetskopiering av Data Protection Manager/Azure-servern.

Utför de här stegen för att lösa problemet:
1. Hämta valvautentiseringsfilen från Recovery Services-valvet som Data Protection Manager/Azure Backup-servern är registrerad.
2. Försök att registrera servern med valvet med hjälp av nyligen hämtade valvautentiseringsfilen.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Fel: Inga återställningsbara data eller den valda servern inte Data Protection Manager-server

Det här felet uppstår av följande skäl:
- Inga andra Data Protection Manager/Azure Backup-servrar är registrerade på Recovery Services-valvet.
- Servrar har inte ännu har överförts metadata.
- Den valda servern är inte en säkerhetskopiering av Data Protection Manager/Azure-server.

När andra Data Protection Manager/Azure Backup-servrar har registrerats till Recovery Services-valvet, utföra stegen nedan för att lösa problemet:
1. Kontrollera att den senaste Azure Backup-agenten är installerad.
2. När du har kontrollerat att den senaste agenten är installerad, vänta en dag innan du startar återställningsprocessen. Varje natt säkerhetskopieringsjobbet Överför metadata för alla skyddade säkerhetskopior till molnet. Säkerhetskopierade data är sedan tillgängliga för återställning.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Fel: Angivna krypteringslösenfrasen matchar inte lösenfrasen för server

Det här felet uppstår under krypteringsprocessen vid återställning av Data Protection Manager/Azure Backup server-data. Den krypterade lösenfrasen som används i återställningsprocessen matchar inte serverns krypteringslösenfrasen. Därför kan agenten kan inte dekryptera data och återställningen misslyckas.

> [!IMPORTANT]
> Om du glömmer bort krypteringslösenfrasen, finns inga andra metoder för att återställa data. Det enda alternativet är att återskapa lösenfrasen. Du kan använda den nya lösenfrasen för att kryptera framtida säkerhetskopierade data.
>
> När du återställer data, alltid ge samma krypteringslösenfrasen som är associerat med Data Protection Manager/Azure Backup-servern. 
>

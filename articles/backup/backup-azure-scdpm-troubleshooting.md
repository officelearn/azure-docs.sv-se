---
title: Felsöka System Center Data Protection Manager
description: I den här artikeln kan du hitta lösningar på problem som kan uppstå när du använder System Center Data Protection Manager.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: bcb30fa7eb3e05099761fc751b09a9fb16134e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75664755"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Felsöka System Center Data Protection Manager

I den här artikeln beskrivs lösningar på problem som kan uppstå när du använder Data Protection Manager.

De senaste versionsanteckningarna för System Center Data Protection Manager finns i [dokumentationen](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)till System Center . Du kan läsa mer om stöd för Data Protection Manager i [den här matrisen](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="error-replica-is-inconsistent"></a>Fel: Repliken är inkonsekvent

En replik kan vara inkonsekvent av följande skäl:

- Skapande av repliker misslyckas.
- Det finns problem med ändringsjournalen.
- Bitmappen för volymnivåfilter innehåller fel.
- Källmaskinen stängs av oväntat.
- Synkroniseringsloggen svämmar över.
- Repliken är verkligen inkonsekvent.

Lös problemet genom att utföra följande åtgärder:

- Om du vill ta bort den inkonsekventa statusen kör du konsekvenskontrollen manuellt eller schemalägger en daglig konsekvenskontroll.
- Kontrollera att du använder den senaste versionen av Microsoft Azure Backup Server och Data Protection Manager.
- Kontrollera att inställningen **Automatisk konsekvens** är aktiverad.
- Försök att starta om tjänsterna från kommandotolken. Använd `net stop dpmra` kommandot följt `net start dpmra`av .
- Kontrollera att du uppfyller kraven för nätverksanslutning och bandbredd.
- Kontrollera om källmaskinen stängdes av oväntat.
- Se till att disken är felfri och att det finns tillräckligt med utrymme för repliken.
- Kontrollera att det inte finns några dubbla säkerhetskopieringsjobb som körs samtidigt.

## <a name="error-online-recovery-point-creation-failed"></a>Fel: Det gick inte att skapa återställningspunkt online

Lös problemet genom att utföra följande åtgärder:

- Se till att du använder den senaste versionen av Azure Backup-agenten.
- Försök att manuellt skapa en återställningspunkt i skyddsaktivitetsområdet.
- Se till att du kör en konsekvenskontroll av datakällan.
- Kontrollera att du uppfyller kraven för nätverksanslutning och bandbredd.
- När replikdata är i ett inkonsekvent tillstånd skapar du en diskåterställningspunkt för den här datakällan.
- Kontrollera att repliken finns och inte saknas.
- Kontrollera att repliken har tillräckligt med utrymme för att skapa journalen för uppdateringssekvensnummer (USN).

## <a name="error-unable-to-configure-protection"></a>Fel: Det gick inte att konfigurera skyddet

Det här felet uppstår när dataskyddshanterarens server inte kan kontakta den skyddade servern.

Lös problemet genom att utföra följande åtgärder:

- Se till att du använder den senaste versionen av Azure Backup-agenten.
- Kontrollera att det finns anslutning (nätverk/brandvägg/proxy) mellan dataskyddshanterarens server och den skyddade servern.
- Om du skyddar en SQL-server kontrollerar du att egenskapen **Inloggningsegenskaper** > **NT AUTHORITY\SYSTEM** visar den **systeminställning som** är aktiverad.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Fel: Servern har inte registrerats som angiven i arkivautentiseringsfilen

Det här felet uppstår under återställningsprocessen för dataskyddshanteraren/Azure Backup-serverdata. Arkivautentiseringsfilen som används i återställningsprocessen tillhör inte Recovery Services-valvet för data protection manager/Azure Backup-servern.

Lös problemet genom att utföra följande steg:

1. Hämta arkivautentiseringsfilen från Recovery Services-valvet som dataskyddshanteraren/Azure Backup-servern är registrerad på.
2. Försök att registrera servern med valvet med hjälp av den senast nedladdade autentiseringsuppgifterna för valvet.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Fel: Inga återställningsbara data eller en vald server som inte är en Data Protection Manager-server

Det här felet uppstår av följande skäl:

- Inga andra data protection manager/Azure Backup-servrar är registrerade i Recovery Services-valvet.
- Servrarna har ännu inte laddat upp metadata.
- Den valda servern är inte en Data Protection Manager/Azure Backup-server.

När andra Data Protection Manager/Azure Backup-servrar är registrerade i Recovery Services-valvet utför du de här stegen för att lösa problemet:

1. Kontrollera att den senaste Azure Backup-agenten är installerad.
2. När du har försäkrat att den senaste agenten är installerad väntar du en dag innan du startar återställningsprocessen. Det nattliga säkerhetskopieringsjobbet överför metadata för alla skyddade säkerhetskopior till molnet. Säkerhetskopieringsdata är sedan tillgängliga för återställning.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Fel: Förutsatt att krypteringslösenfras inte matchar lösenfras för servern

Det här felet uppstår under krypteringsprocessen när datahanteringshanteraren/Azure Backup-serverdata återställs. Krypteringslösenfrasen som används i återställningsprocessen matchar inte serverns krypteringslösenfras. Agenten kan därför inte dekryptera data och återställningen misslyckas.

> [!IMPORTANT]
> Om du glömmer eller förlorar krypteringslösenfrasen finns det inga andra metoder för att återställa data. Det enda alternativet är att återskapa lösenfrasen. Använd den nya lösenfrasen för att kryptera framtida säkerhetskopierade data.
>
> När du återställer data ska du alltid tillhandahålla samma krypteringslösenfras som är associerad med dataskyddshanteraren/Azure Backup-servern.
>

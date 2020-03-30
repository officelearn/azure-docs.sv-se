---
title: Hantera säkerhetskopior med rollbaserad åtkomstkontroll
description: Använd rollbaserad åtkomstkontroll för att hantera åtkomst till säkerhetskopieringshanteringsåtgärder i Recovery Services-valvet.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e2e32ac6981635e3b9885119fdf397783ac32cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273207"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Använda rollbaserad åtkomstkontroll för att hantera återställningspunkter för Azure Backup

Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med hjälp av RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete.

> [!IMPORTANT]
> Roller som tillhandahålls av Azure Backup är begränsade till åtgärder som kan utföras i Azure-portalen eller via REST API eller Recovery Services-valv PowerShell- eller CLI-cmdlets. Åtgärder som utförs i Azure backup Agent Client UI eller System center Data Protection Manager UI eller Azure Backup Server UI är utom kontroll över dessa roller.

Azure Backup innehåller tre inbyggda roller för att styra säkerhetskopieringshanteringsåtgärder. Läs mer om [Azure RBAC inbyggda roller](../role-based-access-control/built-in-roles.md)

* [Deltagare för säkerhetskopiering](../role-based-access-control/built-in-roles.md#backup-contributor) – Den här rollen har alla behörigheter för att skapa och hantera säkerhetskopiering utom att ta bort Recovery Services-valv och ge åtkomst till andra. Föreställ dig den här rollen som administratör för säkerhetskopieringshantering som kan utföra alla säkerhetskopieringsåtgärder.
* [Säkerhetskopiera operatör](../role-based-access-control/built-in-roles.md#backup-operator) - Den här rollen har behörighet till allt en deltagare gör förutom att ta bort principer för säkerhetskopiering och hantering av säkerhetskopiering. Den här rollen motsvarar deltagare förutom att den inte kan utföra destruktiva åtgärder som att stoppa säkerhetskopiering med ta bort data eller ta bort registrering av lokala resurser.
* [Backup Reader](../role-based-access-control/built-in-roles.md#backup-reader) - Den här rollen har behörighet att visa alla säkerhetskopieringshanteringsåtgärder. Föreställ dig denna roll att vara en övervakning person.

Om du vill definiera dina egna roller för ännu mer kontroll kan du se hur du [skapar anpassade roller](../role-based-access-control/custom-roles.md) i Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mappa inbyggda roller för säkerhetskopiering till säkerhetskopieringsåtgärder

I följande tabell fångas hanteringsåtgärderna för säkerhetskopiering och motsvarande minsta RBAC-roll som krävs för att utföra åtgärden.

| Hanteringsåtgärd | Minsta RBAC-roll krävs | Omfattning krävs |
| --- | --- | --- |
| Skapa Recovery Services-valv | Deltagare för säkerhetskopiering | Resursgrupp som innehåller valvet |
| Aktivera säkerhetskopiering av virtuella Azure-datorer | Operatör för säkerhetskopiering | Resursgrupp som innehåller valvet |
| | Virtuell datordeltagare | VM-resurs |
| Säkerhetskopiering på begäran av vm | Operatör för säkerhetskopiering | Recovery Services-valv |
| Återställa virtuell dator | Operatör för säkerhetskopiering | Recovery Services-valv |
| | Deltagare | Resursgrupp där den virtuella datorn ska distribueras |
| | Virtuell datordeltagare | Käll-VM som säkerhetskopierades |
| Återställa säkerhetskopiering av ohanterade diskar VM | Operatör för säkerhetskopiering | Recovery Services-valv |
| | Virtuell datordeltagare | Käll-VM som säkerhetskopierades |
| | Lagringskontodeltagare | Lagringskontoresurs där diskar ska återställas |
| Återställa hanterade diskar från vm-säkerhetskopiering | Operatör för säkerhetskopiering | Recovery Services-valv |
| | Virtuell datordeltagare | Käll-VM som säkerhetskopierades |
| | Lagringskontodeltagare | Tillfälligt lagringskonto som valts som en del av återställningen för att lagra data från valvet innan de konverteras till hanterade diskar |
| | Deltagare | Resursgrupp som hanterade diskar ska återställas till |
| Återställa enskilda filer från säkerhetskopiering av virtuella datorer | Operatör för säkerhetskopiering | Recovery Services-valv |
| | Virtuell datordeltagare | Käll-VM som säkerhetskopierades |
| Skapa säkerhetskopieringsprincip för Azure VM-säkerhetskopiering | Deltagare för säkerhetskopiering | Recovery Services-valv |
| Ändra säkerhetskopieringsprincipen för azure vm-säkerhetskopiering | Deltagare för säkerhetskopiering | Recovery Services-valv |
| Ta bort säkerhetskopieringsprincipen för Azure VM-säkerhetskopiering | Deltagare för säkerhetskopiering | Recovery Services-valv |
| Stoppa säkerhetskopiering (med lagring av data eller ta bort data) på säkerhetskopiering av virtuella datorer | Deltagare för säkerhetskopiering | Recovery Services-valv |
| Registrera lokalt Windows Server/klient/SCDPM eller Azure Backup Server | Operatör för säkerhetskopiering | Recovery Services-valv |
| Ta bort registrerade lokala Windows Server/client/SCDPM eller Azure Backup Server | Deltagare för säkerhetskopiering | Recovery Services-valv |

> [!IMPORTANT]
> Om du anger VM-deltagare på en VM-resursomfattning och klickar på Säkerhetskopiering som en del av vm-inställningarna öppnas skärmen Aktivera säkerhetskopiering även om den virtuella datorn redan har säkerhetskopierats eftersom anropet för att verifiera säkerhetskopieringsstatus fungerar endast på prenumerationsnivå. Undvik detta genom att antingen gå till valvet och öppna säkerhetskopian av den virtuella datorn eller ange rollen VM-deltagare på prenumerationsnivå.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Minimikrav på roll för säkerhetskopian av Azure-filresurser

Följande tabell samlar in hanteringsåtgärder för säkerhetskopiering och motsvarande roll som krävs för att utföra Azure File-resursåtgärd.

| Hanteringsåtgärd | Roll krävs | Resurser |
| --- | --- | --- |
| Aktivera säkerhetskopiering av Azure File-resurser | Deltagare för säkerhetskopiering |Recovery Services-valv |
| |Lagringskonto | Kontoresurs för deltagarelagring |
| Säkerhetskopiering på begäran av vm | Operatör för säkerhetskopiering | Recovery Services-valv |
| Återställ filresurs | Operatör för säkerhetskopiering | Recovery Services-valv |
| | Lagringskontodeltagare | Lagringskontoresurser där återställningskälla och Target-filresurser finns |
| Återställa enskilda filer | Operatör för säkerhetskopiering | Recovery Services-valv |
| |Lagringskontodeltagare|Lagringskontoresurser där återställningskälla och Target-filresurser finns |
| Sluta skydda |Deltagare för säkerhetskopiering | Recovery Services-valv |
| Avregistrera lagringskonto från valvet |Deltagare för säkerhetskopiering | Recovery Services-valv |
| |Lagringskontodeltagare | Resurs för lagringskonto|

## <a name="next-steps"></a>Nästa steg

* [Rollbaserad åtkomstkontroll:](../role-based-access-control/role-assignments-portal.md)Kom igång med RBAC i Azure-portalen.
* Läs om hur du hanterar åtkomst med:
  * [Powershell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Felsökning av rollbaserad åtkomstkontroll](../role-based-access-control/troubleshooting.md): Få förslag på hur du åtgärdar vanliga problem.

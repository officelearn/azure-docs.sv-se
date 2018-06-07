---
title: Hantera säkerhetskopior med Azure rollbaserad åtkomstkontroll '
description: Använda rollbaserad åtkomstkontroll för att hantera åtkomst till säkerhetskopiering hanteringsåtgärder i Recovery Services-valvet.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2017
ms.author: trinadhk
ms.openlocfilehash: 37240b211e055682c4ce93c9057c4b266bba49e3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607454"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Använda rollbaserad åtkomstkontroll för att hantera Azure Backup återställningspunkter
Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du särskilja uppgifter i din grupp och ge bara mängden åtkomst till användare som de behöver för att utföra sitt arbete.

> [!IMPORTANT]
> Roller som tillhandahålls av Azure Backup är begränsade till åtgärder som kan utföras i Azure-portalen eller Recovery Services-valvet PowerShell-cmdlets. Åtgärder som utförs i Azure backup Agent klientens användargränssnitt eller System center Data Protection Manager UI- eller Azure Backup Server UI ligger utanför kontroll över dessa roller.

Azure Backup är 3 inbyggda roller för att styra säkerhetskopiering hanteringsåtgärder. Läs mer om [Azure RBAC inbyggda roller](../role-based-access-control/built-in-roles.md)

* [Säkerhetskopiera deltagare](../role-based-access-control/built-in-roles.md#backup-contributor) -rollen har alla behörigheter att skapa och hantera säkerhetskopia, förutom att skapa Recovery Services-valvet och ge åtkomst till andra. Anta rollen som administratör för hantering av säkerhetskopiering som kan göra varje säkerhetskopiering management-åtgärd.
* [Säkerhetskopiera operatorn](../role-based-access-control/built-in-roles.md#backup-operator) -rollen har behörighet att allt deltagare förutom att ta bort säkerhetskopiering och hantera principer för säkerhetskopiering. Den här rollen motsvarar deltagare, förutom det går inte att utföra skadliga åtgärder som stoppa säkerhetskopiering med ta bort data eller ta bort registreringen av lokala resurser.
* [Säkerhetskopiera Reader](../role-based-access-control/built-in-roles.md#backup-reader) -rollen har behörighet att visa alla åtgärder för hantering av säkerhetskopiering. Anta rollen för att vara en person som övervakning.

Om du behöver för att definiera egna roller för ännu mer kontroll, se hur du [skapa anpassade roller](../role-based-access-control/custom-roles.md) i Azure RBAC.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mappa säkerhetskopiering inbyggda roller till hanteringsåtgärder för säkerhetskopiering
I följande tabell innehåller hanteringsåtgärder för säkerhetskopiering och motsvarande minsta RBAC roll som krävs för att utföra åtgärden.

| Management-åtgärd | Minsta RBAC rollen krävs |
| --- | --- |
| Skapa Recovery Services-valvet | Deltagare i resursgruppen för valvet |
| Aktivera säkerhetskopiering av virtuella Azure-datorer | Ansvarig för säkerhetskopiering på valvet, Virtual machine-deltagare på virtuella datorer |
| Säkerhetskopiering på begäran för den virtuella datorn | Ansvarig för säkerhetskopiering |
| Återställa VM | Ansvarig för säkerhetskopiering, resurs grupp deltagare där VM och Vnet kommer att distribueras |
| Återställa enskilda filer från en säkerhetskopia av Virtuella diskar | Ansvarig för säkerhetskopiering, Virtual machine-deltagare på virtuella datorer |
| Skapa princip för säkerhetskopiering för Virtuella Azure-säkerhetskopiering | Säkerhetskopiering deltagare |
| Ändra princip för säkerhetskopiering av Virtuella Azure-säkerhetskopiering | Säkerhetskopiering deltagare |
| Ta bort princip för säkerhetskopiering av Virtuella Azure-säkerhetskopiering | Säkerhetskopiering deltagare |
| Stoppa säkerhetskopiering (och behålla data eller ta bort data) på säkerhetskopiering | Säkerhetskopiering deltagare |
| Registrera lokalt Windows Server/client/SCDPM eller Azure Backup-Server | Ansvarig för säkerhetskopiering |
| Ta bort registrerade lokalt Windows Server/client/SCDPM eller Azure Backup-Server | Säkerhetskopiering deltagare |

## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md): komma igång med RBAC på Azure-portalen.
* Lär dig mer om att hantera åtkomst med:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Rollbaserad åtkomstkontroll felsökning](../role-based-access-control/troubleshooting.md): få förslag om hur du löser vanliga problem.

---
title: Hantera säkerhetskopior med rollbaserad åtkomstkontroll i Azure.
description: Använda rollbaserad åtkomstkontroll för att hantera åtkomst till åtgärder för hantering av säkerhetskopiering i Recovery Services-valvet.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/11/2018
ms.author: trinadhk
ms.openlocfilehash: 855b75652fca421df12766f7711152d1e3ca2aeb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009254"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Använda rollbaserad åtkomstkontroll för att hantera Azure Backup-återställningspunkter
Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med hjälp av RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete.

> [!IMPORTANT]
> Roller som tillhandahålls av Azure Backup är begränsade till åtgärder som kan utföras i Azure-portalen eller PowerShell-cmdletar för Recovery Services-valv. Åtgärder som utförs i Azure backup Agent-klientens användargränssnitt eller System center Data Protection Manager UI eller Användargränssnittet för Azure Backup Server är alldeles av dessa roller.

Azure Backup innehåller 3 inbyggda roller som styr säkerhetskopiering hanteringsåtgärder. Läs mer om [Azure RBAC inbyggda roller](../role-based-access-control/built-in-roles.md)

* [Säkerhetskopiera deltagare](../role-based-access-control/built-in-roles.md#backup-contributor) – den här rollen har alla behörigheter att skapa och hantera säkerhetskopia, förutom att skapa Recovery Services-valvet och ge åtkomst till andra. Anta att den här rollen som administratör för hantering av säkerhetskopiering som kan göra varje åtgärd för hantering av säkerhetskopiering.
* [Säkerhetskopiera operatorn](../role-based-access-control/built-in-roles.md#backup-operator) – den här rollen har behörighet att allt en deltagare utom ta bort säkerhetskopiering och hantera principer för säkerhetskopiering. Den här rollen motsvarar deltagare, förutom den inte kan utföra skadliga åtgärder som Avbryt säkerhetskopiering med ta bort data eller ta bort registreringen av lokala resurser.
* [Säkerhetskopiera läsare](../role-based-access-control/built-in-roles.md#backup-reader) – den här rollen har behörighet att visa alla åtgärder för hantering av säkerhetskopiering. Anta att den här rollen för att vara en person som övervakning.

Om du vill definiera egna roller för ännu mer kontroll, se hur du [skapa anpassade roller](../role-based-access-control/custom-roles.md) i Azure RBAC.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mappa säkerhetskopiering inbyggda roller till åtgärder för hantering av säkerhetskopiering
Följande tabell visar de hanteringsåtgärder för säkerhetskopiering och motsvarande minsta RBAC-roller krävs för att utföra åtgärden.

| Hanteringsåtgärd | Minsta RBAC-roll som krävs |
| --- | --- |
| Skapa Recovery Services-valv | Deltagare för resursgruppen för valvet |
| Aktivera säkerhetskopiering av virtuella Azure-datorer | Ansvarig för säkerhetskopiering som definierats i omfattningen av resursgruppen som innehåller valvet, virtuell datordeltagare på virtuella datorer |
| Säkerhetskopiering på begäran för virtuell dator | Ansvarig för säkerhetskopiering |
| Återställa en virtuell dator | Ansvarig för säkerhetskopiering, Resource group deltagare där Virtuella datorn kommer att distribueras, läsa för virtuella nätverk och delta i det undernät som valts |
| Återställ diskar, enskilda filer från säkerhetskopiering av virtuella datorer | Ansvarig för säkerhetskopiering, virtuell datordeltagare på virtuella datorer |
| Skapa säkerhetskopieringsprincip för virtuell Azure-säkerhetskopiering | Säkerhetskopieringsmedarbetare |
| Ändra princip för säkerhetskopiering av virtuell Azure-säkerhetskopiering | Säkerhetskopieringsmedarbetare |
| Ta bort princip för säkerhetskopiering av virtuell Azure-säkerhetskopiering | Säkerhetskopieringsmedarbetare |
| Stoppa säkerhetskopiering (och behålla data eller ta bort data) säkerhetskopiering av virtuella datorer | Säkerhetskopieringsmedarbetare |
| Registrera den lokala Windows Server/klient-/ SCDPM eller Azure Backup Server | Ansvarig för säkerhetskopiering |
| Ta bort registrerade lokala Windows Server/klient-/ SCDPM eller Azure Backup Server | Säkerhetskopieringsmedarbetare |

## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md): Kom igång med RBAC i Azure-portalen.
* Lär dig mer om att hantera åtkomst med:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Role-Based Access Control – felsökning](../role-based-access-control/troubleshooting.md): få förslag för att åtgärda vanliga problem.

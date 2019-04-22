---
title: Hantera säkerhetskopior med rollbaserad åtkomstkontroll i Azure.
description: Använda rollbaserad åtkomstkontroll för att hantera åtkomst till åtgärder för hantering av säkerhetskopiering i Recovery Services-valvet.
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: trinadhk
ms.openlocfilehash: ed3797183e13a00d2c5381fa6449c111c3bc9ab9
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682533"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Använda rollbaserad åtkomstkontroll för att hantera Azure Backup-återställningspunkter
Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med hjälp av RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete.

> [!IMPORTANT]
> Roller som tillhandahålls av Azure Backup är begränsade till åtgärder som kan utföras i Azure portal eller via REST-API eller PowerShell eller CLI-cmdletar för Recovery Services-valv. Åtgärder som utförs i Azure backup Agent-klientens användargränssnitt eller System center Data Protection Manager UI eller Användargränssnittet för Azure Backup Server är alldeles av dessa roller.

Azure Backup innehåller tre inbyggda roller som styr säkerhetskopiering hanteringsåtgärder. Läs mer om [Azure RBAC inbyggda roller](../role-based-access-control/built-in-roles.md)

* [Säkerhetskopiera deltagare](../role-based-access-control/built-in-roles.md#backup-contributor) – den här rollen har alla behörigheter att skapa och hantera säkerhetskopia, förutom att skapa Recovery Services-valvet och ge åtkomst till andra. Anta att den här rollen som administratör för hantering av säkerhetskopiering som kan göra varje åtgärd för hantering av säkerhetskopiering.
* [Säkerhetskopiera operatorn](../role-based-access-control/built-in-roles.md#backup-operator) – den här rollen har behörighet att allt en deltagare utom ta bort säkerhetskopiering och hantera principer för säkerhetskopiering. Den här rollen motsvarar deltagare, förutom den inte kan utföra skadliga åtgärder som Avbryt säkerhetskopiering med ta bort data eller ta bort registreringen av lokala resurser.
* [Säkerhetskopiera läsare](../role-based-access-control/built-in-roles.md#backup-reader) – den här rollen har behörighet att visa alla åtgärder för hantering av säkerhetskopiering. Anta att den här rollen för att vara en person som övervakning.

Om du vill definiera egna roller för ännu mer kontroll, se hur du [skapa anpassade roller](../role-based-access-control/custom-roles.md) i Azure RBAC.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mappa säkerhetskopiering inbyggda roller till åtgärder för hantering av säkerhetskopiering
Följande tabell visar de hanteringsåtgärder för säkerhetskopiering och motsvarande minsta RBAC-roller krävs för att utföra åtgärden.

| Hanteringsåtgärd | Minsta RBAC-roll som krävs | Omfång som krävs |
| --- | --- | --- |
| Skapa Recovery Services-valv | Deltagare | Resursgruppen som innehåller valvet |
| Aktivera säkerhetskopiering av virtuella Azure-datorer | Säkerhetskopieringsoperatör | Resursgruppen som innehåller valvet |
| | Virtuell datordeltagare | VM-resurs |
| Säkerhetskopiering på begäran för virtuell dator | Säkerhetskopieringsoperatör | Recovery vault-resursen |
| Återställa en virtuell dator | Säkerhetskopieringsoperatör | Recovery Services-valv |
| | Deltagare | Resursgruppen där virtuella datorn ska distribueras |
| | Virtuell datordeltagare | Virtuella säkerhetskopierades källdatorn |
| Återställa ohanterade diskar säkerhetskopiering av virtuella datorer | Säkerhetskopieringsoperatör | Recovery vault-resursen |
| | Virtuell datordeltagare | Virtuella säkerhetskopierades källdatorn |
| | Lagringskontodeltagare | Resursen för lagringskonton där diskar ska återställas |
| Återställa hanterade diskar från säkerhetskopiering av virtuella datorer | Säkerhetskopieringsoperatör | Recovery vault-resursen |
| | Virtuell datordeltagare | Virtuella säkerhetskopierades källdatorn |
| | Lagringskontodeltagare | Tillfällig lagringskonto har valts som en del av återställning för att lagra data från valvet innan du konverterar dem till hanterade diskar |
| | Deltagare | Resursgruppen som hanterade diskar ska återställas |
| Återställa enskilda filer från säkerhetskopiering av virtuella datorer | Säkerhetskopieringsoperatör | Recovery vault-resursen |
| | Virtuell datordeltagare | Virtuella säkerhetskopierades källdatorn |
| Skapa säkerhetskopieringsprincip för virtuell Azure-säkerhetskopiering | Säkerhetskopieringsmedarbetare | Recovery vault-resursen |
| Ändra princip för säkerhetskopiering av virtuell Azure-säkerhetskopiering | Säkerhetskopieringsmedarbetare | Recovery vault-resursen |
| Ta bort princip för säkerhetskopiering av virtuell Azure-säkerhetskopiering | Säkerhetskopieringsmedarbetare | Recovery vault-resursen |
| Stoppa säkerhetskopiering (och behålla data eller ta bort data) säkerhetskopiering av virtuella datorer | Säkerhetskopieringsmedarbetare | Recovery vault-resursen |
| Registrera den lokala Windows Server/klient-/ SCDPM eller Azure Backup Server | Säkerhetskopieringsoperatör | Recovery vault-resursen |
| Ta bort registrerade lokala Windows Server/klient-/ SCDPM eller Azure Backup Server | Säkerhetskopieringsmedarbetare | Recovery vault-resursen |

> [!IMPORTANT]
> Om du anger VM deltagare i ett omfång för VM-resurs och klicka på säkerhetskopiering som en del av inställningarna för virtuella datorer, öppnas ”Aktivera säkerhetskopiering” skärmen även om den virtuella datorn redan har säkerhetskopierats som anropet för att kontrollera status för säkerhetskopiering fungerar endast på prenumerationsnivån. För att undvika detta kan antingen går du till valvet och öppna vyn säkerhetskopieringsobjekt för den virtuella datorn eller ange VM-deltagarroll på prenumerationsnivå.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Minimiroll krav på Azure säkerhetskopiering av filresurser
Följande tabell visar de hanteringsåtgärder för säkerhetskopiering och motsvarande rollen som krävs för att utföra åtgärden för Azure File-resursen.

| Hanteringsåtgärd | Rollen krävs | Resurser |
| --- | --- | --- |
| Aktivera säkerhetskopiering av Azure-filresurser | Säkerhetskopieringsmedarbetare | Recovery Services-valv |
| | Lagringskonto | Deltagare resursen för lagringskonton |
| Säkerhetskopiering på begäran för virtuell dator | Säkerhetskopieringsoperatör | Recovery Services-valv |
| Återställa filresursen | Säkerhetskopieringsoperatör | Recovery Services-valv |
| | Lagringskontodeltagare | Lagringskontoresurserna där återställning källa och mål-filresurser finns |
| Återställa enskilda filer | Säkerhetskopieringsoperatör | Recovery Services-valv |
| | Lagringskontodeltagare |   Lagringskontoresurserna där återställning källa och mål-filresurser finns |
| Sluta skydda | Säkerhetskopieringsmedarbetare | Recovery Services-valv |      
| Avregistrera lagringskontot från valvet |   Säkerhetskopieringsmedarbetare | Recovery Services-valv |
| | Lagringskontodeltagare | Resursen för lagringskonton|


## <a name="next-steps"></a>Nästa steg
* [Rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md): Kom igång med RBAC i Azure-portalen.
* Lär dig mer om att hantera åtkomst med:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Role-Based Access Control – felsökning](../role-based-access-control/troubleshooting.md): Få förslag för att åtgärda vanliga problem.

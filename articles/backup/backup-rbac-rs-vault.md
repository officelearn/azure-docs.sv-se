---
title: Hantera säkerhets kopior med rollbaserad Access Control
description: Använd rollbaserad Access Control för att hantera åtkomst till säkerhets kopierings hanterings åtgärder i Recovery Services valvet.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: afb8067f2547e3a26a505bb5ec8063dd340e8500
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172194"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Använd rollbaserad Access Control för att hantera Azure Backup återställnings punkter

Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med hjälp av RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete.

> [!IMPORTANT]
> Roller som tillhandahålls av Azure Backup är begränsade till åtgärder som kan utföras i Azure Portal eller via REST API eller Recovery Services valv PowerShell-eller CLI-cmdletar. Åtgärder som utförs i användar gränssnittet för Azure Backup-agenten eller System Center-Data Protection Manager användar gränssnitt eller Azure Backup Server användar gränssnitt är inte längre kontroll över dessa roller.

Azure Backup innehåller tre inbyggda roller för att kontrol lera säkerhets kopierings hanterings åtgärder. Läs mer om [Azure RBAC inbyggda roller](../role-based-access-control/built-in-roles.md)

* [Säkerhets kopierings deltagare](../role-based-access-control/built-in-roles.md#backup-contributor) – den här rollen har alla behörigheter att skapa och hantera säkerhets kopior, förutom att ta bort Recovery Services valv och ge till gång till andra. Föreställ dig att rollen som administratör för säkerhets kopierings hantering som kan utföra varje säkerhets kopierings hanterings åtgärd.
* [Säkerhets kopierings ansvarig](../role-based-access-control/built-in-roles.md#backup-operator) – den här rollen har behörighet till allt en deltagare gör, förutom att ta bort säkerhets kopiering och hantera säkerhets kopierings principer. Den här rollen motsvarar bidrags givaren, förutom att den inte kan utföra destruktiva åtgärder, till exempel stoppa säkerhets kopiering med ta bort data eller ta bort registrering av lokala resurser.
* [Säkerhets kopierings läsare](../role-based-access-control/built-in-roles.md#backup-reader) – den här rollen har behörighet att visa alla säkerhets kopierings hanterings åtgärder. Föreställ dig att den här rollen är en övervaknings person.

Om du vill definiera egna roller för ännu mer kontroll, se så här [skapar du anpassade roller](../role-based-access-control/custom-roles.md) i Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mappa inbyggda säkerhets kopierings roller till säkerhets kopierings hanterings åtgärder

I följande tabell inhämtas säkerhets kopierings hanterings åtgärder och motsvarande lägsta RBAC-roll som krävs för att utföra åtgärden.

| Hanterings åtgärd | Minimal RBAC-roll krävs | Omfattning krävs |
| --- | --- | --- |
| Skapa Recovery Services-valv | Säkerhets kopierings deltagare | Resurs grupp som innehåller valvet |
| Aktivera säkerhets kopiering av virtuella Azure-datorer | Ansvarig för säkerhets kopiering | Resurs grupp som innehåller valvet |
| | Virtuell dator deltagare | VM-resurs |
| Säkerhets kopiering på begäran av virtuell dator | Ansvarig för säkerhets kopiering | Återställnings valv resurs |
| Återställ virtuell dator | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| | Deltagare | Resurs grupp där den virtuella datorn ska distribueras |
| | Virtuell dator deltagare | Virtuell käll dator som har säkerhetskopierats |
| Återställa ohanterade diskar VM-säkerhetskopiering | Ansvarig för säkerhets kopiering | Återställnings valv resurs |
| | Virtuell dator deltagare | Virtuell käll dator som har säkerhetskopierats |
| | Lagrings konto deltagare | Lagrings konto resurs där diskarna ska återställas |
| Återställa hanterade diskar från VM-säkerhetskopiering | Ansvarig för säkerhets kopiering | Återställnings valv resurs |
| | Virtuell dator deltagare | Virtuell käll dator som har säkerhetskopierats |
| | Lagrings konto deltagare | Tillfälligt lagrings konto valdes som en del av Restore för att lagra data från valvet innan de konverteras till Managed disks |
| | Deltagare | Resurs grupp som den eller de hanterade diskarna ska återställas till |
| Återställa enskilda filer från VM-säkerhetskopiering | Ansvarig för säkerhets kopiering | Återställnings valv resurs |
| | Virtuell dator deltagare | Virtuell käll dator som har säkerhetskopierats |
| Skapa säkerhets kopierings princip för säkerhets kopiering av virtuella Azure-datorer | Säkerhets kopierings deltagare | Återställnings valv resurs |
| Ändra säkerhets kopierings princip för säkerhets kopiering av virtuella Azure-datorer | Säkerhets kopierings deltagare | Återställnings valv resurs |
| Ta bort säkerhets kopierings princip för virtuell Azure-säkerhetskopiering | Säkerhets kopierings deltagare | Återställnings valv resurs |
| Stoppa säkerhets kopiering (med Behåll data eller ta bort data) vid säkerhets kopiering av virtuell dator | Säkerhets kopierings deltagare | Återställnings valv resurs |
| Registrera lokala Windows Server-/klient-SCDPM eller Azure Backup Server | Ansvarig för säkerhets kopiering | Återställnings valv resurs |
| Ta bort registrerade lokala Windows Server-eller klient-SCDPM eller Azure Backup Server | Säkerhets kopierings deltagare | Återställnings valv resurs |

> [!IMPORTANT]
> Om du anger VM-deltagare vid en VM-resurs omfånget och klickar på säkerhets kopiering som en del av inställningarna för virtuella datorer öppnas skärmen "Aktivera säkerhets kopiering" trots att den virtuella datorn redan har säkerhetskopierats som anrop för att kontrol lera säkerhets kopierings statusen endast fungerar på prenumerations nivå. Undvik detta genom att gå till valvet och öppna vyn säkerhets kopiering av den virtuella datorn eller ange rollen VM-deltagare på en prenumerations nivå.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Lägsta roll krav för säkerhets kopiering av Azure-filresurs

I följande tabell inhämtas åtgärder för säkerhets kopierings hantering och motsvarande roll som krävs för att utföra en Azure-filresurs åtgärd.

| Hanterings åtgärd | Roll krävs | Resurser |
| --- | --- | --- |
| Aktivera säkerhets kopiering av Azure-filresurser | Säkerhets kopierings deltagare |Recovery Services-valv |
| |Lagringskonto | Deltagar lagrings konto resurs |
| Säkerhets kopiering på begäran av virtuell dator | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| Återställ fil resurs | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| | Lagrings konto deltagare | Lagrings konto resurser där återställning av käll-och mål fil resurser finns |
| Återställa enskilda filer | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| |Lagrings konto deltagare|Lagrings konto resurser där återställning av käll-och mål fil resurser finns |
| Stoppa skydd |Säkerhets kopierings deltagare | Recovery Services-valv |
| Avregistrera lagrings kontot från valvet |Säkerhets kopierings deltagare | Recovery Services-valv |
| |Lagrings konto deltagare | Lagrings konto resurs|

## <a name="next-steps"></a>Nästa steg

* [Rollbaserad Access Control](../role-based-access-control/role-assignments-portal.md): kom igång med RBAC i Azure Portal.
* Lär dig hur du hanterar åtkomst med:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Rollbaserad Access Control fel sökning](../role-based-access-control/troubleshooting.md): få förslag på hur du löser vanliga problem.

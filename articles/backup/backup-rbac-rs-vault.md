---
title: Hantera säkerhets kopiering med rollbaserad åtkomst kontroll i Azure
description: Använd rollbaserad åtkomst kontroll i Azure för att hantera åtkomst till säkerhets kopierings hanterings åtgärder i Recovery Services valvet.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 0dd8d08c4ee79082f47929cf7d453f3f4bbd60ee
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090887"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Använd rollbaserad åtkomst kontroll i Azure för att hantera Azure Backup återställnings punkter

Rollbaserad åtkomst kontroll i Azure (Azure RBAC) möjliggör detaljerad åtkomst hantering för Azure. Med hjälp av Azure RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete.

> [!IMPORTANT]
> Roller som tillhandahålls av Azure Backup är begränsade till åtgärder som kan utföras i Azure Portal eller via REST API eller Recovery Services valv PowerShell-eller CLI-cmdletar. Åtgärder som utförs i användar gränssnittet för Azure Backup Agent-klienten eller System Center Data Protection Manager UI eller Azure Backup Server användar gränssnittet är inte längre styrda av rollerna.

Azure Backup innehåller tre inbyggda roller för att kontrol lera säkerhets kopierings hanterings åtgärder. Lär dig mer om [inbyggda Azure-roller](../role-based-access-control/built-in-roles.md)

* [Säkerhets kopierings deltagare](../role-based-access-control/built-in-roles.md#backup-contributor) – den här rollen har alla behörigheter att skapa och hantera säkerhets kopior, förutom att ta bort Recovery Services valv och ge till gång till andra. Föreställ dig att rollen som administratör för säkerhets kopierings hantering som kan utföra varje säkerhets kopierings hanterings åtgärd.
* [Säkerhets kopierings ansvarig](../role-based-access-control/built-in-roles.md#backup-operator) – den här rollen har behörighet till allt en deltagare gör, förutom att ta bort säkerhets kopiering och hantera säkerhets kopierings principer. Den här rollen motsvarar bidrags givaren, förutom att den inte kan utföra destruktiva åtgärder, till exempel stoppa säkerhets kopiering med ta bort data eller ta bort registrering av lokala resurser.
* [Säkerhets kopierings läsare](../role-based-access-control/built-in-roles.md#backup-reader) – den här rollen har behörighet att visa alla säkerhets kopierings hanterings åtgärder. Föreställ dig att den här rollen är en övervaknings person.

Om du vill definiera egna roller för ännu mer kontroll, se så här [skapar du anpassade roller](../role-based-access-control/custom-roles.md) i Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mappa inbyggda säkerhets kopierings roller till säkerhets kopierings hanterings åtgärder

I följande tabell inhämtas åtgärder för säkerhets kopierings hantering och motsvarande minsta Azure-roll som krävs för att utföra åtgärden.

| Hanterings åtgärd | Lägsta Azure-roll som krävs | Omfattning krävs |
| --- | --- | --- |
| Skapa Recovery Services-valv | Säkerhets kopierings deltagare | Resurs grupp som innehåller valvet |
| Aktivera säkerhets kopiering av virtuella Azure-datorer | Ansvarig för säkerhets kopiering | Resurs grupp som innehåller valvet |
| | Virtuell datordeltagare | VM-resurs |
| Säkerhets kopiering på begäran av virtuell dator | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| Återställa virtuell dator | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| | Deltagare | Resurs grupp där den virtuella datorn ska distribueras |
| | Virtuell datordeltagare | Virtuell käll dator som har säkerhetskopierats |
| Återställa ohanterade diskar VM-säkerhetskopiering | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| | Virtuell datordeltagare | Virtuell käll dator som har säkerhetskopierats |
| | Lagringskontodeltagare | Lagrings konto resurs där diskarna ska återställas |
| Återställa hanterade diskar från VM-säkerhetskopiering | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| | Virtuell datordeltagare | Virtuell käll dator som har säkerhetskopierats |
| | Lagringskontodeltagare | Tillfälligt lagrings konto valdes som en del av Restore för att lagra data från valvet innan de konverteras till Managed disks |
| | Deltagare | Resurs grupp som den eller de hanterade diskarna ska återställas till |
| Återställa enskilda filer från VM-säkerhetskopiering | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| | Virtuell datordeltagare | Virtuell käll dator som har säkerhetskopierats |
| Skapa säkerhets kopierings princip för säkerhets kopiering av virtuella Azure-datorer | Säkerhets kopierings deltagare | Recovery Services-valv |
| Ändra säkerhets kopierings princip för säkerhets kopiering av virtuella Azure-datorer | Säkerhets kopierings deltagare | Recovery Services-valv |
| Ta bort säkerhets kopierings princip för virtuell Azure-säkerhetskopiering | Säkerhets kopierings deltagare | Recovery Services-valv |
| Stoppa säkerhets kopiering (med Behåll data eller ta bort data) vid säkerhets kopiering av virtuell dator | Säkerhets kopierings deltagare | Recovery Services-valv |
| Registrera lokala Windows Server-/klient-SCDPM eller Azure Backup Server | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| Ta bort registrerade lokala Windows Server-eller klient-SCDPM eller Azure Backup Server | Säkerhets kopierings deltagare | Recovery Services-valv |

> [!IMPORTANT]
> Om du anger VM-deltagare vid en VM-resurs omfattning och väljer **säkerhets kopiering** som en del av inställningarna för virtuella datorer öppnas skärmen **Aktivera säkerhets kopiering** , även om den virtuella datorn redan har säkerhetskopierats. Detta beror på att anropet att verifiera säkerhets kopierings status endast fungerar på prenumerations nivå. Undvik detta genom att gå till valvet och öppna säkerhets kopierings objekt visningen av den virtuella datorn eller ange rollen VM-deltagare på en prenumerations nivå.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Lägsta roll krav för säkerhets kopiering av Azure-filresurs

I följande tabell inhämtas åtgärder för säkerhets kopierings hantering och motsvarande roll som krävs för att utföra en Azure-filresurs åtgärd.

| Hanterings åtgärd | Roll krävs | Resurser |
| --- | --- | --- |
| Aktivera säkerhets kopiering av Azure-filresurser | Säkerhets kopierings deltagare |Recovery Services-valv |
| |Lagringskonto | Deltagar lagrings konto resurs |
| Säkerhets kopiering på begäran av virtuell dator | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| Återställ fil resurs | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| | Lagringskontodeltagare | Lagrings konto resurser där återställning av käll-och mål fil resurser finns |
| Återställa enskilda filer | Ansvarig för säkerhets kopiering | Recovery Services-valv |
| |Lagringskontodeltagare|Lagrings konto resurser där återställning av käll-och mål fil resurser finns |
| Sluta skydda |Säkerhets kopierings deltagare | Recovery Services-valv |
| Avregistrera lagrings kontot från valvet |Säkerhets kopierings deltagare | Recovery Services-valv |
| |Lagringskontodeltagare | Lagrings konto resurs|

## <a name="next-steps"></a>Nästa steg

* [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): kom igång med Azure rbac i Azure Portal.
* Lär dig hur du hanterar åtkomst med:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Fel sökning av rollbaserad åtkomst kontroll i Azure](../role-based-access-control/troubleshooting.md): få förslag på hur du löser vanliga problem.

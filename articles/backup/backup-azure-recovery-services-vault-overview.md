---
title: Översikt över Recovery Services-valv
description: En översikt och jämförelse mellan Recovery Services valv och Azure Backup valv.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e1d061c6baf31fad2e937a604098f0baff6086d
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041909"
---
# <a name="recovery-services-vaults-overview"></a>Översikt över Recovery Services-valv

I den här artikeln beskrivs funktionerna i ett Recovery Services-valv. Ett Recovery Services-valv är en lagrings enhet i Azure som huserar data. Data är vanligt vis kopior av data eller konfigurations information för virtuella datorer (VM), arbets belastningar, servrar eller arbets stationer. Du kan använda Recovery Services valv för att lagra säkerhets kopierings data för olika Azure-tjänster, till exempel virtuella IaaS-datorer (Linux eller Windows) och Azure SQL-databaser. Recovery Services valv stöder System Center DPM, Windows Server, Azure Backup Server med mera. Med Recovery Services-valv är det enkelt att organisera dina säkerhetskopierade data samtidigt som du minimerar hanteringskostnaden. Recovery Services-valv baseras på Azure Resource Manager modell för Azure, som innehåller funktioner som:

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Jämföra Recovery Services valv och säkerhets kopierings valv

- **Förbättrade funktioner för att skydda säkerhets kopierings data**: med Recovery Services-valv tillhandahåller Azure Backup säkerhetsfunktioner för säkerhets kopiering av molnet. Säkerhetsfunktionerna garanterar att du kan skydda dina säkerhets kopior och återställa data på ett säkert sätt, även om produktions-och säkerhets kopierings servrar komprometteras. [Läs mer](backup-azure-security-feature.md)

- **Central övervakning för din hybrid IT-miljö**: med Recovery Services valv kan du bara övervaka dina [virtuella Azure IaaS-datorer](backup-azure-manage-vms.md) utan även dina [lokala till gångar](backup-azure-manage-windows-server.md#manage-backup-items) från en central Portal. [Läs mer](backup-azure-monitoring-built-in-monitor.md)

- **Rollbaserad Access Control (RBAC)**: RBAC ger detaljerad åtkomst hanterings kontroll i Azure. [Azure tillhandahåller olika inbyggda roller](../role-based-access-control/built-in-roles.md)och Azure Backup har tre [inbyggda roller för att hantera återställnings punkter](backup-rbac-rs-vault.md). Recovery Services-valv är kompatibla med RBAC, vilket begränsar säkerhets kopiering och återställning av åtkomst till den definierade uppsättningen användar roller. [Läs mer](backup-rbac-rs-vault.md)

- **Mjuk borttagning**: med mjuk borttagning, även om en obehörig aktör tar bort en säkerhets kopia (eller säkerhetskopierade data tas bort av misstag), bevaras säkerhets kopierings data i ytterligare 14 dagar, vilket gör att det inte går att återställa säkerhets kopierings objekt utan data förlust. De ytterligare 14 dagarna för kvarhållning av säkerhets kopierings data i läget "mjuk borttagning" kostar ingen kostnad för kunden. [Läs mer](backup-azure-security-feature-cloud.md).

- **Återställning mellan regioner**: kors regions återställning (CRR) gör att du kan återställa virtuella Azure-datorer i en sekundär region, som är en Azure-kopplad region. Om Azure deklarerar en katastrof i den primära regionen, är de data som replikeras i den sekundära regionen tillgänglig för återställning i den sekundära regionen för att minimera den verkliga stillestånds tiden i den primära regionen för deras miljö. [Läs mer](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Lagrings inställningar i Recovery Services valvet

Ett Recovery Services-valv är en entitet som lagrar säkerhets kopior och återställnings punkter som skapats med tiden. Recovery Services valvet innehåller också de säkerhets kopierings principer som är associerade med de skyddade virtuella datorerna.

- Azure Backup hanterar automatiskt lagring för valvet. Se hur [lagrings inställningarna kan ändras](./backup-create-rs-vault.md#set-storage-redundancy).

- Mer information om redundans finns i de här artiklarna om [geo](../storage/common/storage-redundancy.md) -och [lokal](../storage/common/storage-redundancy.md) redundans.

### <a name="additional-resources"></a>Ytterligare resurser

- [Scenarier som stöds och scenarier som inte stöds](backup-support-matrix.md#vault-support)
- [Vanliga frågor och svar om valvet](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) är en anpassad moln konsult som hjälper dig att optimera användningen av Azure. Den analyserar din Azure-användning och ger rekommendationer som hjälper dig att optimera och skydda dina distributioner. Det ger rekommendationer i fyra kategorier: hög tillgänglighet, säkerhet, prestanda och kostnad.

Azure Advisor ger [rekommendationer](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) för virtuella datorer som inte har säkerhetskopierats, så att du aldrig har säkerhetskopierat viktiga virtuella datorer. Du kan också styra rekommendationerna genom att försätta dem i vilo läge.  Du kan välja rekommendationen och aktivera säkerhets kopiering på virtuella datorer på en rad genom att ange valvet (där säkerhets kopior ska lagras) och säkerhets kopierings policyn (schema för säkerhets kopior och kvarhållning av säkerhets kopior).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Nästa steg

Använd följande artiklar för att:</br>
[Säkerhetskopiera en virtuell IaaS-dator](backup-azure-arm-vms-prepare.md)</br>
[Säkerhetskopiera en Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Säkerhetskopiera en Windows Server](backup-windows-with-mars-agent.md)

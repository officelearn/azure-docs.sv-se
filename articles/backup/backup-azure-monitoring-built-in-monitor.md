---
title: 'Azure Backup: Övervaka Azure Backup skyddade arbetsbelastningar'
description: Övervaka Azure Backup-arbetsbelastningar med hjälp av Azure portal
services: backup
author: pvrk
manager: shivamg
keywords: Azure Backup; Aviseringar;
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 2513f2e7a2e82a541fa5638b70d0543192c84765
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885356"
---
# <a name="monitoring-azure-backup-workloads"></a>Övervaka Azure Backup-arbetsbelastningar

Azure Backup innehåller flera lösningar för säkerhetskopiering baserat på säkerhetskopiering krav och infrastruktur topologin (lokalt eller Azure). Alla säkerhetskopierade användare/admin bör se vad som händer i alla lösningar och förväntas bli meddelad i viktiga scenarier. Den här artikeln beskriver övervakning och avisering funktionerna i Azure Backup-tjänsten.

## <a name="backup-jobs-in-recovery-services-vault"></a>Säkerhetskopieringsjobb i Recovery Services-valv

Azure Backup innehåller inbyggd övervakning och aviseringsfunktioner för arbetsbelastningar som skyddas av Azure Backup. I Recovery Services vault-inställningar ger inbyggd jobb och aviseringar i avsnittet ”övervakning”.

![RS valv inbyggd övervakning](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Jobb skapas när åtgärder, till exempel konfigurera säkerhetskopieringen, säkerhetskopiering, återställning osv delete säkerhetskopiering utförs.
Jobb från följande Azure Backup-lösningar visas här.

- Azure VM-säkerhetskopieringar
- Azure säkerhetskopior av filer
- Azure-arbetsbelastning säkerhetskopior, till exempel SQL
- Azure Backup-agenten (MAB)

Jobb från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas inte här.

> [!NOTE]
> För Azure-arbetsbelastningar, till exempel SQL-säkerhetskopior i Azure virtuella datorer är antalet säkerhetskopieringsjobb stor. Loggsäkerhetskopior kan till exempel köra för var 15: e minut. För sådana DB-arbetsbelastningar, därför visas bara utlöses användaråtgärder. Schemalagda säkerhetskopieringsåtgärder visas inte.

## <a name="backup-alerts-in-recovery-services-vault"></a>Aviseringar om säkerhetskopiering i Recovery Services-valv

Aviseringar är främst scenarier där kunden måste du få ett meddelande så att de kan vidta relevanta åtgärder. ”Säkerhetskopiering” med ett varningsavsnitt visar aviseringar som genereras av Azure Backup-tjänsten. Dessa aviseringar definieras av tjänsten och användare kan inte anpassade skapa några aviseringar. Följande scenarier definieras av tjänsten som kanske scenarier

- Säkerhetskopiering/återställning fel
- Säkerhetskopieringen slutfördes med varningar
- Avbryt skyddet med Behåll data/Avbryt skyddet med ta borttagningsdata

Det finns få undantag när en avisering inte aktiveras på ett fel.

- Användaren avbröt uttryckligen det pågående jobbet
- Jobbet misslyckas eftersom en annan säkerhetskopiering pågår (ingenting att agera på här eftersom vi bara behöver vänta på föregående jobbet är slutfört)
- VM-säkerhetskopiering misslyckas eftersom den virtuella datorn Azure säkerhetskopieras inte längre finns

Undantagen ovan är utformade från uppfattningen att resultatet av dessa åtgärder (främst användare som utlöste) visas direkt på portalen/PS/CLI-klienter. Därför kan användaren känner direkt och behöver inte ett meddelande.

Aviseringar från följande Azure Backup-lösningar visas här.

- Azure VM-säkerhetskopieringar
- Azure säkerhetskopior av filer
- Azure-arbetsbelastning säkerhetskopior, till exempel SQL
- Azure Backup-agenten (MAB)

> [!NOTE]
> Aviseringar från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas inte här.

Baserat på allvarlighetsgrad för avisering, kan aviseringar definieras i tre typer:

- **Kritiskt:** I princip underlåtenhet säkerhetskopiering eller återställning (schemalagt eller användare som utlöste) skulle leda till generering av en avisering och skulle visas som en kritisk varning. Destruktiva åtgärder, till exempel ta bort backup skulle också också generera kritiska aviseringar.
- **Varning**: Om säkerhetskopieringen har slutförts men med några varningar, de listas som varningar.
- **Endast i informationssyfte**: Från och med dagens datum genereras inga informationsavisering av Azure Backup-tjänsten.

### <a name="notification-for-backup-alerts"></a>Meddelande för Säkerhetskopieringsaviseringar

> [!NOTE]
> Konfiguration av meddelande som kan göras endast via Azure-portalen. PS/CLI/REST API/Azure Resource Manager-mall-support tillhandahålls inte ännu.

När en avisering genereras, måste kunden vill få information. Azure Backup innehåller en inbyggd meddelande via e-post. En kan ange enskilda e-postadresser eller distributionslistor vill meddelas när en varning hämtar genereras. Du kan också välja om du vill få information för varje enskild varning eller gruppera dem i en sammanställning per timme och sedan få ett meddelande.

![RS Vault inbyggda e-postavisering](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

När du har konfigurerat meddelanden får du en Välkommen/inledande email, som bekräftar att Azure Backup kan skicka e-postmeddelanden till dessa adresser när en avisering genereras. Om frekvensen ställdes in på en sammanställning per timme och om en avisering har aktiverats och löses inom en timme, kan den inte en del av den kommande sammanställning per timme.

> [!NOTE]
> Om en destruktiv åtgärd, till exempel 'Avbryt skyddet med ta borttagningsdata ”utförs en avisering genereras och ett e-postmeddelande skickas till prenumerationsägare, administratörer och medadministratörer även om meddelanden inte är konfigurerade för RS-valvet.

## <a name="next-steps"></a>Nästa steg

[Övervaka Azure backup-arbetsbelastningar med Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
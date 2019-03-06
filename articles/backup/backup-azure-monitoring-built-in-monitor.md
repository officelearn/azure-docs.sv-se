---
title: 'Azure Backup: Övervaka Azure Backup skyddade arbetsbelastningar'
description: Övervaka Azure Backup-arbetsbelastningar med hjälp av Azure portal
services: backup
author: pvrk
manager: shivamg
keywords: Azure Backup; Aviseringar;
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 72a23617440e26c9dca69cab2a06370427c49768
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404730"
---
# <a name="monitoring-azure-backup-workloads"></a>Övervaka Azure Backup-arbetsbelastningar

Azure Backup innehåller flera lösningar för säkerhetskopiering baserat på säkerhetskopiering krav och infrastruktur topologin (lokalt eller Azure). Alla säkerhetskopierade användaren eller administratören bör se vad som händer i alla lösningar och förväntas bli meddelad i viktiga scenarier. Den här artikeln beskriver övervakning och avisering funktionerna i Azure Backup-tjänsten.

## <a name="backup-jobs-in-recovery-services-vault"></a>Säkerhetskopieringsjobb i Recovery Services-valv

Azure Backup innehåller inbyggd övervakning och aviseringsfunktioner för arbetsbelastningar som skyddas av Azure Backup. I Recovery Services-valv inställningar, den **övervakning** avsnittet innehåller inbyggd jobb och aviseringar.

![RS valv inbyggd övervakning](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Jobb skapas när utförs åtgärder som till exempel konfigurera säkerhetskopiering, säkerhetskopiering, återställning, ta bort säkerhetskopia och så vidare.

Jobb från följande Azure Backup-lösningar visas här:

  - Säkerhetskopiering av virtuell Azure-dator
  - Azure säkerhetskopiering av filer
  - Arbetsbelastning i Azure backup, till exempel SQL
  - Azure Backup-agenten (MAB)

Jobb från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas inte.

> [!NOTE]
> Azure-arbetsbelastningar, till exempel SQL-säkerhetskopior i Azure virtuella datorer har stort antal säkerhetskopieringsjobb. Loggsäkerhetskopior kan till exempel köra för var 15: e minut. För sådana DB-arbetsbelastningar, därför visas bara utlöses användaråtgärder. Schemalagda säkerhetskopieringsåtgärder visas inte.

## <a name="backup-alerts-in-recovery-services-vault"></a>Aviseringar om säkerhetskopiering i Recovery Services-valv

Aviseringar är främst scenarier där användarna får en avisering så att de kan vidta relevanta åtgärder. Den **säkerhetskopiering aviseringar** visar aviseringar som genereras av Azure Backup-tjänsten. Dessa aviseringar definieras av tjänsten och användare kan inte anpassade skapa några aviseringar.

### <a name="alert-scenarios"></a>Scenarier för avisering
Följande scenarier definieras av tjänsten som kanske scenarier.

  - Säkerhetskopiering/återställning fel
  - Säkerhetskopieringen slutfördes med varningar
  - Avbryt skyddet med Behåll data/Avbryt skyddet med ta borttagningsdata

### <a name="exceptions-when-an-alert-is-not-raised"></a>Undantag när en avisering inte aktiveras
Det finns få undantag när en avisering inte aktiveras på ett fel, de är:

  - Användaren avbröt uttryckligen det pågående jobbet
  - Jobbet misslyckas eftersom en annan säkerhetskopiering pågår (ingenting att agera på här eftersom vi bara behöver vänta på föregående jobbet är slutfört)
  - VM-säkerhetskopiering misslyckas eftersom den virtuella datorn Azure säkerhetskopieras inte längre finns

Undantagen ovan är utformade från uppfattningen att resultatet av dessa åtgärder (främst användare som utlöste) visas direkt på portalen/PS/CLI-klienter. Därför kan användaren känner direkt och behöver inte ett meddelande.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Aviseringar från följande Azure Backup-lösningar visas här:

  - Azure VM-säkerhetskopieringar
  - Azure säkerhetskopior av filer
  - Azure-arbetsbelastning säkerhetskopior, till exempel SQL
  - Azure Backup-agenten (MAB)

> [!NOTE]
> Aviseringar från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas inte här.

### <a name="alert-types"></a>Aviseringstyper
Baserat på allvarlighetsgrad för avisering, kan aviseringar definieras i tre typer:

  - **Kritiska**: I princip, alla säkerhetskopiering eller återställning fel (schemalagt eller användare som utlöste) skulle leda till generering av en avisering och skulle visas som en kritisk varning och destruktiva åtgärder, till exempel ta bort säkerhetskopiering.
  - **Varning**: Om säkerhetskopieringen lyckas men med några varningar, de listas som varningar.
  - **Endast i informationssyfte**: Från och med dagens datum genereras inga informationsavisering av Azure Backup-tjänsten.

## <a name="notification-for-backup-alerts"></a>Meddelande för Säkerhetskopieringsaviseringar

> [!NOTE]
> Konfiguration av meddelande som kan göras endast via Azure-portalen. PS/CLI/REST API/Azure Resource Manager-mall support stöds inte.

Användarna meddelas när en avisering genereras. Azure Backup innehåller en inbyggd meddelande via e-post. En kan ange enskilda e-postadresser eller distributionslistor vill meddelas när en avisering genereras. Du kan också välja om du vill få information för varje enskild varning eller gruppera dem i en sammanställning per timme och sedan få ett meddelande.

![RS Vault inbyggda e-postavisering](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

När meddelandet har konfigurerats, får du ett e-postmeddelande med Välkommen eller inledande. Detta bekräftar att Azure Backup kan skicka e-postmeddelanden till dessa adresser när en avisering genereras.<br>

Om frekvensen ställdes in på en sammanställning per timme och en avisering har ut och löses inom en timme, kommer det inte vara en del av den kommande sammanställning per timme.

> [!NOTE]
>
* Om en destruktiv åtgärd som **stoppa skyddet med borttagningsdata** är utförs, en avisering genereras och ett e-postmeddelande skickas till prenumerationsägare, administratörer och medadministratörer även om meddelanden inte är konfigurerade för återställa tjänsten valvet.
* Aviseringar genereras inte för lyckad säkerhetskopiering därför avisering aktiveras inte. Konfigurera meddelande för lyckade jobb använder [aktivitetsloggen](backup-azure-monitoring-use-azuremonitor.md#using-rs-vaults-activity-logs) eller [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>Nästa steg

[Övervaka Azure backup-arbetsbelastningar med Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)

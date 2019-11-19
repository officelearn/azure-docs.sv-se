---
title: Övervaka Azure Backup skyddade arbets belastningar
description: I den här artikeln får du lära dig mer om övervaknings-och aviserings funktionerna för Azure Backup arbets belastningar med hjälp av Azure Portal.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: ea5102a95a9bef17f25219e00dec4654bf7f06d6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172867"
---
# <a name="monitoring-azure-backup-workloads"></a>Övervaka Azure Backup arbets belastningar

Azure Backup innehåller flera säkerhets kopierings lösningar baserade på säkerhets kopierings kravet och infrastruktur sto pol Ogin (lokalt vs Azure). Alla säkerhets kopierings användare och administratörer bör se vad som händer på alla lösningar och som förväntas bli aviserad i viktiga scenarier. Den här artikeln beskriver de övervaknings-och aviserings funktioner som tillhandahålls av Azure Backups tjänsten.

## <a name="backup-jobs-in-recovery-services-vault"></a>Säkerhets kopierings jobb i Recovery Services valvet

Azure Backup tillhandahåller inbyggda övervaknings-och aviserings funktioner för arbets belastningar som skyddas av Azure Backup. I inställningarna för Recovery Services valvet tillhandahåller **övervaknings** avsnittet inbyggda jobb och aviseringar.

![Inbyggd RS-valv övervakning](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Jobb skapas när åtgärder som att konfigurera säkerhets kopiering, säkerhets kopiering, återställning, borttagning av säkerhets kopiering och så vidare utförs.

Jobb från följande Azure Backup lösningar visas här:

- Säkerhetskopiering av virtuell Azure-dator
- Säkerhets kopiering av Azure-filer
- Azures arbets belastnings säkerhets kopiering, till exempel SQL
- Azure Backup-agent (MAB)

Jobb från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas inte.

> [!NOTE]
> Azure-arbetsbelastningar som SQL-säkerhetskopieringar i virtuella Azure-datorer har ett stort antal säkerhets kopierings jobb. Logg säkerhets kopior kan till exempel köras under var 15: e minut. För sådana databas arbets belastningar visas därför bara åtgärder som utlöses av användaren. Schemalagda säkerhets kopierings åtgärder visas inte.

## <a name="backup-alerts-in-recovery-services-vault"></a>Säkerhets kopierings aviseringar i Recovery Services valv

Aviseringar är främst scenarier där användare meddelas så att de kan vidta relevanta åtgärder. Avsnittet om **säkerhets kopierings aviseringar** visar aviseringar som genererats av tjänsten Azure Backup. De här aviseringarna definieras av tjänsten och användaren kan inte anpassa skapandet av aviseringar.

### <a name="alert-scenarios"></a>Aviserings scenarier

Följande scenarier definieras av service som aviserings bara scenarier.

- Fel vid säkerhetskopiering/återställning
- Säkerhetskopieringen slutfördes med varningar för Azure Backup Agent (MAB)
- Stoppa skyddet med Behåll data/stoppa skydd med ta bort data

### <a name="exceptions-when-an-alert-is-not-raised"></a>Undantag när en avisering inte höjs

Det finns några undantag när en avisering inte utlöses vid ett fel:

- Användaren avbröt ett jobb som körs uttryckligen
- Det går inte att utföra jobbet eftersom en annan säkerhets kopiering pågår (inget att agera här eftersom vi bara behöver vänta tills det tidigare jobbet har slutförts)
- Den virtuella datorns säkerhets kopierings jobb Miss lyckas eftersom den säkerhetskopierade virtuella Azure-datorn inte längre finns

Ovanstående undantag är utformade från förståelsen av att resultatet av dessa åtgärder (främst användare som utlöses) visas direkt på Portal/PS/CLI-klienter. Därför är användaren omedelbart medveten och behöver ingen avisering.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Aviseringar från följande Azure Backup lösningar visas här

- Säkerhetskopior av virtuella Azure-datorer
- Säkerhetskopior av Azure-fil
- Azure-arbetsbelastningssäkerhetskopior, till exempel SQL
- Azure Backup-agent (MAB)

> [!NOTE]
> Aviseringar från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas inte här.

### <a name="alert-types"></a>Aviseringstyper

Utifrån aviseringens allvarlighets grad kan aviseringar definieras i tre typer:

- **Kritiskt**: i princip skulle eventuella säkerhets kopierings-eller återställnings problem (schemalagda eller användare som utlöses) leda till generering av en avisering och visas som en kritisk varning och även destruktiva åtgärder som att ta bort säkerhets kopiering.
- **Varning**! om säkerhets kopieringen lyckas men med få varningar visas de som varnings aviseringar.
- **Information**: från och med idag genereras ingen informations avisering av Azure Backup tjänsten.

## <a name="notification-for-backup-alerts"></a>Meddelande om säkerhets kopierings aviseringar

> [!NOTE]
> Konfiguration av aviseringar kan bara göras via Azure Portal. Stöd för PS/CLI/REST API/Azure Resource Manager-mall stöds inte.

När en avisering har Aktiver ATS meddelas användarna. Azure Backup tillhandahåller en inbyggd meddelande funktion via e-post. En kan ange enskilda e-postadresser eller distributions listor som ska meddelas när en avisering genereras. Du kan också välja om du vill bli meddelad om varje enskild avisering eller gruppera dem i en Tim sammandrag och sedan få ett meddelande.

![Inbyggt e-postmeddelande för RS Vault](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

> [!NOTE]
> Aviseringarna för SQL-säkerhetskopieringar konsol IDE ras och e-postmeddelandet skickas bara för den första förekomsten. Men om aviseringen inaktive ras av användaren utlöses nästa förekomst av ett annat e-postmeddelande.

När ett meddelande har kon figurer ATS kommer du att få ett välkomst eller inledande e-postmeddelande. Detta bekräftar att Azure Backup kan skicka e-post till dessa adresser när en avisering utlöses.<br>

Om frekvensen har angetts till en Tim sammandrag och en avisering har genererats och lösts inom en timme, kommer den inte att ingå i den kommande Tim sammandrag.

> [!NOTE]
>
> - Om en destruktiv åtgärd, till exempel **stoppa skydd med ta bort data** , görs, aktive ras en avisering och ett e-postmeddelande skickas till prenumerations ägare, administratörer och medadministratörer även om aviseringar inte har kon figurer ATS för tjänsten för återställning av tjänst.
> - Om du vill konfigurera meddelanden för lyckade jobb använder [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Inaktivera aviseringar

Om du vill inaktivera/lösa en aktiv avisering kan du klicka på listobjektet som motsvarar den avisering som du vill inaktivera. Då öppnas en skärm som visar detaljerad information om aviseringen, med knappen inaktive rad överst. Om du klickar på den här knappen ändras aviseringens status till inaktiv. Du kan också inaktivera en avisering genom att högerklicka på listobjektet som motsvarar aviseringen och välja "inaktive rad".

![Inaktive ring av RS Vault-avisering](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Nästa steg

[Övervaka arbets belastningar för Azure Backup med Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)

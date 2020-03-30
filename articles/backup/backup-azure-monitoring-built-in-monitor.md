---
title: Övervaka Skyddade arbetsbelastningar för Azure Backup
description: I den här artikeln kan du läsa mer om övervaknings- och meddelandefunktionerna för Azure Backup-arbetsbelastningar med Hjälp av Azure-portalen.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: de5a82f5ad1d8113b27c07484f2f08f4cf97c759
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294923"
---
# <a name="monitoring-azure-backup-workloads"></a>Övervaka Azure Backup-arbetsbelastningar

Azure Backup tillhandahåller flera säkerhetskopieringslösningar baserat på säkerhetskopieringskravet och infrastrukturtopologin (Lokalt vs Azure). Alla användare eller administratörer för säkerhetskopiering bör se vad som händer i alla lösningar och förväntas meddelas i viktiga scenarier. I den här artikeln beskrivs övervaknings- och meddelandefunktionerna som tillhandahålls av Azure Backup-tjänsten.

## <a name="backup-jobs-in-recovery-services-vault"></a>Säkerhetskopieringsjobb i valvet för återställningstjänster

Azure Backup tillhandahåller inbyggda övervaknings- och aviseringsfunktioner för arbetsbelastningar som skyddas av Azure Backup. I inställningarna för Återställningstjänster tillhandahåller avsnittet **Övervakning** inbyggda jobb och aviseringar.

![RS valv inbyggd övervakning](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Jobb genereras när åtgärder som konfigurerar säkerhetskopiering, säkerhetskopiering, återställning, borttagning av säkerhetskopiering och så vidare utförs.

Jobb från följande Azure Backup-lösningar visas här:

- Säkerhetskopiering av virtuell Azure-dator
- Säkerhetskopiering av Azure-filer
- Azure-säkerhetskopiering av arbetsbelastninger som SQL och SAP HANA
- Azure Backup-agent (MAB)

Jobb från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas INTE.

> [!NOTE]
> Azure-arbetsbelastningar som SQL- och SAP HANA-säkerhetskopior i virtuella Azure-datorer har ett stort antal säkerhetskopieringsjobb. Till exempel kan loggsäkerhetskopior köras var 15:e minut. Därför visas endast användarutlösta åtgärder för sådana DB-arbetsbelastningar. Schemalagda säkerhetskopieringsåtgärder visas INTE.

## <a name="backup-alerts-in-recovery-services-vault"></a>Säkerhetskopior i valvet för återställningstjänster

Aviseringar är i första hand scenarier där användare meddelas så att de kan vidta relevanta åtgärder. Avsnittet **Säkerhetskopieringsvarningar** visar aviseringar som genereras av Azure Backup-tjänsten. Dessa aviseringar definieras av tjänsten och användaren kan inte anpassa skapa några aviseringar.

### <a name="alert-scenarios"></a>Varningsscenarier

Följande scenarier definieras av tjänst som aviseringsbara scenarier.

- Fel vid säkerhetskopiering/återställning
- Säkerhetskopieringen slutfördes med varningar för Azure Backup Agent (MAB)
- Stoppa skyddet med behålla data/Stoppskydd med borttagningsdata

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Aviseringar från följande Azure Backup-lösningar visas här

- Säkerhetskopior av virtuella Azure-datorer
- Säkerhetskopior av Azure-fil
- Azure-arbetsbelastningssäkerhetskopior som SQL, SAP HANA
- Azure Backup-agent (MAB)

> [!NOTE]
> Aviseringar från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas INTE här.

### <a name="consolidated-alerts"></a>Konsoliderade aviseringar

För Azure-säkerhetskopieringslösningar för arbetsbelastning som SQL och SAP HANA kan loggsäkerhetskopior genereras mycket ofta (upp till var 15:e minut enligt principen). Så det är också möjligt att loggen backup fel är också mycket vanliga (upp till var 15 minuter). I det här fallet kommer slutanvändaren att bli överväldigad om en avisering utlöses för varje felförekomst. Så en avisering skickas för den första förekomsten och om de efterföljande felen är på grund av samma grundorsak, genereras inte ytterligare aviseringar. Den första aviseringen uppdateras med antalet misslyckade försök. Men om aviseringen inaktiveras av användaren utlöser nästa förekomst en annan avisering och detta kommer att behandlas som den första aviseringen för den förekomsten. Det här är hur Azure Backup utför aviseringskonsolidering för SQL- och SAP HANA-säkerhetskopior.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Undantag när en avisering inte utlöses

Det finns få undantag när en avisering inte utlöses vid ett fel. De är:

- Användaren avbröt uttryckligen det jobb som körs
- Jobbet misslyckas eftersom en annan backup jobb pågår (inget att agera på här eftersom vi bara måste vänta på det tidigare jobbet att avsluta)
- Säkerhetskopieringsjobbet för virtuella datorer misslyckas eftersom den säkerhetskopierade Azure-virtuella datorn inte längre finns
- [Konsoliderade aviseringar](#consolidated-alerts)

Ovanstående undantag är utformade från insikten att resultatet av dessa åtgärder (främst användarutlösta) visas omedelbart på portal/PS/CLI-klienter. Så användaren är omedelbart medveten och behöver inte ett meddelande.

### <a name="alert-types"></a>Aviseringstyper

Baserat på allvarlighetsgrad för aviseringar kan aviseringar definieras i tre typer:

- **Kritisk:** I princip skulle alla säkerhetskopierings- eller återställningsfel (schemalagda eller utlösare av användaren) leda till generering av en avisering och visas som en kritisk avisering och även destruktiva åtgärder som att ta bort säkerhetskopiering.
- **Varning**: Om säkerhetskopieringen lyckas men med få varningar visas de som varningsaviseringar.
- **Information :** Från och med idag genereras ingen informationsavisering av Azure Backup-tjänsten.

## <a name="notification-for-backup-alerts"></a>Avisering för aviseringar om säkerhetskopiering

> [!NOTE]
> Konfiguration av meddelanden kan endast göras via Azure Portal. PS/CLI/REST API/Azure Resource Manager-mallstöd stöds inte.

När en avisering har höjts meddelas användarna. Azure Backup tillhandahåller en inbyggd meddelandemekanism via e-post. Man kan ange enskilda e-postadresser eller distributionslistor som ska meddelas när en avisering genereras. Du kan också välja om du vill få ett meddelande för varje enskild avisering eller gruppera dem i en sammanfattning per timme och sedan få ett meddelande.

![E-postmeddelande med inbyggt e-postmeddelande för RS Vault](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

När meddelandet är konfigurerat får du ett välkomst- eller introduktionsmeddelande. Detta bekräftar att Azure Backup kan skicka e-postmeddelanden till dessa adresser när en avisering utlöses.<br>

Om frekvensen var inställd på en timsammandrag och en avisering höjdes och löstes inom en timme, kommer det inte att vara en del av den kommande sammanfattningen per timme.

> [!NOTE]
>
> - Om en destruktiv åtgärd som **stoppskydd med borttagningsdata** utförs, utlöses en avisering och ett e-postmeddelande skickas till prenumerationsägare, administratörer och medadministratörer även om meddelanden INTE är konfigurerade för valvet för återställningstjänsten.
> - Logga [Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)om du vill konfigurera meddelanden för lyckade jobb .

## <a name="inactivating-alerts"></a>Inaktivera aviseringar

Om du vill inaktivera/lösa en aktiv avisering kan du klicka på listobjektet som motsvarar den avisering som du vill inaktivera. Detta öppnar en skärm som visar detaljerad information om aviseringen, med knappen "Inaktivera" högst upp. Om du klickar på den här knappen ändras statusen för aviseringen till "Inaktiv". Du kan också inaktivera en avisering genom att högerklicka på listobjektet som motsvarar aviseringen och välja "Inaktivera".

![Inaktivering av RS Vault-avisering](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Nästa steg

[Övervaka Azure-arbetsbelastningar för säkerhetskopiering med Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
